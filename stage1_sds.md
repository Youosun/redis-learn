## 动态字符串sds的实现 

**SDS的定义**
```
struct sdshdr {
    int len;         //记录buf数组中已使用的字节的数量  等于SDS所保存的字符串的长度
    int free;        //buf数组中未使用的字节数量
    char buf[];      //字节数组，保存字符串
}

typedef char* sds
```
- len字段   可以保证以常数的复杂度获得字符串的长度
- free字段  表示当前已分配的空间中未使用的字节长度
            在使用一些c字符串函数时可杜绝缓冲区溢出风险 同时减少内存的分配次数
            strcat、 strcpy等
- buf数组   真正保存了字符串的空间
            兼容了C字符串的'\0'格式  同时也可支持保存二进制格式的数据

**学习几个相关的API**
```
sds sdsnew(const char* init);  //使用一个C串 初始化一个sds
{
    ...
    sh = s_malloc(hdrlen + initlen + 1);
    s = (char*)sh + hdrlen;
    if(initlen && init)    //当使用一个非空串初始化sds时
        memcpy(s, init, initlen);
    s[initlen] = '\0';     //保证C字符串风格
    return s;
}

//去除sds串两端在cset中出现的字符
sds sdstrim(sds s, const char* *cset) {
    char *start, *end, *sp, *ep;
    size_t len;
    
    sp = start = s;
    ep = end = s + sdslen(s) - 1;
    while(sp <= end && strchr(cset, *sp)) sp++;
    while(ep > sp && strchr*cset, *ep)) ep--;
    len = (sp > ep) ? 0 : ((ep - sp) + 1);
    if(s != sp) memmove(s, sp, len);
    s[len] = '\0';
    sdssetlen(s, len);
    return s;
}

sds sdscat(sds s, const char *t) {
    return sdscatlen(s, t, strlen(t));
}

sds sdscatlen(sds s, const void *t, size_t len) {
    size_t curlen = sdslen(s);
    
    s = sdsMakeRoomFor(s, len);     //检查free空间是否足够， 不足时分配
    if(s == NULL) return NULL;
    memcpy(s + curlen, t, len);
    sdssetlen(s, curlen+len);
    s[curlen+len] = '\0';
    return s;
}

//关于sds内存分配的几个wrapper函数
//当使用不同的allocator时，只需要实现不同的s_malloc s_realloc s_free函数即可
void *sds_malloc(size_t size) {return s_malloc(size);}
void *sds_reallloc(void *ptr, size_t size) {return s_realloc(ptr,size);}
void *sds_free(void *ptr) {s_free(ptr);}
```