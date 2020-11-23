# io_uring

- [hands-on-iouring-go](https://developers.mattermost.com/blog/hands-on-iouring-go/)
- [lwn.net](https://lwn.net/Articles/810414/)

- 헷갈리는 관계
  - AIO
  - epoll
  - io_uring

Linux system call의 네트워크 I/O에서 상당히 빠른 성능을 보여주는 epoll이 File I/O부분에서는 약하다는 단점에서 이를 뛰어넘는 성능을 보여주는 io_uring이 있다는 소식을 아는 동생을 통해 전해듣게 되었다. 이를 계기로 io_uring에 대해 정리해보고자 한다. (**기존에는 network I/O부분에서만 비동기를 지원했지만 io_uring을 활용하면 file I/O작업에서도 비동기 처리가 가능하다.**)

## TL;DR

- epoll의 고질적인 문제점은 커널과 어플리케이션 간 system call이 발생한다는 점이다.
- system call이 문제가 되는 이유는 해당 호출의 비용이 크다는 점이다.
  - syscall
    1. call from application
    2. copy of the data to kernel space
    3. execute
    4. copy of data to user space
    5. return
    6. 이 과정동안 **syscall remains blocked**
- `io_uring`해결1: 블록킹에 대한 문제는 고루틴을 통해 asyncI/O로 처리 (go로 처리했을 경우)
- `io_uring`해결2: copy로 메모리 공유는 user-space와 kernel-space를 공유 메모리로 존재하는 링버퍼로 해결


## `io_uring`해결1: goroutine
between the application and the kernel에 새로운 layer를 추가하였다.(**virtual entity commonly referred to as P**). 가상 entity(고루틴들 큐: OS threads = 1:1)은 syscall이 발생할 때, runtime이 이를 알아차리기 위해서 
It uses a virtual entity () which contains a queue of goroutines to run, which is then mapped to OS threads.

This level of indirection allows it to do some interesting optimizations. Whenever we make a blocking syscall, the runtime is aware of it, and it detaches the thread from the P executing the goroutine, and acquires a new thread to execute other goroutines. This is known as a hand-off. And when the syscall returns, the runtime tries to re-attach it to a P. If it cannot get a free P, it just pushes the goroutine to a queue to be executed later, and stores the thread in a pool. This is how Go gives the appearance of “non-blocking”-ness when your code enters a system call.



## kernel에서 소개 하는 io_uring

- https://kernel.dk/io_uring.pdf

읽는 중