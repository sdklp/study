# Crypto算法库详解

2020-01-02阅读 1.3K0

### 安装与使用

Crypto 算法库在 python 中最初叫 pycrypto，这个作者有点懒，好几年没有更新，后来就有大佬写了个替代库 pycryptodome。这个库目前只支持 python3，安装也很简单`pip install pycryptodome`就行了！详细的用法可以看看 [官方文档](https://www.pycryptodome.org/en/latest/src/api.html)

> 常见对称密码在 Crypto.Cipher 库下，主要有：`DES 3DES AES RC4 Salsa20` 非对称密码在 Crypto.PublicKey 库下，主要有：`RSA ECC DSA` 哈希密码在 Crypto.Hash 库下，常用的有：`MD5 SHA-1 SHA-128 SHA-256` 随机数在 Crypto.Random 库下 实用小工具在 Crypto.Util 库下 数字签名在 Crypto.Signature 库下

### 对称密码AES

注意：python3 和 python2 在字符串方面有个明显的区别 - `python3 中有字节串 b'byte'，python2 中没有字节`。由于这个库是在 python3 下的，所以加解密用的都是字节！

使用这个库来加解密特别简单，记住这四步：

- 导入所需库

```
from Crypto.Cipher import AES
```

- 初始化 key

```
key = b'this_is_a_key'
```

- 实例化加解密对象

```
aes = AES.new(key,AES.MODE_ECB)
```

- 使用实例加解密

```
text_enc = aes.encrypt(b'helloworld')
from Crypto.Cipher import AES
import base64

key = bytes('this_is_a_key'.ljust(16,' '),encoding='utf8')
aes = AES.new(key,AES.MODE_ECB)

# encrypt
plain_text = bytes('this_is_a_plain'.ljust(16,' '),encoding='utf8')
text_enc = aes.encrypt(plain_text)
text_enc_b64 = base64.b64encode(text_enc)
print(text_enc_b64.decode(encoding='utf8'))

# decrypt
msg_enc = base64.b64decode(text_enc_b64)
msg = aes.decrypt(msg_enc)
print(msg.decode(encoding='utf8'))
```

复制

注意：key和明文是需要填充到指定位数的，可以使用`ljust`或者`zfill`之类的填充，也可以用`Util中的pad()`函数填充！

### 对称密码DES

```javascript
from Crypto.Cipher import DES
import base64

key = bytes('test_key'.ljust(8,' '),encoding='utf8')
des = DES.new(key,DES.MODE_ECB)

# encrypt
plain_text = bytes('this_is_a_plain'.ljust(16,' '),encoding='utf8')
text_enc = des.encrypt(plain_text)
text_enc_b64 = base64.b64encode(text_enc)
print(text_enc_b64.decode(encoding='utf8'))

# decrypt
msg_enc = base64.b64decode(text_enc_b64)
msg = des.decrypt(msg_enc)
print(msg.decode(encoding='utf8'))
```

复制

### 非对称密码RSA

这个库的 RSA 主要是用来`生成`公钥文件/私钥文件或者`读取`公钥文件/私钥文件 生成公/私钥文件：

```javascript
from Crypto.PublicKey import RSA

rsa = RSA.generate(2048) # 返回的是密钥对象

public_pem = rsa.publickey().exportKey('PEM') # 生成公钥字节流
private_pem = rsa.exportKey('PEM') # 生成私钥字节流

f = open('public.pem','wb')
f.write(public_pem) # 将字节流写入文件
f.close()
f = open('private.pem','wb')
f.write(private_pem) # 将字节流写入文件
f.close()
#
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArreg3IX19DbszqSdBKhR
9cm495XAk9PBQJwHiwjKv6S1Tk5h7xL9/fPZIITy1M1k8LwuoSJPac/zcK6rYgMb
DT9tmVLbi6CdWNl5agvUE2WgsB/eifEcfnZ9KiT9xTrpmj5BJql9H+znseA1AzlP
iTukrH1frD3SzZIVnq/pBly3QbsT13UdUhbmIgeqTo8wL9V0Sj+sMFOIZY+xHscK
IeDOv4/JIxw0q2TMTsE3HRgAX9CXvk6u9zJCH3EEzl0w9EQr8TT7ql3GJg2hJ9SD
biebjImLuUii7Nv20qLOpIJ8qR6O531kmQ1gykiSfqj6AHqxkufxTHklCsHj9B8F
8QIDAQAB
-----END PUBLIC KEY-----

-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEArreg3IX19DbszqSdBKhR9cm495XAk9PBQJwHiwjKv6S1Tk5h
7xL9/fPZIITy1M1k8LwuoSJPac/zcK6rYgMbDT9tmVLbi6CdWNl5agvUE2WgsB/e
ifEcfnZ9KiT9xTrpmj5BJql9H+znseA1AzlPiTukrH1frD3SzZIVnq/pBly3QbsT
13UdUhbmIgeqTo8wL9V0Sj+sMFOIZY+xHscKIeDOv4/JIxw0q2TMTsE3HRgAX9CX
vk6u9zJCH3EEzl0w9EQr8TT7ql3GJg2hJ9SDbiebjImLuUii7Nv20qLOpIJ8qR6O
531kmQ1gykiSfqj6AHqxkufxTHklCsHj9B8F8QIDAQABAoI...
-----END RSA PRIVATE KEY-----
```

复制

读取公/私钥文件加解密：

```javascript
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5
import base64

def rsa_encrypt(plain):
    with open('public.pem','rb') as f:
        data = f.read()
        key = RSA.importKey(data)
        rsa = PKCS1_v1_5.new(key)
        cipher = rsa.encrypt(plain)
        return base64.b64encode(cipher)

def rsa_decrypt(cipher):
    with open('private.pem','rb') as f:
        data = f.read()
        key = RSA.importKey(data)
        rsa = PKCS1_v1_5.new(key)
        plain = rsa.decrypt(base64.b64decode(cipher),'ERROR') # 'ERROR'必需
        return plain

if __name__ == '__main__':
    plain_text = b'This_is_a_test_string!'
    cipher = rsa_encrypt(plain_text)
    print(cipher)
    plain = rsa_decrypt(cipher)
    print(plain)
```

复制

注意：RSA 有两种填充方式，一种是 `PKCS1_v1_5`，另一种是 `PKCS1_OAEP`

### Hash算法

和 `hashlib` 库的用法类似，先实例化某个 Hash 算法，再用 update() 调用就可以了！

具体栗子：

```javascript
from Crypto.Hash import SHA1,MD5

sha1 = SHA1.new()
sha1.update(b'sha1_test')
print(sha1.digest()) # 返回字节串
print(sha1.hexdigest()) # 返回16进制字符串
md5 = MD5.new()
md5.update(b'md5_test')
print(md5.hexdigest())
```

复制

### 数字签名

发送发用私钥签名，验证方用公钥验证

```javascript
from Crypto.Signature import pkcs1_15
from Crypto.Hash import SHA256
from Crypto.PublicKey import RSA

# 签名
message = 'To be signed'
key = RSA.import_key(open('private_key.der').read())
h = SHA256.new(message)
signature = pkcs1_15.new(key).sign(h)

# 验证
key = RSA.import_key(open('public_key.der').read())
h = SHA.new(message)
try:
    pkcs1_15.new(key).verify(h, signature):
    print "The signature is valid."
    except (ValueError, TypeError):
        print "The signature is not valid."
```

复制

### 随机数

和 `random` 库类似。第一个函数很常用

```javascript
import Crypto.Random
import Crypto.Random.random

print(Crypto.Random.get_random_bytes(4)) # 得到n字节的随机字节串
print(Crypto.Random.random.randrange(1,10,1)) # x到y之间的整数，可以给定step
print(Crypto.Random.random.randint(1,10)) # x到y之间的整数
print(Crypto.Random.random.getrandbits(16)) # 返回一个最大为N bit的随机整数
```

复制

### 其它功能

常用到 Util 中的`pad()`函数来填充密钥

```javascript
from Crypto.Util.number import *
from Crypto.Util.Padding import *

# 按照规定的几种类型 pad，自定义 pad可以用 ljust()或者 zfill()
str1 = b'helloworld'
pad_str1 = pad(str1,16,'pkcs7') # 填充类型默认为'pkcs7'，还有'iso7816'和'x923'
print(unpad(pad_str1,16))
# number
print(GCD(11,143)) # 最大公约数
print(bytes_to_long(b'hello')) # 字节转整数
print(long_to_bytes(0x41424344)) # 整数转字节
print(getPrime(16)) # 返回一个最大为 N bit 的随机素数
print(getStrongPrime(512)) # 返回强素数
print(inverse(10,5)) # 求逆元
print(isPrime(1227)) # 判断是不是素数
```

复制

### END