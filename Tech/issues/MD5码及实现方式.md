# ## MD5码及实现方式
什么是MD5算法

MD5讯息摘要演算法（英语：MD5 Message-Digest Algorithm），一种被广泛使用的密码杂凑函数，可以产生出一个128位元（16位元组）的散列值（hash value），用于确保信息传输完整一致。

MD5功能

输入任意长度的信息，经过处理，输出为128位的信息（数字指纹）； 
不同的输入得到的不同的结果（唯一性）；

MD5属不属于加密算法

认为不属于的人是因为他们觉得不能从密文（散列值）反过来得到原文，即没有解密算法，所以这部分人认为MD5只能属于算法，不能称为加密算法； 
认为属于的人是因为他们觉得经过MD5处理后看不到原文，即已经将原文加密，所以认为MD5属于加密算法；我个人支持前者，正如认为BASE64算法只能算编码一样。

MD5算法是否可逆？

MD5不可逆的原因是其是一种散列函数，使用的是hash算法，在计算过程中原文的部分信息是丢失了的。

不过有个地方值得指出的是，一个MD5理论上的确是可能对应无数多个原文的，因为MD5是有限多个的而原文可以是无数多个。比如主流使用的MD5将任意长度的“字节串映射为一个128bit的大整数。也就是一共有2^128种可能，大概是3.4*10^38，这个数字是有限多个的，而但是世界上可以被用来加密的原文则会有无数的可能性。

不过需要注意的一点是，尽量这是一个理论上的有限对无限，不过问题是这个无限在现实生活中并不完全成立，因为一方面现实中原文的长度往往是有限的（以常用的密码为例，一般人都在20位以内），另一方面目前想要发现两段原文对应同一个MD5（专业的说这叫杂凑冲撞）值非常困难，因此某种意义上来说，在一定范围内想构建MD5值与原文的一一对应关系是完全有可能的。所以对于MD5目前最有效的攻击方式就是彩虹表，具体详情你可以通过谷歌了解。

MD5相当于超损压缩。

MD5用途

1.防止被篡改： 
1）比如发送一个电子文档，发送前，我先得到MD5的输出结果a。然后在对方收到电子文档后，对方也得到一个MD5的输出结果b。如果a与b一样就代表中途未被篡改。 
2）比如我提供文件下载，为了防止不法分子在安装程序中添加木马，我可以在网站上公布由安装文件得到的MD5输出结果。 
3）SVN在检测文件是否在CheckOut后被修改过，也是用到了MD5.

2.防止直接看到明文： 
现在很多网站在数据库存储用户的密码的时候都是存储用户密码的MD5值。这样就算不法分子得到数据库的用户密码的MD5值，也无法知道用户的密码。（比如在UNIX系统中用户的密码就是以MD5（或其它类似的算法）经加密后存储在文件系统中。当用户登录的时候，系统把用户输入的密码计算成MD5值，然后再去和保存在文件系统中的MD5值进行比较，进而确定输入的密码是否正确。通过这样的步骤，系统在并不知道用户密码的明码的情况下就可以确定用户登录系统的合法性。这不但可以避免用户的密码被具有系统管理员权限的用户知道，而且还在一定程度上增加了密码被破解的难度。）

3.防止抵赖（数字签名）： 
这需要一个第三方认证机构。例如A写了一个文件，认证机构对此文件用MD5算法产生摘要信息并做好记录。若以后A说这文件不是他写的，权威机构只需对此文件重新产生摘要信息，然后跟记录在册的摘要信息进行比对，相同的话，就证明是A写的了。这就是所谓的“数字签名”。

MD5安全性

普遍认为MD5是很安全，因为暴力破解的时间是一般人无法接受的。实际上如果把用户的密码MD5处理后再存储到数据库，其实是很不安全的。因为用户的密码是比较短的，而且很多用户的密码都使用生日，手机号码，身份证号码，电话号码等等。或者使用常用的一些吉利的数字，或者某个英文单词。如果我把常用的密码先MD5处理，把数据存储起来，然后再跟你的MD5结果匹配，这时我就有可能得到明文。比如某个MD5破解网站http://www.cmd5.com/default.aspx，所以现在大多数网站密码的策略是强制要求用户使用数字大小写字母的组合的方式提高用户密码的安全度。

MD5算法过程

