---
title: 计算机网络TCP协议
date: 2019-06-11 17:10:47
tags: TCP
categories: 计算机网络
---

注意：octet 与 byte 相同都是指 8 位 1 字节，但在计算机网络中 byte 易引起混淆，故都用 octet，译为八位字节

### TCP 头部格式

```text
TCP Header Format

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |          Source Port          |       Destination Port        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                        Sequence Number                        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                    Acknowledgment Number                      |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |  Data |           |U|A|P|R|S|F|                               |
   | Offset| Reserved  |R|C|S|S|Y|I|            Window             |
   |       |           |G|K|H|T|N|N|                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           Checksum            |         Urgent Pointer        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                    Options                    |    Padding    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                             data                              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

                            TCP Header Format

          Note that one tick mark represents one bit position.
```

TCP 协议头部一般为 20 字节，最大不超过 60 字节，且是一个整数的 32 位长。

<!-- more -->

Source Port：源端口号，16 位

Destination Port：目标端口号，16 位

Sequence Number：该数据分片第一个数据八位字节的序列号，32 位，该字段用于正确组装数据分片（segment，也译为数据包或数据分段），TCP 可靠性依赖于序列号实现。当 SYN 控制位存在时，则序列号为初始序列号 ISN，第一个数据八位字节的序列号为 ISN + 1

Acknowledgment Number：确认号，32 位，若设置了 ACK 控制位，确认号代表的值为发送方期望收到的下一个数据分片的序列号，当连接建立后，确认号总是会被发送

Data Offset：数据偏移，4 位，代表 TCP 头部有多少个 32 位词。这指示了数据部分从哪里开始。

Reserved：保留字，6 位，为以后使用预留，必需为 0

Control Bits：控制位，6 位

- URG：紧急指针字段标识

- ACK：确认字段标识

- PSH：推送功能

- RST：重置连接

- SYN：同步序列号，只在请求建立连接时存在，即仅在三次握手的前两次中使用同步位，代表序列号为初始序列号 ISN

- FIN：发送方无更多数据

Window：窗口大小，16 位，发送方能够接收到缓冲区八位字节数据数量

Checksum：校验和，16 位，由发送端计算，接收端验证的判断数据分片头部与正文是否有差错的字段

Urgent Pointer：紧急指针，16 位，代表在序列号基础上的正偏移量，指向紧急数据之后的八位字节序列号，这个字段只在 URG 控制位存在的情况下生效

Options：TCP 头部可选项，变长字段，在 TCP 头部的末尾，是八位字节的倍数

Padding：填充项，变长，补零以保证 TCP 头部结束与数据开始于 32 位的边界。

### TCP 状态图

![tcp-state-transition-diagram.png](/images/tcp-state-transition-diagram.png)

#### 状态说明

LISTEN - 代表等待从远程端口来的 TCP 连接请求

SYN-SENT - 代表发送连接请求后等待匹配的连接

SYN-RECEIVED - 代表已经接收了连接请求并回复了连接确认请求后等待对方的连接确认请求

ESTABLISHED - 代表一个开放连接，接收到的数据可以传送给用户。这是一个连接在数据传输阶段的通常状态

FIN-WAIT-1 - 代表发送了连接终止请求后，等待远程 TCP 的连接的确认请求（前置状态为 ESTABLISHED）

FIN-WAIT-2 - 代表收到了远程 TCP 连接对于终止请求的确认请求后，等待远程 TCP 的连接的终止请求（前置状态为 FIN-WAIT-2）

CLOSE-WAIT - 代表收到远程 TCP 连接的终止请求并发送了确认请求后，等待关闭，此时会判断是否还有数据要发送（前置状态为 ESTABLISHED）

CLOSING - 代表向远程 TCP 连接发送连接终止请求后没有收到对方的确认请求，而是收到了远程 TCP 的连接终止请求，这时会向远程 TCP 发送终止确认请求。CLOSING 状态不常见，几乎双方同时发送连接终止请求才会出现（前置状态为 FIN-WAIT-1）

LAST-ACK - 代表待发送的数据已经发送完成，并发送了连接终止请求，等待之前发送给远程 TCP 连接终止请求的确认请求（前置状态为 CLOSE-WAIT）

