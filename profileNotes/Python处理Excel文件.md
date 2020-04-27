## Python处理Excel文件

#### XlsxWriter

XlsxWriter用于创建xlsx文件，可以键入文本，数字，超链接和公式等。它只能创建新文件，不能读取或修改已经存在的文件

##### 安装：

```python
$ pip install XlsxWriter
```

##### 应用：

demo

```python
import xlsxwriter

# 创建一个workbook并添加一个sheet页
workbook = xlsxwriter.Workbook('hello.xlsx')
# 如果不给add_worksheet()传递参数，则默认生成的sheet文件名为（‘sheet1’，‘sheet2’，....），若要设置sheet文件名，则传入参数即可
worksheet = workbook.add_worksheet()
# 向文件的第A0个单元格中写入内容"Hello world"
worksheet.write('A1', 'Hello world')
# 保存并关闭这个文件
worksheet.close()
```

向文件中传入多个内容

```python
# 创建内容容器
content = (
['Rent', 1000],
['Gas', 100],
['Food', 300],
['Gym', 50],
)
# 设置起始行列号
row = 0
col = 0
# 生成的数据竖向排列
for item, cost in content:
	worksheet.write(row, col, item)
	worksheet.write(row, col + 1, cost)
	row += 1
# 生成的数据横向排列
for item, cost in content:
	worksheet.write(row, col, item)
	worksheet.write(row + 1, col, cost)
	col += 1 
	
# 若想增加一列求和可用公式sum
worksheet.write(row, col, 'total')
woeksheet.write(row, col, '=SUM(B1:B4)')
	
workbook.close()
```

给单元格中的内容添加样式：

```python
# 给内容加黑
bold = workbook.add_format({'bold': True})
# 给数字加属性（$:表示money）
money = workbook.add_format({'num_format': '$#, ##0'})
# 时间格式
date_format = wworkbook.add_format({'num_format':'mmmm d yyyy'})
# 设置列宽（第一个参数为要改变的列的区间，第二个参数为列宽）
worksheet.set_column('B:B', 15)
# 应用
worksheet.write('A1', 'Item', bold)
worksheet.write("A2", 100, money)
```

#### xlrd读取Excel文件内容

安装 ：pip install xlrd