对MD5算法简要的叙述可以为：MD5以512位分组来处理输入的信息，且每一分组又被划分为16个32位子分组，经过了一系列的处理后，算法的输出由四个32位分组组成，将这四个32位分组级联后将生成一个128位散列值。

第一步、填充：如果输入信息的长度(bit)对512求余的结果不等于448，就需要填充使得对512求余的结果等于448。填充的方法是填充一个1和n个0。填充完后，信息的长度就为N*512+448(bit)；

第二步、记录信息长度：用64位来存储填充前信息长度。这64位加在第一步结果的后面，这样信息长度就变为N*512+448+64=(N+1)*512位。

第三步、装入标准的幻数（四个整数）：标准的幻数（物理顺序）是（A=(01234567)16，B=(89ABCDEF)16，C=(FEDCBA98)16，D=(76543210)16）。如果在程序中定义应该是: 
（A=0X67452301L，B=0XEFCDAB89L，C=0X98BADCFEL，D=0X10325476L）。有点晕哈，其实想一想就明白了。

第四步、四轮循环运算：循环的次数是分组的个数（N+1）
1）将每一512字节细分成16个小组，每个小组64位（8个字节）
2）先认识四个线性函数(&是与,|是或,是非,^是异或)
```bash
F(X,Y,Z)=(X&Y)|((~X)&Z)
G(X,Y,Z)=(X&Z)|(Y&(~Z))
H(X,Y,Z)=X^Y^Z
I(X,Y,Z)=Y^(X|(~Z))
```
3）设Mj表示消息的第j个子分组（从0到15），\<\<
```bash
FF(a,b,c,d,Mj,s,ti)表示a=b+((a+F(b,c,d)+Mj+ti)<<<s)
GG(a,b,c,d,Mj,s,ti)表示a=b+((a+G(b,c,d)+Mj+ti)<<<s)
HH(a,b,c,d,Mj,s,ti)表示a=b+((a+H(b,c,d)+Mj+ti)<<<s)
II(a,b,c,d,Mj,s,ti)表示a=b+((a+I(b,c,d)+Mj+ti)<<<s)
```
4）四轮运算
```bash
第一轮
a=FF(a,b,c,d,M0,7,0xd76aa478)
b=FF(d,a,b,c,M1,12,0xe8c7b756)
c=FF(c,d,a,b,M2,17,0x242070db)
d=FF(b,c,d,a,M3,22,0xc1bdceee)
a=FF(a,b,c,d,M4,7,0xf57c0faf)
b=FF(d,a,b,c,M5,12,0x4787c62a)
c=FF(c,d,a,b,M6,17,0xa8304613)
d=FF(b,c,d,a,M7,22,0xfd469501)
a=FF(a,b,c,d,M8,7,0x698098d8)
b=FF(d,a,b,c,M9,12,0x8b44f7af)
c=FF(c,d,a,b,M10,17,0xffff5bb1)
d=FF(b,c,d,a,M11,22,0x895cd7be)
a=FF(a,b,c,d,M12,7,0x6b901122)
b=FF(d,a,b,c,M13,12,0xfd987193)
c=FF(c,d,a,b,M14,17,0xa679438e)
d=FF(b,c,d,a,M15,22,0x49b40821)

第二轮
a=GG(a,b,c,d,M1,5,0xf61e2562)
b=GG(d,a,b,c,M6,9,0xc040b340)
c=GG(c,d,a,b,M11,14,0x265e5a51)
d=GG(b,c,d,a,M0,20,0xe9b6c7aa)
a=GG(a,b,c,d,M5,5,0xd62f105d)
b=GG(d,a,b,c,M10,9,0x02441453)
c=GG(c,d,a,b,M15,14,0xd8a1e681)
d=GG(b,c,d,a,M4,20,0xe7d3fbc8)
a=GG(a,b,c,d,M9,5,0x21e1cde6)
b=GG(d,a,b,c,M14,9,0xc33707d6)
c=GG(c,d,a,b,M3,14,0xf4d50d87)
d=GG(b,c,d,a,M8,20,0x455a14ed)
a=GG(a,b,c,d,M13,5,0xa9e3e905)
b=GG(d,a,b,c,M2,9,0xfcefa3f8)
c=GG(c,d,a,b,M7,14,0x676f02d9)
d=GG(b,c,d,a,M12,20,0x8d2a4c8a)

第三轮
a=HH(a,b,c,d,M5,4,0xfffa3942)
b=HH(d,a,b,c,M8,11,0x8771f681)
c=HH(c,d,a,b,M11,16,0x6d9d6122)
d=HH(b,c,d,a,M14,23,0xfde5380c)
a=HH(a,b,c,d,M1,4,0xa4beea44)
b=HH(d,a,b,c,M4,11,0x4bdecfa9)
c=HH(c,d,a,b,M7,16,0xf6bb4b60)
d=HH(b,c,d,a,M10,23,0xbebfbc70)
a=HH(a,b,c,d,M13,4,0x289b7ec6)
b=HH(d,a,b,c,M0,11,0xeaa127fa)
c=HH(c,d,a,b,M3,16,0xd4ef3085)
d=HH(b,c,d,a,M6,23,0x04881d05)
a=HH(a,b,c,d,M9,4,0xd9d4d039)
b=HH(d,a,b,c,M12,11,0xe6db99e5)
c=HH(c,d,a,b,M15,16,0x1fa27cf8)
d=HH(b,c,d,a,M2,23,0xc4ac5665)

第四轮
a=II(a,b,c,d,M0,6,0xf4292244)
b=II(d,a,b,c,M7,10,0x432aff97)
c=II(c,d,a,b,M14,15,0xab9423a7)
d=II(b,c,d,a,M5,21,0xfc93a039)
a=II(a,b,c,d,M12,6,0x655b59c3)
b=II(d,a,b,c,M3,10,0x8f0ccc92)
c=II(c,d,a,b,M10,15,0xffeff47d)
d=II(b,c,d,a,M1,21,0x85845dd1)
a=II(a,b,c,d,M8,6,0x6fa87e4f)
b=II(d,a,b,c,M15,10,0xfe2ce6e0)
c=II(c,d,a,b,M6,15,0xa3014314)
d=II(b,c,d,a,M13,21,0x4e0811a1)
a=II(a,b,c,d,M4,6,0xf7537e82)
b=II(d,a,b,c,M11,10,0xbd3af235)
c=II(c,d,a,b,M2,15,0x2ad7d2bb)
d=II(b,c,d,a,M9,21,0xeb86d391)
```
5）每轮循环后，将A，B，C，D分别加上a，b，c，d，然后进入下一循环。
如果上面的过程用JAVA代码来实现的话，代码如下：
```bash
public class MD5 {

    static final String hexs[]={"0","1","2","3","4","5","6","7","8","9","A","B","C","D","E","F"};
    //标准的幻数
    private static final long A=0x67452301L;
    private static final long B=0xefcdab89L;
    private static final long C=0x98badcfeL;
    private static final long D=0x10325476L;


    //下面这些S11-S44实际上是一个4*4的矩阵，在四轮循环运算中用到
    static final int S11 = 7;
    static final int S12 = 12;
    static final int S13 = 17;
    static final int S14 = 22;

    static final int S21 = 5;
    static final int S22 = 9;
    static final int S23 = 14;
    static final int S24 = 20;

    static final int S31 = 4;
    static final int S32 = 11;
    static final int S33 = 16;
    static final int S34 = 23;

    static final int S41 = 6;
    static final int S42 = 10;
    static final int S43 = 15;
    static final int S44 = 21;

    //java不支持无符号的基本数据（unsigned）
    private long [] result={A,B,C,D};//存储hash结果，共4×32=128位，初始化值为（幻数的级联）

    public static void main(String []args){
        MD5 md=new MD5();
        System.out.println("md5(abc)="+md.digest("abc"));
    }

    private String digest(String inputStr){
        byte [] inputBytes=inputStr.getBytes();
        int byteLen=inputBytes.length;//长度（字节）
        int groupCount=0;//完整分组的个数
        groupCount=byteLen/64;//每组512位（64字节）
        long []groups=null;//每个小组(64字节)再细分后的16个小组(4字节)

        //处理每一个完整 分组
        for(int step=0;step<groupCount;step++){
            groups=divGroup(inputBytes,step*64);
            trans(groups);//处理分组，核心算法
        }

        //处理完整分组后的尾巴
        int rest=byteLen%64;//512位分组后的余数
        byte [] tempBytes=new byte[64];
        if(rest<=56){
            for(int i=0;i<rest;i++)
                tempBytes[i]=inputBytes[byteLen-rest+i];
            if(rest<56){
                tempBytes[rest]=(byte)(1<<7);
                for(int i=1;i<56-rest;i++)
                    tempBytes[rest+i]=0;
            }
            long len=(long)(byteLen<<3);
            for(int i=0;i<8;i++){
                tempBytes[56+i]=(byte)(len&0xFFL);
                len=len>>8;
            }
            groups=divGroup(tempBytes,0);
            trans(groups);//处理分组
        }else{
            for(int i=0;i<rest;i++)
                tempBytes[i]=inputBytes[byteLen-rest+i];
            tempBytes[rest]=(byte)(1<<7);
            for(int i=rest+1;i<64;i++)
                tempBytes[i]=0;
            groups=divGroup(tempBytes,0);
            trans(groups);//处理分组

            for(int i=0;i<56;i++)
                tempBytes[i]=0;
            long len=(long)(byteLen<<3);
            for(int i=0;i<8;i++){
                tempBytes[56+i]=(byte)(len&0xFFL);
                len=len>>8;
            }
            groups=divGroup(tempBytes,0);
            trans(groups);//处理分组
        }

        //将Hash值转换成十六进制的字符串
        String resStr="";
        long temp=0;
        for(int i=0;i<4;i++){
            for(int j=0;j<4;j++){
                temp=result[i]&0x0FL;
                String a=hexs[(int)(temp)];
                result[i]=result[i]>>4;
                temp=result[i]&0x0FL;
                resStr+=hexs[(int)(temp)]+a;
                result[i]=result[i]>>4;
            }
        }
        return resStr;
    }

    /**
     * 从inputBytes的index开始取512位，作为新的分组
     * 将每一个512位的分组再细分成16个小组，每个小组64位（8个字节）
     * @param inputBytes
     * @param index
     * @return
     */
    private static long[] divGroup(byte[] inputBytes,int index){
        long [] temp=new long[16];
        for(int i=0;i<16;i++){
            temp[i]=b2iu(inputBytes[4*i+index])|
                (b2iu(inputBytes[4*i+1+index]))<<8|
                (b2iu(inputBytes[4*i+2+index]))<<16|
                (b2iu(inputBytes[4*i+3+index]))<<24;
        }
        return temp;
    }

    /**
     * 这时不存在符号位（符号位存储不再是代表正负），所以需要处理一下
     * @param b
     * @return
     */
    public static long b2iu(byte b){
        return b < 0 ? b & 0x7F + 128 : b;
     }

    /**
     * 主要的操作，四轮循环
     * @param groups[]--每一个分组512位（64字节）
     */
    private void trans(long[] groups) {
        long a = result[0], b = result[1], c = result[2], d = result[3];
        /*第一轮*/
        a = FF(a, b, c, d, groups[0], S11, 0xd76aa478L); /* 1 */
        d = FF(d, a, b, c, groups[1], S12, 0xe8c7b756L); /* 2 */
        c = FF(c, d, a, b, groups[2], S13, 0x242070dbL); /* 3 */
        b = FF(b, c, d, a, groups[3], S14, 0xc1bdceeeL); /* 4 */
        a = FF(a, b, c, d, groups[4], S11, 0xf57c0fafL); /* 5 */
        d = FF(d, a, b, c, groups[5], S12, 0x4787c62aL); /* 6 */
        c = FF(c, d, a, b, groups[6], S13, 0xa8304613L); /* 7 */
        b = FF(b, c, d, a, groups[7], S14, 0xfd469501L); /* 8 */
        a = FF(a, b, c, d, groups[8], S11, 0x698098d8L); /* 9 */
        d = FF(d, a, b, c, groups[9], S12, 0x8b44f7afL); /* 10 */
        c = FF(c, d, a, b, groups[10], S13, 0xffff5bb1L); /* 11 */
        b = FF(b, c, d, a, groups[11], S14, 0x895cd7beL); /* 12 */
        a = FF(a, b, c, d, groups[12], S11, 0x6b901122L); /* 13 */
        d = FF(d, a, b, c, groups[13], S12, 0xfd987193L); /* 14 */
        c = FF(c, d, a, b, groups[14], S13, 0xa679438eL); /* 15 */
        b = FF(b, c, d, a, groups[15], S14, 0x49b40821L); /* 16 */

        /*第二轮*/
        a = GG(a, b, c, d, groups[1], S21, 0xf61e2562L); /* 17 */
        d = GG(d, a, b, c, groups[6], S22, 0xc040b340L); /* 18 */
        c = GG(c, d, a, b, groups[11], S23, 0x265e5a51L); /* 19 */
        b = GG(b, c, d, a, groups[0], S24, 0xe9b6c7aaL); /* 20 */
        a = GG(a, b, c, d, groups[5], S21, 0xd62f105dL); /* 21 */
        d = GG(d, a, b, c, groups[10], S22, 0x2441453L); /* 22 */
        c = GG(c, d, a, b, groups[15], S23, 0xd8a1e681L); /* 23 */
        b = GG(b, c, d, a, groups[4], S24, 0xe7d3fbc8L); /* 24 */
        a = GG(a, b, c, d, groups[9], S21, 0x21e1cde6L); /* 25 */
        d = GG(d, a, b, c, groups[14], S22, 0xc33707d6L); /* 26 */
        c = GG(c, d, a, b, groups[3], S23, 0xf4d50d87L); /* 27 */
        b = GG(b, c, d, a, groups[8], S24, 0x455a14edL); /* 28 */
        a = GG(a, b, c, d, groups[13], S21, 0xa9e3e905L); /* 29 */
        d = GG(d, a, b, c, groups[2], S22, 0xfcefa3f8L); /* 30 */
        c = GG(c, d, a, b, groups[7], S23, 0x676f02d9L); /* 31 */
        b = GG(b, c, d, a, groups[12], S24, 0x8d2a4c8aL); /* 32 */

        /*第三轮*/
        a = HH(a, b, c, d, groups[5], S31, 0xfffa3942L); /* 33 */
        d = HH(d, a, b, c, groups[8], S32, 0x8771f681L); /* 34 */
        c = HH(c, d, a, b, groups[11], S33, 0x6d9d6122L); /* 35 */
        b = HH(b, c, d, a, groups[14], S34, 0xfde5380cL); /* 36 */
        a = HH(a, b, c, d, groups[1], S31, 0xa4beea44L); /* 37 */
        d = HH(d, a, b, c, groups[4], S32, 0x4bdecfa9L); /* 38 */
        c = HH(c, d, a, b, groups[7], S33, 0xf6bb4b60L); /* 39 */
        b = HH(b, c, d, a, groups[10], S34, 0xbebfbc70L); /* 40 */
        a = HH(a, b, c, d, groups[13], S31, 0x289b7ec6L); /* 41 */
        d = HH(d, a, b, c, groups[0], S32, 0xeaa127faL); /* 42 */
        c = HH(c, d, a, b, groups[3], S33, 0xd4ef3085L); /* 43 */
        b = HH(b, c, d, a, groups[6], S34, 0x4881d05L); /* 44 */
        a = HH(a, b, c, d, groups[9], S31, 0xd9d4d039L); /* 45 */
        d = HH(d, a, b, c, groups[12], S32, 0xe6db99e5L); /* 46 */
        c = HH(c, d, a, b, groups[15], S33, 0x1fa27cf8L); /* 47 */
        b = HH(b, c, d, a, groups[2], S34, 0xc4ac5665L); /* 48 */

        /*第四轮*/
        a = II(a, b, c, d, groups[0], S41, 0xf4292244L); /* 49 */
        d = II(d, a, b, c, groups[7], S42, 0x432aff97L); /* 50 */
        c = II(c, d, a, b, groups[14], S43, 0xab9423a7L); /* 51 */
        b = II(b, c, d, a, groups[5], S44, 0xfc93a039L); /* 52 */
        a = II(a, b, c, d, groups[12], S41, 0x655b59c3L); /* 53 */
        d = II(d, a, b, c, groups[3], S42, 0x8f0ccc92L); /* 54 */
        c = II(c, d, a, b, groups[10], S43, 0xffeff47dL); /* 55 */
        b = II(b, c, d, a, groups[1], S44, 0x85845dd1L); /* 56 */
        a = II(a, b, c, d, groups[8], S41, 0x6fa87e4fL); /* 57 */
        d = II(d, a, b, c, groups[15], S42, 0xfe2ce6e0L); /* 58 */
        c = II(c, d, a, b, groups[6], S43, 0xa3014314L); /* 59 */
        b = II(b, c, d, a, groups[13], S44, 0x4e0811a1L); /* 60 */
        a = II(a, b, c, d, groups[4], S41, 0xf7537e82L); /* 61 */
        d = II(d, a, b, c, groups[11], S42, 0xbd3af235L); /* 62 */
        c = II(c, d, a, b, groups[2], S43, 0x2ad7d2bbL); /* 63 */
        b = II(b, c, d, a, groups[9], S44, 0xeb86d391L); /* 64 */

        /*加入到之前计算的结果当中*/
        result[0] += a;
        result[1] += b;
        result[2] += c;
        result[3] += d;
        result[0]=result[0]&0xFFFFFFFFL;
        result[1]=result[1]&0xFFFFFFFFL;
        result[2]=result[2]&0xFFFFFFFFL;
        result[3]=result[3]&0xFFFFFFFFL;
    }

    /**
     * 下面是处理要用到的线性函数
     */
    private static long F(long x, long y, long z) {
        return (x & y) | ((~x) & z);
    }

    private static long G(long x, long y, long z) {
        return (x & z) | (y & (~z));
    }

    private static long H(long x, long y, long z) {
        return x ^ y ^ z;
    }

    private static long I(long x, long y, long z) {
        return y ^ (x | (~z));
    }

    private static long FF(long a, long b, long c, long d, long x, long s,
            long ac) {
        a += (F(b, c, d)&0xFFFFFFFFL) + x + ac;
        a = ((a&0xFFFFFFFFL)<< s) | ((a&0xFFFFFFFFL) >>> (32 - s));
        a += b;
        return (a&0xFFFFFFFFL);
    }

    private static long GG(long a, long b, long c, long d, long x, long s,
            long ac) {
        a += (G(b, c, d)&0xFFFFFFFFL) + x + ac;
        a = ((a&0xFFFFFFFFL) << s) | ((a&0xFFFFFFFFL) >>> (32 - s));
        a += b;
        return (a&0xFFFFFFFFL);
    }

    private static long HH(long a, long b, long c, long d, long x, long s,
            long ac) {
        a += (H(b, c, d)&0xFFFFFFFFL) + x + ac;
        a = ((a&0xFFFFFFFFL) << s) | ((a&0xFFFFFFFFL) >>> (32 - s));
        a += b;
        return (a&0xFFFFFFFFL);
    }

    private static long II(long a, long b, long c, long d, long x, long s,
            long ac) {
        a += (I(b, c, d)&0xFFFFFFFFL) + x + ac;
        a = ((a&0xFFFFFFFFL) << s) | ((a&0xFFFFFFFFL) >>> (32 - s));
        a += b;
        return (a&0xFFFFFFFFL);
    }
}
```
JAVA实现MD5

