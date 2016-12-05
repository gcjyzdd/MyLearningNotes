# Python 学习笔记

[toc]

## 卸载package

Ubuntu Oneiric (and I expect newer versions too) install pip packages to `/usr/local/lib/python2.7/dist-packages`, and apt packages to `/usr/lib/python2.7/dist-packages`. So just check the former directory and 
```bash
sudo pip uninstall every package
```
you find there.

## 合并list
假设`a=['1','2','3'],b=['4','5'], 我们想要得到合并的list,python里面提供了很简单的方法
```python
c=a+b
print c
c=['1','2','3','4','5']
a+=b
print a
a=['1','2','3','4','5']
```

##How can you produce the following list with range() in Python?
like
```python
[9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

use reversed() function:
```python
reversed(range(10))
```
It's much more meaningful.

Update:

If you want it to be a list (as btk pointed out):
```python
list(reversed(range(10)))
```

# Split a string by space
The `str.split()` method without an argument splits on whitespace:
```python
>>> "many   fancy word \nhello    \thi".split()
['many', 'fancy', 'word', 'hello', 'hi']
```

