### 变更记录
| 序号 | 录入时间 | 录入人 | 备注 |
| -- | -- | -- | -- |
| 1 | 2015-03-12 | Alfred Jiang | - |

### 方案名称
NSString / NSData - RSA 加密(包含JAVA服务器端解密)

### 方案类型（推荐 or 参考）
推荐方案

### 关键字
加密 \ 解密 \ RSA

### 需求场景
1. 移动端与服务器敏感数据通讯加密需求

### 参考链接

1. [iOS中使用RSA对数据进行加密解密](http://witcheryne.iteye.com/blog/2171850)
2. [gen_rsakey.sh](https://gist.github.com/lvjian700/635368d6f1e421447680)
3. [RSAEncryptor](https://gist.github.com/lvjian700/204c23226fdffd6a505d)

### 详细内容

#####1. 使用openssl生成密匙对

gen_rsakey.sh

    #!/usr/bin/env bash
    echo "Generating RSA key pair ..."
    echo "1024 RSA key: private_key.pem"
    openssl genrsa -out private_key.pem 1024

    echo "create certification require file: rsaCertReq.csr"
    openssl req -new -key private_key.pem -out rsaCertReq.csr

    echo "create certification using x509: rsaCert.crt"
    openssl x509 -req -days 3650 -in rsaCertReq.csr -signkey private_key.pem -out rsaCert.crt

    echo "create public_key.der For IOS"
    openssl x509 -outform der -in rsaCert.crt -out public_key.der

    echo "create private_key.p12 For IOS. Please remember your password. The password will be used in iOS."
    openssl pkcs12 -export -out private_key.p12 -inkey private_key.pem -in rsaCert.crt

    echo "create rsa_public_key.pem For Java"
    openssl rsa -in private_key.pem -out rsa_public_key.pem -pubout
    echo "create pkcs8_private_key.pem For Java"
    openssl pkcs8 -topk8 -in private_key.pem -out pkcs8_private_key.pem -nocrypt

    echo "finished."

#####2. 导入工具类 RSAEncryptor

RSAEncryptor.h

    //
    //  RSAEncryptor.h
    //  RSATestProjcet
    //
    //  Created by Alfred Jiang on 3/12/15.
    //  Copyright (c) 2015 Alfred Jiang. All rights reserved.
    //

    #import <Foundation/Foundation.h>

    @interface RSAEncryptor : NSObject

    #pragma mark - Instance Methods

    -(void) loadPublicKeyFromFile: (NSString*) derFilePath;
    -(void) loadPublicKeyFromData: (NSData*) derData;

    -(void) loadPrivateKeyFromFile: (NSString*) p12FilePath password:(NSString*)p12Password;
    -(void) loadPrivateKeyFromData: (NSData*) p12Data password:(NSString*)p12Password;

    -(NSString*) rsaEncryptString:(NSString*)string;
    -(NSData*) rsaEncryptData:(NSData*)data ;

    -(NSString*) rsaDecryptString:(NSString*)string;
    -(NSData*) rsaDecryptData:(NSData*)data;

    -(BOOL) rsaSHA1VerifyData:(NSData *) plainData
                withSignature:(NSData *) signature;

    #pragma mark - Class Methods

    +(void) setSharedInstance: (RSAEncryptor*)instance;
    +(RSAEncryptor*) sharedInstance;

    @end

RSAEncryptor.m

    //
    //  RSAEncryptor.m
    //  RSATestProjcet
    //
    //  Created by Alfred Jiang on 3/12/15.
    //  Copyright (c) 2015 Alfred Jiang. All rights reserved.
    //

    #import <Security/Security.h>
    #import "RSAEncryptor.h"
    #import "GTMBase64.h"
    #import <CommonCrypto/CommonCrypto.h>

    @implementation RSAEncryptor
    {
        SecKeyRef publicKey;
        SecKeyRef privateKey;
    }

    -(void)dealloc
    {
        if (nil != publicKey) {
            CFRelease(publicKey);
        }
        if (nil != privateKey) {
            CFRelease(privateKey);
        }
    }

    -(SecKeyRef) getPublicKey {
        return publicKey;
    }

    -(SecKeyRef) getPrivateKey {
        return privateKey;
    }

    -(void) loadPublicKeyFromFile: (NSString*) derFilePath
    {
        NSData *derData = [[NSData alloc] initWithContentsOfFile:derFilePath];
        [self loadPublicKeyFromData: derData];
    }
    -(void) loadPublicKeyFromData: (NSData*) derData
    {
        publicKey = [self getPublicKeyRefrenceFromeData: derData];
    }

    -(void) loadPrivateKeyFromFile: (NSString*) p12FilePath password:(NSString*)p12Password
    {
        NSData *p12Data = [NSData dataWithContentsOfFile:p12FilePath];
        [self loadPrivateKeyFromData: p12Data password:p12Password];
    }

    -(void) loadPrivateKeyFromData: (NSData*) p12Data password:(NSString*)p12Password
    {
        privateKey = [self getPrivateKeyRefrenceFromData: p12Data password: p12Password];
    }

    #pragma mark - Private Methods

    -(SecKeyRef) getPublicKeyRefrenceFromeData: (NSData*)derData
    {
        SecCertificateRef myCertificate = SecCertificateCreateWithData(kCFAllocatorDefault, (__bridge CFDataRef)derData);
        SecPolicyRef myPolicy = SecPolicyCreateBasicX509();
        SecTrustRef myTrust;
        OSStatus status = SecTrustCreateWithCertificates(myCertificate,myPolicy,&myTrust);
        SecTrustResultType trustResult;
        if (status == noErr) {
            status = SecTrustEvaluate(myTrust, &trustResult);
        }
        SecKeyRef securityKey = SecTrustCopyPublicKey(myTrust);
        CFRelease(myCertificate);
        CFRelease(myPolicy);
        CFRelease(myTrust);

        return securityKey;
    }

    -(SecKeyRef) getPrivateKeyRefrenceFromData: (NSData*)p12Data password:(NSString*)password
    {
        SecKeyRef privateKeyRef = NULL;
        NSMutableDictionary * options = [[NSMutableDictionary alloc] init];
        [options setObject: password forKey:(__bridge id)kSecImportExportPassphrase];
        CFArrayRef items = CFArrayCreate(NULL, 0, 0, NULL);
        OSStatus securityError = SecPKCS12Import((__bridge CFDataRef) p12Data, (__bridge CFDictionaryRef)options, &items);
        if (securityError == noErr && CFArrayGetCount(items) > 0) {
            CFDictionaryRef identityDict = CFArrayGetValueAtIndex(items, 0);
            SecIdentityRef identityApp = (SecIdentityRef)CFDictionaryGetValue(identityDict, kSecImportItemIdentity);
            securityError = SecIdentityCopyPrivateKey(identityApp, &privateKeyRef);
            if (securityError != noErr) {
                privateKeyRef = NULL;
            }
        }
        CFRelease(items);

        return privateKeyRef;
    }

    #pragma mark - Encrypt

    -(NSString*) rsaEncryptString:(NSString*)string {
        NSData* data = [string dataUsingEncoding:NSUTF8StringEncoding];
        NSData* encryptedData = [self rsaEncryptData: data];
        NSString* base64EncryptedString = [GTMBase64 stringByEncodingData:encryptedData];
    //    [encryptedData base64EncodedString];
        return base64EncryptedString;
    }

    // 加密的大小受限于SecKeyEncrypt函数，SecKeyEncrypt要求明文和密钥的长度一致，如果要加密更长的内容，需要把内容按密钥长度分成多份，然后多次调用SecKeyEncrypt来实现
    -(NSData*) rsaEncryptData:(NSData*)data {
        SecKeyRef key = [self getPublicKey];
        size_t cipherBufferSize = SecKeyGetBlockSize(key);
        uint8_t *cipherBuffer = malloc(cipherBufferSize * sizeof(uint8_t));
        size_t blockSize = cipherBufferSize - 11;       // 分段加密
        size_t blockCount = (size_t)ceil([data length] / (double)blockSize);
        NSMutableData *encryptedData = [[NSMutableData alloc] init] ;
        for (int i=0; i<blockCount; i++) {
            int bufferSize = MIN(blockSize,[data length] - i * blockSize);
            NSData *buffer = [data subdataWithRange:NSMakeRange(i * blockSize, bufferSize)];
            OSStatus status = SecKeyEncrypt(key, kSecPaddingPKCS1, (const uint8_t *)[buffer bytes], [buffer length], cipherBuffer, &cipherBufferSize);
            if (status == noErr){
                NSData *encryptedBytes = [[NSData alloc] initWithBytes:(const void *)cipherBuffer length:cipherBufferSize];
                [encryptedData appendData:encryptedBytes];
            }else{
                if (cipherBuffer) {
                    free(cipherBuffer);
                }
                return nil;
            }
        }
        if (cipherBuffer){
            free(cipherBuffer);
        }
        return encryptedData;
    }

    #pragma mark - Decrypt

    -(NSString*) rsaDecryptString:(NSString*)string {

        NSData* data = [[NSData alloc] initWithBase64EncodedString:string options:NSDataBase64DecodingIgnoreUnknownCharacters];
        NSData* decryptData = [self rsaDecryptData: data];
        NSString* result = [[NSString alloc] initWithData: decryptData encoding:NSUTF8StringEncoding];
        return result;
    }

    -(NSData*) rsaDecryptData:(NSData*)data {
        SecKeyRef key = [self getPrivateKey];
        size_t cipherLen = [data length];
        void *cipher = malloc(cipherLen);
        [data getBytes:cipher length:cipherLen];
        size_t plainLen = SecKeyGetBlockSize(key) - 12;
        void *plain = malloc(plainLen);
        OSStatus status = SecKeyDecrypt(key, kSecPaddingPKCS1, cipher, cipherLen, plain, &plainLen);

        if (status != noErr) {
            return nil;
        }

        NSData *decryptedData = [[NSData alloc] initWithBytes:(const void *)plain length:plainLen];

        return decryptedData;
    }

    #pragma marke - verify file SHA1
    -(BOOL) rsaSHA1VerifyData:(NSData *) plainData
                withSignature:(NSData *) signature {

        size_t signedHashBytesSize = SecKeyGetBlockSize([self getPublicKey]);
        const void* signedHashBytes = [signature bytes];

        size_t hashBytesSize = CC_SHA1_DIGEST_LENGTH;
        uint8_t* hashBytes = malloc(hashBytesSize);
        if (!CC_SHA1([plainData bytes], (CC_LONG)[plainData length], hashBytes)) {
            return NO;
        }

        OSStatus status = SecKeyRawVerify(publicKey,
                                          kSecPaddingPKCS1SHA1,
                                          hashBytes,
                                          hashBytesSize,
                                          signedHashBytes,
                                          signedHashBytesSize);

        return status == errSecSuccess;
    }

    #pragma mark - Class Methods

    static RSAEncryptor* sharedInstance = nil;

    +(void) setSharedInstance: (RSAEncryptor*)instance
    {
        sharedInstance = instance;
    }

    +(RSAEncryptor*) sharedInstance
    {
        return sharedInstance;
    }

    @end

#####3. 使用 RSAEncryptor 进行RSA加密解密

    RSAEncryptor *rsa = [[RSAEncryptor alloc] init];

    NSLog(@"encryptor using rsa");
    NSString *publicKeyPath = [[NSBundle mainBundle] pathForResource:@"public_key" ofType:@"der"];
    NSLog(@"public key: %@", publicKeyPath);
    [rsa loadPublicKeyFromFile:publicKeyPath];

    NSString *securityText = @"hello ~";
    NSString *encryptedString = [rsa rsaEncryptString:securityText];
    NSLog(@"encrypted data: %@", encryptedString);

    NSLog(@"decryptor using rsa");
    [rsa loadPrivateKeyFromFile:[[NSBundle mainBundle] pathForResource:@"private_key" ofType:@"p12"] password:@"1234"];
    NSString *decryptedString = [rsa rsaDecryptString:encryptedString];
    NSLog(@"decrypted data: %@", decryptedString);


#####4. 在服务器端解码数据(Java)
1. 生成的pkcs8 private key

    gen shell
        openssl pkcs8 -topk8 -in private_key.pem -out pkcs8_private_key.pem -nocrypt

2. 具体解码步骤

    加载pkcs8 private key:
        读取private key文件
        去掉private key头尾的"-----BEGIN PRIVATE KEY-----"和"-----BEGIN PRIVATE KEY-----"
        删除private key中的换行
        对处理后的数据进行Base64解码
        使用解码后的数据生成private key.

    解密数据:
        对数据进行Base64解码
        使用RSA decrypt数据.

3. 实现代码
        import javax.crypto.BadPaddingException;
        import javax.crypto.Cipher;
        import javax.crypto.IllegalBlockSizeException;
        import javax.crypto.NoSuchPaddingException;
        import java.io.IOException;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.security.InvalidKeyException;
        import java.security.KeyFactory;
        import java.security.NoSuchAlgorithmException;
        import java.security.PrivateKey;
        import java.security.spec.InvalidKeySpecException;
        import java.security.spec.PKCS8EncodedKeySpec;
        import java.util.Base64;

        import static java.lang.String.format;

        public class Encryptor {

            public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeySpecException, NoSuchPaddingException, InvalidKeyException, BadPaddingException, IllegalBlockSizeException {
                PrivateKey privateKey = readPrivateKey();

                String message = "AFppaFPTbmboMZD55cjCfrVaWUW7+hZkaq16Od+6fP0lwz/yC+Rshb/8cf5BpBlUao2EunchnzeKxzpiPqtCcCITKvk6HcFKZS0sN9wOhlQFYT+I4f/CZITwBVAJaldZ7mkyOiuvM+raXMwrS+7MLKgYXkd5cFPxEsTxpMSa5Nk=";
                System.out.println(format("- decrypt rsa encrypted base64 message: %s", message));
                // hello ~,  encrypted and encoded with Base64:
                byte[] data = encryptedData(message);
                String text = decrypt(privateKey, data);
                System.out.println(text);
            }

            private static String decrypt(PrivateKey privateKey, byte[] data) throws NoSuchAlgorithmException, NoSuchPaddingException, InvalidKeyException, IllegalBlockSizeException, BadPaddingException {
                Cipher cipher = Cipher.getInstance("RSA/ECB/PKCS1Padding");
                cipher.init(Cipher.DECRYPT_MODE, privateKey);
                byte[] decryptedData = cipher.doFinal(data);

                return new String(decryptedData);
            }

            private static byte[] encryptedData(String base64Text) {
                return Base64.getDecoder().decode(base64Text.getBytes(Charset.forName("UTF-8")));
            }

            private static PrivateKey readPrivateKey() throws IOException, NoSuchAlgorithmException, InvalidKeySpecException {
                byte[] privateKeyData = Files.readAllBytes(
                        Paths.get("/Users/twer/macspace/ios_workshop/Security/SecurityLogin/tools/pkcs8_private_key.pem"));

                byte[] decodedKeyData = Base64.getDecoder()
                        .decode(new String(privateKeyData)
                                .replaceAll("-----\\w+ PRIVATE KEY-----", "")
                                .replace("\n", "")
                                .getBytes());

                return KeyFactory.getInstance("RSA").generatePrivate(new PKCS8EncodedKeySpec(decodedKeyData));
            }
        }



### 效果图
（无）

### 备注

RSA使用"秘匙对"对数据进行加密解密.在加密解密数据前,需要先生成公钥(public key)和私钥(private key).

公钥(public key): 用于加密数据. 用于公开, 一般存放在数据提供方, 例如iOS客户端.

私钥(private key): 用于解密数据. 必须保密, 私钥泄露会造成安全问题.