在java中实现MD5是很简单的，在包java.security有个类MessageDigest。官方文档如下 
MessageDigest 类为应用程序提供信息摘要算法的功能，如 MD5 或 SHA 算法。信息摘要是安全的单向哈希函数，它接收任意大小的数据，输出固定长度的哈希值。

MessageDigest 对象开始被初始化。该对象通过使用 update 方法处理数据。任何时候都可以调用 reset 方法重置摘要。一旦所有需要更新的数据都已经被更新了，应该调用 digest 方法之一完成哈希计算。

对于给定数量的更新数据，digest 方法只能被调用一次。digest 被调用后，MessageDigest 对象被重新设置成其初始状态。

JAVA代码如下：
```bash
import java.security.MessageDigest;

public class MyMD5 {

    static char[] hex = {'0','1','2','3','4','5','6','7','8','9','A','B','C','D','E','F'}; 
    public static void main(String[] args) {
        try{
            MessageDigest md5 = MessageDigest.getInstance("MD5");//申明使用MD5算法
            md5.update("a".getBytes());//
            System.out.println("md5(a)="+byte2str(md5.digest()));
            md5.update("a".getBytes());
            md5.update("bc".getBytes());
            System.out.println("md5(abc)="+byte2str(md5.digest()));
        }catch(Exception e){
            e.printStackTrace();
        }
    }

    /**
     * 将字节数组转换成十六进制字符串
     * @param bytes
     * @return
     */
    private static String byte2str(byte []bytes){
        int len = bytes.length;   
        StringBuffer result = new StringBuffer();    
        for (int i = 0; i < len; i++) {   
            byte byte0 = bytes[i];   
            result.append(hex[byte0 >>> 4 & 0xf]);   
            result.append(hex[byte0 & 0xf]);   
        }
        return result.toString();
    }
} 
```

#技术笔记/Subjects