# python Crypto 加密解密





本片文字记录使用python 的Crypto 工具对图片或者文本进行加密解密的方法：

```
import numpy as np
from PIL import Image
from base64 import b64encode, b64decode
from Crypto.Cipher import  AES BS = 16
iv = 16 * b'\0'
key = b'25jkUjx14hkc@q58gxU3mcaaaaaaaaaa'
imgPath = './test/data/0_biaoge.jpg' # pad for length not multiple of string
pad_txt = lambda s: s + (BS - len(s) % BS) * chr(BS - len(s) % BS) # jiami
obj = AES.new(key, AES.MODE_CBC, iv)
img = open(imgPath, 'rb').read()
img_base64 = b64encode(img)
img_base64_str = str(img_base64, encoding='utf-8')
data_jiami = obj.encrypt(pad_txt(img_base64_str)) # jiemi
obj2 = AES.new(key, AES.MODE_CBC, iv)
data_jiemi = obj2.decrypt(data_jiami)
data_jiemi_str = str(data_jiemi, encoding='utf-8').replace('\x04', '')
data_jiemi_base64 = b64decode(data_jiemi_str)
img = np.asarray(bytearray(data_jiemi_base64), dtype="uint8")
```