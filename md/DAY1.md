## 第一天
### 序列化操作
序列化有两个方案：Pickle & Json
#### Pickle
引入:
```
try:
    import cPickle as pickle        # cPickle 是 Pickle 的C实现，速度比Pickle快
except: ImportError:
    import pickle
```
序列化 & 反序列化：
```
str = pickle.dumps(obj)                     # 把一个对象序列化成为一个字符串，然后写入文件
obj = pickle.loads(str)                     # 从文件中读取字符串，然后反序列化成一个对象

with open(File,'wb') as file_obj            # 把一个对象序列化，并写入一个文件对象
    pickle.dump(obj,file_obj)
with open(File,'rb') as file_obj            # 从文件对象反序列化出一个对象
    dict = pickle.dump(file_obj)
```
#### Json
Json是多语言支持的序列化方案，如果可以尽量使用Json方式：
```
import json

dict = {}
str  = json.dumps(dict)                     # Json 把一个Python字典对象序列化成一个字符串
dict = json.loads(str)                      # 把一个json字符串反序列化成一个Python字典对象

with open(File,'w') as file_obj:            # 类似的把dict对象序列化成一个文件对象和反序列化
    json.dumps(dict,file_obj)
with open(File,'r') as file_obj:
    dict = json.loads(file_obj)
```
Json非标准的类对象序列化成json字符串，所以自己定义的类需要先转化成字典或列表，然后再进行序列化操作
```
class User(object):
    def __init__(self,*arg,**kwargs):
        self.__name = kwargs.get("__name")
        self.__password = kwargs.get("__password")
        self.__locked   = kwargs.get("__locked")

    def getname(self):pass
    def setname(self):pass

user = User()
str = json.dumps(user.__dict__)     # 实例的__dict__属性会记录实例的相关属性，通过这个字典可以把实例信息序列化
sUser = User(json.loads(str))       # 反序列化的时候先通过字符串将对象的属性信息还原成字典，再通过类的初始化函数还原对象

```
