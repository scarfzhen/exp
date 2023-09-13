### **一些常用的功能函数，以python为主**

#### python2.7的一些常用函数和包
##### shutil包
```python
import shutil
import os
shutil.copy(src="",dst="") # 用于拷贝文件，注意dst可能是个文件夹，那就是将文件copy到目标文件夹下
shutil.rmtree(path="/unused") # 用于删除文件夹
os.remove(path="/filepath") # 用于删除文件
```

##### socket包

```python
# 看目标服务器DNS解析是否正常
import socket
dns_hostname = "baidu.com"
ip = socket.gethostbyname(dns_hostname)
```

```python
# 网络端口访问是否正常
#!/usr/bin/python
# coding=utf-8
from socket import *
def portscanner(host_list, port):
    s = socket(AF_INET, SOCK_STREAM)    
    for j in host_list:
        for p in port:
            try:
                result_code = s.connect_ex((j, p))
                if result_code == 0:
                    print("[+] {0:16}{1:6} open".format(j, p))
                else:
                    print("\t[-] {0:16}{1:6} closed".format(j, p))
            except Exception as e:
                print(e)
            finally:
                s.close()
def main():
    setdefaulttimeout(1)
    h1 = ['127.0.0.1']
    p1 = [21]

    portscanner(h1, p1)
    #portscanner(h2, p2)
    #portscanner(h3, p3)
if __name__ == '__main__':
    main()
```
