## python xlwt写入excel操作](https://www.cnblogs.com/python-robot/p/9958352.html)



安装

```
$ pip install xlwt
```

例子:

```python
import xlwt
# 创建一个workbook 设置编码
workbook = xlwt.Workbook(encoding = 'utf-8')
# 创建一个worksheet
worksheet = workbook.add_sheet('My Worksheet')

# 写入excel
# 参数对应 行, 列, 值
worksheet.write(1,0, label = 'this is test')

# 保存
workbook.save('Excel_test.xls')
```

运行后 会在当前目录生成一个Excel_test.xls

```python
import xlwt

workbook = xlwt.Workbook(encoding = 'ascii')
worksheet = workbook.add_sheet('My Worksheet')
style = xlwt.XFStyle() # 初始化样式
font = xlwt.Font() # 为样式创建字体
font.name = 'Times New Roman' 
font.bold = True # 黑体
font.underline = True # 下划线
font.italic = True # 斜体字
style.font = font # 设定样式
worksheet.write(0, 0, 'Unformatted value') # 不带样式的写入

worksheet.write(1, 0, 'Formatted value', style) # 带样式的写入

workbook.save('formatting.xls') # 保存文件
```

效果:

![img](https://upload-images.jianshu.io/upload_images/5796306-b8f7b88c519259d4.jpg)

 

设置单元格宽度:

```python
import xlwt

workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
worksheet.write(0, 0,'My Cell Contents')

# 设置单元格宽度
worksheet.col(0).width = 3333
workbook.save('cell_width.xls')
```

输入一个日期到单元格:

```python
import xlwt
import datetime
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
style = xlwt.XFStyle()
style.num_format_str = 'M/D/YY' # Other options: D-MMM-YY, D-MMM, MMM-YY, h:mm, h:mm:ss, h:mm, h:mm:ss, M/D/YY h:mm, mm:ss, [h]:mm:ss, mm:ss.0
worksheet.write(0, 0, datetime.datetime.now(), style)
workbook.save('Excel_Workbook.xls')
```

向单元格添加一个公式:

```python
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
worksheet.write(0, 0, 5) # Outputs 5
worksheet.write(0, 1, 2) # Outputs 2
worksheet.write(1, 0, xlwt.Formula('A1*B1')) # Should output "10" (A1[5] * A2[2])
worksheet.write(1, 1, xlwt.Formula('SUM(A1,B1)')) # Should output "7" (A1[5] + A2[2])
workbook.save('Excel_Workbook.xls')
```

向单元格添加一个超链接:

```python
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
worksheet.write(0, 0, xlwt.Formula('HYPERLINK("http://www.google.com";"Google")')) # Outputs the text "Google" linking to http://www.google.com
workbook.save('Excel_Workbook.xls')
```

合并列和行:

```python
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
worksheet.write_merge(0, 0, 0, 3, 'First Merge') # Merges row 0's columns 0 through 3.
font = xlwt.Font() # Create Font
font.bold = True # Set font to Bold
style = xlwt.XFStyle() # Create Style
style.font = font # Add Bold Font to Style
worksheet.write_merge(1, 2, 0, 3, 'Second Merge', style) # Merges row 1 through 2's columns 0 through 3.
workbook.save('Excel_Workbook.xls')
```

设置单元格内容的对其方式:

```python
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
alignment = xlwt.Alignment() # Create Alignment
alignment.horz = xlwt.Alignment.HORZ_CENTER # May be: HORZ_GENERAL, HORZ_LEFT, HORZ_CENTER, HORZ_RIGHT, HORZ_FILLED, HORZ_JUSTIFIED, HORZ_CENTER_ACROSS_SEL, HORZ_DISTRIBUTED
alignment.vert = xlwt.Alignment.VERT_CENTER # May be: VERT_TOP, VERT_CENTER, VERT_BOTTOM, VERT_JUSTIFIED, VERT_DISTRIBUTED
style = xlwt.XFStyle() # Create Style
style.alignment = alignment # Add Alignment to Style
worksheet.write(0, 0, 'Cell Contents', style)
workbook.save('Excel_Workbook.xls')
```

为单元格议添加边框:

```python
# Please note: While I was able to find these constants within the source code, on my system (using LibreOffice,) I was only presented with a solid line, varying from thin to thick; no dotted or dashed lines.
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
borders = xlwt.Borders() # Create Borders
borders.left = xlwt.Borders.DASHED 
    DASHED虚线
    NO_LINE没有
    THIN实线
    
# May be: NO_LINE, THIN, MEDIUM, DASHED, DOTTED, THICK, DOUBLE, HAIR, MEDIUM_DASHED, THIN_DASH_DOTTED, MEDIUM_DASH_DOTTED, THIN_DASH_DOT_DOTTED, MEDIUM_DASH_DOT_DOTTED, SLANTED_MEDIUM_DASH_DOTTED, or 0x00 through 0x0D.
borders.right = xlwt.Borders.DASHED
borders.top = xlwt.Borders.DASHED
borders.bottom = xlwt.Borders.DASHED
borders.left_colour = 0x40
borders.right_colour = 0x40
borders.top_colour = 0x40
borders.bottom_colour = 0x40
style = xlwt.XFStyle() # Create Style
style.borders = borders # Add Borders to Style
worksheet.write(0, 0, 'Cell Contents', style)
workbook.save('Excel_Workbook.xls')
```

为单元格设置背景色:

```python
import xlwt
workbook = xlwt.Workbook()
worksheet = workbook.add_sheet('My Sheet')
pattern = xlwt.Pattern() # Create the Pattern
pattern.pattern = xlwt.Pattern.SOLID_PATTERN # May be: NO_PATTERN, SOLID_PATTERN, or 0x00 through 0x12
pattern.pattern_fore_colour = 5 # May be: 8 through 63. 0 = Black, 1 = White, 2 = Red, 3 = Green, 4 = Blue, 5 = Yellow, 6 = Magenta, 7 = Cyan, 16 = Maroon, 17 = Dark Green, 18 = Dark Blue, 19 = Dark Yellow , almost brown), 20 = Dark Magenta, 21 = Teal, 22 = Light Gray, 23 = Dark Gray, the list goes on...
style = xlwt.XFStyle() # Create the Pattern
style.pattern = pattern # Add Pattern to Style
worksheet.write(0, 0, 'Cell Contents', style)
workbook.save('Excel_Workbook.xls')
```

合并单元格：

```python
font = xlwt.Font()
font.name = '宋体'
font.height = font_height
# 设置单元格对齐方式
alignment = xlwt.Alignment()
# 0x01(左端对齐)、0x02(水平方向上居中对齐)、0x03(右端对齐)
alignment.horz = align_h
# 0x00(上端对齐)、 0x01(垂直方向上居中对齐)、0x02(底端对齐)
alignment.vert = align_v
# 1-自动换行,0-不自动换行
alignment.wrap = align_wrap
# 缩小字体填充
alignment.shri = align_shri

style = xlwt.XFStyle()
style.font = font
style.alignment = alignment

if borders_set is not None:
    # 设置边框
    borders = xlwt.Borders()
    # 细实线:1，小粗实线:2，细虚线:3，中细虚线:4，大粗实线:5，双线:6，细点虚线:7
    # 大粗虚线:8，细点划线:9，粗点划线:10，细双点划线:11，粗双点划线:12，斜点划线:13
    borders.left = borders_set
    borders.right = borders_set
    borders.top = borders_set
    borders.bottom = borders_set
    borders.left_colour = 8
    borders.right_colour = 8
    borders.top_colour = 8
    borders.bottom_colour = 8
    style.borders = borders

# 设置背景颜色的模式
if pattern_color is not None:
    pattern_top = xlwt.Pattern()
    pattern_top.pattern = xlwt.Pattern.SOLID_PATTERN
    pattern_top.pattern_fore_colour = pattern_color
    style.pattern = pattern_top
    return style

write_excel = xlwt.Workbook()
flight_plan_st = write_excel.add_sheet('计划')  # 添加sheet
flight_info_st = write_excel.add_sheet('动态')
# 合并第0行到第1行的第0列到第7列
style = excel_style(20*18, 0x02, 0x01)
flight_plan_st.write_merge(0, 1, 0, 6, date + "航班计划", style)
style = excel_style(20*12, 0x02, 0x01, pattern_color=22, borders_set=1)
flight_plan_st.write(2, 0, "机号", style)
flight_plan_st.write(2, 1, "性质", style)
flight_plan_st.write(2, 2, "班号", style)
flight_plan_st.write(2, 3, "飞场", style)

for i in range(0, 4):
# 设置列宽，一个中文等于两个英文等于两个字符，11为字符数，256为衡量单位
flight_plan_st.col(i).width = 14 * 245
tall_style = xlwt.easyxf('font:height 360')  # 24pt，设置行高
flight_plan_st.row(0).set_style(tall_style)
flight_plan_st.row(1).set_style(tall_style)
tall_style = xlwt.easyxf('font:height 279')  # 18pt
flight_plan_st.row(2).set_style(tall_style)
write_excel.save("test.xls")
```

```python


```

合并行列中的相同信息

```python
import xlwt
import numpy as np

def write_excel():
    '''
    第二种方式，自动判断需要合并的单元格范围
    :return:
    '''
    table_head = ['部门','姓名','联系方式','入职时间','地址']
    content = [["测试部",'小王',15933333333,'2016-02-09',"四川，成都"],
               ["测试部",'小张',15933333334,'2017-02-09','四川，雅安'],
               ["测试部",'小李',15933333335,'2015-02-09','双流'],
               ["开发部", '小熊1', 15933333336, '2012-02-09', '华阳'],
               ["开发部",'小熊2',15933333337,'2014-12-31','华阳'],
               ["市场部",'小熊3',15933333338,'2014-02-09','华阳']
               ]
    # 初始化workbook 并且添加excel Sheet
    workbook = xlwt.Workbook(encoding = 'utf-8')
    xlsheet = workbook.add_sheet("excel写入练习",cell_overwrite_ok=True)

    #写excel表头
    headlen = len(table_head)
    for i in range(headlen):
        xlsheet.write(0,i,table_head[i])


    contentRow = len(content) #列表元素个数  = 待写入内容行数

    #从content获取要写入的第一列的内容,存入列表
    first_col = []
    for i in range(contentRow):
        print(content)
        first_col.append(content[i][0])
    print("first_col",first_col)

    #去掉列表中重复元素，并且顺序不变
    nfirst_col = list(set(first_col))
    nfirst_col.sort(key=first_col.index) # sort排序与原顺序一致
    print("nfirst_col",nfirst_col)


    row = 1
    for i in nfirst_col:
        count = first_col.count(i)  # 计算元素的重复个数，比如测试 ：3
        uprange = row+count-1  #合并范围后的上行数
        xlsheet.write_merge(row,uprange,0,0,i)  #合并单元格写入内容
        row = uprange+1 #从下一行开始写入

    #获取content子列表第二个元素，循环写入excel第2列到最后开始的数据
    for row in range(contentRow):
        for col in range(1,len(content[row])):
            xlsheet.write(row+1,col,content[row][col])

    workbook.save(r'E:\excel合并写入.xls'

# write_excel()

data = [['a','e','c'],
        ['b','e','f'],
        ['a', 'i', 'j']]
arry = np.array(data)  # 列表转数组
arry = arry[arry[:,0].argsort()] # 按照第0列排序
lis = arry.tolist()  # 数组转列表
print(lis)
```

