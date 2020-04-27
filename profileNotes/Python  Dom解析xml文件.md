### Python  Dom解析xml文件

```python
 #导入minidom
 2     from xml.dom import minidom
 3 
 4     # 1.创建DOM树对象
 5     dom=minidom.Document()
 6     # 2.创建根节点。每次都要用DOM对象来创建任何节点。
 7     root_node=dom.createElement('root')
 8     # 3.用DOM对象添加根节点
 9     dom.appendChild(root_node)
10 
11     # 用DOM对象创建元素子节点
12     book_node=dom.createElement('book')
13     # 用父节点对象添加元素子节点
14     root_node.appendChild(book_node)
15     # 设置该节点的属性
16     book_node.setAttribute('price','199')
17 
18     name_node=dom.createElement('name')
19     root_node.appendChild(name_node)
20     # 也用DOM创建文本节点，把文本节点（文字内容）看成子节点
21     name_text=dom.createTextNode('计算机程序设计语言 第1版')
22     # 用添加了文本的节点对象（看成文本节点的父节点）添加文本节点
23     name_node.appendChild(name_text)
        删除 removeChild()
        父.removeChild(子节点);
        自己.parentNode.removeChild(自己)
        插入insertBefore()
        父.insertBefore(新的子节点,要参考的节点)
24 
25     # 每一个结点对象（包括dom对象本身）都有输出XML内容的方法，如：toxml()--字符串, toprettyxml()--美化树形格式。
26     
27     try:
28         with open('dom_write.xml','w',encoding='UTF-8') as fh:
29             # 4.writexml()第一个参数是目标文件对象，第二个参数是根节点的缩进格式，第三个参数是其他子节点的缩进格式，
30             # 第四个参数制定了换行格式，第五个参数制定了xml内容的编码。
31             dom.writexml(fh,indent='',addindent='\t',newl='\n',encoding='UTF-8')
32             print('写入xml OK!')
33     except Exception as err:
34         print('错误信息：{0}'.format(err))
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　结果如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```xml
<?xml version="1.0" encoding="utf8"?>
<root>
    <book price="99">
        <name>计算机程序设计语言 第1版</name>
    </book>
</root>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 二、DOM解析XML文件

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```python
 1     from xml.dom import minidom
 2     with open('dom_write.xml','r',encoding='utf8') as fh:
 3         # parse()获取DOM对象
 4         dom=minidom.parse(fh)
 5         # 获取根节点
 6         root=dom.documentElement
 7         # 节点名称
 8         print(root.nodeName)
 9         # 节点类型：'ELEMENT_NODE'，元素节点； 'TEXT_NODE'，文本节点； 'ATTRIBUTE_NODE'，属性节点
10         print(root.nodeType)
11         # 获取某个节点下所有子节点，是个列表
12         print(root.childNodes)
13         # 通过dom对象或根元素，再根据标签名获取元素节点，是个列表
14         book=root.getElementsByTagName('book')[0]
15         # 获取节点属性
16         print(book.getAttribute('price'))
17         
18         # 获取某个元素节点的文本内容，先获取子文本节点，然后通过“data”属性获取文本内容
19         name=root.getElementsByTagName('name')[0]
20         name_text_node=name.childNodes[0]
21         print(name_text_node.data)
22     
23         # 获取某节点的父节点
24         print(name.parentNode.nodeName)
```

svn密码：d%jRS@7wBt