TIME-WAIT - 代表等待足够的时间以确保远程 TCP 收到了其连接终止请求的确认请求（前置状态为 FIN-WAIT-2）

CLOSED - 代表无连接

### TCP 连接流程图

![tcp.png](/images/tcp.png)

### TCP 三次握手

#### 为什么要握手三次

> 为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误
> ——《计算机网络》谢希仁
>
> 防止服务器开销的浪费
> 两次握手已经建立了全双工的通信。这个通信将占用客户端与服务器的通信线路，直到它被一方关闭为止。
> 如果没有请求方的第三次的发送确认数据，且网络环境不佳的情况下，一旦客户端超时没有收到服务器的数据（可能是客户端发往服务端的数据慢了，也可能是服务端确认的数据慢了），客户端就会重新请求建立连接。
>
> 1. 如果是客户端往服务端发送的请求慢了但最终还是被收到了，那么服务端就会认为这是客户端的新请求，建立了多余的连接，浪费了服务器资源。
> 2. 如果是服务端返回的确认慢了，客户端认为超时了，那么服务端其实在返回确认的时候就已经建立了连接，而客户端认为连接失败了，重新请求，那么又建立多余连接，浪费了服务器资源。
> ——来源于网络
>
> A：喂，你能听到我说话吗？
> B：我能听到，你能听到我说话吗？
> A：我也能听到你说话，今天 blabla
> ——常见类比

准确地说，以上几种说法与类比仅仅是从某一个方面片面地解释了为什么 TCP 握手要三次的表因，而没有把握到三次握手的本质原因。

