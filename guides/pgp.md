# PGP on K9+

## 开始之前需要知道的

一张PGP智能卡有2个PIN，一个Reset Code：

- PIN（使用智能卡时需要提供）
- Admin PIN（对智能卡做修改时需要提供）
- Reset Code（用于PIN尝试次数过多导致锁死时 解锁卡片并重置PIN）

在开始使用之前，要修改这三个凭据并妥善保存。

```
PS C:\Users\Mikiiki>gpg --card-edit

gpg/card> passwd
gpg: OpenPGP card no. xxxxxxxxxxxxxxxxxxxx detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit
```

## 创建密钥

1. 安装GnuPG

   - Linux：各发行版一般自带
   - Windows：https://www.gnupg.org/download/

2. 生成4096位主密钥：

   ```powershell
   PS C:\Users\Mikiiki> gpg --full-gen-key
   gpg (GnuPG) 2.2.28; Copyright (C) 2021 g10 Code GmbH
   This is free software: you are free to change and redistribute it.
   There is NO WARRANTY, to the extent permitted by law.
   
   Please select what kind of key you want:
      (1) RSA and RSA (default)
      (2) DSA and Elgamal
      (3) DSA (sign only)
      (4) RSA (sign only)
     (14) Existing key from card
   Your selection? 1
   
   # 选择默认的1
   
   RSA keys may be between 1024 and 4096 bits long.
   What keysize do you want? (3072) 4096
   
   # 这里可以选择给主密钥4096位，因为主密钥不会存进智能卡，位数越多越好
   
   Requested keysize is 4096 bits
   Please specify how long the key should be valid.
            0 = key does not expire
         <n>  = key expires in n days
         <n>w = key expires in n weeks
         <n>m = key expires in n months
         <n>y = key expires in n years
   Key is valid for? (0)
   
   # 主密钥在妥善存放的情况下可以设置永不过期
   
   Key does not expire at all
   Is this correct? (y/N) y
   
   GnuPG needs to construct a user ID to identify your key.
   
   Real name: ftsafe
   Email address: ftsafe@ftsafe.com
   Comment:
   
   # 输入一些个人信息
   
   You selected this USER-ID:
       "ftsafe <ftsafe@ftsafe.com>"
   
   Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
   
   
   We need to generate a lot of random bytes. It is a good idea to perform
   some other action (type on the keyboard, move the mouse, utilize the
   disks) during the prime generation; this gives the random number
   generator a better chance to gain enough entropy.
   
   # 随机移动鼠标
   
   gpg: key C090F947B8664E7B marked as ultimately trusted
   gpg: revocation certificate stored as 'C:/Users/Mikiiki/AppData/Roaming/gnupg/openpgp-revocs.d\4CC385BC59B1CAFCCCCCA610C090F947B8664E7B.rev'
   public and secret key created and signed.
   
   pub   rsa4096 2021-10-22 [SC]
         4CC385BC59B1CAFCCCCCA610C090F947B8664E7B
   uid                      ftsafe <ftsafe@ftsafe.com>
   sub   rsa4096 2021-10-22 [E]
   ```

