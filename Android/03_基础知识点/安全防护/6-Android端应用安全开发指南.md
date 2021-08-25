# 1. 6-Android端应用安全开发指南


[原文链接：《Android端应用安全开发指南》](https://blog.csdn.net/carson2440/article/details/111867469)

## 1.1. 数据存储

 标题|描述
---|---
概述 |移动应用经常需要在某些场景下（比如用户登录）处理和用户或业务相关的敏感数据，有时候为满足某些业务需求，需要把这些敏感数据存储在本地，如果不对这些数据进行适当处理，就有可能存在敏感信息泄漏的风险。
安全准则 | A.      敏感数据总是优先考虑存储在内部空间。 <br> B.      敏感数据无论是存储在内部还是外部空间均应经过加密后再存储，应避免直接明文存储。 <br> C.      避免将敏感数据存储在全局可访问的缓存里（如log、剪切板等）。 <br> D.      敏感数据避免硬编码在代码里面，常见的有用户账户口令和加密密钥等。
详细描述 | A.      可以使用JDK提供的javax.crypto包进行加/解密，注意加密算法的选择以及密钥复杂度策略。<br> B.      使用System.out.print系列以及android.util.Log类的方法（比如Log.d()）会将日志存储在系统缓冲区，任意应用都可以通过logcat指令查看缓存里面的敏感信息（比如密码和sessionID等），因此Release版本应移除这些主要用于debug的方法。
备注 | A.      常见的敏感数据有用户口令、用户个人信息和sessionID等等，但也有一些是和业务强相关的，当不太容易判断时，可以和安全工程师一起确认。 <br> B.      Android使用沙箱技术对不同应用之间的内部存储空间进行了隔离，但考虑到应用自身的漏洞（比如SQL注入）和ROOT的场景，对内部存储数据进行加密还是非常必要的。


安全的数据存储需要使用安全的加/解密方案：

### 1.1.1. AES128对称加密方案：

```java

public class AES128Enc {

    public static String encrypt(byte[] rawKey, String cleartext) throws Exception {
        SecretKeySpec skeySpec = new SecretKeySpec(rawKey, "AES");
        Cipher cipher = Cipher.getInstance("AES");
        cipher.init(Cipher.ENCRYPT_MODE, skeySpec);
        byte[] encrypted = cipher.doFinal(cleartext.getBytes());
        return toHex(encrypted);
    }

    public static String decrypt(byte[] rawKey, String encrypted) throws Exception {
        byte[] enc = toByte(encrypted);
        SecretKeySpec skeySpec = new SecretKeySpec(rawKey, "AES");
        Cipher cipher = Cipher.getInstance("AES");
        cipher.init(Cipher.DECRYPT_MODE, skeySpec);
        byte[] decrypted = cipher.doFinal(enc);
        return new String(decrypted);
    }

    /* 产生随机的128bit AES密钥 */
    public static byte[] getRawKey(byte[] seed) throws Exception {
        KeyGenerator kgen = KeyGenerator.getInstance("AES");
        SecureRandom sr = SecureRandom.getInstance("SHA1PRNG");
        sr.setSeed(seed);
        kgen.init(128, sr);
        SecretKey skey = kgen.generateKey();
        byte[] raw = skey.getEncoded();
        return raw;
    }

    public static byte[] toByte(String hexString) {
        int len = hexString.length() / 2;
        byte[] result = new byte[len];
        for (int i = 0; i < len; i++)
            result[i] = Integer.valueOf(hexString.substring(2 * i, 2 * i + 2), 16).byteValue();
        return result;
    }

    public static String toHex(byte[] buf) {
        if (buf == null)
            return "";
        StringBuffer result = new StringBuffer(2 * buf.length);
        for (int i = 0; i < buf.length; i++) {
            appendHex(result, buf[i]);
        }
        return result.toString();
    }

    private final static String HEX = "0123456789ABCDEF";

    private static void appendHex(StringBuffer sb, byte b) {
        sb.append(HEX.charAt((b >> 4) & 0x0f)).append(HEX.charAt(b & 0x0f));
    }
}
```

### 1.1.2. SHA256哈希算法：

```java
public class Sha256Hash {

    private static byte [] getHash(String strForHash) {
        MessageDigest digest = null ;
        try {
            digest = MessageDigest. getInstance( "SHA-256");
        } catch (NoSuchAlgorithmException e1) {
            e1.printStackTrace();
        }

        digest.reset();
        return digest.digest(strForHash.getBytes());
    }

    public static String bin2hex(String strForHash) {
        byte [] data = getHash(strForHash);
        return String.format( "%0" + (data.length * 2) + "X", new BigInteger(1, data));
    }
}
```

## 1.2. 数据传输

-|-
---|---
概述 | 移动应用很多时候并非孤立存在，在多数场景下存在前、后台以及第三方服务之间进行数据交互，因此，在网络中传输敏感数据在所难免，如果不使用正确安全的传输方式，有可能存在敏感信息泄漏的风险。
安全准则| A.      使用SSL协议（或基于SSL的协议）传输敏感数据。<br> B.      验证服务器证书的有效性，防止中间人攻击，当证书有效性验证失败时应强行断开连接，而不是提示用户。<br> C.      尽可能地减少使用短信发送敏感信息。<br> D.      增强项：应用程序可以对敏感数据进行一次独立加密后再通过SSL进行传输。
详细描述| A.      可以使用“证书锁定”（certificate pinning）的方式验证证书的有效性，即在代码中精确的验证当前服务器是否持有某张指定的证书。X509TrustManager接口是实现证书锁定一种方法，它通过在SSL回调函数中读取服务器证书密钥并和程序预埋的证书密钥进行对比，如果两者不一致则强行断开链接
备注 | A.      任何申请了READ_SMS permission的应用都可以读取短信内容，故应尽量减少使用短信发送敏感信息。<br> B.      应用自身进行一次独立加密的好处是，当出现SSL协议相关漏洞（比如影响巨大的openssl heart bleed漏洞）时能够有效减少损失。<br>  C.      “证书锁定”的缺点是：由于证书是预埋在应用程序内的，是当服务器更新证书时，应用程序也要同步更新。


### 1.2.1. 使用“证书锁定”验证证书有效性方案：

```java

/**
 * 使用https需要定义实现X509TrustManager接口的类，并重写里面的方法，这些方法会在建立SSL链接的过程中被自动调用
 */
public final class PubKeyManager implements X509TrustManager {

    /* 此处存放服务器证书密钥 */
    private static String PUB_KEY =

            "30820122300d06092a864886f70d01010105000382010f"
                    + "003082010a0282010100973a0569971991dc9446f309ec0af7646377dca80eb1"
                    + "e1357f5fb5c69d046d03d23f6cf743d155e7b44d834cf71d6500a8b1e38110b5"
                    + "35ad07212a50e1f3ab497acfde74e065018d64136d14d63d04604124aacd74ea"
                    + "037a5f8d6894aadd58f7774655761c9fba22426935794f6740fa89b6f7e902b8"
                    + "e02c0b842116272701c9edaef62c977b0df488847c2e0a75028865be34c98903"
                    + "be11a2cf4495acec5c958ddf64619808e641bac64ab432e4638e969f4214d7bc"
                    + "88db81feaef4d5d329f93f2e79535b2d00c01145823b664ca7ab8db9de858d29"
                    + "161c6cce86decb6a4f66cf86afb79e182a5b5a2bb6d8795af749f7af356aef2e"
                    + "64b6ae785ff88d456f970203010001";

    /**
     * https协商中获取的服务器证书链(chain)将自动传入该方法
     */
    public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
        if (chain == null) {
            throw new IllegalArgumentException("checkServerTrusted: X509Certificate array is null");
        }

        if (!(chain.length > 0)) {
            throw new IllegalArgumentException("checkServerTrusted: X509Certificate is empty");
        }

        /* authType为建立SSL链接时使用的非对称加密算法，RSA为业界主流算法 */
        if (!(null != authType && authType.equalsIgnoreCase("RSA"))) {
            throw new CertificateException("checkServerTrusted: AuthType is not RSA");
        }

        /* 执行SSL/TLS常规性检查 */
        try {
            TrustManagerFactory tmf = TrustManagerFactory.getInstance("X509");
            tmf.init((KeyStore) null);
            for (TrustManager trustManager : tmf.getTrustManagers()) {
                ((X509TrustManager) trustManager).checkServerTrusted(chain, authType);
            }
        } catch (Exception e) {
            throw new CertificateException(e);
        }

        /* 将编码后的密钥转换成16进制形式的大整数 */
        RSAPublicKey pubkey = (RSAPublicKey) chain[0].getPublicKey();
        String encoded = new BigInteger(1, pubkey.getEncoded()).toString(16);

        /* 比较预埋证书密钥和服务器证书密钥是否一致 */
        final boolean expected = PUB_KEY.equalsIgnoreCase(encoded);

        if (!expected) {
            throw new CertificateException("checkServerTrusted: Expected public key: "
                    + PUB_KEY + ", got public key:" + encoded);
        }
    }
}
```

## 1.3. 数据验证

-|-
---|---
概述|移动应用往往通过数据的发送、接收和处理来完成一系列功能，通常情况下，处理的数据绝大部分都来源于外部（比如网络、内部或外部存储和用户输入等），对这些数据处理不当会导致各种各样的漏洞和风险，比代码执行和信息泄漏等等。
安全准则| A.      一般性原则：对所有外部数据进行数据验证，数据验证建议采用白名单的方式，即只允许指定的字符通过，其它字符一律过滤，同时验证数据的长度和类型等。 <br> B.      使用参数化查询语句防止SQL注入。 <br> C.      使用WebViews时，将JavaScript和插件支持特性关掉（此为默认值），如果一定要打开该特性，需要对输出的内容进行html转义。 <br> D.      设置禁止WebViews对文件系统进行存取，以防止文件包含漏洞。 <br> E.       在activity内使用Intent Filter对action和data进行过滤。
详细描述| A.      进行白名单数据验证的常用方法是使用正则表达式。 <br> B.      预编译查询可以有效防止应用把数据当作代码来执行， <br> C.      Html输出转义规则如下（详情可参考附录7）：`& - &amp;`、 `< - &lt;`、 `> - &gt;` 、`“ - &quot;`、`‘ - &#x27;` 、`/ - &#x2F;` <br> D.      webview.getSettings().setAllowFileAccess(false)可用于关闭WebViews对文件系统进行存取的能力。
备注 | 这里的外部数据包含但不限于以下来源的数据：网络、存储（特别注意外部存储）、文件、数据库、IPC和用户输入等。


### 1.3.1. SQLite防止SQL注入漏洞方案

#### 1.3.1.1. 使用自带的防SQL注入的方法

使用 SQLiteDatabase 对象自带的防 SQL 注入的方法，比如 query(), insert(), update 和 delete()：

```java
DatabaseHelper dbhelper = new DatabaseHelper(SqliteActivity.this,"sqliteDB");
SQLiteDatabase db = dbhelper.getReadableDatabase();

/*查询操作，userInputID和userInputName是用户可控制的输入数据 */
Cursor cur = db.query("user", new String[]{"id","name"}, "id=? and name=?", new String[]{userInputID,userInputName}, null, null, null);

/* 插入记录操作*/
ContentValues val = new ContentValues();
val.put("id", userInputID);
val.put("name", userInputName);
db.insert("user", null, val);

/*更新记录操作*/
ContentValues val = new ContentValues();
val.put("id", userInputName);
db.update("user", val, "id=?", new String[]{userInputID });

/*删除记录操作*/
db.delete("user", "id=? and name=?", new String[]{userInputID , userInputName });
```

#### 1.3.1.2. 正确地使用rawQuery()方法：

正确地使用 SQLiteDatabase 对象的 rawQuery() 方法（仅以查询为例）

```java
DatabaseHelper dbhelper = new DatabaseHelper(SqliteActivity.this,"sqliteDB");
SQLiteDatabase db = dbhelper.getReadableDatabase();

/* userInputID和userInputName是用户可控制的输入数据*/
String[] selectionArgs = new String[]{userInputID , userInputName };
String sql = "select * from user where id=? and name=?";//正确！此处绑定变量
Cursor curALL = db.rawQuery(sql, selectionArgs);
```


#### 1.3.1.3. 以下为错误案例！仅供参考：

下面是动态拼接的方式，容易产生 sql 注入。

```java
DatabaseHelper dbhelper = new DatabaseHelper(SqliteActivity.this,"sqliteDB");
SQLiteDatabase db = dbhelper.getReadableDatabase();

/*案例1：错误地使用rawQuery()，未绑定参数*/
String sql = "select * from user where id='" + userInputID +"'";//错误！动态拼接，未绑定变量
Cursor curALL = db.rawQuery(sql, null);

/*案例2：使用execSQL()方法*/
String sql = "INSERT INTO user values('"+userInputID +"','"+userInputName +"')";//错误同上
db.execSQL(sql);
```

### 1.3.2. HTML转义输出样例

#### 1.3.2.1. 使用Android自带的html转义函数：

```java
String str = TextUtils.htmlEncode(str);
```

#### 1.3.2.2. 自定义的html转义函数：

```java
public static String htmlEncode(String str) {
    char c;
    StringBuilder sb = new StringBuilder();
    int len = str.length();
    for (int i = 0; i < len; i++) {
        c = str.charAt(i);
        switch (c) {
            case '<':
                sb.append("&lt;");
                break;
            case '>':
                sb.append("&gt;");
                break;
            case '"':
                sb.append("&quot;");
                break;
            case '\'':
                sb.append("&#x27;");
                break;
            case '/':
                sb.append("&#x2F;");
                break;
            case '&':
                sb.append("&amp;");
                break;
            default:
                sb.append(c);
        }
    }
    return sb.toString();
}
```

## 1.4. 应用加固

-|-
---|---
概述|通常一个应用发布后可能会面临以下风险：A．  应用被别人解包植入广告或恶意代码再重打包发布。  <br>  B．  应用被暴力破解。 <br> C．  应用的核心关键代码逻辑被逆向。 <br> 因此，有必要在技术层面采取一定的缓解措施。
安全准则| A.      对Java代码进行混淆，对抗反编译。 <br>  B.      对Native代码进行加壳，对抗反汇编。  <br>  C.      应用程序加入动态反调试方法。  <br> D.      应用程序加入防二次打包的方法。
详细描述 | A.        可以使用proguard对Java源码进行混淆。  <br>  B.        可以使用UPX进行加壳保护，请参考《Android SO加壳指南v1.0》。 <br>  C.        预先在AndroidManifest.xml文件插入android:debuggable=”false”，在程序中判断该标志位是否被篡改，此外，android SDK也提供了相关方法来检测调试器是否已连接，可在程序中随机插入检测，关键代码如下  <br> D.        应用程序被篡改并重打包时必定要重新签名，签名值和原开发者的必定不一样（不考虑证书丢失的情况），另外，重新编译程序classes.dex文件肯定会变，因此可在程序运行时对比签名或CRC值的方法对抗重打包（参考附录11）。
备注 | 以上方案参考《Android软件安全与逆向分析》一书，但只能提供比较基本的保护措施，如果要进一步提高攻击者的攻击门槛，建议使用第三方的定制方案。

### 1.4.1. Android动态反调试方案：

#### 1.4.1.1. 检测 AndroidManifest.xml 的调试标志位是否被篡改：

```java
if((getApplicationInfo().flags &= ApplicationInfo.FLAG_DEBUGGABLE) != 0){
    System.exit(1);//使程序强制退出
}
```

注意：使用此方法时必须预先在 AndroidManifest.xml 设置 android:debuggable="false"，攻击者要尝试调试应用时很有可能去修改该参数，因而此手法可用于做动态反调试检测。

#### 1.4.1.2. 检测应用程序是否连接调试器：

```java
if(android.os.Debug.isDebuggerConnected()){
    System.exit(1);
}
```

### 1.4.2. 防止二次打包方案：

#### 1.4.2.1. 签名检查

```java
public class getSign {
    public static int getSignature(PackageManager pm, String packageName) {
        PackageInfo pi = null;
        int sig = 0;
        Signature[] s = null;

        try {
            pi = pm.getPackageInfo(packageName, PackageManager.GET_SIGNATURES);
            s = pi.signatures;
            //s[0]是签名证书的公钥，此处获取hashcode方便对比
            sig = s[0].hashCode();
        } catch (Exception e) {
            handleException();
        }
        return sig;
    }
}
```

主程序代码参考：

```java
pm = this.getPackageManager();
int s = getSign.getSignature(pm, "com.hik.getsinature");

if(s != ORIGNAL_SGIN_HASHCODE){
    //对比当前和预埋签名的hashcode是否一致, 不一致则强制程序退出
    System.exit(1);
}
```

### 1.4.3. CRC校验保护

CRC 即循环冗余校验。

```java
private boolean checkcrc() {
    boolean checkResult = false;
    //获取字符资源中预埋的crc值
    long crc = Long.parseLong(getString(R.string.crc));
    ZipFile zf;
    try {
        //获取apk安装路径
        String path = getApplicationContext().getPackageCodePath();
        //将apk封装成zip对象
        zf = new ZipFile(path);
        //获取apk中的classes.dex
        ZipEntry ze = zf.getEntry("classes.dex");
        //计算当前应用classes.dex的crc值
        long CurrentCRC = ze.getCrc();
        //crc值对比
        if (CurrentCRC != crc) {
            checkResult = true;
        }
    } catch (IOException e) {
        handleError();
        checkResult = false;
    }
    return checkResult;
}
```

## 1.5. 其他


### 1.5.1. 认证和授权

-|-
---|---
概述 | 认证是用来证明用户身份合法性的过程，授权是用来证明用户可以合法地做哪些事的过程，这两个过程一般是在服务器端执行的，但也有的APP出于性能提升或用户体验等原因，将其做在客户端完成，由此导致客户端绕过等问题。
安全准则 | A.      在客户端做认证和授权是很难保证安全的，所以应该把认证和授权做在服务器端。如果确实有特殊的需求，可以和安全工程师进行沟通做单一case分析。  <br> B.      尽可能避免在设备上存储用户名和密码，可以使用登录认证后获得的token进行鉴权（同时注意控制token的有效期）。
详细描述 | Item B的典型应用场景是实现自动登录，用户登录认证后在本地存储用户的认证 token，用户退出程序时认证token不会被删除，再次打开程序时可直接携带认证token获取数据，同时为了保证了足够的安全性，可以根据当前终端硬件信息产生独立密钥，然后对token进行加密。
备注 | 在本地存储对称加密后的用户口令密文，在登录时还原出明文也是实现自动登录的一种可选方案，但其安全性偏低，故在当今已并非一种主流的做法。

### 1.5.2. 加密解密

-|-
---|---
概述 | 开发人员在移动应用中通常会对敏感数据进行加密处理，但是使用不当有可能让其保护强度削弱，甚至大打折扣，因此，正确的选择加解密算法显得非常重要。
安全准则|A.      在不需要还原用户明文密码的场景使用哈希算法，在需要还原用户明文密码的场景下使用对称加密算法，并且始终优先选择使用哈希算法。<br> B.      使用目前主流的安全加密算法，比如哈希算法可以使用sha256，对称加密算法可以使用AES128/256，不使用过时的不安全算法，比如RC4、RC5、MD5和SHA1。 <br> C.      不使用自定义的加密算法。 <br> D.      密文和密钥不要放在同一文件或同一目录内，应分开存放。 <br> E.       密钥不可硬编码在代码里面。
详细描述|使用AES128加密算法时，密钥应同时满足长度（128bit）和复杂度的要求，建议使用安全的随机数发生器产生安全的密钥，Sha256哈希算法和AES128对称加密算法的使用方法。
备注 | 注意：在某些不牵涉敏感数据的场景下，不安全的哈希算法仍然是可用的，比如用于校验文件或数据的完整性。

### 1.5.3. 安全配置和部署

-|-
---|---
概述|安全开发可以大大降低移动应用的安全风险，同样地，安全的配置和部署可以让风险降到最低。
安全准则|A.      确保使用的第三方组件是从官方下载的，并且是最新版本的。  <br>  B.      为应用程序申请最小的Permissions，如果用不上就不要申请。  <br> C.      应用和补丁在发布前建议进行病毒和恶意代码检测。 <br> D.      为敏感数据输入界面提供防截屏措施，对抗木马。
详细描述 | 在Activity的onCreate()方法的初始化部分加入以下代码可用于防截屏： `getWindow().addFlags(WindowManager.LayoutParams. FLAG_SECURE);`
备注| 非官方的库可能被植入恶意代码，而使用最新版本的库（非beta版）可以降低漏洞存在的可能性。



### 1.5.4. MISC（其它）

-|-
---|---
概述|本项作为其它移动客户端项的进一步补充。
安全准则 | A.      关键性业务逻辑代码应放在native代码实现，除此以外，尽量使用android SDK做开发，减少对native代码的依赖（native代码一般采用C/C++编写，容易出现缓冲区溢出等漏洞）。 <br> B.      尽量少用动态加载的方式执行代码（比如使用DexClassLoader），如果需要从外部存储动态加载可执行文件或类文件（比如使用DexClassLoader），应经过严格的文件完整性验证。
详细描述|文件完整性校验方案：A.        对待动态加载的可执行文件或类文件进行哈希计算，并与存储在服务器端的正确的哈希值进行对比，如果一致则表示文件未被篡改过，否则拒绝执行加载。
备注|存放在外部存储的文件是可公共访问的，可能会被其它恶意进程篡改，动态加载这些文件就有可能导致恶意代码执行。



### 1.5.5. 服务器端的安全

-|-
---|---
概述 | 大部分移动应用并非一个独立的单机程序，需要在服务器的支撑下完成一系列的功能，而服务器一般是以web API、web service等方式为移动客户端提供服务，因此，也同样存在web应用的安全问题，而这部分问题牵涉面广而复杂，无法在单独的item内进行描述，可参考《web安全开发指南》
