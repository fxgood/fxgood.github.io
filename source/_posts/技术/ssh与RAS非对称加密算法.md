---
title: SSH和RSA非对称加密算法
excerpt: 远程登录等场景经常会使用到SSH协议，但是对其原理并不是很清楚，因此学习一下并加以记录
---

# SSH工作原理

## 基本原理

SSH的安全性比较好，其对数据进行加密的方式主要有两种：对称加密和非对称加密（公钥加密）。

对称加密指加密解密使用的是同一套秘钥。Client端把密钥加密后发送给Server端，Server用同一套密钥解密。对称加密的加密强度比较高，很难破解。但是，Client数量庞大，很难保证密钥不泄漏。如果有一个Client端的密钥泄漏，那么整个系统的安全性就存在严重的漏洞。为了解决对称加密的漏洞，于是就产生了非对称加密。非对称加密有两个密钥：“公钥”和“私钥”。公钥加密后的密文，只能通过对应的私钥进行解密。想从公钥推理出私钥几乎不可能，所以非对称加密的安全性比较高。

SSH的加密原理中，使用了RSA非对称加密算法。
整个过程是这样的：
（1）远程主机收到用户的登录请求，把自己的公钥发给用户。
（2）用户使用这个公钥，将登录密码加密后，发送回来。
（3）远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

在通信过程中，使用的是对称加密算法。

Host Key 分为 Public 和 Private 两种。服务端拥有 Public Key 和 Private Key，并将 Public Key 发送给客户端。客户端用 Public Host Key 验证这台服务器确实是自己要连接的服务器后，双方使用 Diffie-Hellman 算法生成一致的 SessionKey。

Host Key 由 SSH 自行生成，不需要用户做什么。如果客户端通过 Host Key 发现从来没有连接过这台服务器，会询问用户是否要继续连接，用户回答 yes 之后会在本地的 known_hosts 文件记录这台服务器，下次连接时客户端就不会再次询问。由于仅靠服务端下发 Host Key 的方法无法防范中间人攻击，后来又出现了 Public Key Certificates，由一个可靠的第三方机构给服务端签发证书，从而确保了安全性。

Session Key 用于之后通讯时对消息进行加密解密。这个 Session Key 的机制被称作对称加密（Symmetric Encryption），也就是两端使用的相同的 Key 来加密和解密信息。可以看出 SSH 信息的加密解密时并不是用大家自己生成的 Public/ Private Key，而是用双方都一致的 Session Key。

生成 Session Key 的步骤大致如下：

1. 客户端和服务端使用沟通时的信息，协商加密算法以及一个双方都知道的数字。
2. 双方各自生成只有自己才知道的 private 密码（一个密码而不是公钥私钥），并使用上一步中的数字进行加密，再次生成密码。
3. 双方交换再次加密后的密码。
4. 双方在对方发来的密码基础上，加上第二步自己的 private 密码再次加密。本次加密之后得到的结果就是在双方处都相同的 Session Key。

> 简单来说就是，我生成一个密码使用双方都知道的数字加密，然后将结果互相交换，再用自己的密码进行加密，这样我们俩手头的密码就是一样的了（对称加密）

## 中间人攻击

SSH之所以能够保证安全，原因在于它采用了公钥加密，这个过程本身是安全的，但是实际用的时候存在一个风险：如果有人截获了登录请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪。因为不像[https协议](https://www.zhihu.com/search?q=https协议&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A267701810})，SSH协议的公钥是没有证书中心（CA）公证的，是自己签发的。

如果攻击者插在用户与远程主机之间（比如在公共的wifi区域），用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么SSH的安全机制就不存在了。这种风险就是著名的"中间人攻击"（Man-in-the-middle attack）。那么SSH协议是怎样应对的呢？

## 如何应对中间人攻击

如果是第一次登录远程机，会出现以下提示：

```text
$ ssh user@host
The authenticity of host 'host (12.18.429.21)' can't be established.
RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
Are you sure you want to continue connecting (yes/no)?
```

因为公钥长度较长（采用[RSA算法](https://www.zhihu.com/search?q=RSA算法&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A267701810})，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。如98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d，这样比对就容易多了。

经过比对后，如果用户接受这个远程主机的公钥，系统会出现一句提示语：

```text
Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.
```

表示[host主机](https://www.zhihu.com/search?q=host主机&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A267701810})已得到认可，然后再输入登录密码就可以登录了。

当远程主机的公钥被接受以后，它就会被保存在文件~/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，一般是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。

# 公钥登录

使用密码登录，每次都必须输入密码，非常麻烦。好在SSH还提供了公钥登录，可以省去输入密码的步骤。
所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用[ssh-keygen](https://www.zhihu.com/search?q=ssh-keygen&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A267701810})生成一个： $ ssh-keygen

运行上面的[命令](https://link.zhihu.com/?target=https%3A//www.linuxcool.com/)以后，系统会出现一系列提示，可以一路回车。其中有一个问题是，要不要对私钥设置口令（passphrase），如果担心私钥的安全，这里可以设置一个。
运行结束以后，在~/.ssh/目录下，会新生成两个文件：id_rsa.pub和[id_rsa](https://www.zhihu.com/search?q=id_rsa&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A267701810})。前者是公钥，后者是私钥。

这时再输入下面的[命令](https://link.zhihu.com/?target=https%3A//www.linuxcool.com/)，将公钥传送到[远程主机host](https://www.zhihu.com/search?q=远程主机host&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A267701810})上面：

```text
$ ssh-copy-id user@host
```

远程主机将用户的公钥，保存在登录后的用户主目录的~/.ssh/authorized_keys文件中。
这样，以后就登录远程主机不需要输入密码了。



# RSA原理简介

RSA 算法的加密原理是什么？ - AngelYJ的回答 - 知乎 https://www.zhihu.com/question/25038691/answer/81565068

RSA 算法的加密原理是什么？ - 童凌的回答 - 知乎 https://www.zhihu.com/question/25038691/answer/388573650

