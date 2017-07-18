### 登录接口
```python
import hashlib,getpass,os
try:
    import cPickle as pickle
except ImportError:
    import pickle

'''
用户数据以字典形式存储，key = username，value = userinfo：
{username1：usrinfo，username2:userinfo}
其中 userinfo 也以字典形式存储，
{psword:"str",locked:True/False}
例子：{admin：{password：'password',locked:False},}
'''

def info(name,password):
    return Smd5(str(name)+str(password))

def Smd5(Str):
    M5 = hashlib.md5()
    M5.update(str(Str).encode('utf8'))
    return M5.hexdigest()

def setinfo(name,password):
    return {'password':str(info(name,password)),'locked':False}

def Register():
    global userData
    while True:
        name = input("Plesse input a username,or type q\Q to quit:".rjust(40)).strip()
        if name == "q" or name == "Q":
            break

        if userData.get(name) is not None:
            print(" Name has being used , chose another one ! ")
            continue

        password = Smd5(input("Please input a password:".rjust(40)).strip())
        #password = Smd5(getpass.getpass("Please input a password:"))
        if Smd5(input("password again:".rjust(40)).strip()) != password:
        #if Smd5(getpass.getpass("password again")) != password:
            print("password is not matching , please retry !")
            continue

        userData.setdefault(name,setinfo(name,password))
        print("User adding !")
        break
    return

def Login():
    count = 3
    global userData
    name = input("NAME:".rjust(20)).strip()
    if userData.get(name) is None:
        print("NO user named {name}".format(name=name))
        return False
    U_info = userData.get(name)
    if U_info.get('locked'):
        print("You Account Have Being Locked, Place Email Admin To Unlock !")
        return False
    while count > 0:
        password = Smd5(input("Password:".rjust(20)).strip())
        # password = Smd5(str(getpass.getpass("Password:".rjust(20))).strip())
        if U_info.get('password') == info(name,password):
            # do some thing
            print("LOGING SCUSESS !!")
            return True
        else:
            count -= 1
            print("Password Wrong , You Have {c} Change".format(c=count))
    else:
        U_info['locked'] = True
        print("You Account Have Being Locked, Place Email Admin To Unlock !")
        return False

def _init():
    global idfile
    global userData
    if not os.path.isfile(idfile):
        with open(idfile,'w+') as f: pass
    with open(idfile,'rb') as File :
        # with pickle.load(File) as userData:pass
        try:
            userData = pickle.load(File)
        except BaseException as e:
            print("ERROR:" + str(e))
            userData = {'admin':{'password':'c0e024d9200b5705bc4804722636378a','locked':False}}
    return
    # try:
    #     File = open(idfile,'rb')
    #     D = pickle.load(File)
    # except BaseException as e:
    #     logging.log(logging.INFO,e)
    #     File = open(idfile,'w')
    #     D = dict()
    # return D

def _destructor():
    global userData
    global idfile
    with open(idfile,'wb') as File:
        try:
            pickle.dump(userData,File)
        except BaseException as e:
            print("ERROR : " + str(e))
    return

def Run():
    global userData
    global idfile
    idfile = "Identity.txt"
    Logined = False

    _init()
    while not Logined:
        userinput = input("What do you want to do ?\n1. Register \n2. Login\nQ for quit\n:").strip()
        if userinput ==str(1) : Register()
        elif userinput==str(2) : Logined = Login()
        elif userinput=="q" or userinput=="Q":break
        else:print("Invalid Input")
    _destructor()

if __name__ == '__main__':
    Run()

```
### 流程图
###### 程序主流程
```
graph TB
S((START))-->OP1[环境初始化]
OP1-->JU1{判断是否登陆}

subgraph 
JU1-->|否|OP2{选择操作}
OP2-->|注册|SUB1[注册流程]
OP2-->|登陆|SUB2{登陆流程}
OP2-->|非预期的输入|JU1
SUB1-->JU1
SUB2-->|登陆不成功|JU1
end
JU1-->|是|OP3[存储数据]
OP2-->|Q\q|OP3
SUB2-->|登陆成功|OP3
OP3-->E
E((END))

```
###### 注册流程
```
graph TB
S((START))-->A[声明变量]
A-->B{输入NAME}
B-->|NAME被占用|A
B-->|NAME未使用|D[输入密码]
B-->|退出|Z((END))
D-->E{校验输入的密码}
E-->|密码匹配|F[将用户信息计入userData]
E-->|密码不匹配|A
F-->Z((END))
```
###### 登录流程
```
graph TB
S((START))-->A[声明变量,count=3]
A-->B{输入NAME}
B-->|NAME不存在|C[Return False]
C-->E((END))
B-->|NAME存在|D{count>0}
B-->|NAME被锁定|C[Return False]

subgraph 
D-->|是|F{输入密码}
F-->|密码与预存密码相同|H[Return Ture]
F-->|密码与预存密码不同|J[count -1]
J-->D
end

D-->|否|G[锁定NAME]
G-->C[Return False]
H-->E
E((END))
```