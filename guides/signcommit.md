# 使用K9+签名GitHub Commit

## 开始之前

先配置[PGP](https://github.com/AsterisMono/ftsafe-k9plus-user-guide/blob/main/guides/pgp.md)。

## GitHub配置

右上角`Settings - SSH & GPG Keys`，右侧的`GPG Keys`中点击`New GPG Key`，导入你的GPG公钥。

## 本地Git配置

运行`gpg --edit-card YOUR_EMAIL`，查看签名子密钥的信息，记录下面信息中的`SIGNINGKEY`：

```
gpg (GnuPG) 2.2.28; Copyright (C) 2021 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret subkeys are available.

pub  rsa4096/DE5FC836090FFC49
     created: 2021-10-21  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa2048/0EBDC5FB7D78FCD9
     created: 2021-10-21  expires: 2022-10-21  usage: E
     card-no: 000B 00000001
ssb  rsa2048/425F8A621EBC775A
     created: 2021-10-21  expires: 2022-10-21  usage: A
     card-no: 000B 00000001
ssb  rsa2048/SIGNINGKEY
     created: 2021-10-21  expires: 2022-10-21  usage: S
     card-no: 000B 00000001
```

运行下列配置指令：

```bash
git config --global gpg.program "c:\Program Files (x86)\GnuPG\bin\gpg.exe"
git config --global commit.gpgsign true # 如果你想要所有的Commit都被签名，否则写false
git config --global user.signingkey SIGNINGKEY
```

如果`commit.gpgsign`为`false`，可以手动签名Commit（`-S`）：

```bash
git commit -S -m your commit message
```

