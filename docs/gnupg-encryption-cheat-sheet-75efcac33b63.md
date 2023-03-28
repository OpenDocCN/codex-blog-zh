# GnuPG 加密备忘单

> 原文：<https://medium.com/codex/gnupg-encryption-cheat-sheet-75efcac33b63?source=collection_archive---------2----------------------->

![](img/d1884716693527554d94724eb4c7c4a9.png)

图片作者[👀梅布尔·安珀，她将有一天从](https://pixabay.com/ru/users/mabelamber-1377835/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1600617)[变成](https://pixabay.com/ru/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1600617)的

GnuPG(gpg)是一个很棒的工具，通常在 Linux 和 Mac 上都可以使用。它可用于**生成加密和签名密钥，并使用它们来加密和解密您的敏感数据**。这里有一个基本使用场景的快速备忘单。

# 生成新密钥

生成新的 gpg 密钥:

```
gpg --full-generate-key
```

该实用程序会询问一些问题，对于大多数问题，缺省值是合适的:

*   你想要什么样的钥匙？(RSA 和 RSA)
*   你想要什么尺寸的钥匙？(3072)
*   密钥的有效期应该有多长？(密钥不会过期)
*   实名？(输入名称以识别密钥)
*   邮箱地址？(输入您希望与密钥相关联的电子邮件地址)
*   保护密钥的密码。

**确保您记住了密码并将其存储在安全的地方**，否则您将无法访问密钥，从而无法访问加密文件。下面提供了一个生成输出的示例。

```
$ gpg --full-generate-key
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection? 1RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bitsPlease specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) yGnuPG needs to construct a user ID to identify your key.Real name: forketyfork
Email address: [forketyfork@example.com](mailto:forketyfork@example.com)
Comment: 
You selected this USER-ID:
    "forketyfork <[forketyfork@example.com](mailto:forketyfork@example.com)>"Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? oWe need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.gpg: key 2338E8E84DC0810C marked as ultimately trusted
gpg: revocation certificate stored as '/home/user/.gnupg/openpgp-revocs.d/ABCDEF0123456789ABCDEF0123456789ABCDEF01.rev'
public and secret key created and signed.pub   rsa4096 2021-04-08 [SC]
      ABCDEF0123456789ABCDEF0123456789ABCDEF01
uid                      forketyfork <[forketyfork@example.com](mailto:forketyfork@example.com)>
sub   rsa4096 2021-04-08 [E]
```

gpg 文件，包括密钥，位于`~/.gnupg`目录中。

下面是如何**列出现有的键**:

```
$ gpg --list-secret-keys
/home/user/.gnupg/pubring.kbx
-------------------------------------------------------
sec   rsa2048 2021-04-06 [SCEA]
      ABCDEF0123456789ABCDEF0123456789ABCDEF01
uid           [ultimate] forketyfork <[forketyfork@example.com](mailto:forketyfork@example.com)>
ssb   rsa2048 2021-04-06 [SEA]
```

注意这条线`[ultimate] forketyfork <forketyfork@example.com>`。在这里，`forketyfork`是关键名称。它的前面是`[ultimate]`信任级别，后面是与密钥相关的电子邮件。

# 备份和导入密钥

你可能想把关键文件转移到另一台机器上。以下是如何创建密钥的安全备份:

```
gpg --output backupkeys.pgp --armor --export-secret-keys --export-options export-backup "forketyfork"
```

文件`backupkeys.pgp`将被创建。你应该把它存放在一个安全的地方。选项`--armor`允许创建一个文本文件，而不是二进制文件，所以这样的文件应该是任何文本编辑器可读的。

你可以用这个备份文件**导入另一台机器**上的密钥。以下是如何从备份中导入密钥:

```
gpg --import backupkeys.pgp
```

当您列出密钥时，您会看到新导入的密钥的信任级别是`[unknown]`:

```
$ gpg --list-keys
/home/user/.gnupg/pubring.kbx
-------------------------------------------------------pub   rsa4096 2021-04-08 [SC]
      ABCDEF0123456789ABCDEF0123456789ABCDEF01
uid           [unknown] forketyfork <[forketyfork@example.com](mailto:forketyfork@example.com)>
sub   rsa4096 2021-04-08 [E]
```

因此，您还需要**为导入的密钥**设置信任级别——对于您自己的密钥，您可以在被询问时通过选择“我最终信任”将其设置为`ultimate`:

```
gpg --edit-key "forketyfork"
gpg> trust
gpg> save
```

现在信任级别应该是`[ultimate]`:

```
$ gpg --list-keys
/home/user/.gnupg/pubring.kbx
-------------------------------------------------------pub   rsa4096 2021-04-08 [SC]
      ABCDEF0123456789ABCDEF0123456789ABCDEF01
uid           [ultimate] forketyfork <[forketyfork@example.com](mailto:forketyfork@example.com)>
sub   rsa4096 2021-04-08 [E]
```

# 加密和解密文件

**加密和签名**一个文件`filename`并将结果存储在`filename.gpg`中就像:

```
gpg --encrypt --sign --recipient "forketyfork" --output filename.gpg filename
```

或者更简短的版本:

```
gpg -esr "forketyfork" -o filename.gpg filename
```

**解密**文件`filename.gpg`到`filename`:

```
gpg --decrypt --output filename filename.gpg
```

或者更简短的版本:

```
gpg -do filename filename.gpg
```

**归档文件或整个目录**，签名和加密，作为一行程序:

```
tar cz directory | gpg -esr "forketyfork" directory.tar.gz.gpg
```