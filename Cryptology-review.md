# Cryptology Review

​																														*----不要停止思考*



==**主要内容：数学基础、古典密码、MD5、SHA-1、RC4、DES、AES、RSA、ECC**==

==1字节（byte）= 8位（bit）==

==1字（word）= 2字节（byte）==

==1 KB = 1024 Bytes==



## 数学基础

逆元（inverse）

1. 加法模逆元：若a+b≡0 (mod n), 则称a是b的加法模n逆元，b是a的加法模n逆元

2. 乘法模逆元：若a*b≡1 (mod n)，则称a是b的乘法模逆元，b是a的乘法模n逆元。a的乘法逆元记作a-1

   <img src="..\Cryptology-review\pic\image-20200824130104573.png" alt="image-20200824130104573" style="zoom:50%;" />

## 散列

* **MD-5**

  **Message digest algorithm** 

  有一段不定长的信息序列，产生出一个128位（16字节）的hash value

  MD5加密原理步骤：

  1. 填充，最终结果模512为0

     1. 使位长(bits length) % 512 =448。*在后面填充一个1以及若干个0，以达到目标*
     2. 加上一个64位的二进制数，表示填充前信息长度，如果超过64位则取低64位

  2. 初始化四个32位的被称作链接变量的整数参数，这是个参数我们定义为A、B、C、D其取值为：A=0x01234567，B=0x89abcdef，C=0xfedcba98，D=0x76543210。但考虑到内存数据存储大小端的问题我们将其赋值为：A=0x67452301，B=0xefcdab89，C=0x98badcfe，D=0x10325476<img src="..\Cryptology-review\pic\image-20200829092534857.png" alt="image-20200829092534857" style="zoom: 80%;" />

     *将message分为16个子分组，每个子分组就是32位*

  3. 循环计算

     ```c++
     //第一轮循环计算
             FF(a,b,c,d,M[0],7,0xd76aa478);
             FF(d,a,b,c,M[1],12,0xe8c7b756);
             FF(c,d,a,b,M[2],17,0x242070db);
             FF(b,c,d,a,M[3],22,0xc1bdceee);
             FF(a,b,c,d,M[4],7,0xf57c0faf);
             FF(d,a,b,c,M[5],12,0x4787c62a);
             FF(c,d,a,b,M[6],17,0xa8304613);
             FF(b,c,d,a,M[7],22,0xfd469501) ;
             FF(a,b,c,d,M[8],7,0x698098d8) ;
             FF(d,a,b,c,M[9],12,0x8b44f7af) ;
             FF(c,d,a,b,M[10],17,0xffff5bb1) ;
             FF(b,c,d,a,M[11],22,0x895cd7be) ;
             FF(a,b,c,d,M[12],7,0x6b901122) ;
             FF(d,a,b,c,M[13],12,0xfd987193) ;
             FF(c,d,a,b,M[14],17,0xa679438e) ;
             FF(b,c,d,a,M[15],22,0x49b40821);
     //后面类似，第二轮用的函数就是GG
     ```

  4. 结果输出

     处理完所有的512位的分组后，得到一组新的A,B,C,D的值，将这些值按ABCD的顺序级联，就得到了想要的MD5散列值。当然，输出依然要考虑内存存储的大小端问题

  **算法的主要过程，具体代码参考[信息摘要算法之一：MD5算法解析及实现](https://www.cnblogs.com/foxclever/p/7668369.html)**

* **SHA（Secure Hash Algorithm）**

  *SHA系列（单位都为位）*

  ![image-20200829103616676](..\Cryptology-review\pic\image-20200829103616676.png)

  下面以SHA-1为例介绍算法的基本步骤和原理

  *SHA-1是将一个小于2^64^位的信息压缩成160位的信息摘要*

  1. 填充（类似MD5）*需要注意的是：MD5的信息长度是没有限制的，如果message长度超过2^64^位则取低64位。SHA-1算法要求Message长度最多为2^64^位*

     1. 填充到mod512余448
     2. 加上64位二进制数表示信息长度（注意低位优先）

  2. 分组

     1. 将明文以512分组，称为明文组。
     2. 对于每个512位明文组，将其分为16份更小的明文分组，称为子明文组，每个子明文组32位。我们且使用M[t]（t= 0, 1,……15）来表示这16个子明文分组。然后需要将这16个子明文分组扩充到80个子明文分组，我们将其记为W[t]（t= 0, 1,……79），扩充的具体方法是：当0≤t≤15时，Wt = Mt；当16≤t≤79时，Wt = ( Wt-3 ⊕ Wt-8⊕ Wt-14⊕ Wt-16) <<< 1，从而得到80个子明文分组。

  3. 初始化缓存*（MD5因为输出为128位，所以是四个，SHA-1输出160位所以是5个）*

     初始化5个32位链接变量，记为A、B、C、D、E。其初始赋值分别为：A = 0x67452301、B = 0xEFCDAB89、Ｃ = 0x98BADCFE、Ｄ = 0x10325476、Ｅ = 0xC3D2E1F0

  4. 计算 

     一共有4轮计算，每轮包括20个步骤，最终产生的160位digest存在5个32位链接变量中。

     <img src="..\Cryptology-review\pic\image-20200829113904038.png" alt="image-20200829113904038" style="zoom: 50%;" />

     **详细代码参看[信息摘要算法之二：SHA1算法分析及实现](https://www.cnblogs.com/foxclever/p/8282366.html)**

## 分组密码工作模式与流密码

1. 分组密码工作模式

   分组加密（block cipher）即为将明文分成多个等长的模块，使用确定的算法和对称密钥对每组分别加密

   常见的分组密码算法分组长度和密钥长度如下，填充方式暂略，具体可以参考[分组密码工作模式](http://www.beesfun.com/2017/03/06/分组密码工作模式/)，且本文也是参考该篇blog

   <img src="..\Cryptology-review\pic\image-20200829141745995.png" alt="image-20200829141745995" style="zoom: 67%;" />

   1. 电子密码簿（ECB，Electronic CodeBook mode）

      ECB 将需要加密的明文按照块密码的块大小分为若干块，并对每个块独立进行加密

      <img src="..\Cryptology-review\pic\clip_image002.jpg" alt="img" style="zoom:50%;" />

      原理清晰明了，明文与密文一一对应。优点是简单，可并行。缺点显而易见，容易被攻击

   2. 密文块链接模式（CBC，Cipher Block Chaining mode）

      CBC模式加密，先将明文分组与前一个密文分组(或为初始化向量IV)进行XOR运算，然后再进行加密

      解密，密文分组先进行解密，然后再进行xor运算得到明文分组

      <img src="..\Cryptology-review\pic\clip_image001.jpg" alt="img" style="zoom:50%;" />

      加密是不能并行的，但是解密可以并行计算

   3. 密文反馈模式（CFB，Cipher FeedBack mode）

      CFB模式可以看做是一种使用分组密码来实现流密码的方式, CFB模式中由密码算法所生成的比特序列称为密钥流（key stream）。在CFB模式中，密码算法就相当于用来生成密钥流的伪随机数生成器，而初始化向量就相当于伪随机数生成器的“种子”， 它的明文数据可以被逐比特加密

      <img src="..\Cryptology-review\pic\clip_image003.jpg" alt="img" style="zoom:50%;" />

      <img src="..\Cryptology-review\pic\image-20200829144312790.png" alt="image-20200829144312790" style="zoom:60%;" />

2. 流密码算法RC4（Rivest Cipher 4）

   流密码，Stream Cipher，对称加密算法（eg. DES）

   RC4算法密钥长度可变（1-256byte）

   * 关键变量

     * 密钥流：RC4算法根据明文和密钥生成相应的密钥流，长度和明文长度对应。C~i~=P~i~  Xor  KS~i~

       *KS即为密钥流*

     * 状态向量S，长度256 byte

     * 临时向量，256 byte

     * 密钥K，与plaintext的长度，密钥流的长度**没有**必然联系

   * 算法步骤

     1. 初始化密钥，根据输入的秘钥key，使用密钥调度算法（KSA）生成一个256字节的sbox
     2. 再通过伪随机数生成算法（PRGA）得到密钥流（keystream）
     3. 加密：密钥流与明文进行异或运算得到密文
     4. 解密：秘文与密钥流进行异或运算得到明文

   ```c
   #include <stdio.h>
   #include <string.h>
   #include <stdlib.h>
   
   void swap(unsigned char *a, unsigned char *b) {
     unsigned char t = *a;
     *a = *b;
     *b = t;
   }
   
   void rc4KSA(unsigned char *s, const unsigned char *key, int len) {
       for (int i = 0; i < 256; i++) s[i] = i;
       int j = 0;
       for (int i = 0; i < 256; i++ ) {
           j = (j + s[i] + *(key + i % len)) % 256;
           swap(s + i, s + j);
       }
   }
   
   void rc4PRGA(unsigned char *s, char *data, size_t len) {
       int i = 0;
       int j = 0;
       for ( size_t idx = 0; idx < len; idx++ ) {
           i = (i + 1) % 256;
           j = (j + s[i]) % 256;
           swap(s + i, s + j);
           unsigned char k = s[(s[i] + s[j]) % 256];
           data[idx] ^= k;  // k是伪随机数，和明文进行XOR进行加密
       }
   }
   
   int main(int argc, const char * argv[]) {
     unsigned char s[256]; // S-box
     unsigned char key[128]; // key的长度1到256字节，本例子使用128字节
     arc4random_buf((unsigned char *)key, sizeof(key)); // 用伪随机数算法生成key
     char data[1000] = "这里是要机密的数据";
   
     // 加密
     rc4KSA(s, key, sizeof(key));
     rc4PRGA(s, data, strlen(data));
   
     // 解密
     rc4KSA(s, key, sizeof(key));
     rc4PRGA(s, data, strlen(data));
     return 0;
   }
   ```

## DES & AES



## RSA  

~RC4，DES，AES都是对称密码体系，RSA是非对称密码体系~

1. 密钥产生过程

   1. 选出两个大素数，$p 和 q$ ，计算乘积 $n = p *q$，其中n公开，$p和q$保密
   2. 随机选取加密密钥e，使e和$(p-1)*(q-1)$互素
   3. 找出d，使得$e*d=1\ mod((p-1)*(q-1))$

   加密与解密时**都没有用到**：$p，q，(p-1)*(q-1)$

   *公开公钥(e,n)，保留私钥(d,n)*

2. 下面为加密公式和解密公式:

   Encryption：$C = M^e(mod\ n)$

   Decryption：$M=C^d(mod\ n)$

3. 加密：用对方的公钥加密，对方收到后用自己的私钥解密

   签名：用自己的私钥加密，对方收到后用自己的公钥解密

## ECC 椭圆曲线（Elliptic Curve）算法

