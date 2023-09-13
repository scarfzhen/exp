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
看目标服务器DNS解析是否正常
```python
import socket
dns_hostname = "baidu.com"
ip = socket.gethostbyname(dns_hostname)
```
