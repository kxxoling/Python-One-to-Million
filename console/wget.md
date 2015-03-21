# wget

[wget](http://bitbucket.org/techtonik/python-wget/) 是一个很强大的 Python 下载库，
使用方法如下：

```python
>>> import wget
>>> url = 'http://www.futurecrew.com/skaven/song_files/mp3/razorback.mp3'
>>> filename = wget.download(url)
100% [................................................] 3841532 / 3841532>
>> filename
'razorback.mp3'
```

