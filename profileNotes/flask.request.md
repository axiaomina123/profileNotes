## flask.request

```
除了URL，请求报文中的其他信息都可以通过request对象的属性和方法获取，常用的属性有：path：base_url
full_path：url
host：url_root
host_url
args：Werkzeug的ImmutableMultiDict对象。存储解析后的查询字符串，可通过字典方式获取键值。如果你想获取未解析的原生查询字符串，可以用query_string属性
blueprint:当前蓝本的名称
cookies：一个包含所有随请求提交的cookies的字典
data：包含字符串形式的请求数据
endpoint：于当前请求相匹配的端点值
files：Werkzeug的MultiDict对象，包含所有上传文件，可以使用字典的形式获取文件。使用的键为文件input标签中的name值，对应的值为Werkzeug的FileStorage对象。可以调用save()方法并传入保存路径来保存文件
form：Werkzeug的ImmutableMultiDict对象。于files类似，包含解析后的表单数据。表单字段值通过input标签的name属性值作为键获取
values：Werkzeug的CombinedMultiDict对象，结合了args和form属性的值
get_data(cache=True,as_text=False,parse_from_data=False)：获取请求中的数据，默认读取为字节字符串（bytestring），将as_text设为True则返回值将是解码后的unicode字符串
get_json(self,force=False,silent=False,cache=True)：作为json解析并返回数据，如果MIME类型不是json，返回None（除非force设为True）；解析出错则抛出Werkzeug提供的BadRequest异常（如果未开启调试模式，则返回400错误响应），如果silent设为True则返回None；cache设置是否缓存解析后的json数据
headers：一个Werkzeug的EnvironHeaders对象，包含首部字段，可以以字典的形式操作
json：包含解析后的json数据，内部调用get_json()，可通过字典的方式获取键值
method：请求的HTTP方法
referrer：请求发起的源URL，即referer
scheme：请求的URL模式（http或https）
user_agent：用户代理（User Agent, UA），包含了用户的客户端类型，操作系统类型等信息
1.get请求 
访问时会在地址栏直接显示参数不安全，且参数大小比较小。
2.post请求 
参数不显示在地址栏，一般用户注册、登录都通过post请求完成。
flask获取参数方式：
request.form.get("key", type=str, default=None) 获取表单数据
request.args.get("key") 获取get请求参数
request.values.get("key") 获取所有参数
```



```python
print(request.method) #获取访问方式 GET
print(request.url) #获取url http://127.0.0.1:5000/req?id=1&name=wl
print(request.cookies) #获取cookies {}
print(request.path)  # 获取访问路径 /req
print(request.args) #获取url传过来的值  ImmutableMultiDict([('id', '1'), ('name', 'wl')])
print(request.args.get("id")) #get获取id  1
print(request.args["name"]) # 索引获取name wl
print(request.args.to_dict()) # 获取到一个字典 {'id': '1', 'name': 'wl'}
```