# 前端CryptoJS和后端(java python)crypto实现前后端密码加密解密功能





#### 前端代码

```
// 1. vue package.json 引入crypto-js的依赖："crypto-js": "^4.0.0", 
// 导入
import CryptoJS from 'crypto-js';
// 这两个必须是16位前后端一致
const key = CryptoJS.enc.Utf8.parse('FaceSunAweSome_K');
const iv = CryptoJS.enc.Utf8.parse('FaceSunAweSomeIV');
 // 加密
const encrypt = (pass) => {
   
  const password = CryptoJS.enc.Utf8.parse(pass);
  return CryptoJS.AES.encrypt(password, key, {
   
    mode: CryptoJS.mode.CBC,
    iv: iv,
    padding: CryptoJS.pad.Pkcs7
  }).toString();
};
// 解密
const decrypt = (pass) => {
   
  return CryptoJS.AES.decrypt(pass, key, {
   
    mode: CryptoJS.mode.CBC,
    iv: iv,
    padding: CryptoJS.pad.Pkcs7
  }).toString(CryptoJS.enc.Utf8);
};
 
export {
   
  encrypt, decrypt
};
```

#### java 代码

```
import javax.crypto.Cipher;
import javax.crypto.spec.IvParameterSpec;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.lang3.StringUtils;
import sun.misc.BASE64Decoder;
import sun.misc.BASE64Encoder;
import java.nio.charset.StandardCharsets;
 
/** * @author FaceSun * @title: AESUtil */
public class AESUtil {
   
    // 密钥 (需要前端和后端保持一致)十六位作为密钥
    private static final String KEY = "FaceSunAweSome_K";
    // 密钥偏移量 (需要前端和后端保持一致)十六位作为密钥偏移量
    private static final String IV = "FaceSunAweSomeIV";
    // 算法
    private static final String ALGORITHMSTR = "AES/CBC/PKCS5Padding";
    /** * base 64 decode * @param base64Code 待解码的base 64 code * @return 解码后的byte[] * @throws Exception */
    public static byte[] base64Decode(String base64Code) throws Exception{
   
        return StringUtils.isEmpty(base64Code) ? null : new BASE64Decoder().decodeBuffer(base64Code);
    }
 
    /** * AES===========解密 * @param encryptStr 待解密的 * @return 解密后的String * @throws Exception Exception */
    public static String aesDecrypt(String encryptStr) throws Exception {
   
        if (StringUtils.isNotBlank(encryptStr)){
   
            Cipher cipher = Cipher.getInstance(ALGORITHMSTR);
            byte[] temp = IV.getBytes(StandardCharsets.UTF_8);
            IvParameterSpec iv = new IvParameterSpec(temp);
            cipher.init(Cipher.DECRYPT_MODE, new SecretKeySpec(KEY.getBytes(), "AES"), iv);
            byte[] decryptBytes = cipher.doFinal(base64Decode(encryptStr));
            return new String(decryptBytes);
        }
       return null;
    }
 
    /** * ==============================================AES加密 * @param password 待加密的 * @return 加密后的String * @throws Exception Exception */
    public static String aesEncrypt(String password) throws Exception {
   
        if (StringUtils.isNotBlank(password)){
   
            Cipher cipher = Cipher.getInstance(ALGORITHMSTR);
            byte[] temp = IV.getBytes(StandardCharsets.UTF_8);
            IvParameterSpec iv = new IvParameterSpec(temp);
            cipher.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(KEY.getBytes(), "AES"), iv);
            byte[] encryptBytes = cipher.doFinal(password.getBytes());
            return base64Encoder(encryptBytes);
        }
        return null;
    }
 
    /** * base 64 加码 */
    public static String base64Encoder(byte[] base64Code){
   
        return new BASE64Encoder().encodeBuffer(base64Code);
    }
}
```

#### python需要安装pycrypto包

```
pip install  pycrypto
```

#### python代码

```
from Crypto.Cipher import AES
import base64

# 密钥（key）, 密斯偏移量（iv） CBC模式加密
BLOCK_SIZE = 16  # Bytes
pad = lambda s: s + (BLOCK_SIZE - len(s) % BLOCK_SIZE) * \
                chr(BLOCK_SIZE - len(s) % BLOCK_SIZE)
unpad = lambda s: s[:-ord(s[len(s) - 1:])]

vi = 'FaceSunAweSomeIV'
key = 'FaceSunAweSome_K'


def aes_encrypt(data):
    data = pad(data)
    # 字符串补位
    cipher = AES.new(key.encode('utf8'), AES.MODE_CBC, vi.encode('utf8'))
    encryptedbytes = cipher.encrypt(data.encode('utf8'))
    # 加密后得到的是bytes类型的数据，使用Base64进行编码,返回byte字符串
    encodestrs = base64.b64encode(encryptedbytes)
    # 对byte字符串按utf-8进行解码
    enctext = encodestrs.decode('utf8')
    return enctext


def aes_decrypt(data):
    data = data.encode('utf8')
    encodebytes = base64.decodebytes(data)
    # 将加密数据转换位bytes类型数据
    cipher = AES.new(key.encode('utf8'), AES.MODE_CBC, vi.encode('utf8'))
    text_decrypted = cipher.decrypt(encodebytes)
    # 去补位
    text_decrypted = unpad(text_decrypted)
    text_decrypted = text_decrypted.decode('utf8')
    return text_decrypted
```