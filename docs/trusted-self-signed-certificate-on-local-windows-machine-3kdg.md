# 本地 Windows 计算机上的可信自签名证书

> 原文：<https://dev.to/akuryan/trusted-self-signed-certificate-on-local-windows-machine-3kdg>

这是从我的博客交叉发布的。

我创建了 powershell 脚本来自动生成本地信任的自签名证书。它将创建根证书，用作将来生成其他证书的签名证书。

请参见[我的知识库](https://github.com/akuryan/ConfigurationHelpers/tree/master/Win%20server/sslCertificate-gen)中的脚本。希望有帮助。

生成证书后，您可以与您的开发人员共享它们:导出为您的域的 PFX 证书，并导出为来自 LocalMachine/Root 的 CER 文件证书，该证书颁发给“do not trust-LocalSigningCertificate”。您的开发伙伴应该将 CER 文件(没有密钥的证书)导入到“LocalMachine/Root”，将 PFX 导入到“LocalMachine/My ”,并且可以愉快地使用自签名的本地可信证书(注意:您不能在 open 中使用此证书，因为它在默认情况下是不可信的——这只是为了节省时间和金钱的开发目的)