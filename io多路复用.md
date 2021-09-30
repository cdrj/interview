[图解 | 原来这就是 IO 多路复用 - 闪客sun - 博客园 (cnblogs.com)](https://www.cnblogs.com/flashsun/p/14591563.html)



## 用户态的多路复用

每 accept 一个客户端连接后，将这个文件描述符（connfd）放到一个数组里。

```
fdlist.add(connfd);
```

然后弄一个新的线程去不断遍历这个数组，调用每一个元素的非阻塞 read 方法。

```
while(1) {
  for(fd <-- fdlist) {
    if(read(fd) != -1) {
      doSomeThing();
    }
  }
}
```

这样，我们就成功用一个线程处理了多个客户端连接。

![img](https://p6-tt-ipv6.byteimg.com/img/pgc-image/8c9573d80f5044a98f34718d07400fc5~tplv-obj.image)



### 缺点

每次遍历遇到 read 返回 -1 时仍然是一次浪费资源的系统调用。

## select

首先一个线程不断接受客户端连接，并把 socket 文件描述符放到一个 list 里。

```c++
while(1) {
  connfd = accept(listenfd);
  fcntl(connfd, F_SETFL, O_NONBLOCK);
  fdlist.add(connfd);
}
```

然后，另一个线程不再自己遍历，而是调用 select，将这批文件描述符 list 交给操作系统去遍历。

```c++
while(1) {
  // 把一堆文件描述符 list 传给 select 函数
  // 有已就绪的文件描述符就返回，nready 表示有多少个就绪的
  nready = select(list);
  ...
}
```

将连接的文件描述符fd放到一个数组list里面，将list交给select函数，拷贝一份到内核，select在内核层遍历检查文件描述符的就绪状态，

最后返回就绪的数量，只不过，操作系统会将准备就绪的文件描述符做上标识，具体哪个可读还需要用户去遍历

![img](https://p6-tt-ipv6.byteimg.com/img/pgc-image/567fa1ffacc84953ba48cb8074acce03~tplv-obj.image)

## poll

它和 select 的主要区别就是，去掉了 select 只能监听 1024 个文件描述符的限制。

## epoll

1. 内核中保存一份文件描述符集合，无需用户每次都重新传入，只需告诉内核修改的部分即可。

2. 内核不再通过轮询的方式找到就绪的文件描述符，而是通过异步 IO 事件唤醒。

3. 内核仅会将有 IO 事件的文件描述符返回给用户，用户也无需遍历整个文件描述符集合。

![img](https://p26-tt.byteimg.com/img/pgc-image/24b34419c9c4404c93ceba46a6e013f7~tplv-obj.image)