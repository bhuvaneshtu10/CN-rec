# CN-rec
## stop and wait
client
```
import socket
s=socket.socket()
s.bind(('localhost',8000))
s.listen(5)
c,addr=s.accept()
while True:
i=input("Enter a data: ")
c.send(i.encode())
ack=c.recv(1024).decode()
if ack:
print(ack)
continue
else:
c.close()
breakx
```
server
```
import socket
s=socket.socket()
s.connect(('localhost',8000))
while True:
print(s.recv(1024).decode())
s.send("Acknowledgement Recived".encode())
```
## sliding window protocol
client
```
import socket
s=socket.socket()
s.bind(('localhost',8000))
s.listen(5)
c,addr=s.accept()
size=int(input("Enter number of frames to send : "))
l=list(range(size))
s=int(input("Enter Window Size : "))
st=0
i=0
while True:
while(i<len(l)):
st+=s
c.send(str(l[i:st]).encode())
ack=c.recv(1024).decode()
if ack:
print(ack)
i+=s
```
server
```
import socket
s=socket.socket()
s.connect(('localhost',8000))
while True:
print(s.recv(1024).decode())
s.send("acknowledgement recived from the server".encode())
```

## arp
client
```
import socket
s=socket.socket()
s.bind(('localhost',8000))
s.listen(5)
c,addr=s.accept()
address={"165.165.80.80":"6A:08:AA:C2","165.165.79.1":"8A:BC:E3:FA"};
while True:
ip=c.recv(1024).decode()
try:
c.send(address[ip].encode())
except KeyError:
c.send("Not Found".encode())
```
server
```
import socket
s=socket.socket()
s.connect(('localhost',8000))
while True:
ip=input("Enter logical Address : ")
s.send(ip.encode())
print("MAC Address",s.recv(1024).decode())
```

## rarp
client
```
import socket
s=socket.socket()
s.bind(('localhost',9000))
s.listen(5)
c,addr=s.accept()
address={"6A:08:AA:C2":"192.168.1.100","8A:BC:E3:FA":"192.168.1.99"};
while True:
ip=c.recv(1024).decode()
try:
c.send(address[ip].encode())
except KeyError:
c.send("Not Found".encode())
```
server
```
import socket
s=socket.socket()
s.connect(('localhost',9000))
while True:
ip=input("Enter MAC Address : ")
s.send(ip.encode())
print("Logical Address",s.recv(1024).decode())
```

## echo client and server
client
```
import socket
s=socket.socket()
s.connect(('localhost',8000))
while True:
msg=input("Client > ")
s.send(msg.encode())
print("Server > ",s.recv(1024).decode())
```
server
```
import socket
s=socket.socket()
s.bind(('localhost',8000))
s.listen(5)
c,addr=s.accept()
while True:
ClientMessage=c.recv(1024).decode()
c.send(ClientMessage.encode())
```
## chat using tcp socket
client
```
import socket
s=socket.socket()
s.connect(('localhost',8000))
while True:
msg=input("Client > ")
s.send(msg.encode())
print("Server > ",s.recv(1024).decode())
```
server
```
import socket
s=socket.socket()
s.bind(('localhost',8000))
s.listen(5)
c,addr=s.accept()
while True:
ClientMessage=c.recv(1024).decode()
print("Client > ",ClientMessage)
msg=input("Server > ")
c.send(msg.encode())
```
## socket creation for http
client
```
import socket

def upload(filename):
    s = socket.socket()
    s.connect(('localhost', 9000))
    s.send(b'UPLOAD')
    with open(filename, "rb") as f:
        s.send(f.read())
    print(s.recv(1024).decode())
    s.close()

def download(save_as):
    s = socket.socket()
    s.connect(('localhost', 9000))
    s.send(b'DOWNLOAD')
    data = s.recv(4096)
    with open(save_as, "wb") as f:
        f.write(data)
    print("Downloaded.")
    s.close()

# Sample usage:
# upload('myfile.txt')
# download('downloaded.txt')

```
server
```
import socket

s = socket.socket()
s.bind(('localhost', 9000))  # Listen on port 9000
s.listen(1)
print("Server ready!")

while True:
    conn, addr = s.accept()
    cmd = conn.recv(1024).decode()
    if cmd == 'UPLOAD':
        with open("server_copy.txt", "wb") as f:
            data = conn.recv(4096)
            f.write(data)
        conn.send(b'Done')
    elif cmd == 'DOWNLOAD':
        try:
            with open("server_copy.txt", "rb") as f:
                conn.send(f.read())
        except:
            conn.send(b'No file.')
    conn.close()

```