3. 删除默认的4096位加密子密钥，并重新分别创建三个子密钥（加密、签名、认证）

   > Credit：[可能是最好的 Yubikey + GPG/SSH 智能卡教程 (mechanus.io)](https://mechanus.io/ke-neng-shi-zui-hao-de-yubikey-gpg-ssh-zhi-neng-qia-jiao-cheng/)

   删除默认的加密子密钥：

   ```powershell
   PS C:\Users\Mikiiki> gpg --edit-key "ftsafe@ftsafe.com"
   gpg (GnuPG) 2.2.28; Copyright (C) 2021 g10 Code GmbH
   This is free software: you are free to change and redistribute it.
   There is NO WARRANTY, to the extent permitted by law.
   
   Secret key is available.
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   ssb  rsa4096/14C4338525609AE0
        created: 2021-10-22  expires: never       usage: E
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   
   gpg> key 1 # 选择key1，即第一个子密钥（主密钥是0号并默认选择）
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   ssb* rsa4096/14C4338525609AE0 # 可以看到这里有星号，代表已经选中
        created: 2021-10-22  expires: never       usage: E
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   
   gpg> delkey
   Do you really want to delete this key? (y/N) y
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   ```

   创建签名密钥：

   ```powershell
   gpg> addkey
   Please select what kind of key you want:
      (3) DSA (sign only)
      (4) RSA (sign only)
      (5) Elgamal (encrypt only)
      (6) RSA (encrypt only)
     (14) Existing key from card
   Your selection? 4
   RSA keys may be between 1024 and 4096 bits long.
   What keysize do you want? (3072) 2048 # 选择2048位，否则无法保存进k9+
   Requested keysize is 2048 bits
   Please specify how long the key should be valid.
            0 = key does not expire
         <n>  = key expires in n days
         <n>w = key expires in n weeks
         <n>m = key expires in n months
         <n>y = key expires in n years
   Key is valid for? (0) 1y # 设置子密钥有效期
   Key expires at 2022/10/22 20:21:06
   Is this correct? (y/N) y
   Really create? (y/N) y
   We need to generate a lot of random bytes. It is a good idea to perform
   some other action (type on the keyboard, move the mouse, utilize the
   disks) during the prime generation; this gives the random number
   generator a better chance to gain enough entropy.
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   ssb  rsa2048/95607DD86BA8BAE2
        created: 2021-10-22  expires: 2022-10-22  usage: S
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   ```
   按同样方法创建加密密钥和认证密钥：

   ```powershell
   gpg> addkey
   Please select what kind of key you want:
      (3) DSA (sign only)
      (4) RSA (sign only)
      (5) Elgamal (encrypt only)
      (6) RSA (encrypt only)
     (14) Existing key from card
   Your selection? 6
   RSA keys may be between 1024 and 4096 bits long.
   What keysize do you want? (3072) 2048
   Requested keysize is 2048 bits
   Please specify how long the key should be valid.
            0 = key does not expire
         <n>  = key expires in n days
         <n>w = key expires in n weeks
         <n>m = key expires in n months
         <n>y = key expires in n years
   Key is valid for? (0) 1y
   Key expires at 2022/10/22 20:25:21
   Is this correct? (y/N) y
   Really create? (y/N) y
   We need to generate a lot of random bytes. It is a good idea to perform
   some other action (type on the keyboard, move the mouse, utilize the
   disks) during the prime generation; this gives the random number
   generator a better chance to gain enough entropy.
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   ssb  rsa2048/95607DD86BA8BAE2
        created: 2021-10-22  expires: 2022-10-22  usage: S
   ssb  rsa2048/6FED5C0411B2B3DB
        created: 2021-10-22  expires: 2022-10-22  usage: E
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   ```

   ```powershell
   PS C:\Users\Mikiiki> gpg --expert --edit-key "ftsafe@ftsafe.com"
   
   # 注意这里：如果你的密钥类型选择没有RSA (set your own capabilities)，请用--expert开关重新启动gpg
   
   gpg (GnuPG) 2.2.28; Copyright (C) 2021 g10 Code GmbH
   This is free software: you are free to change and redistribute it.
   There is NO WARRANTY, to the extent permitted by law.
   
   Secret key is available.
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   ssb  rsa4096/14C4338525609AE0
        created: 2021-10-22  expires: never       usage: E
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   
   gpg> addkey
   Please select what kind of key you want:
      (3) DSA (sign only)
      (4) RSA (sign only)
      (5) Elgamal (encrypt only)
      (6) RSA (encrypt only)
      (7) DSA (set your own capabilities)
      (8) RSA (set your own capabilities) # 见上文
     (10) ECC (sign only)
     (11) ECC (set your own capabilities)
     (12) ECC (encrypt only)
     (13) Existing key
     (14) Existing key from card
   Your selection? 8
   
   Possible actions for a RSA key: Sign Encrypt Authenticate
   Current allowed actions: Sign Encrypt
   
      (S) Toggle the sign capability
      (E) Toggle the encrypt capability
      (A) Toggle the authenticate capability
      (Q) Finished
   
   Your selection? s
   
   Possible actions for a RSA key: Sign Encrypt Authenticate
   Current allowed actions: Encrypt
   
      (S) Toggle the sign capability
      (E) Toggle the encrypt capability
      (A) Toggle the authenticate capability
      (Q) Finished
   
   Your selection? e
   
   Possible actions for a RSA key: Sign Encrypt Authenticate
   Current allowed actions:
   
      (S) Toggle the sign capability
      (E) Toggle the encrypt capability
      (A) Toggle the authenticate capability
      (Q) Finished
   
   Your selection? a
   
   Possible actions for a RSA key: Sign Encrypt Authenticate
   Current allowed actions: Authenticate
   
      (S) Toggle the sign capability
      (E) Toggle the encrypt capability
      (A) Toggle the authenticate capability
      (Q) Finished
   
   Your selection? q
   RSA keys may be between 1024 and 4096 bits long.
   What keysize do you want? (3072) 2048
   Requested keysize is 2048 bits
   Please specify how long the key should be valid.
            0 = key does not expire
         <n>  = key expires in n days
         <n>w = key expires in n weeks
         <n>m = key expires in n months
         <n>y = key expires in n years
   Key is valid for? (0) 1y
   Key expires at 2022/10/22 20:26:23
   Is this correct? (y/N) y
   Really create? (y/N) y
   We need to generate a lot of random bytes. It is a good idea to perform
   some other action (type on the keyboard, move the mouse, utilize the
   disks) during the prime generation; this gives the random number
   generator a better chance to gain enough entropy.
   
   sec  rsa4096/C090F947B8664E7B
        created: 2021-10-22  expires: never       usage: SC
        trust: ultimate      validity: ultimate
   ssb  rsa4096/14C4338525609AE0
        created: 2021-10-22  expires: never       usage: E
   ssb  rsa2048/06D78CEB182521C8
        created: 2021-10-22  expires: 2022-10-22  usage: A
   [ultimate] (1). ftsafe <ftsafe@ftsafe.com>
   ```

## 备份私钥与导出公钥

> 注意：如果你使用的是Powershell，请手动将产出文件的编码修改为`UTF-8 without BOM`，否则导入时无法被程序识别

```bash
gpg --export -a "i@example.com" > public.key # 导出公钥
gpg --export-secret-key -a "i@example.com" > private.key
```

## 将三个子密钥导入智能卡

> 此步结束后将无法再备份私钥。（或者可能我弄混了？应该是不能备份子密钥的私钥吧）

> Credit: [可能是最好的 Yubikey + GPG/SSH 智能卡教程 (mechanus.io)](https://mechanus.io/ke-neng-shi-zui-hao-de-yubikey-gpg-ssh-zhi-neng-qia-jiao-cheng/)

```powershell
gpg> key 1 # 使用 key [数字] 选定一个key
gpg> keytocard
Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

gpg> key 1 # 先反选1号
gpg> key 2 # 再选择2号
gpg> keytocard
Please select where to store the key:
   (2) Encryption key
Your selection? 2

gpg> key 2
gpg> key 3
gpg> keytocard
Please select where to store the key:
   (3) Authentication key
Your selection? 3

gpg> save # 保存修改
```

## 生成撤销证书

> 假如你忘了主密钥的密码，或者丢失了对主密钥的控制权（丢失，被夺取），如果没有撤销凭证的话， 除了一个个通知你的朋友们没有任何办法 证明你不再使用这个密钥，这简直是灾难。
>
> Credit: [2021年，用更现代的方法使用PGP（上）](https://ulyc.github.io/2021/01/13/2021年-用更现代的方法使用PGP-上/)

```
# step 0
gpg --gen-revoke -ao   revoke.pgp   linus # uid 或者key id

# step 1
sec  rsa3072/99F583599B7E31F1 2021-01-11 linus <linus@outlook.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here) 3

# 按提示走完流程就可以
```

## 删除电脑上的私钥

> 你备份私钥了没？

```bash
gpg --delete-secret-keys "i@example.com"
```

## 使用

然后可以使用Kleopatra查看一下密钥，测试一下加解密操作

安卓设备可以使用OpenKeychain

