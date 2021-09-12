### 源码分析



### 代码示例

**服务器**

```cpp
#include <arpa/inet.h>
#include <cassert>
#include <iostream>
#include <netinet/in.h>
#include <string>
#include <sys/epoll.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <unistd.h>

int main() {
    int server_fd = socket(AF_INET, SOCK_STREAM, 0);
    assert(server_fd != -1);

    struct sockaddr_in server_addr;
    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(8080);
    server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
    int ret = bind(server_fd, (struct sockaddr *)&server_addr, sizeof(server_addr));
    assert(ret != -1);

    ret = listen(server_fd, 1024);
    assert(ret != -1);

    int epfd = epoll_create(1);
    assert(epfd != -1);

    struct epoll_event event, ready_events[8];
    event.events = EPOLLIN;
    event.data.fd = server_fd;
    ret = epoll_ctl(epfd, EPOLL_CTL_ADD, server_fd, &event);
    assert(ret != -1);
    std::cout << "epoll_ctl add listen fd." << std::endl;

    while (true) {
        std::cout << "waiting ready_events." << std::endl;
        int ready_size = epoll_wait(epfd, ready_events, sizeof(ready_events) / sizeof(struct epoll_event), -1);
        assert(ready_size != -1);

        for (int i = 0; i < ready_size; ++i) {
            if (ready_events[i].data.fd == server_fd) {
                struct sockaddr_in client_addr;
                socklen_t client_addr_length = sizeof(client_addr);

                int client_fd = accept(server_fd, (struct sockaddr *)&client_addr, &client_addr_length);
                assert(client_fd != -1);
                std::cout << "accepted new connect." << std::endl;

                event.events = EPOLLIN | EPOLLET; // 边缘触发
                event.data.fd = client_fd;
                ret = epoll_ctl(epfd, EPOLL_CTL_ADD, client_fd, &event);
                assert(ret != -1);
            } else {
                int client_fd = ready_events[i].data.fd;

                char buf[1024] = {0};
                ssize_t recv_size = recv(client_fd, (void *)buf, sizeof(buf), 0);
                assert(recv_size != -1);
                std::cout << "RECV: " << buf << std::endl;

                std::string send_str = "i have recv: " + std::string(buf);
                ssize_t send_size = send(client_fd, (void *)send_str.c_str(), send_str.size() + 1, 0);
                assert(send_size != -1);
                std::cout << "SEND: " << send_str << std::endl;

                ret = epoll_ctl(epfd, EPOLL_CTL_DEL, client_fd, &event);
                assert(ret != -1);
                /* close(client_fd); // 关闭连接epoll会自动移除此文件描述符 */
            }
        }
    }

    close(server_fd);

    return 0;
}
```

**客户端**

```cpp
#include <arpa/inet.h>
#include <cassert>
#include <iostream>
#include <netinet/in.h>
#include <string>
#include <sys/epoll.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <time.h>
#include <unistd.h>

int main() {
    int client_fd = socket(AF_INET, SOCK_STREAM, 0);
    assert(client_fd != -1);

    struct sockaddr_in server_addr;
    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(8080);
    server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");
    int ret = connect(client_fd, (struct sockaddr *)&server_addr, sizeof(server_addr));
    assert(ret != -1);

    std::string data;
    std::cin >> data;

    ssize_t send_size = send(client_fd, data.c_str(), data.size() + 1, 0);
    assert(send_size != -1);
    std::cout << "SEND: " << data << std::endl;

    char buf[1024];
    ssize_t recv_size = recv(client_fd, (void *)buf, sizeof(buf), 0);
    assert(recv_size != -1);
    std::cout << "RECV: " << buf << std::endl;

    close(client_fd);

    return 0;
}
```

