---
title: Study Notes of Algorithm
description: 算法学习笔记
hidden: true
date: 2019-01-05 17:01:29
categories:
 - Algorithm
tags:
 - Algorithm
---

<img src="https://www.kaleandcaramel.com/wp-content/uploads/2018/06/honest-insta-word-cloud.png" width="100%"/>

<!-- more -->

## 排名算法

> [基于用户投票的排名算法（一）：Delicious和Hacker News](http://www.ruanyifeng.com/blog/2012/02/ranking_algorithm_hacker_news.html)
>
> [基于用户投票的排名算法（二）：Reddit](http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_reddit.html)
>
> [基于用户投票的排名算法（三）：Stack Overflow](http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_stack_overflow.html)
>
> [基于用户投票的排名算法（四）：牛顿冷却定律](http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_newton_s_law_of_cooling.html)
>
> [基于用户投票的排名算法（五）：威尔逊区间](http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_wilson_score_interval.html)
>
> [基于用户投票的排名算法（六）：贝叶斯平均](http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_bayesian_average.html)

### Hacker News

![](https://i.loli.net/2018/12/12/5c10c01fd8f48.png)

P：表示帖子的得票数，减去1是为了忽略发帖人的投票。

T：表示距离发帖的时间（单位为小时），加上2是为了防止最新的帖子导致分母过小（之所以选择2，可能是因为从原始文章出现在其他网站，到转贴至Hacker News，平均需要两个小时）。

G：表示"重力因子"（gravityth power），即将帖子排名往下拉的力量，默认值为1.8

结论：

1. 得票越多，排名越高。
2. 发帖时间越新，排名越高。或者说，帖子的排名会随着时间不断下降。（经过24小时之后，所有帖子的得分基本上都小于1）
3. **重力因子G**数值大小决定了排名随时间下降的速度。G值越大，排名下降得越快。

缺点：用户只能投赞成票，不允许用户投反对票

### Reddit

![](https://i.loli.net/2018/12/12/5c10c8a584474.png)

t：帖子的新旧程度，`t = 发贴时间 - 2005年12月8日7:46:43`，单位为秒，用unix时间戳计算。

x：赞成票与反对票的差，`x = 赞成票 - 反对票`

y：投票方向，y是一个符号变量，表示对文章的总体看法。如果赞成票居多，y就是+1；如果反对票居多，y就是-1；如果赞成票和反对票相等，y就是0。

![](https://i.loli.net/2018/12/12/5c10c965abb44.png)

z：帖子的受肯定（否定）的程度，z表示赞成票与反对票之间差额的绝对值。如果对某个帖子的评价，越是一边倒，z就越大。如果赞成票等于反对票，z就等于1。

![](https://i.loli.net/2018/12/12/5c10c9b792fa4.png)

总结：

1. 赞成票与反对票的差额z越大，得分越高。
2. 差额越大，分数越不受投票影响。
3. 帖子越新，分数越高。分数会随时间下降

缺点：不利于赞成票和反对票都比较高，但两者非常接近的情况。假定同一时间有两个帖子发表，文章A有1张赞成票（发帖人投的）、0张反对票，文章B有1000张赞成票、1000张反对票，那么A的排名会高于B。

**Reddit的排名，基本上由发帖时间决定，超级受欢迎的文章会排在最前面，一般性受欢迎的文章、有争议的文章都不会很靠前。**这决定了Reddit是一个符合大众口味的社区，不是一个很激进、可以展示少数派想法的地方。

### Stack Overflow

![](https://i.loli.net/2018/12/12/5c10d5fa90af2.png)

**Qviews：问题的浏览次数**

问题的浏览次数越多，就代表越受关注，得分也就越高。取对数的用意是当访问量越来越大，它对得分的影响将不断变小。

**Qscore：问题得分**

Qscore = 赞成票 - 反对票

**Qanswers：回答的数量**

回答的数量代表有多少人参与这个问题。这个值越大，得分将成倍放大。这里需要注意的是，如果无人回答，Qscore再高也没用，意味着再好的问题，也必须有人回答，否则进不了热点问题排行榜。

**Ascores：回答得分**

"回答"比"问题"更有意义。这一项的得分越高，就代表回答的质量越高。

简单加总的设计还不够全面。**首先**，一个正确的回答胜过一百个无用的回答，简单加总会导致，1个得分为100的回答与100个得分为1的回答，总得分相同。**其次**，由于得分会出现负值，因此那些特别差的回答，会拉低正确回答的得分。

**Qage：距离问题发表的时间**

随着时间流逝，这个值都会越变越大，导致分母增大，因此总得分会越来越小。

**Qupdated：距离最后一个回答的时间**

随着时间流逝，这个值都会越变越大，导致分母增大，因此总得分会越来越小。

**总结**

Stack Overflow热点问题的排名，与**参与度**（Qviews和Qanswers）和**质量**（Qscore和Ascores）成正比，与**时间**（Qage和Qupdated）成反比。

## 分布式ID生成算法

### Twitter SnowFlake

> [snowflake](https://github.com/twitter-archive/snowflake)
>
> [Twitter-Snowflake，64位自增ID算法详解](https://www.lanindex.com/twitter-snowflake%EF%BC%8C64%E4%BD%8D%E8%87%AA%E5%A2%9Eid%E7%AE%97%E6%B3%95%E8%AF%A6%E8%A7%A3/)
>
> [Twitter Snowflake算法详解](https://blog.csdn.net/yangding_/article/details/52768906)

把**时间戳**，**工作机器id**，**序列号**组合在一起。

![](https://www.lanindex.com/wp-content/uploads/2015/04/snowflake-64bit.jpg)

​	默认情况下 41bit 的时间戳可以支持该算法使用到 2082 年，10bit 的工作机器 id 可以支持1024台机器，序列号持 1 毫秒产生 4096 个自增序列 id。SnowFlake 的优点是，整体上按照时间自增排序，并且整个分布式系统内不会产生 ID 碰撞(由数据中心 ID 和机器 ID 作区分)，并且效率较高，经测试，SnowFlake 每秒能够产生 26 万 ID 左右。

```java
/**
 * Twitter_Snowflake
 * SnowFlake的结构如下(每部分用-分开):
 * 0 - 0000000000 0000000000 0000000000 0000000000 0 - 00000 - 00000 - 000000000000
 * 1位标识，由于long基本类型在Java中是带符号的，最高位是符号位，正数是0，负数是1，id一般是正数，所以最高位是0
 * 41位时间截(毫秒级)，注意，41位时间截不是存储当前时间的时间截，而是存储时间截的差值（当前时间截 - 开始时间截)得到的值）
 * 这里的的开始时间截，一般是我们的id生成器开始使用的时间，由我们程序来指定的（如下下面程序IdWorker类的startTime属性）。
 * 41位的时间截，可以使用69年： (1L << 41) / (1000L * 60 * 60 * 24 * 365) = 69
 * 10位的数据机器位，可以部署1024个节点，包括5位datacenterId和5位workerId
 * 12位序列，毫秒内的计数，12位的计数顺序号支持每个节点每毫秒(同一机器，同一时间截)产生4096个ID序号
 * 加起来刚好64位，为一个Long型。
 * SnowFlake的优点是，整体上按照时间自增排序，并且整个分布式系统内不会产生ID碰撞(由数据中心ID和机器ID作区分)，并且效率较高，经测试，SnowFlake每秒能够产生26万ID左右。
 */
public class SnowflakeIdWorker {

    // ==============================Fields===========================================
    /** 开始时间截 (2015-01-01) */
    private final long twepoch = 1420041600000L;

    /** 机器id所占的位数 */
    private final long workerIdBits = 5L;

    /** 数据标识id所占的位数 */
    private final long datacenterIdBits = 5L;

    /** 支持的最大机器id，结果是31 (这个移位算法可以很快的计算出几位二进制数所能表示的最大十进制数) */
    private final long maxWorkerId = -1L ^ (-1L << workerIdBits);

    /** 支持的最大数据标识id，结果是31 */
    private final long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);

    /** 序列在id中占的位数 */
    private final long sequenceBits = 12L;

    /** 机器ID向左移12位 */
    private final long workerIdShift = sequenceBits;

    /** 数据标识id向左移17位(12+5) */
    private final long datacenterIdShift = sequenceBits + workerIdBits;

    /** 时间截向左移22位(5+5+12) */
    private final long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;

    /** 生成序列的掩码，这里为4095 (0b111111111111=0xfff=4095) */
    private final long sequenceMask = -1L ^ (-1L << sequenceBits);

    /** 工作机器ID(0~31) */
    private long workerId;

    /** 数据中心ID(0~31) */
    private long datacenterId;

    /** 毫秒内序列(0~4095) */
    private long sequence = 0L;

    /** 上次生成ID的时间截 */
    private long lastTimestamp = -1L;

    //==============================Constructors=====================================
    /**
     * 构造函数
     * @param workerId 工作ID (0~31)
     * @param datacenterId 数据中心ID (0~31)
     */
    public SnowflakeIdWorker(long workerId, long datacenterId) {
        if (workerId > maxWorkerId || workerId < 0) {
            throw new IllegalArgumentException(String.format("worker Id can't be greater than %d or less than 0", maxWorkerId));
        }
        if (datacenterId > maxDatacenterId || datacenterId < 0) {
            throw new IllegalArgumentException(String.format("datacenter Id can't be greater than %d or less than 0", maxDatacenterId));
        }
        this.workerId = workerId;
        this.datacenterId = datacenterId;
    }

    // ==============================Methods==========================================
    /**
     * 获得下一个ID (该方法是线程安全的)
     * @return SnowflakeId
     */
    public synchronized long nextId() {
        long timestamp = timeGen();

        //如果当前时间小于上一次ID生成的时间戳，说明系统时钟回退过这个时候应当抛出异常
        if (timestamp < lastTimestamp) {
            throw new RuntimeException(
                    String.format("Clock moved backwards.  Refusing to generate id for %d milliseconds", lastTimestamp - timestamp));
        }

        //如果是同一时间生成的，则进行毫秒内序列
        if (lastTimestamp == timestamp) {
            sequence = (sequence + 1) & sequenceMask;
            //毫秒内序列溢出
            if (sequence == 0) {
                //阻塞到下一个毫秒,获得新的时间戳
                timestamp = tilNextMillis(lastTimestamp);
            }
        }
        //时间戳改变，毫秒内序列重置
        else {
            sequence = 0L;
        }

        //上次生成ID的时间截
        lastTimestamp = timestamp;

        //移位并通过或运算拼到一起组成64位的ID
        return ((timestamp - twepoch) << timestampLeftShift) //
                | (datacenterId << datacenterIdShift) //
                | (workerId << workerIdShift) //
                | sequence;
    }

    /**
     * 阻塞到下一个毫秒，直到获得新的时间戳
     * @param lastTimestamp 上次生成ID的时间截
     * @return 当前时间戳
     */
    protected long tilNextMillis(long lastTimestamp) {
        long timestamp = timeGen();
        while (timestamp <= lastTimestamp) {
            timestamp = timeGen();
        }
        return timestamp;
    }

    /**
     * 返回以毫秒为单位的当前时间
     * @return 当前时间(毫秒)
     */
    protected long timeGen() {
        return System.currentTimeMillis();
    }

    //==============================Test=============================================
    /** 测试 */
    public static void main(String[] args) {
        SnowflakeIdWorker idWorker = new SnowflakeIdWorker(0, 0);
        for (int i = 0; i < 1000; i++) {
            long id = idWorker.nextId();
            System.out.println(Long.toBinaryString(id));
            System.out.println(id);
        }
    }
}
```

#### 利用位运算计算指定位数的最大值

```java
-1L ^ (-1L << len);
```

eg:  5 位二进制最大值为 31

```
-1L =
1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111

-1L << 5 =
1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1110 0000

-1L ^ (-1L << len) =
  1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111
^ 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1111 1110 0000
= 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0001 1111
= 31(D)
```

## 签名算法

### 排序加密签名算法

> [App开放接口api安全性—Token签名sign的设计与实现](https://blog.csdn.net/fengshizty/article/details/48754609)

1. 按照参数名ASCII码从小到大排序（字典序）
2. 使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串。
3. 加密算法对字符串进行加密，得到签名。

```java
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.Arrays;
import java.util.Map;
import java.util.Set;

/**
 * @description：
 * @author： libo
 * @date： 2019/1/14:15:58
 */
public class SignUtil {
    
    public static String createSign(Map<String, String> params, boolean encode) throws UnsupportedEncodingException {
        Set<String> keysSet = params.keySet();
        Object[] keys = keysSet.toArray();
        Arrays.sort(keys);
        StringBuffer sb = new StringBuffer();
        boolean first = true;
        for (Object key : keys) {
            if (first) {
                first = false;
            } else {
                sb.append("&");
            }
            sb.append(key).append("=");
            Object value = params.get(key);
            String valueString = "";
            if (null != value) {
                valueString = String.valueOf(value);
            }
            if (encode) {
                sb.append(URLEncoder.encode(valueString, "UTF-8"));
            } else {
                sb.append(valueString);
            }
        }

        return md5(sb.toString()).toUpperCase();
    }

    public static String md5(String value) {
        MessageDigest md = null;
        try {
            byte[] data = value.getBytes("utf-8");
            md = MessageDigest.getInstance("MD5");
            byte[] digestData = md.digest(data);
            return toHex(digestData);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            return null;
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
    }

    public static String toHex(byte input[]) {
        if (input == null) {
            return null;
        }
        StringBuffer output = new StringBuffer(input.length * 2);
        for (int i = 0; i < input.length; i++) {
            int current = input[i] & 0xff;
            if (current < 16)
                output.append("0");
            output.append(Integer.toString(current, 16));
        }

        return output.toString();
    }
}
```

### RC4算法

> [【密码学】RC4加解密原理及其Java和C实现算法](https://blog.csdn.net/White_Idiot/article/details/65937877)
>
> [RC4加密解密算法原理与完整源代码实例演示](https://www.cnblogs.com/zwios/p/4196836.html)
>
> [RC4加密算法](https://www.cnblogs.com/zibility/p/5404478.html)

**加密原理**

![](http://upload-images.jianshu.io/upload_images/2482101-2c5eb05fcc70d1fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

![](https://images2015.cnblogs.com/blog/889809/201604/889809-20160418145446757-1328061961.png)

```java
public class RC4 {

    public static void main(String[] args) {
        RC4 rc4 = new RC4();

        String plaintext = "helloworld";
        String key = "key";

        String ciphertext = rc4.encrypt(plaintext, key);
        String decryptText = rc4.encrypt(ciphertext, key);

        System.out.println("明文为：" + plaintext);
        System.out.println("密钥为：" + key);
        System.out.println("密文为：" + ciphertext);
        System.out.println("解密为：" + decryptText);
    }

    // 1 加密
    public String encrypt(final String plaintext, final String key) {
        Integer[] S = new Integer[256]; // S盒
        Character[] keySchedule = new Character[plaintext.length()]; // 生成的密钥流
        StringBuffer ciphertext = new StringBuffer();

        ksa(S, key);
        rpga(S, keySchedule, plaintext.length());

        for (int i = 0; i < plaintext.length(); ++i) {
            ciphertext.append((char) (plaintext.charAt(i) ^ keySchedule[i]));
        }

        return ciphertext.toString();
    }

    // 1.1 KSA--密钥调度算法--利用key来对S盒做一个置换，也就是对S盒重新排列
    public void ksa(Integer[] s, String key) {
        for (int i = 0; i < 256; ++i) {
            s[i] = i;
        }

        int j = 0;
        for (int i = 0; i < 256; ++i) {
            j = (j + s[i] + key.charAt(i % key.length())) % 256;
            swap(s, i, j);
        }
    }

    // 1.2 RPGA--伪随机生成算法--利用上面重新排列的S盒来产生任意长度的密钥流
    public void rpga(Integer[] s, Character[] keySchedule, int plaintextLength) {
        int i = 0, j = 0;
        for (int k = 0; k < plaintextLength; ++k) {
            i = (i + 1) % 256;
            j = (j + s[i]) % 256;
            swap(s, i, j);
            keySchedule[k] = (char) (s[(s[i] + s[j]) % 256]).intValue();
        }
    }

    // 1.3 置换
    public void swap(Integer[] s, int i, int j) {
        Integer mTemp = s[i];
        s[i] = s[j];
        s[j] = mTemp;
    }
}
```

