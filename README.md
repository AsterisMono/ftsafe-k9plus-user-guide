# ftsafe-k9plus-user-guide
## 安装飞天官方的管理工具
打开[Feitian SK Manager Tool](https://fido.ftsafe.com/feitian-sk-manager-tool-user-manual/)，下载`Windows Release`和`Mini Driver`并安装

> 使用建议：先打开`SK Manager`，再插入你的K9+，我手头这把先插上再开程序会有一点小问题
## 确保所有功能都已经启用
打开安装好的`SK Manager`，选择左侧的`Interfaces`，确保六个复选框都是勾选状态

如果有缺失，勾选后点击`Save`，重新插入Key
## PGP
[PGP on K9+](https://github.com/AsterisMono/ftsafe-k9plus-user-guide/blob/main/guides/pgp.md)
## FIDO & FIDO2
一般不需要配置，可以直接使用。

也可以在使用前配置PIN：`Windows设置 - 账户 - 登录选项 - 安全密钥 - 管理`

关于FIDO的视频介绍：[fido安全密钥、两步认证？一部视频解答你所有的疑惑！](https://www.bilibili.com/video/BV1GL4y1h7aq)
## PIV
TBD

## OTP
TBD



## 一共有几个PIN？
 - PGP：3个
   - PIN（使用PGP智能卡时需要提供）
   - Admin PIN（编辑PGP智能卡时需要提供）
   - Reset Code（用于编辑PIN，或者在PIN尝试次数过多被锁死时重置PIN）
 - PIV：3个
   - PIN（使用PIV时需要提供）
   - PUK（用来重置PIN或者在PIN锁死时进行解锁）
   - MKey（用于管理PIV，比如导入证书）
 - FIDO2：1个
   - PIN（可以在Win10的密钥管理中设置，每次两步验证时使用）
