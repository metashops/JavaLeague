（1）MID函数　(left right函数同理）

功能：从一个文本字符串的指定位置开始，截取指定数目的字符。　

格式：MID(text,start_num,num_chars)　

参数说明：text代表一个文本字符串；start_num表示指定的起始位置；num_chars表示要截取的数目。

（2）函数名称：COUNTIF 　　

主要功能：统计某个单元格区域中符合指定条件的单元格数目。 　　

使用格式：COUNTIF(Range,Criteria) 　　

参数说明：Range代表要统计的单元格区域；Criteria表示指定的条件表达式。　　 　　

（3）VLOOKUP函数　　

功能：在数据表的首列查找指定的数值，并由此返回数据表当前行中指定列处的数值。　　

使用格式：VLOOKUP(lookup_value,table_array,col_index_num,range_lookup)　　

```
参数说明：Lookup_value代表需要查找的数值；Table_array代表需要在其中查找数据的单元格区域；Col_index_num为在table_array区域中待返回的匹配值的列序号（当Col_index_num为2时,返回table_array第2列中的数值，为3时，返回第3列的值……）；Range_lookup为一逻辑值，如果为TRUE或省略，则返回近似匹配值，也就是说，如果找不到精确匹配值，则返回小于lookup_value的最大数值；如果为FALSE，则返回精确匹配值，如果找不到，则返回错误值#N/A。　　
```

