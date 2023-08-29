---
layout:     post
title:      python常用库
subtitle:   openpyxl操作excel
date:       2010-05-07
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - openpyxl
---


```
from openpyxl import *

class read_excel(object):
    '''
    ###读取excel表格内容
    '''

    def __init__(self,file_name,table_name):
        '''
        file_name表文excel文件名
        table_name表示excel的表名
        '''
        self.file=file_name
        self.sheet=table_name
        self.excel=load_workbook(self.file)
        self.table=self.excel[self.sheet]
    
    def get_row_col_num(self):
        '''
        获取excel表的总行数和总列数
        row代表行数
        col代表列数
        '''
        rows=self.table.max_row
        cols=self.table.max_column
        row_cols=[rows,cols]
        return row_cols

    def get_cell_value(self,row,column):
        '''
        获取某个单元格的值
        '''
        cell_value=self.table.cell(row=row,column=column).value
        return cell_value


    def get_row_value(self,row):
        '''
        获取某一行的所有值
        '''
        row_data=[]
        column=self.table.max_column
        for i in range(1,column+1):
            cell_value=self.table.cell(row=row,column=i).value
            row_data.append(cell_value)
        return row_data

    def get_column_value(self,column):
        '''
        获取某一列的所有值
        '''
        column_data=[]
        rows=self.table.max_row
        for i in range(1,rows+1):
            cell_value=self.table.cell(row=i,column=column).value
            column_data.append(cell_value)
        return column_data

    def set_cell_value(self,row,column,cellvalue):
        '''
        设置某个单元格的值
        '''
        try:
            self.table.cell(row=row,column=column).value=cellvalue
            self.excel.save(r'C:\Users\Administrator\Desktop\紫薇项目编号.xlsx')
        except:
            self.table.cell(row=row, column=column).value = "writefail"
            self.excel.save(r'C:\Users\Administrator\Desktop\紫薇项目编号.xlsx')

    def save(self):
        '''
        保存文件
        '''
        self.excel.save(r'C:\Users\Administrator\Desktop\紫薇项目编号.xlsx')
if __name__ == "__main__":
    

    a=read_excel(r'C:\Users\Administrator\Desktop\紫薇项目编号.xlsx','Sheet1')
    b=a.get_row_col_num()
    c=a.get_row_value(6)
    d=a.get_column_value(2)
    a.set_cell_value(1,4,1)
    a.save()

```
