# ftsafe-k9plus-user-guide
## 安装飞天官方的管理工具
打开[Feitian SK Manager Tool](https://fido.ftsafe.com/feitian-sk-manager-tool-user-manual/)，下载`Windows Release`和`Mini Driver`并安装

> 使用建议：先打开`SK Manager`，再插入你的K9+，我手头这把先插上再开程序会有一点小问题
## 确保所有功能都已经启用
打开安装好的`SK Manager`，选择左侧的`Interfaces`，确保六个复选框都是勾选状态

如果有缺失，勾选后点击`Save`，重新插入Key
## PGP
[PGP on K9+](https://github.com/AsterisMono/ftsafe-k9plus-user-guide/blob/main/guides/pgp.md)

[使用K9+签名GitHub Commit](https://github.com/AsterisMono/ftsafe-k9plus-user-guide/blob/main/guides/signcommit.md)

## FIDO & FIDO2
一般不需要配置，可以直接使用。

> 注意：当网页弹出含有`Windows Hello`选项的验证提示时，并不能使用安全密钥，需要按取消跳过`Windows Hello`认证环节。
<img src="https://user-images.githubusercontent.com/54884471/138551444-c50b10f1-f03d-4c5e-8368-60913726f74a.png" width="300" height="350">

这里点击取消

<img src="https://user-images.githubusercontent.com/54884471/138551558-4e712a3c-e267-49bb-b18a-46fb6f429812.png" width="330" height="230">


也可以在使用前配置PIN：`Windows设置 - 账户 - 登录选项 - 安全密钥 - 管理`

关于FIDO的视频介绍：[fido安全密钥、两步认证？一部视频解答你所有的疑惑！](https://www.bilibili.com/video/BV1GL4y1h7aq)

## OTP
K9+和a4b支持`HOTP (HMAC-based one-time password)`，但不支持基于时间的`TOTP`。

可以在飞天官方程序中配置`HOTP`或`Challenge-Response`Key，但暂时我还没有发现能够使用的场景

## PIV
[PIV on K9+](https://github.com/AsterisMono/ftsafe-k9plus-user-guide/blob/main/guides/piv.md)

## SSH
[SSH on K9+](https://github.com/AsterisMono/ftsafe-k9plus-user-guide/blob/main/guides/ssh.md)

## 一共有几个PIN？
 - PGP：3个
   - PIN（使用PGP智能卡时需要提供）默认：123456
   - Admin PIN（编辑PGP智能卡时需要提供）默认：12345678
   - Reset Code（用于编辑PIN，或者在PIN尝试次数过多被锁死时重置PIN）默认未设定
 - PIV：3个
   - PIN（使用PIV时需要提供）默认：123456
   - PUK（用来重置PIN或者在PIN锁死时进行解锁）默认：12345678
   - MKey（用于管理PIV，比如导入证书）默认：353637383132333435363738...（管理工具内置）
 - FIDO2：1个
   - PIN（可以在Win10的密钥管理中设置，每次两步验证时使用）
