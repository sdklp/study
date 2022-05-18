## python实现CryptoJS加密算法逆向_python版服务器

### 前言

最近在研究某网站参数过程中，发现前端使用crypto-js.js(`AES算法`)加密，对其深度研究，遇到很多坑，特简单记录一下。

### AES简介

高级加密标准(AES,Advanced Encryption Standard)为最常见的对称加密算法。对称加密算法也就是加密和解密用相同的密钥.

### AES 加密的参数

AES 加密主要有以下几个参数(坑)：

- `key`：加密的时候用秘钥，解密的时候需要同样的秘钥才能解出来。
- `word`：需要加密的参数。
- `model`：aes 加密常用的有 ECB 和 CBC 模式（我只用了这两个模式，还有其他模式）。
- `iv`：偏移量 ，这个参数在 ECB 模式下不需要，在 CBC 模式下需要。

###### 参数条件：

key：必须是16位字节或者24位字节或者32位字节
word：字节长度需要是16位的倍数

### 步骤

1、调试js，将具体实现加密的js代码抠出来
2、将js代码清洗简化，放在html中运行，即可实现整个加密过程
3、pip3安装`pycrytodome`库，实现同样的加密过程

### crypto-js实现前端AES加密

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="./crypto-js.js"></script>
</head>
<body>
    <div>我不显示，请查看console查看</div>
    <script>
        function Decrypt(word, keyStr) {
     
            let key = CryptoJS.enc.Utf8.parse('1234567890123456')
            let iv = CryptoJS.enc.Utf8.parse('1234567890123456')
            var decrypt = CryptoJS.AES.decrypt(word, key, {
     
                iv: iv,
                mode: CryptoJS.mode.CBC,
                padding: CryptoJS.pad.ZeroPadding
            })
            var decryptedStr = decrypt.toString(CryptoJS.enc.Utf8)
            return decryptedStr.toString()
        }
        var pwd = '';
        pwd = Decrypt('wU/2oHphdJ5qzRQh9AltsQ==', '1234567890123456');
        console.log(pwd);
    </script>
</body>
</html>
```

### python3实现AES加密

```python
# coding:utf-8

from Crypto.Cipher import AES
import base64
class AesCrypt():
    def __init__(self, key, model, iv):
        self.model = {
    'ECB': AES.MODE_ECB, 'CBC': AES.MODE_CBC}[model]
        self.key = self.add_16 (key)
        self.iv = iv.encode ()
        if model == 'ECB':
            self.aes = AES.new (self.key, self.model)  # 创建aes对象
        elif model == 'CBC':
            self.aes = AES.new (self.key, self.model, self.iv)  # 创建aes对象

    def add_16(self,par):
    	# python3字符串是unicode编码，需要 encode才可以转换成字节型数据
        par = par.encode('utf-8')
        while len(par) % 16 != 0:
            par += b'\x00'
        return par

    def aesdecrypt(self, text):
        # CBC解密需要重新创建一个aes对象
        if self.model == AES.MODE_CBC:
            self.aes = AES.new(self.key, self.model, self.iv)
        text = base64.decodebytes(text.encode('utf-8'))
        self.decrypt_text = self.aes.decrypt(text)
        return self.decrypt_text.decode('utf-8').strip('\0')

if __name__ == '__main__':
    key = '1234567890123456'
    iv = '1234567890123456'
    word = 'wU/2oHphdJ5qzRQh9AltsQ=='
    model = 'CBC'
    pr = AesCrypt(key,model,iv)
    print (pr.aesdecrypt (word))
```

以上即可实现和crypto-js同样结果。

可能有同学会问，为什么不使用execjs，我第一个想到的就是execjs直接运行js代码，但是会提示无法找到crypto-js，因此放弃