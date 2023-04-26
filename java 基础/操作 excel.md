[TOC]

## 写在前面

因为工作原因偶然需要去对excel进行操作，匆忙临时赶工，并不是很系统的知道这块儿，所以进行一个总结以及遇到的问题

------

## poi 和 jxl 区别

poi 是apache 组织的一个子项目，主要是提供一组操作windows 文档的java api。是创建和维护操作各种符合OOXML标准和微软的复合文档格式。

jxl 全称JavaExcel 是一个开放源码项目，通过它java 开发人员可以做以下事情：

- •

  读取excel文件的内容

- •

  创建新的excel文件

- •

  更新已经存在的excel

使用该API 非windows 操作熊也可以通过纯java 应用来处理excel数据表。因为是java编写的，所以我们在web应用种可以通过JSP、Servlet来调用API实现对Exce数据表的访问

jxl 现在基本没有被维护了，相反，poi是属于Apache 开源项目的一部分，更新维护的比较好，同时poi可以支持更高版本的excel,而jxl 只支持EXCEL 2003 以及之前的版本（局限的数据量）

- 数据读取用POI，只要不涉及单元格样式的写入使用jxl效率高点。

1. 效率高，这点很好


1. 支持公式，宏，一些企业应用上会非常实用


1. 能够修饰单元格属性


1. 支持字体、数字、日期操作	

缺点：不成熟,代码不能跨平台，貌似不少同行在使用工程中还碰到让人郁闷的BUG

- POI读取效率高，但是大数据量写入Excel效率比jxl慢

- 支持Excel 95-2000的所有版本


- 生成[Excel](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/Excel/9564) 2000标准格式


- 支持字体、数字、日期操作（这点比POI要使用的方便）


- 能够修饰单元格属性


- 支持图像和图表（重要的是支持图像）

------

## POI

| 类库   | 功能                                   |      |
| ---- | ------------------------------------ | ---- |
| HSSF | 读写Microsoft Excel XLS 格式档案的库         |      |
| XSSF | 读写Microsoft Excel XLSX 格式档案的库(OOXML) |      |
| HWPF | 读写Microsoft Word DOC97格式档案的库         |      |
| XWPF | 读写Microsoft Word DOC2003格式档案的库       |      |
| HSLF | 读写Microsoft PowerPonit 格式档案的库        |      |
| HDGF | 读写Microsoft Visio 格式档案的库             |      |

### 导出数据

- 创建工作簿

  ```
  HSSFWorkbook wkb=new HSSFWorkbook();
  ```

  ​

- 建立新的sheet对象

  ```
  HSSFSheet sheet=wkb.createSheet("工单信息表");
  ```

- 生成样式

  ``````
  HSSFCellStyle style=wkb.createCellStyle();
  ``````

- 准备数据

  ```
  List<Map<String,String>> dataList=new ArrayList<>();
          for(int t=0;t<1000;t++){
              Map<String,String> temp=new HashMap<>();
              temp.put("groupid",String.valueOf(1+t));
              temp.put("productcode","abc"+String.valueOf(1+t));
              dataList.add(temp);
          }

  ```

  ​

- 填充数据

  ```for(short i=0;i
  for(short i=0;i<dataList.size();i++){
              row=sheet.createRow(i+1);
              row.createCell(0).setCellValue(dataList.get(i).get("groupid"));
              row.createCell(1).setCellValue(dataList.get(i).get("productcode"));
          }
  ```

- 导出数据

  ```
  FileOutputStream out=new FileOutputStream("D://工单信息表.xls");
  wkb.write(out);
  out.close();
  ```

  ​

### 读取数据

```
public List<List<List>> readExcel(File file) {
        try {
            //create input stream ,get data
            InputStream stream = new FileInputStream(file);
            Workbook wb = new HSSFWorkbook(stream);
            int sheetSize = wb.getNumberOfSheets();
            List<List<List>> sheetList = new ArrayList<>();
            for (int i = 0; i < sheetSize; i++) {
                List<List> outerList = new ArrayList<>();
                Sheet sheet = wb.getSheetAt(i);
                for (int j = 0; j <= sheet.getLastRowNum(); j++) {
                    List<String> list = new ArrayList<>();
                    for (int c = 0; c < sheet.getRow(j).getLastCellNum(); c++) {
                        Cell cell = sheet.getRow(j).getCell(c);
                        String str = cell.getStringCellValue();
                        list.add(str);
                    }
                    outerList.add(list);
                }
                sheetList.add(outerList);
            }
            return sheetList;

        } catch (Exception e) {
            log.info(e.getMessage());
        }
        return null;
    }
```

## 参考

> [https://www.jianshu.com/p/7afb123fc18d](https://www.jianshu.com/p/7afb123fc18d)
>
> [https://www.jianshu.com/p/5dcaa4d4e61e](https://www.jianshu.com/p/5dcaa4d4e61e)
>
> [https://zhuanlan.zhihu.com/p/66543765](https://zhuanlan.zhihu.com/p/66543765)
>
> [https://wenku.baidu.com/view/ff1b1906463610661ed9ad51f01dc281e53a5630.html?fr=sogou&_wkts_=1682491683901](https://wenku.baidu.com/view/ff1b1906463610661ed9ad51f01dc281e53a5630.html?fr=sogou&_wkts_=1682491683901)
>
> [https://www.cnblogs.com/caibixiang123/p/10164423.html](https://www.cnblogs.com/caibixiang123/p/10164423.html)
>
> https://www.iteye.com/blog/universes-854485