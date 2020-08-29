# Cryptology Review

----不要停止思考

**主要内容：数学基础、古典密码、MD5、SHA-1、RC4、DES、AES、RSA、ECC**

## 数学基础

1. 逆元（inverse）

   1. 加法模逆元：若a+b≡0 (mod n), 则称a是b的加法模n逆元，b是a的加法模n逆元

   2. 乘法模逆元：若a*b≡1 (mod n)，则称a是b的乘法模逆元，b是a的乘法模n逆元。a的乘法逆元记作a-1

      ![image-20200824130104573](..\Cryptology-review\pic\image-20200824130104573.png)

## 散列

**Message digest algorithm**

==1字节，8位==

* **MD-5**

  有一段不定长的信息序列，产生出一个128位（16字节）的hash value

1. MD5加密原理步骤

   1. 填充，最终结果模512为0

      1. 使位长(bits length) % 512 =448。*在后面填充一个1以及若干个0，以达到目标*
      2. 加上一个64位的二级制数，表示填充前信息长度，如果超过64位则取低64位

   2. 初始化四个32位的被称作链接变量的整数参数，这是个参数我们定义为A、B、C、D其取值为：A=0x01234567，B=0x89abcdef，C=0xfedcba98，D=0x76543210。但考虑到内存数据存储大小端的问题我们将其赋值为：A=0x67452301，B=0xefcdab89，C=0x98badcfe，D=0x10325476

      ![image-20200829092534857](..\Cryptology-review\pic\image-20200829092534857.png)

      *将message分为16个子分组，每个子分组就是32位*

   3. 循环计算

      ```C++
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

   以上为MD5算法的主要过程，具体代码参考https://www.cnblogs.com/foxclever/p/7668369.html

* **SHA-1**

  1.





