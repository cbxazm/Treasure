# Python With语法

## 执行顺序

![image-20200524101241375](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200524101241375.png)

## with代码正常结束

![image-20200524101634661](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200524101634661.png)

## with代码发生异常

![image-20200524101655012](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200524101655012.png)

```

def open_file():
    # 使用with打开文件
   # f=open('./static/test.txt','r',encoding='utf-8')
   # res=f.read()
   # print(res)
   # f.close()
   # with open('./static/test.txt','r',encoding='utf-8') as f:
   #     res=f.read()
   #     print(res)
   try:
       f=open('./static/test.txt','r',encoding='utf-8')
       res=f.read()
       print(res)
   except:
       pass
   finally:
       f.close()

if __name__ == '__main__':
    open_file()
```

# Python操作excel

## 参考地址

https://openpyxl.readthedocs.io/en/stable/usage.html

## 安装插件

```
pip install openpyxl
```

插入图片的时候需要安装pillow

```
pip install pillow
```

```
from datetime import datetime

from openpyxl import Workbook,load_workbook
from openpyxl.drawing.image import Image
from openpyxl.styles import colors, Font


class ExcelUtils(object):

  def __init__(self):
      self.wb=Workbook()
      self.ws=self.wb.active
      self.ws_two=self.wb.create_sheet('my form')
      self.ws.title='我的表单'
      self.ws.sheet_properties.tabColor='000000'
      self.ws_three=self.wb.create_sheet()

  def do_sth(self):
      #插入数据
      self.ws['A1']=66
      self.ws['A2']='您好'
      self.ws['A3']=datetime.now()

      #插入图片 安装pip install pillow
      # image=Image('./static/timg.jpg')
      # self.ws.add_image(image,'B1')


      #合并单元格
      # self.ws.merge_cells('A4:E5')
      # self.ws.unmerge_cells('A4:E5')

      #设置文字
      font1=Font(color=colors.BLUE)
      self.ws['A2'].font=font1
      for row in self.ws_two['A1:E5']:
          for cell in row:
              cell.value=2

      #对数据进行求和
      self.ws_two['F1']='=SUM(A1:E1)'


      self.wb.save('./static/test.xlsx')
  def read_xls(self):
      ws=load_workbook('./static/pythonTest.xlsx')
      names=ws.get_sheet_names()
      wb=ws.active
      for row in wb.rows:
          for cell in row:
              print(cell.value)


      print(names)

if __name__ == '__main__':
        client=ExcelUtils()
        # client.read_xls()
        client.read_xls()

```



# Python连接数据库

```
pip install mysqlclient
```

## excel表格数据导入数据库

```
from datetime import datetime
import MySQLdb

from openpyxl import Workbook,load_workbook
from openpyxl.drawing.image import Image
from openpyxl.styles import colors, Font


class ExcelUtils(object):

  def __init__(self):
      self.wb=Workbook()
      self.ws=self.wb.active
      self.ws_two=self.wb.create_sheet('my form')
      self.ws.title='我的表单'
      self.ws.sheet_properties.tabColor='000000'
      self.ws_three=self.wb.create_sheet()

  def do_sth(self):
      #插入数据
      self.ws['A1']=66
      self.ws['A2']='您好'
      self.ws['A3']=datetime.now()

      #插入图片 安装pip install pillow
      # image=Image('./static/timg.jpg')
      # self.ws.add_image(image,'B1')


      #合并单元格
      # self.ws.merge_cells('A4:E5')
      # self.ws.unmerge_cells('A4:E5')

      #设置文字
      font1=Font(color=colors.BLUE)
      self.ws['A2'].font=font1
      for row in self.ws_two['A1:E5']:
          for cell in row:
              cell.value=2

      #对数据进行求和
      self.ws_two['F1']='=SUM(A1:E1)'


      self.wb.save('./static/test.xlsx')
  def read_xls(self):
      ws=load_workbook('./static/pythonTest.xlsx')
      names=ws.get_sheet_names()
      wb=ws.active
      for(i,row) in enumerate(wb.rows):
          if i<1:
              continue
          year=wb['A{0}'.format(i+1)].value
          max=wb['B{0}'.format(i+1)].value
          avg=wb['C{0}'.format(i+1)].value
          # print(year)
          # print(max)
          # print(avg)
          # for row in wb.rows:
          #     for cell in row:
          #         print(cell.value)
          conn = self.get_conn()
          # # 获取游标
          cursor = conn.cursor()
          sql = 'insert into grade (year,max,avg) values({year},{max},{avg})'.format(year=year,max=max,avg=avg)
          cursor.execute(sql)
          conn.autocommit(True)
          print(conn)


      # print(names)

  def get_conn(self):
      conn=MySQLdb.connect(
          db='pythonproject',
          host='localhost',
          user='root',
          password='123',
          charset='utf8'
      )
      return conn


if __name__ == '__main__':
        client=ExcelUtils()
        # client.read_xls()
        client.read_xls()

```

