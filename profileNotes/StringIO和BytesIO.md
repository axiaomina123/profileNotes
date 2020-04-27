StringIO和BytesIO

```python
from io import StringIO
from io import BytesIO
```

StringIO: 将str形式的数据写入内存当中

```python
f = StringIO()
f.write('hello')

f.write(' ')

f.write('world!')
# getvalue()方法用于获取写入后的str
print(f.getvalue())
hello world!
```

读取StringIO

```python
f = StringIO('Hello!\nHi!\nGoodbye!')
while True:
     s = f.readline()
     if s == '':
         break
     print(s.strip())

Hello!
Hi!
Goodbye!
```

BytesIO: 将bytes形式的数据写入内存当中

```python
f = BytesIO()
# 写入的不是str，而是经过UTF-8编码的bytes
f.write('中文'.encode('utf-8'))

print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87'
```

从内存中读取BytesIO

```python
f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
f.read()
b'\xe4\xb8\xad\xe6\x96\x87'
```

