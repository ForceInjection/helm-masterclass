# Helm 图表签名和验证

## 步骤-01: 介绍

- [GnuPG](https://gnupg.org/)
- 使用 gpg 生成私钥/公钥
- 签名 Helm 包
- 导出公钥
- 使用公钥验证 Helm 包

## 步骤-02: 安装 gnupg

### 步骤-02-01: 在 MacOS 上安装 gnupg

- [使用 homebrew 安装 gnupg](https://formulae.brew.sh/formula/gnupg)

```bash
# 在 MacOS 上安装 gnupg
brew install gnupg

# 验证版本
gpg --version
```

### 步骤-02-02: 在 WindowsOS 上安装 gnupg

- [使用 chocolatey 在 windows 上安装 gnupg](https://community.chocolatey.org/packages/gnupg#individual)

```bash
# 在 WindowsOS 上安装 gnupg
choco install gnupg

# 验证版本
gpg --version
```

## 步骤-03: 使用 gpg 生成私钥/公钥对

```bash
# 列出密钥
gpg --list-keys

# 使用 gpg 生成私钥/公钥
gpg --full-generate-key
-> kind of key: 选择 1 (1) RSA and RSA
-> What keysize do you want? (3072) 
-> Please specify how long the key should be valid.
-> Key is valid for? (0) "0 = key does not expire"
-> Is this correct? (y/N) 
-> Real name: helmsigndemo1
-> Email address: helmsigndemo1@gmail.com
-> Comment: Keys used to sign Helm Charts
-> Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
-> Passphrase: helm1234

# 列出密钥
gpg --list-keys

# 将新的秘密密钥环转换为旧格式
gpg --export-secret-keys >~/.gnupg/helmsigndemo1-secring-privatekey.gpg
Passphrase: helm1234
附加说明:
1. 为了签名图表，Helm 目前更喜欢旧格式。
2. 将新的秘密密钥环格式转换为旧格式并存储在名为 secring 的文件中。

# 验证文件是否创建
ls ~/.gnupg/helmsigndemo1-secring-privatekey.gpg

# 将私钥复制到课程目录
cd 43-Helm-Sign-and-Verify-Charts 
cp ~/.gnupg/helmsigndemo1-secring-privatekey.gpg myhelmcharts/private-key/

# 使用单个命令导出私钥（而不是导出到 .gnupg 文件夹并复制到 private-key 文件夹）
cd myhelmcharts
gpg --export-secret-keys > private-key/helmsigndemo1-secring-privatekey.gpg
```

## 步骤-04: 签名 Helm 图表

```bash
# 切换目录
cd myhelmcharts
1. 我们将拥有 "myfirstchart" helm 图表文件夹

# 签名和打包 Helm 图表
helm package --sign --key 'helmsigndemo1' --keyring private-key/helmsigndemo1-secring-privatekey.gpg myfirstchart/
Passphrase: helm1234

# 验证创建的来源文件
ls -lrta
1. 我们应该找到以 ".prov" 结尾的文件 "myfirstchart-2.0.0.tgz.prov"
```

## 步骤-05: 导出公钥

- 使用公钥验证图表的完整性
- 在现实世界的场景中，这些公钥将发布在密钥服务器上（keyserver.ubuntu.com、keyserver.openpgp.com）
- 我们应该下载这些公钥来验证图表的完整性。

```bash
# 切换到目录
cd myhelmcharts

# 导出公钥
gpg --export 'helmsigndemo1' > public-key/helmsigndemo1-publickey.gpg

# 验证文件是否创建
ls public-key/helmsigndemo1-publickey.gpg
```

## 步骤-06: 使用公钥验证 Helm 包

```bash
# 切换目录
cd myhelmcharts

# Helm 验证
helm verify --keyring public-key/helmsigndemo1-publickey.gpg myfirstchart-0.1.0.tgz

## 示例输出
Kalyans-Mac-mini:myhelmcharts kalyanreddy$ helm verify --keyring public-key/helmsigndemo1-publickey.gpg myfirstchart-0.1.0.tgz
Signed by: helmsigndemo1 (Keys used to sign Helm Charts) <helmsigndemo1@gmail.com>
Using Key With Fingerprint: 0494EA24668AE1516A31E5EC467D1996D2158381
Chart Hash Verified: sha256:099c8a0cd0609f0e252bd63856ea1998c55e4af1b587c435d4b74d33283e0ad4
Kalyans-Mac-mini:myhelmcharts kalyanreddy$ 
```

## 步骤-07: 在 helm install 和 Upgrade 期间验证图表 - 正面测试

```bash
# 切换目录
cd myhelmcharts

# 使用 --verify 进行 Helm 安装
helm install myapp1 myfirstchart-0.1.0.tgz --verify --keyring public-key/helmsigndemo1-publickey.gpg --atomic

# 列出 Helm Release
helm list

# Helm 状态
helm status --show-resources

# 访问应用程序
http://localhost:31239

# 使用 --verify 进行 Helm 升级
helm upgrade myapp1 myfirstchart-0.1.0.tgz --verify --keyring public-key/helmsigndemo1-publickey.gpg --atomic --set image.tag="0.2.0"

# 卸载 Helm Release
helm uninstall myapp1
```

## 步骤-08: 在 helm install 和 Upgrade 期间验证图表 - 负面测试

```bash
# 切换目录
cd myhelmcharts

# 在 public-key 文件夹中创建一些虚拟文件
touch public-key/dummy-publickey.gpg

# 使用 --verify 进行 Helm 安装
helm install myapp1 myfirstchart-0.1.0.tgz --verify --keyring public-key/dummy-publickey.gpg --atomic
观察结果:
1. 应该抛出如下错误

## 示例输出
Kalyans-Mac-mini:myhelmcharts kalyanreddy$ helm install myapp1 myfirstchart-0.1.0.tgz --verify --keyring public-key/dummy-publickey.gpg --atomic
Error: INSTALLATION FAILED: openpgp: signature made by unknown entity
Kalyans-Mac-mini:myhelmcharts kalyanreddy$ 
```