## 数据库数据导出到excel

```
from datetime import datetime
import MySQLdb

from openpyxl import Workbook,load_workbook
from openpyxl.drawing.image import Image
from openpyxl.styles import colors, Font


class ExcelUtils(object):

  def __init__(self):
      self.wb=Workbook()
      self.ws=self.wb.active
      self.ws_two=self.wb.create_sheet('my form')
      self.ws.title='我的表单'
      self.ws.sheet_properties.tabColor='000000'
      self.ws_three=self.wb.create_sheet()

  def do_sth(self):
      #插入数据
      self.ws['A1']=66
      self.ws['A2']='您好'
      self.ws['A3']=datetime.now()

      #插入图片 安装pip install pillow
      # image=Image('./static/timg.jpg')
      # self.ws.add_image(image,'B1')


      #合并单元格
      # self.ws.merge_cells('A4:E5')
      # self.ws.unmerge_cells('A4:E5')

      #设置文字
      font1=Font(color=colors.BLUE)
      self.ws['A2'].font=font1
      for row in self.ws_two['A1:E5']:
          for cell in row:
              cell.value=2

      #对数据进行求和
      self.ws_two['F1']='=SUM(A1:E1)'


      self.wb.save('./static/test.xlsx')
  def read_xls(self):
      ws=load_workbook('./static/pythonTest.xlsx')
      names=ws.get_sheet_names()
      wb=ws.active
      for(i,row) in enumerate(wb.rows):
          if i<1:
              continue
          year=wb['A{0}'.format(i+1)].value
          max=wb['B{0}'.format(i+1)].value
          avg=wb['C{0}'.format(i+1)].value
          # print(year)
          # print(max)
          # print(avg)
          # for row in wb.rows:
          #     for cell in row:
          #         print(cell.value)
          conn = self.get_conn()
          # # 获取游标
          cursor = conn.cursor()
          sql = 'insert into grade (year,max,avg) values({year},{max},{avg})'.format(year=year,max=max,avg=avg)
          cursor.execute(sql)
          conn.autocommit(True)
          print(conn)


      # print(names)

  def export_xls(self):
      #从mysql导出数据到excel
     conn=self.get_conn()
     cursor=conn.cursor()
     sql='select year,max,avg from grade'
     cursor.execute(sql)
     rows=cursor.fetchall()
     wb=Workbook()
     ws=wb.active
     for (i,row) in enumerate(rows):
         # print(row)
         ws['D{0}'.format(i+1)]=row[0]
         ws['E{0}'.format(i+1)]=row[1]
         ws['F{0}'.format(i+1)]=row[2]
     wb.save('./static/aa.xlsx')


  def get_conn(self):
      conn=MySQLdb.connect(
          db='pythonproject',
          host='localhost',
          user='root',
          password='123',
          charset='utf8'
      )
      return conn


if __name__ == '__main__':
        client=ExcelUtils()
        # client.read_xls()
        # client.read_xls()
        client.export_xls()

```

