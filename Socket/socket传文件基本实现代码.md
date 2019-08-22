## socket文件传输基本实现过程

### 形式一(解决粘包)：

**客户端**

```
import socket
import struct
import os
client = socket.socket()
client.connect(('127.0.0.1', 8080))
# 获取文件大小
size = os.path.getsize('18164.jpg')
# 制作报头
head = struct.pack('i', size)
# 发送报头
client.send(head)
# 读文件，遍历文件，for循环发送
with open('18164.jpg', 'rb') as f:
    for line in f:
        client.send(line)
```

**服务端**

```
import socket
import struct
server = socket.socket()
# 绑定ip和端口
server.bind(('127.0.0.1', 8080))
# 设置半连接池
server.listen(5)
# 建立双向连接
conn, client_addr = server.accept()
# 收报头
head = conn.recv(4)
# 解析报头，获取待传文件大小
size = struct.unpack('i', head)[0]
# 为接收文件作准备
cur_size = 0
print('文件大小为%s' % size)
# 准备写入接收文件
with open('recv_file.jpg', 'wb') as f:
    # 判断循环结束条件，当接收文件完毕就结束循环
    while cur_size < size:
        # 每次接收1024字节，当然这个数字可自己设置，比如2048，建议不要过大
        data = conn.recv(1024)
        f.write(data)
        # 每次写记录一次总写入大小，用于结束循环
        cur_size += len(data)
print('接收完毕')
```

### 形式二(解决粘包)：

客户端

```
from socket import *
import struct
import os
client = socket(AF_INET, SOCK_STREAM)
client.connect(('127.0.0.1', 8080))
# 获取文件大小
size = os.path.getsize('18164.jpg')
# 制作报头
head = struct.pack('i', size)
# 发送报头
client.send(head)
# while循环读文件
with open('18164.jpg', 'rb') as f:
    while True:
        # 每次读取1024字节，并发送
        data = f.read(1024)
        if not data:
            break
        client.send(data)
```

服务端

```
from socket import *
import struct
server = socket(AF_INET, SOCK_STREAM)
# 绑定ip和端口
server.bind(('127.0.0.1', 8080))
# 设置半连接池
server.listen(5)
# 建立双向连接
conn, client_addr = server.accept()
# 收报头
head = conn.recv(4)
# 解析报头，获取待传文件大小
size = struct.unpack('i', head)[0]
# 为接收文件作准备
cur_size = 0
print('文件大小为%s' % size)
# 准备写入接收文件
with open('recv_file2.jpg', 'wb') as f:
    # 判断循环结束条件，当接收文件完毕就结束循环
    while cur_size < size:
        # 每次接收1024字节，当然这个数字可自己设置，比如2048，建议不要过大
        data = conn.recv(1024)
        f.write(data)
        # 每次写记录一次总写入大小，用于结束循环
        cur_size += len(data)
print('接收完毕')
```

