# progressbar

[progressbar](https://pypi.python.org/pypi/progressbar)
提供了一个控制台进度条工具，示例代码：


```python
from progressbar import ProgressBar
import time
pbar = ProgressBar(maxval=10)
for i in range(1, 11):
    pbar.update(i)
    time.sleep(1)
pbar.finish()
# 60% |########################################################                                      |
```
