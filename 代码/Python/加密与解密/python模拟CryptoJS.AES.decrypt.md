# python模拟CryptoJS.AES.decrypt解密 - Cache One





### python模拟CryptoJS.AES.decrypt

- [思路：](https://cache.one/read/14837919#_2)
- [准备工作:](https://cache.one/read/14837919#_4)
- [需求](https://cache.one/read/14837919#_16)



# 思路：

从CryptoJS源码中抠出python模拟报错的代码

# 准备工作:

1.安装execjs库

```
pip install execjs
```

2.安装nodejs
百度一下一大堆

3.使用npm安装crypto-js库

4.讲crypto-js库放入安装的node_modules文件夹下

# 需求

已知加密后文本和密钥反推出原始文本

```
text = 'A3ReBKoR6IDZSR4Jdxq72fXPsnWTZMhOr5sXl/lJ8/3GWFmsy2fTHG/0+Uz8fZmopZ0Ru0cskOWNX8hWlUy19scqauL28x3daP9IQn2ZPKUP4L6w1bWnAKuJH2eyp9eq1OhQXiRFSH4T62nWr/XhuP34vgZrv6rQppWFzEWFUaZ9E6YgR72lf9SawzUSwsZ6eOMpZSccxe2MnJPX+hfjYgN5vx637IRFkJKj55AC0Xm2g1IMU+kCWWDpHuaUeeKxhrmmzFmAselK6RszyJOaB3BxvQe1GfIer/HJZZIGgoB24zgH5K6+B2UIuNjFHQWiT2L9EKMnz8HUIYDg76H1QeAqEihFjL+SjXUb1w33PxZIf7wM2ZOtzYrA54qSsLT5iciumUD9mWVZdN+FKlx0i3Tu1gqUAyOdZXw9aHO2rRQ1ALLamncDBMaBKKsvF+MShQcIIu+VFrBQpgtNSkTisoa5psxZgLHpSukbM8iTmgdwcb0HtRnyHq/xyWWSBoKAYHZFruMM7Ty1wIZEt0YMY09i/RCjJ8/B1CGA4O+h9UHgKhIoRYy/ko11G9cN9z8Ww7LMTn9GVIMxFBIDy2xNq7mdCm3EIrsAgqDzuBSLUaWuFSKcUo82/ypSjoMRXG9kRvjPI5DHCu3T8zwvG0D5jVdmAsdAafTANG7SZKrZbbYizQBf+90cXpYESW87n4rLAcENrJ+rQ5Lpiizt3mnxRPslX37uGL7b+wNbnmdST6gBM6iNz5+MGbCmuAPxjg6lMNGp7QXZ3hArw4fmtCifTxBcVD5wC99wMuw5eyRWVyhyEGTocvJVeq3CRTLTaJ5V/MRNEPXGqOTER8Nm+JTMwtH8Vzamif3LRnZHtT86+08='
key = 'd719303e82ed09cdb24b717b1375f986'
```

使用ECB模式无须偏移量

python代码

```python
import execjs
from Crypto.Cipher import AES
from binascii import b2a_hex, a2b_hex
import os
from urllib import parse

os.environ["NODE_PATH"] = "D:/New Folder/node_modules"
# print(execjs.get().name)

js = open('min3.js', 'r', encoding='utf-8').read()

text = 'A3ReBKoR6IDZSR4Jdxq72fXPsnWTZMhOr5sXl/lJ8/3GWFmsy2fTHG/0+Uz8fZmopZ0Ru0cskOWNX8hWlUy19scqauL28x3daP9IQn2ZPKUP4L6w1bWnAKuJH2eyp9eq1OhQXiRFSH4T62nWr/XhuP34vgZrv6rQppWFzEWFUaZ9E6YgR72lf9SawzUSwsZ6eOMpZSccxe2MnJPX+hfjYgN5vx637IRFkJKj55AC0Xm2g1IMU+kCWWDpHuaUeeKxhrmmzFmAselK6RszyJOaB3BxvQe1GfIer/HJZZIGgoB24zgH5K6+B2UIuNjFHQWiT2L9EKMnz8HUIYDg76H1QeAqEihFjL+SjXUb1w33PxZIf7wM2ZOtzYrA54qSsLT5iciumUD9mWVZdN+FKlx0i3Tu1gqUAyOdZXw9aHO2rRQ1ALLamncDBMaBKKsvF+MShQcIIu+VFrBQpgtNSkTisoa5psxZgLHpSukbM8iTmgdwcb0HtRnyHq/xyWWSBoKAYHZFruMM7Ty1wIZEt0YMY09i/RCjJ8/B1CGA4O+h9UHgKhIoRYy/ko11G9cN9z8Ww7LMTn9GVIMxFBIDy2xNq7mdCm3EIrsAgqDzuBSLUaWuFSKcUo82/ypSjoMRXG9kRvjPI5DHCu3T8zwvG0D5jVdmAsdAafTANG7SZKrZbbYizQBf+90cXpYESW87n4rLAcENrJ+rQ5Lpiizt3mnxRPslX37uGL7b+wNbnmdST6gBM6iNz5+MGbCmuAPxjg6lMNGp7QXZ3hArw4fmtCifTxBcVD5wC99wMuw5eyRWVyhyEGTocvJVeq3CRTLTaJ5V/MRNEPXGqOTER8Nm+JTMwtH8Vzamif3LRnZHtT86+08='
key = 'd719303e82ed09cdb24b717b1375f986'
ctx = execjs.compile(js)
s = ctx.call('getAesEncrypt',text,key)
print(s)
print((parse.unquote(s)))
```

js代码 = min3.js

```js
var CryptoJS = require("crypto-js");

function stringify1(t) {
                for (var r = t.words, e = t.sigBytes, i = [], n = 0; n < e; n++) {
                    var o = r[n >>> 2] >>> 24 - n % 4 * 8 & 255;
                    i.push(String.fromCharCode(o))
                }

                return escape(i.join(""))
            }
// function stringify(t) {
//                 try {
//                     return decodeURIComponent(escape(stringify1(t)))
//                 } catch (t) {
//                     throw new Error("Malformed UTF-8 data")
//                 }}
function getAesEncrypt(t,key) {//加密
    var r = CryptoJS.enc.Hex.parse(key);
    var p = CryptoJS.AES.decrypt(t, r, {
        mode: CryptoJS.mode.ECB,
        padding: CryptoJS.pad.Pkcs7
    });
    // return decodeURIComponent(escape(stringify1(p)))
    return stringify1(p)
    // return p.sigBytes
    // return CryptoJS.enc.Utf8.stringify(p);
}
```

最终得到结果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928162407913.png#pic_center)