让我们来看看[RFC793](https://www.ietf.org/rfc/rfc793.txt)（TCP 协议的 RFC）中关于可靠性的描述

> Reliability:
>
> The TCP must recover from data that is damaged, lost, duplicated, or delivered out of order by the internet communication system.  This is achieved by assigning a sequence number to each octet transmitted, and requiring a positive acknowledgment (ACK) from the receiving TCP.  If the ACK is not received within a timeout interval, the data is retransmitted.  At the receiver, the sequence numbers are used to correctly order segments that may be received out of order and to eliminate duplicates.  Damage is handled by adding a checksum to each segment transmitted, checking it at the receiver, and discarding damaged segments.
>
> As long as the TCPs continue to function properly and the internet system does not become completely partitioned, no transmission errors will affect the correct delivery of data.  TCP recovers from internet communication system errors.

可靠性：TCP 必须从损坏的、丢失的、重复的和因网络问题乱序的数据中恢复正确的数据。这样的可靠性是基于发送每个八位字节时都带有一个序列号，并且还要求要有一个来自于已接收 TCP 包中的主动确认号。如果在一个超时间隔内，没有收到确认号，那么数据将会被重传。对接收方而言，序列号用于正确排序收到的乱序数据分片（数据包）与丢弃重复的数据分片（数据包）。我们通过在每一个数据分片中加入一个校验和来处理数据损坏的情况，接收方将会检查并丢弃损坏的数据分片。只要 TCPs 工作正常并且网络系统没有被完全分隔，那么传输错误将不会影响到正确的数据接收，TCP 可以从网络错误中恢复。

那么为什么要用三次握手是必要的呢？RFC 中有云：

> A three way handshake is necessary because sequence numbers are not tied to a global clock in the network, and TCPs may have different mechanisms for picking the ISN's.  The receiver of the first SYN has no way of knowing whether the segment was an old delayed one or not, unless it remembers the last sequence number used on the connection (which is not always possible), and so it must ask the sender to verify this SYN.

三次握手之所以必要是因为序列号并没有在网络中绑定全局时钟，并且 TCPs 可能有不同的机制来选取初始序列号 ISN。接收方无法判断第一个获得的数据分片（或称数据包）是否是旧的延迟到达的。除非接收方能够记住这个连接中的最后一个序列号（这并不总是可行的），所以接收方必须向发送方确认同步的标识 SYN，也就是初始序列号 ISN。

从上面两段文字中可以看出**TCP 协议的可靠性是基于 seq 序列号实现的**，由于无法绑定全局时钟，也无法保证一个连接能记住最后一个序列号，所以无法判断数据分片是否是旧的，只能通过三次握手确认通信双方的 ISN（initial sequence number 初始序列号），保证双方后续传输的数据分片最终是连续、可靠、可组装的。

#### 三次握手详细过程

```text
      TCP A                                                TCP B

  1.  CLOSED                                                    LISTEN

  2.  SYN-SENT     -->  <SEQ=100><CTL=SYN>                 -->  SYN-RECEIVED

  3.  ESTABLISHED  <--  <SEQ=300><ACK=101><CTL=SYN,ACK>    <--  SYN-RECEIVED

  4.  ESTABLISHED  -->  <SEQ=101><ACK=301><CTL=ACK>        -->  ESTABLISHED

  5.  ESTABLISHED  -->  <SEQ=101><ACK=301><CTL=ACK><DATA>  -->  ESTABLISHED

          Basic 3-Way Handshake for Connection Synchronization
```

1. TCP A 处于 CLOSE 状态，TCP B 处于 LISTEN 状态
1. TCP A 请求 TCP B 建立连接，发送序列号为 100，控制位为 SYN，则 100 为初始序列号，此时 TCP A 进入 SYN-SENT 状态
1. TCP B 接收到 TCP A 发送的连接请求，发送确认请求，序列号为 300，确认号为 101，控制位为 SYN、ACK，则 TCP B 初始序列号为 300，期望下次接收的 TCP A 数据包序列号为 101，此时 TCP B 进入 SYN-RECEIVED 状态
1. TCP A 收到 TCP B 发送的确认请求，进入 ESTABLISHED 状态，并向 TCP B 发送确认请求，序列号为 101，确认号为 301，控制位为 ACK，TCP B 收到 TCP A 的确认请求后也进入 ESTABLISHED 状态，至此三次握手完成，可以可靠地传输数据。
1. TCP A 向 TCP B 发送数据，由于 4、5 都是 TCP A 向 TCP B 发送，且序列号与确认号不变，故可以在 4 中就带上数据一起发送

### TCP 连接异常处理

> The principle reason for the three-way handshake is to prevent old duplicate connection initiations from causing confusion.  To deal with this, a special control message, reset, has been devised.  If the receiving TCP is in a  non-synchronized state (i.e., SYN-SENT, SYN-RECEIVED), it returns to LISTEN on receiving an acceptable reset. If the TCP is in one of the synchronized states (ESTABLISHED, FIN-WAIT-1, FIN-WAIT-2, CLOSE-WAIT, CLOSING, LAST-ACK, TIME-WAIT), it aborts the connection and informs its user.  We discuss this latter case under "half-open" connections below.

三次握手的主要原因是为了防止建立旧的重复的连接所导致的混乱。为了处理这种情况，发明了特殊的控制位信息 reset（RST）。如果接收中的 TCP 连接正处于非同步状态（比如 SYN_SENT, SYN-RECEIVED），它会在收到 reset 后返回 LISTEN 状态。而如果 TCP 连接处于同步状态如（ESTABLISHED, FIN-WAIT-1, FIN-WAIT-2, CLOSE-WAIT, CLOSING, LAST-ACK, TIME-WAIT），它会中止连接并告知用户。我们会在“半连接”中讨论后一种情况。

```text
      TCP A                                                   TCP B

  1.  CLOSED                                                  LISTEN

  2.  SYN-SENT     -->  <SEQ=100><CTL=SYN>               ...

  3.  (duplicate)  ...  <SEQ=90><CTL=SYN>                -->  SYN-RECEIVED

  4.  SYN-SENT     <--  <SEQ=300><ACK=91><CTL=SYN,ACK>   <--  SYN-RECEIVED

  5.  SYN-SENT     -->  <SEQ=91><CTL=RST>                -->  LISTEN

  6.               ...  <SEQ=100><CTL=SYN>               -->  SYN-RECEIVED

  7.  SYN-SENT     <--  <SEQ=400><ACK=101><CTL=SYN,ACK>  <--  SYN-RECEIVED

  8.  ESTABLISHED  -->  <SEQ=101><ACK=401><CTL=ACK>      -->  ESTABLISHED

                    Recovery from Old Duplicate SYN
```

1. TCP A 处于 CLOSE 状态，TCP B 处于 LISTEN 状态
1. TCP A 发送连接请求，但尚未到达 TCP B
1. TCP A 之前发送的旧的重复连接请求先于本次请求到达了 TCP B
1. TCP B 无法分辨是否为旧的重复请求，正常回应
1. TCP A 发现 TCP B 回复了旧的重复请求，发送重置请求，TCP B 接收到后重回 LISTEN 状态
1. TCP A 本次正常请求到达 TCP B，进入正常的三次握手流程

> Half-Open Connections and Other Anomalies
> An established connection is said to be  "half-open" if one of the TCPs has closed or aborted the connection at its end without the knowledge of the other, or if the two ends of the connection have become desynchronized owing to a crash that resulted in loss of memory.  Such connections will automatically become reset if an attempt is made to send data in either direction. However, half-open  connections are expected to be unusual, and the recovery procedure is mildly involved.

半开连接与其他异常

当一个已经建立的 TCP 连接其中一方关闭或终止了连接但没有告知另一方时，或者双方连接由于内存丢失崩溃导致不再同步时，我们称之为半连接。当我们试图在半连接上任意方向发送数据时，半连接将会自动重置。不论如何，半连接是不正常的，恢复进程将会温和介入。

```text
      TCP A                                            TCP B

  1.  (CRASH)                                          (send 300,receive 100)

  2.  CLOSED                                           ESTABLISHED

  3.  SYN-SENT  -->  <SEQ=400><CTL=SYN>           -->  (??)

  4.  (!!)      <--  <SEQ=300><ACK=100><CTL=ACK>  <--  ESTABLISHED

  5.  SYN-SENT  -->  <SEQ=100><CTL=RST>           -->  (Abort!!)

  6.  SYN-SENT                                         CLOSED

  7.  SYN-SENT  -->  <SEQ=400><CTL=SYN>           -->

                     Half-Open Connection Discovery
```

1. 已建立的连接中，TCP A 崩溃了，TCP B 下一个发送的包序列号为 300，确认号为 100
1. TCP A 重启后处于关闭状态，TCP B 依然是 ESTABLISHED 状态，这是一个半连接
1. TCP A 发送请求重新建立连接，TCP B 还处于同步状态，未返回确认请求
1. TCP B 移动数据发送窗口，继续发送下一个数据分片
1. TCP A 发现 TCP B 仍处于同步状态，向 TCP B 发送重置请求，TCP B 收到重置请求后中断连接
1. TCP A 处于 SYN-SENT 状态，TCP B 处于关闭状态
1. TCP A 继续发送连接请求，进入正常三次握手过程

### TCP 四次挥手

> CLOSE is an operation meaning "I have no more data to send."  The notion of closing a full-duplex connection is subject to ambiguous interpretation, of course, since it may not be obvious how to treat the receiving side of the connection.  We have chosen to treat CLOSE in a simplex fashion.  The user who CLOSEs may continue to RECEIVE until he is told that the other side has CLOSED also.  Thus, a program could initiate several SENDs followed by a CLOSE, and then continue to RECEIVE until signaled that a RECEIVE failed because the other side has CLOSED.  We assume that the TCP will signal a user, even if no RECEIVEs are outstanding, that the other side has closed, so the user can terminate his side gracefully.  A TCP will reliably deliver all buffers SENT before the connection was CLOSED so a user who expects no data in return need only wait to hear the connection was CLOSED successfully to know that all his data was received at the destination TCP.  Users must keep reading connections they close for sending until the TCP says no more data.

关闭连接的操作表示“我没有更多的数据要发送了”。关闭的概念在全双工连接下是一种易混淆的表述，因为如何处理接收方的连接并不明确。我们选择将关闭连接做为一种单一模式处理。关闭方依然能够接收数据，直到它被告知另一方也关闭了连接。这样一个程序就能够发起几个发送请求后再关闭，并继续接收数据直到因为另一方关闭连接而接收失败。我们假定即使另一方已经关闭，没有明确的要接收的数据包了，TCP 将会告知用户，优雅地关闭用户方的连接。TCP 能够在关闭连接前可靠地发送所有缓存区待发送的数据，这样用户期望没有数据返回时，只需要等待接收连接成功关闭的信号，以知晓它所有的数据都被目标方的 TCP 接收。用户必需继续读取他们他们已经发送关闭的连接，直到 TCP 告知没有更多的数据了。

```text
      TCP A                                                  TCP B

  1.  ESTABLISHED                                            ESTABLISHED

  2.  (Close)
      FIN-WAIT-1  -->  <SEQ=100><ACK=300><CTL=FIN,ACK>  -->  CLOSE-WAIT

  3.  FIN-WAIT-2  <--  <SEQ=300><ACK=101><CTL=ACK>      <--  CLOSE-WAIT

  4.                                                         (Close)
      TIME-WAIT   <--  <SEQ=300><ACK=101><CTL=FIN,ACK>  <--  LAST-ACK

  5.  TIME-WAIT   -->  <SEQ=101><ACK=301><CTL=ACK>      -->  CLOSED

  6.  (2 MSL)
      CLOSED

                         Normal Close Sequence
```

正常的 TCP 关闭流程（四次挥手）

1. TCP A 与 TCP B 都是 ESTABLISHED 状态
1. TCP A 已发送完所有数据，准备关闭连接，发送带有 FIN 控制位的关闭请求，此时 TCP A 不能再发送数据，但仍然可以接收
1. TCP B 接收到 FIN 请求，向 TCP A 发送确认请求，TCP B 进入 CLOSE-WAIT 状态，此时 TCP B 会判断缓存区是否还有数据要发送，若有则继续发送数据给 TCP A
1. TCP B 发现所有数据都发送完成或已经没有数据要发送了，则会向 TCP A 发送带有 FIN 控制位的关闭请求，TCP B 进入 LAST-ACK 状态
1. TCP A 收到 TCP B 的关闭请求，并向 TCP B 发送关闭请求的确认请求，此时 TCP A 进入 TIME-WAIT 状态，TCP B 收到确认请求后关闭连接
1. 进入 TIME-WAIT 状态的 TCP A 经过两个最大分片生存时间后确保 TCP B 已经关闭连接，也关闭自身连接，进入 CLOSED 状态

```text
      TCP A                                                   TCP B

  1.  ESTABLISHED                                             ESTABLISHED

  2.  (Close)                                                 (Close)
      FIN-WAIT-1   -->  <SEQ=100><ACK=300><CTL=FIN,ACK>  ...  FIN-WAIT-1
                   <--  <SEQ=300><ACK=100><CTL=FIN,ACK>  <--
                   ...  <SEQ=100><ACK=300><CTL=FIN,ACK>  -->

  3.  CLOSING      -->  <SEQ=101><ACK=301><CTL=ACK>      ...  CLOSING
                   <--  <SEQ=301><ACK=101><CTL=ACK>      <--
                   ...  <SEQ=101><ACK=301><CTL=ACK>      -->

  4.  TIME-WAIT                                               TIME-WAIT
      (2 MSL)                                                 (2 MSL)
      CLOSED                                                  CLOSED

                      Simultaneous Close Sequence
```

同时关闭 TCP 流程

1. TCP A 与 TCP B 同时处于 ESTABLISHED 状态
1. TCP A 与 TCP B 同时向对方发送带有 FIN 控制位的关闭连接请求，双方在请求发送后都进入了 FIN-WAIT-1 状态
1. TCP A 与 TCP B 都收到了对方的关闭连接请求，并向对方发送了对于关闭连接的确认请求，这时双方都进入了 CLOSING 状态
1. TCP A 与 TCP B 都收到对方的确认请求后，进入了 TIME-WAIT 状态，等待两个最大分片生存时间后进入关闭状态

#### 为什么要四次挥手

建立连接时都可以只用三次握手，为什么关闭连接确要四次挥手？核心原因就是TCP连接的关闭是不能发送却仍能接收的半关闭。当TCP A向TCP B发送FIN控制位的关闭连接请求后，TCP A不再接收数据，仍能接收数据。而TCP B收到FIN请求后，可能还有缓存的数据要发送，因此只能先发送ACK请求，表示收到了FIN请求，再将缓存的数据发送完成后，才能再向TCP A发送FIN请求。由于可能有缓存数据发送的原因，导致第二次挥手和第三次挥手不能如同建立连接时的第二次握手那样合二为一。

#### 为什么要有2ML的TIME-WAIT时间

由于TCP A发送给TCP B最后的ACK请求可能丢失，TCP B未收到ACK请求，会重传FIN请求给TCP A，这个过程的最大耗时即两个最大报文生存时间，超过这个时间TCP A就可以认为TCP B收到了ACK请求，也就可以关闭连接了
