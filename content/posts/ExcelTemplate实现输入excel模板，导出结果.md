---
title: ExcelTemplate实现输入excel模板，导出结果
date: 2020-07-30T19:21:23+08:00
lastmod: 2020-07-30T19:21:23+08:00
author: Dreamsfuture
authorlink: https://dreamsfutureblog.utools.club/
cover: /img/excel_template.jpg
categories: ["Backend"]
tags: ["Java", "Backend"]
# showcase: true
draft: true
---

java 如何实现输出Excel模板，然后根据需要替换掉其中的数据，导出我们需要的Excel文件

<!--more-->





## 1. 代码功能(function)

提供输入一个excel模板，然后替换掉部分数据，之后再输出一个excel的文档
Propose a method that input a excel template and output a excel with some datas with which you want to replace the placeholder

## 2. 实现方法(code implement)

excel需要替换的数据，一定要用占位符“${}”包括，这样才可以替换，当然如果你想使用自己定义的占位符，请自行修改代码中的replaceTemplateWithSpecifiedData方法的代码

First, input excel template which contains some placeholders with "${}" that will be replaced with what you want. If you want to use your own placeholder, you should modify the code which is in the method replaceTemplateWithSpecifiedData.

## 代码

```java

public class ExcelTemplate {

    private SXSSFWorkbook writeWorkbook;
    private XSSFWorkbook readWorkbook;
    private XSSFSheet readSheet;

    public ExcelTemplate(String filePath) {

        try {
            readWorkbook = new XSSFWorkbook(new FileInputStream(filePath));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public ExcelTemplate(InputStream inputStream) {
        try {
            ZipSecureFile.setMinInflateRatio(-1.0d);
            readWorkbook = new XSSFWorkbook(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    public String getData(int sheetIndex, int row, int column) {

        readSheet = readWorkbook.getSheetAt(sheetIndex);
        String data = "";
        Row r = readSheet.getRow(row);
        Cell c = r.getCell(column, Row.MissingCellPolicy.RETURN_BLANK_AS_NULL);

        if (c == null) {
        } else {
            if (r.getCell(column).getCellType() == HSSFCell.CELL_TYPE_STRING) {
                data = r.getCell(column).getStringCellValue();
            } else if (r.getCell(column).getCellType() == HSSFCell.CELL_TYPE_NUMERIC) {
                int intData = (int) r.getCell(column).getNumericCellValue();
                data = Integer.toString(intData);
            }
        }
        return data;
    }


    public void replaceTemplateWithSpecifiedData(int sheetIndex, Map<String, String> replaceDataMap) throws Exception {
        readSheet = readWorkbook.getSheetAt(sheetIndex);
        if (readSheet != null) {
            this.replaceTemplateWithSpecifiedData(readSheet.getSheetName(), replaceDataMap);
        }
    }

    public void replaceTemplateWithSpecifiedData(String sheetName, Map<String, String> replaceDataMap) throws Exception {

        readSheet = readWorkbook.getSheet(sheetName);
        if (readSheet == null) {
            throw new Exception("sheet with name " + sheetName + " not found");
        }
        readSheet.setForceFormulaRecalculation(true);

        StrSubstitutor substitutor = new StrSubstitutor(replaceDataMap, "${", "}");

        Iterator<Row> rowIterator = readSheet.iterator();
        while (rowIterator.hasNext()) {
            Row row = rowIterator.next();

            // For each row, iterate through all the columns
            Iterator<Cell> cellIterator = row.cellIterator();
            while (cellIterator.hasNext()) {
                Cell cell = cellIterator.next();
                String cellValue = null;
                try {
                    cellValue = cell.getStringCellValue();
                } catch (Exception e) {
                    try {
                        cellValue = String.valueOf(cell.getNumericCellValue());
                    } catch (Exception e1) {
                        e1.printStackTrace();
                    }
                    e.printStackTrace();
                }

                if (!StringUtils.isEmpty(cellValue)) {
                    cell.setCellValue(substitutor.replace(cellValue));
                }
            }
        }
    }

    /**
     * 清除所有sheet中的占位符
     */
    public void clearRestOfPlaceholder() {
        Iterator<Sheet> sheetIterator = readWorkbook.sheetIterator();
        while (sheetIterator.hasNext()) {
            XSSFSheet xssfSheet = (XSSFSheet) sheetIterator.next();

            xssfSheet.setForceFormulaRecalculation(true);

            Iterator<Row> rowIterator = xssfSheet.iterator();
            while (rowIterator.hasNext()) {
                Row row = rowIterator.next();

                // For each row, iterate through all the columns
                Iterator<Cell> cellIterator = row.cellIterator();
                while (cellIterator.hasNext()) {
                    Cell cell = cellIterator.next();
                    String cellValue = null;
                    try {
                        cellValue = cell.getStringCellValue();
                    } catch (Exception e) {
                        try {
                            cellValue = String.valueOf(cell.getNumericCellValue());
                        } catch (Exception e1) {
                            e1.printStackTrace();
                        }
                        e.printStackTrace();
                    }

                    if (!StringUtils.isEmpty(cellValue)) {
                        if (cellValue.startsWith("${") && cellValue.endsWith("}")) {
                            cell.setCellValue("");
                        }
                    }
                }
            }
        }
    }

    /**
     * 清除指定sheet中剩余的占位符
     *
     * @param sheetName
     */
    public void clearRestOfPlaceholder(String sheetName) throws Exception {
        int sheetIndex = readWorkbook.getSheetIndex(sheetName);
        this.clearRestOfPlaceholder(sheetIndex);
    }

    /**
     * 清除指定sheet中剩余的占位符
     *
     * @param sheetIndex
     */
    public void clearRestOfPlaceholder(int sheetIndex) throws Exception {
        readSheet = readWorkbook.getSheetAt(sheetIndex);
        if (readSheet == null) {
            throw new Exception("sheet with index " + sheetIndex + " not found");
        }
        readSheet.setForceFormulaRecalculation(true);

        Iterator<Row> rowIterator = readSheet.iterator();
        while (rowIterator.hasNext()) {
            Row row = rowIterator.next();

            // For each row, iterate through all the columns
            Iterator<Cell> cellIterator = row.cellIterator();
            while (cellIterator.hasNext()) {
                Cell cell = cellIterator.next();
                String cellValue = null;
                try {
                    cellValue = cell.getStringCellValue();
                } catch (Exception e) {
                    try {
                        cellValue = String.valueOf(cell.getNumericCellValue());
                    } catch (Exception e1) {
                        e1.printStackTrace();
                    }
                    e.printStackTrace();
                }

                if (!StringUtils.isEmpty(cellValue)) {
                    if (cellValue.startsWith("${") && cellValue.endsWith("}")) {
                        cell.setCellValue("");
                    }
                }
            }
        }
    }

    public void writeToFile(String filePath) throws IOException {
        if (StringUtils.isEmpty(filePath)) {
            return;
        }
        FileOutputStream fout = new FileOutputStream(filePath);
        this.writeToFile(fout);
    }

    public void writeToFile(FileOutputStream fileOutputStream) throws IOException {
        if (fileOutputStream == null) {
            return;
        }
        try {
            writeWorkbook = new SXSSFWorkbook(readWorkbook);
            writeWorkbook.write(fileOutputStream);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            this.close();
        }
    }

    public void close() throws IOException {
        readWorkbook.close();
        writeWorkbook.close();
    }
}

```
