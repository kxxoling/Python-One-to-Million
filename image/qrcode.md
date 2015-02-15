# QRCode

QR码（全称为快速响应矩阵码；英语：Quick Response Code）是二维条码的一种，
于1994年由日本DENSO WAVE公司发明。QR 码使用四种标准化编码模式（数字，字母数字，
二进制和 Kanji）来存储数据。

## Python QRCode 库

Python 可以安装 [qrcode](https://github.com/lincolnloop/python-qrcode) 库以获取 QR Code 生成的支持。

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

#### Python API
Python 下使用 qrcode 库：

```python
import qrcode
qr = qrcode.QRCode(
    version=1,                                              # QR Code version，1-4
    error_correction=qrcode.constants.ERROR_CORRECT_L,      # 错误纠正等级 L、M、Q、H 四等，默认是 M
    box_size=10,                                            # QR Code 图片的大小，单位是像素
    border=4,                                               # QR Code 的边框，单位是像素，默认 4
)
qr.add_data('Some data')            # 想要添加到 QR Code 中的内容
qr.make(fit=True)

img = qr.make_image()
```

默认输出格式是 JPG，生成 SVG 需要设定 `image_factory` 参数：

```python
import qrcode
import qrcode.image.svg

if method == 'basic':
    # Simple factory, just a set of rects.
    factory = qrcode.image.svg.SvgImage
elif method == 'fragment':
    # Fragment factory (also just a set of rects)
    factory = qrcode.image.svg.SvgFragmentImage
else:
    # Combined path factory, fixes white space that may occur when zooming
    factory = qrcode.image.svg.SvgPathImage

img = qrcode.make('Some data here', image_factory=factory)
```

如果需要 PNG 支持，还需要安装第三支持：

```shell
pip install git+git://github.com/ojii/pymaging.git#egg=pymaging
pip install git+git://github.com/ojii/pymaging-png.git#egg=pymaging-png
```

依旧是设定 `image_factory` 设置输出格式为 PNG：

```python
import qrcode
from qrcode.image.pure import PymagingImage
img = qrcode.make('Some data here', image_factory=PymagingImage)
```
