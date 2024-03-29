### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-02-27 | Alfred Jiang | - |

### 方案名称
NSString / NSData - 3DES 加密(包含JAVA服务器端解密)

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
加密 \ 解密 \ 3DES

### 需求场景
1. 移动端与服务器敏感数据通讯

2. 移动端本地部分需要保存的敏感数据（NSUserDefaults、文件或数据库）

### 参考链接
1. [iOS 3DES加密 和 java 3DES 解密](http://blog.csdn.net/justinjing0612/article/details/8482689)

### 详细内容
#####1. 首先进入头文件,添加必要的Framework

        #import <CommonCrypto/CommonDigest.h>
        #import <CommonCrypto/CommonCryptor.h>
        #import <Security/Security.h>

#####2. iOS 端实现代码

        #define kChosenDigestLength     CC_SHA1_DIGEST_LENGTH

        #define DESKEY @"D6D2402F1C98E208FF2E863AA29334BD65AE1932A821502D9E5673CDE3C713ACFE53E2103CD40ED6BEBB101B484CAE83D537806C6CB611AEE86ED2CA8C97BBE95CF8476066D419E8E833376B850172107844D394016715B2E47E0A6EECB3E83A361FA75FA44693F90D38C6F62029FCD8EA395ED868F9D718293E9C0E63194E87"

        -(NSString*)TripleDES:(NSString*)plainText encryptOrDecrypt:(CCOperation)encryptOrDecrypt
        {

        const void *vplainText;
        size_t plainTextBufferSize;

        if (encryptOrDecrypt == kCCDecrypt)//解密
        {
            NSData *EncryptData = [GTMBase64 decodeData:[plainText dataUsingEncoding:NSUTF8StringEncoding]];
            plainTextBufferSize = [EncryptData length];
            vplainText = [EncryptData bytes];
        }
        else //加密
        {
            NSData* data = [plainText dataUsingEncoding:NSUTF8StringEncoding];
            plainTextBufferSize = [data length];
            vplainText = (const void *)[data bytes];
        }

        CCCryptorStatus ccStatus;
        uint8_t *bufferPtr = NULL;
        size_t bufferPtrSize = 0;
        size_t movedBytes = 0;

        bufferPtrSize = (plainTextBufferSize + kCCBlockSize3DES) & ~(kCCBlockSize3DES - 1);
        bufferPtr = malloc( bufferPtrSize * sizeof(uint8_t));
        memset((void *)bufferPtr, 0x0, bufferPtrSize);
        // memset((void *) iv, 0x0, (size_t) sizeof(iv));

        const void *vkey = (const void *)[DESKEY UTF8String];
        // NSString *initVec = @"init Vec";
        //const void *vinitVec = (const void *) [initVec UTF8String];
        //  Byte iv[] = {0x12, 0x34, 0x56, 0x78, 0x90, 0xAB, 0xCD, 0xEF};
        ccStatus = CCCrypt(encryptOrDecrypt,
                           kCCAlgorithm3DES,
                           kCCOptionPKCS7Padding | kCCOptionECBMode,
                           vkey,
                           kCCKeySize3DES,
                           nil,
                           vplainText,
                           plainTextBufferSize,
                           (void *)bufferPtr,
                           bufferPtrSize,
                           &movedBytes);
        //if (ccStatus == kCCSuccess) NSLog(@"SUCCESS");
        /*else if (ccStatus == kCC ParamError) return @"PARAM ERROR";
         else if (ccStatus == kCCBufferTooSmall) return @"BUFFER TOO SMALL";
         else if (ccStatus == kCCMemoryFailure) return @"MEMORY FAILURE";
         else if (ccStatus == kCCAlignmentError) return @"ALIGNMENT";
         else if (ccStatus == kCCDecodeError) return @"DECODE ERROR";
         else if (ccStatus == kCCUnimplemented) return @"UNIMPLEMENTED"; */

        NSString *result;

        if (encryptOrDecrypt == kCCDecrypt)
        {
            result = [[[NSString alloc] initWithData:[NSData dataWithBytes:(const void *)bufferPtr
                                                                    length:(NSUInteger)movedBytes]
                                            encoding:NSUTF8StringEncoding]
                      autorelease];
        }
        else
        {
            NSData *myData = [NSData dataWithBytes:(const void *)bufferPtr length:(NSUInteger)movedBytes];
            result = [GTMBase64 stringByEncodingData:myData];
        }

        return result;
        }

#####3. Java 端 3DES 解密和加密算法

        public static String encryptThreeDESECB(String src,String key) throws Exception
        {
            DESedeKeySpec dks = new DESedeKeySpec(key.getBytes("UTF-8"));
            SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DESede");
            SecretKey securekey = keyFactory.generateSecret(dks);

            Cipher cipher = Cipher.getInstance("DESede/ECB/PKCS5Padding");
            cipher.init(Cipher.ENCRYPT_MODE, securekey);
            byte[] b=cipher.doFinal(src.getBytes());

            BASE64Encoder encoder = new BASE64Encoder();
            return encoder.encode(b).replaceAll("\r", "").replaceAll("\n", "");

        }

        //3DESECB解密,key必须是长度大于等于 3*8 = 24 位
        public static String decryptThreeDESECB(String src,String key) throws Exception
        {
            //--通过base64,将字符串转成byte数组
            BASE64Decoder decoder = new BASE64Decoder();
            byte[] bytesrc = decoder.decodeBuffer(src);
            //--解密的key
            DESedeKeySpec dks = new DESedeKeySpec(key.getBytes("UTF-8"));
            SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DESede");
            SecretKey securekey = keyFactory.generateSecret(dks);

            //--Chipher对象解密
            Cipher cipher = Cipher.getInstance("DESede/ECB/PKCS5Padding");
            cipher.init(Cipher.DECRYPT_MODE, securekey);
            byte[] retByte = cipher.doFinal(bytesrc);

            return new String(retByte);
        }

### 效果图
（无）

### 备注
（无）
