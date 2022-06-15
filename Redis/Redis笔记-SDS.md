## 说明

&emsp;&emsp;`Redis`命令执行效果截图基于3.0.504版本，源码基于`GitHub`上的[redis-3.0-annotated](https://github.com/huangz1990/redis-3.0-annotated)项目。

![](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20220222170542.png)



## SDS的用途

&emsp;&emsp;Redis没有直接使用 C 语言传统的字符串表示（以空字符结尾的字符数组，以下简称 C 字符串）， 而是自己构建了一种名为简单动态字符串（simple dynamic string，SDS）的抽象类型，并将 SDS 用作 Redis 的默认字符串表示。

&emsp;&emsp;Redis 键值对中的键是一个字符串对象，该对象的底层实现是一个保存了字符串的 SDS。Redis 键值对中的值也可以是一个字符串对象，该对象的底层实现同样是一个保存了字符串的 SDS。

&emsp;&emsp;除了用来保存数据库中的字符串值之外，SDS 还被用作缓冲区（buffer）：AOF 模块中的 AOF 缓冲区，以及客户端状态中的输入缓冲区，都是由 SDS 实现的。

&emsp;&emsp;`注`: string 并不总是由 SDS 实现，当数据为整数值并且可以用 long 类型表示时会使用 long 来实现。

![](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20220223115538.png)



## SDS定义

&emsp;&emsp;SDS 定义在 sds.h/sdshdr 中。

```c
/*
 * 保存字符串对象的结构
 */
struct sdshdr {

    // 记录 buf 数组中已使用字节的数量
    // 等于 SDS 所保存字符串的长度
    int len;

    // 记录 buf 数组中未使用字节的数量
    int free;

    // 字节数组，用于保存字符串
    char buf[];
};
```



## 为什么使用SDS

&emsp;&emsp;C 语言使用长度为 `N+1` 的字符数组来表示长度为 `N` 的字符串，并且字符数组的最后一个元素总是空字符 `'\0'` 。这样的字符串表示方式不能满足 Redis 对字符串在安全性、效率、以及功能方面的要求。 SDS 比 C 字符串相比之下具有以下的优点。

### 常数复杂度获取字符串长度

&emsp;&emsp;获取一个 C 字符串的长度，需要遍历整个字符串，直到遇到代表字符串结尾的空字符为止，这个操作的复杂度为 O(N) 。而 SDS 在 `len` 属性中记录了 SDS 本身的长度，所以获取一个 SDS 长度的复杂度仅为 O(1) 。

&emsp;&emsp;通过使用 SDS 而不是 C 字符串，Redis 将获取字符串长度所需的复杂度从 O(N) 降低到了 O(1) ，这确保了获取字符串长度的工作不会成为 Redis 的性能瓶颈。下面是获取 SDS 长度和可用空间长度的函数。

```c
/*
 * 返回 sds 实际保存的字符串的长度
 *
 * T = O(1)
 */
static inline size_t sdslen(const sds s) {
    struct sdshdr *sh = (void*)(s-(sizeof(struct sdshdr)));
    return sh->len;
}

/*
 * 返回 sds 可用空间的长度
 *
 * T = O(1)
 */
static inline size_t sdsavail(const sds s) {
    struct sdshdr *sh = (void*)(s-(sizeof(struct sdshdr)));
    return sh->free;
}
```

### 杜绝缓冲区溢出

&emsp;&emsp;C 字符串进行修改时，需要用户来保证有足够的内存容纳字符串中的所有内容，一旦用户分配的内存不足就会产生缓冲区溢出的问题。

&emsp;&emsp;而当 SDS API 需要对 SDS 进行修改时，API 会先检查 SDS 的空间是否满足修改所需的要求，如果不满足的话，API 会自动将 SDS 的空间扩展至执行修改所需的大小，然后才执行实际的修改操作，所以使用 SDS 既不需要手动修改 SDS 的空间大小，也不会出现缓冲区溢出的问题。

### 减少修改字符串时带来的内存重分配次数

&emsp;&emsp;C 字符串的长度和底层数组的长度之间存在着关联性，所以每次增长或者缩短一个 C 字符串，程序都总要对保存这个 C 字符串的数组进行一次内存重分配操作：

- 如果程序执行的是增长字符串的操作，比如拼接操作（append），那么在执行这个操作之前，程序需要先通过内存重分配来扩展底层数组的空间大小 —— 如果忘了这一步就会产生缓冲区溢出。
- 如果程序执行的是缩短字符串的操作，比如截断操作（trim），那么在执行这个操作之后， 程序需要通过内存重分配来释放字符串不再使用的那部分空间 —— 如果忘了这一步就会产生内存泄漏。

&emsp;&emsp; 因为内存重分配涉及复杂的算法，并且可能需要执行系统调用，所以它通常是一个比较耗时的操作：

- 在一般程序中，如果修改字符串长度的情况不太常出现，那么每次修改都执行一次内存重分配是可以接受的。
- 但是 Redis 作为数据库，经常被用于速度要求严苛、数据被频繁修改的场合，如果每次修改字符串的长度都需要执行一次内存重分配的话，那么光是执行内存重分配的时间就会占去修改字符串所用时间的一大部分，如果这种修改频繁地发生的话，可能还会对性能造成影响。

&emsp;&emsp;为了避免 C 字符串的这种缺陷，SDS 通过未使用空间解除了字符串长度和底层数组长度之间的关联：在 SDS 中， `buf` 数组的长度不一定就是字符数量加一，数组里面可以包含未使用的字节，而这些字节的数量就由 SDS 的 `free` 属性记录。

&emsp;&emsp;通过未使用空间，SDS 实现了空间预分配和惰性空间释放两种优化策略。

#### 空间预分配

&emsp;&emsp;空间预分配用于优化 SDS 的字符串增长操作：当 SDS 的 API 对一个 SDS 进行修改，并且需要对 SDS 进行空间扩展的时候，程序不仅会为 SDS 分配修改所必须要的空间，还会为 SDS 分配额外的未使用空间。

其中，额外分配的未使用空间数量由以下公式决定：

- 如果对 SDS 进行修改之后，SDS 的长度（也即是 `len` 属性的值）将小于 `1 MB` ，那么程序分配和 `len` 属性同样大小的未使用空间，这时 SDS `len` 属性的值将和 `free` 属性的值相同。举个例子，如果进行修改之后，SDS 的 `len` 将变成 `13` 字节，那么程序也会分配 `13` 字节的未使用空间，SDS 的 `buf` 数组的实际长度将变成 `13 + 13 + 1 = 27` 字节（额外的一字节用于保存空字符）。

- 如果对 SDS 进行修改之后， SDS 的长度将大于等于 `1 MB` ，那么程序会分配 `1 MB` 的未使用空间。举个例子，如果进行修改之后，SDS 的 `len` 将变成 `30 MB` ，那么程序会分配 `1 MB` 的未使用空间，SDS 的 `buf` 数组的实际长度将为 `30 MB + 1 MB + 1 byte` 。

&emsp;&emsp;在扩展 SDS 空间之前， SDS API 会先检查未使用空间是否足够， 如果足够的话， API 就会直接使用未使用空间， 而无须执行内存重分配。通过这种预分配策略， SDS 将连续增长 `N` 次字符串所需的内存重分配次数从必定 `N` 次降低为最多 `N` 次。SDS 分配空间的函数在 sds.c 中实现。

```c
/* Enlarge the free space at the end of the sds string so that the caller
 * is sure that after calling this function can overwrite up to addlen
 * bytes after the end of the string, plus one more byte for nul term.
 * 
 * Note: this does not change the *length* of the sds string as returned
 * by sdslen(), but only the free buffer space we have. */
/*
 * 对 sds 中 buf 的长度进行扩展，确保在函数执行之后，
 * buf 至少会有 addlen + 1 长度的空余空间
 * （额外的 1 字节是为 \0 准备的）
 *
 * 返回值
 *  sds ：扩展成功返回扩展后的 sds
 *        扩展失败返回 NULL
 *
 * 复杂度
 *  T = O(N)
 */
sds sdsMakeRoomFor(sds s, size_t addlen) {

    struct sdshdr *sh, *newsh;

    // 获取 s 目前的空余空间长度
    size_t free = sdsavail(s);

    size_t len, newlen;

    // s 目前的空余空间已经足够，无须再进行扩展，直接返回
    if (free >= addlen) return s;

    // 获取 s 目前已占用空间的长度
    len = sdslen(s);
    sh = (void*) (s-(sizeof(struct sdshdr)));

    // s 最少需要的长度
    newlen = (len+addlen);

    // 根据新长度，为 s 分配新空间所需的大小
    if (newlen < SDS_MAX_PREALLOC)
        // 如果新长度小于 SDS_MAX_PREALLOC 
        // 那么为它分配两倍于所需长度的空间
        newlen *= 2;
    else
        // 否则，分配长度为目前长度加上 SDS_MAX_PREALLOC
        newlen += SDS_MAX_PREALLOC;
    // T = O(N)
    newsh = zrealloc(sh, sizeof(struct sdshdr)+newlen+1);

    // 内存不足，分配失败，返回
    if (newsh == NULL) return NULL;

    // 更新 sds 的空余长度
    newsh->free = newlen - len;

    // 返回 sds
    return newsh->buf;
}

/*
 * 最大预分配长度
 */
#define SDS_MAX_PREALLOC (1024*1024)// 定义在sds.h中
```

#### 惰性空间释放

&emsp;&emsp;惰性空间释放用于优化 SDS 的字符串缩短操作： 当 SDS 的 API 需要缩短 SDS 保存的字符串时， 程序并不立即使用内存重分配来回收缩短后多出来的字节， 而是使用 `free` 属性将这些字节的数量记录起来， 并等待将来使用。

&emsp;&emsp;通过惰性空间释放策略， SDS 避免了缩短字符串时所需的内存重分配操作， 并为将来可能有的增长操作提供了优化。与此同时， SDS 也提供了相应的 API ， 让我们可以在有需要时， 真正地释放 SDS 里面的未使用空间， 所以不用担心惰性空间释放策略会造成内存浪费。

### 二进制安全

&emsp;&emsp;C 字符串中的字符必须符合某种编码（比如 ASCII）， 并且除了字符串的末尾之外， 字符串里面不能包含空字符， 否则最先被程序读入的空字符将被误认为是字符串结尾 —— 这些限制使得 C 字符串只能保存文本数据， 而不能保存像图片、音频、视频、压缩文件这样的二进制数据。

&emsp;&emsp;虽然数据库一般用于保存文本数据， 但使用数据库来保存二进制数据的场景也不少见， 因此， 为了确保 Redis 可以适用于各种不同的使用场景， SDS 的 API 都是二进制安全的（binary-safe）： 所有 SDS API 都会以处理二进制的方式来处理 SDS 存放在 `buf` 数组里的数据， 程序不会对其中的数据做任何限制、过滤、或者假设 —— 数据在写入时是什么样的， 它被读取时就是什么样。

&emsp;&emsp;这也是我们将 SDS 的 `buf` 属性称为字节数组的原因 —— Redis 不是用这个数组来保存字符， 而是用它来保存一系列二进制数据。通过使用二进制安全的 SDS ， 而不是 C 字符串， 使得 Redis 不仅可以保存文本数据， 还可以保存任意格式的二进制数据。

### 兼容部分 C 字符串函数

&emsp;&emsp;虽然 SDS 的 API 都是二进制安全的， 但它们一样遵循 C 字符串以空字符结尾的惯例： 这些 API 总会将 SDS 保存的数据的末尾设置为空字符， 并且总会在为 `buf` 数组分配空间时多分配一个字节来容纳这个空字符， 这是为了让那些保存文本数据的 SDS 可以重用一部分 `<string.h>` 库定义的函数。



## 总结

- Redis 只会使用 C 字符串作为字面量， 在大多数情况下， Redis 使用 SDS （Simple Dynamic String，简单动态字符串）作为字符串表示。
- 比起 C 字符串， SDS 具有以下优点：
  1. 常数复杂度获取字符串长度。
  2. 杜绝缓冲区溢出。
  3. 减少修改字符串长度时所需的内存重分配次数。
  4. 二进制安全。
  5. 兼容部分 C 字符串函数。



## 参考资料

《Redis 设计与实现》<http://redisbook.com/>