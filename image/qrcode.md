# QRCode

QR码（全称为快速响应矩阵码；英语：Quick Response Code）是二维条码的一种，
于1994年由日本DENSO WAVE公司发明。QR 码使用四种标准化编码模式（数字，字母数字，
二进制和 Kanji）来存储数据。

## Python QRCode 库

Python 可以安装 qrcode 库以获取 QR Code

### 安装

qrcode 库依赖于 Python Image Library（PIL），不过 PIL 已经停止更新，而且其设计并不符合 Python 规范。
因此推荐使用 PIL 的继任者 Pillow 代替。
安装 PIL 或者 Pillow 需要先安装 C  语言 PIL 库，各操作系统各有不同。
Python PIL 或者 Pillow 的安装可以通过 `pip` 或者 `easy_install`：

```shell
pip install pillow
```

或者

```shell
pip install PIL
```

安装 Python QRCode：

```shell
pip install qrcode
```

### 使用 qrcode
QRCode 库提供两种嗲用提供方式——Python 库和系统命令(qr)。

#### qr 命令
`qr` 命令的使用如下：

    qr some_word[ > some_iamge.png]

qrcode 会根据文字的长度自动选择合适的 QRCode 版本
