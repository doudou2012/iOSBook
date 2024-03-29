### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-02-27 | Alfred Jiang | - |

### 方案名称
NSString / NSData - DES 加密(包含PHP服务器端解密)

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
加密 \ 解密 \ DES

### 需求场景
1. 移动端与服务器敏感数据通讯

2. 移动端本地部分需要保存的敏感数据（NSUserDefaults、文件或数据库）

### 参考链接
1. [iOS Objective-C 與 PHP DES 加解密演算法實作](http://blog.toright.com/posts/2657/ios-objective-c-%E8%88%87-php-des-%E5%8A%A0%E8%A7%A3%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95%E5%AF%A6%E4%BD%9C.html)

### 详细内容
#####1. 首先进入头文件,添加必要的Framework


    #import <CommonCrypto/CommonCryptor.h>

#####2. iOS 端实现代码


    //解密
    -(NSString*) decryptUseDES:(NSString*)cipherText key:(NSString*)key {
        // 利用 GTMBase64 解碼 Base64 字串
        NSData* cipherData = [GTMBase64 decodeString:cipherText];
        unsigned char buffer[1024];
        memset(buffer, 0, sizeof(char));
        size_t numBytesDecrypted = 0;

        // IV 偏移量不需使用
        CCCryptorStatus cryptStatus = CCCrypt(kCCDecrypt,
                                              kCCAlgorithmDES,
                                              kCCOptionPKCS7Padding | kCCOptionECBMode,
                                              [key UTF8String],
                                              kCCKeySizeDES,
                                              nil,
                                              [cipherData bytes],
                                              [cipherData length],
                                              buffer,
                                              1024,
                                              &numBytesDecrypted);
        NSString* plainText = nil;
        if (cryptStatus == kCCSuccess) {
            NSData* data = [NSData dataWithBytes:buffer length:(NSUInteger)numBytesDecrypted];
            plainText = [[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] autorelease];
        }
        return plainText;
    }

    //加密
    -(NSString *) encryptUseDES:(NSString *)clearText key:(NSString *)key
    {
        NSData *data = [clearText dataUsingEncoding:NSUTF8StringEncoding allowLossyConversion:YES];
        unsigned char buffer[1024];
        memset(buffer, 0, sizeof(char));
        size_t numBytesEncrypted = 0;

        CCCryptorStatus cryptStatus = CCCrypt(kCCEncrypt,
                                              kCCAlgorithmDES,
                                              kCCOptionPKCS7Padding | kCCOptionECBMode,
                                              [key UTF8String],
                                              kCCKeySizeDES,
                                              nil,
                                              [data bytes],
                                              [data length],
                                              buffer,
                                              1024,
                                              &numBytesEncrypted);

        NSString* plainText = nil;
        if (cryptStatus == kCCSuccess) {
            NSData *dataTemp = [NSData dataWithBytes:buffer length:(NSUInteger)numBytesEncrypted];
            plainText = [GTMBase64 stringByEncodingData:dataTemp];
        }else{
            NSLog(@"DES加密失败");
        }
        return plainText;
    }

#####3. PHP 端 DES 解密和加密算法


    <?php
    /**
     * PHP DES 加密程式
     *
     * @param $key 密鑰（八個字元內）
     * @param $encrypt 要加密的明文
     * @return string 密文
     */
    function encrypt ($key, $encrypt)
    {
        // 根據 PKCS#7 RFC 5652 Cryptographic Message Syntax (CMS) 修正 Message 加入 Padding
        $block = mcrypt_get_block_size(MCRYPT_DES, MCRYPT_MODE_ECB);
        $pad = $block - (strlen($encrypt) % $block);
        $encrypt .= str_repeat(chr($pad), $pad);

        // 不需要設定 IV 進行加密
        $passcrypt = mcrypt_encrypt(MCRYPT_DES, $key, $encrypt, MCRYPT_MODE_ECB);
        return base64_encode($passcrypt);
    }

    /**
     * PHP DES 解密程式
     *
     * @param $key 密鑰（八個字元內）
     * @param $decrypt 要解密的密文
     * @return string 明文
     */
    function decrypt ($key, $decrypt)
    {
        // 不需要設定 IV
        $str = mcrypt_decrypt(MCRYPT_DES, $key, base64_decode($decrypt), MCRYPT_MODE_ECB);

        // 根據 PKCS#7 RFC 5652 Cryptographic Message Syntax (CMS) 修正 Message 移除 Padding
        $pad = ord($str[strlen($str) - 1]);
        return substr($str, 0, strlen($str) - $pad);
    }

    $key = 'skey';
    $plain = '0123ABCD!@#$中文';
    $encrypt = encrypt($key, $plain);
    $decrypt = decrypt($key, $encrypt);

    echo 'plain = ' . $plain . "\n";
    echo 'encrypt = ' . $encrypt . "\n";
    echo 'decrypt = ' . $decrypt . "\n";
    ?>

### 效果图
（无）

### 备注
（无）
