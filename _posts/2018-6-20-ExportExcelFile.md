---
layout: post
title: "编辑器扩展之Unity导出Excel表"
date: 2018-6-20
image: '/assets/img/'
description: '在Unity里导出并赋值给Excel表'
tags:
- Excel
categories:
- Unity编辑器
---

### # 基于EEPlus在Unity编辑器中导出Excel文件
- [官方链接](https://epplus.codeplex.com/releases/view/118053)  
- [百度云下载地址](https://pan.baidu.com/s/1Y9QZYyUs1erI5nd_pd-VdA) 

### # 一些api的使用
* 通过FileInfo来得到Excel文件
```csharp
string excelName = "uiExcel.xlsx";
string path =  Application.dataPath + "/" + excelName;
FileInfo file = new FileInfo(path);
```
* 通过ExcelPackage打开文件
```csharp
ExcelPackage package = new ExcelPackage(file);
```
* 在Excel文件里添加sheet
```csharp
ExcelWorksheet worksheet = package.Workbook.Worksheets.Add("sheet1");
```
* 添加列名
```csharp
worksheet.Cells[1, 1].Value = "ID";
worksheet.Cells[1, 2].Value = "名字";
worksheet.Cells[1, 3].Value = "个数";
worksheet.Cells[1, 4].Value = "价格";
```
* 添加行数据
```csharp
worksheet.Cells["A2"].Value = 1001;
worksheet.Cells["B2"].Value = "李子";
worksheet.Cells["C2"].Value = 5;
worksheet.Cells["D2"].Value = 3;
```
* 保存
```csharp
package.Save();
```
* 设置单元格对齐方式
```csharp
worksheet.Cells.Style.HorizontalAlignment = OfficeOpenXml.Style.ExcelHorizontalAlignment.Center;
```
* 设置单元格宽度
```csharp
worksheet.Column(1).Width = 50;
worksheet.Column(2).Width = 50;
worksheet.Column(3).Width = 50;
```
* 设置Excel字体
```csharp
worksheet.Cells.Style.Font.Size = 20;
worksheet.Cells.Style.Font.Name = "Calibri";
```
* 设置单元格数字格式
```csharp
worksheet.Cells["A1:B3"].Style.NumberFormat.Format = "#,##0";
worksheet.Cells[1,1,3,2].Style.NumberFormat.Format = "#,##0";
```
* 设置单元格背景色
```csharp
var fill = cell.Style.Fill;
fill.PatternType = ExcelFillStyle.Solid;
fill.BackgroundColor.SetColor(Color.Gray);
```
----------

* 具体代码：  
```csharp
using UnityEngine;
using UnityEditor;
using OfficeOpenXml;
using System.IO;
public class BuildAsset : Editor
{
    [MenuItem("Tools/Export Excel")]
    public static void ExportXlsxFile()
    {
        //指定默认路径
        //string path = Application.dataPath + "/" + "UIPrefabExcel.xlsx";
        //自己选择一个路径
        string path = EditorUtility.SaveFilePanel("Save Excel File", "", "UIPrefabExcel.xlsx", "xlsx");
        FileInfo newFile = new FileInfo(path);
        if (newFile.Exists)
        {
            newFile.Delete();
            newFile = new FileInfo(path);
        }

        //通过ExcelPackage打开文件
        using (ExcelPackage package = new ExcelPackage(newFile))
        {
            //添加sheet
            ExcelWorksheet worksheet = package.Workbook.Worksheets.Add("sheet1");
            //添加列名
            worksheet.Cells[1, 1].Value = "ID";
            worksheet.Cells[1, 2].Value = "名字";
            worksheet.Cells[1, 3].Value = "个数";
            worksheet.Cells[1, 4].Value = "价格";

            //添加行数据
            worksheet.Cells["A2"].Value = 1001;
            worksheet.Cells["B2"].Value = "李子";
            worksheet.Cells["C2"].Value = 5;
            worksheet.Cells["D2"].Value = 3;
            //添加行数据
            worksheet.Cells["A3"].Value = 1002;
            worksheet.Cells["B3"].Value = "山竹";
            worksheet.Cells["C3"].Value = 5;
            worksheet.Cells["D3"].Value = 10;
            //添加行数据
            worksheet.Cells["A4"].Value = 1003;
            worksheet.Cells["B4"].Value = "苹果";
            worksheet.Cells["C4"].Value = 20;
            worksheet.Cells["D4"].Value = 5.5;

            package.Save();
            Debug.Log("Create Success!");
        }
    }
}
```
---

