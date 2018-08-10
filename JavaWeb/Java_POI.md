---
title: Java 两种打印方式--POI
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---
**wyoung**

## 前言 ##
最近在做的项目使用到了关于打印的一些功能，记录一下两个方式的主要实现以及在实现过程中遇到的难点。主要有两种：
	
### 1.使用POI打印文件 ###

使用POI一般以输出EXCEL为主，输出文件可以自己在代码中制作，也可以用做好的模板以读取的方式。
	

 - 用读取模板的方式

		
```

 		// 获取模板
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes())
                .getRequest();
        String filePath = request.getServletContext().getRealPath(Constant.TEMPLATE_TABLE_SOUTPUT);
        // 读取模板
        File f = new File(filePath);
        POIFSFileSystem fs = new POIFSFileSystem(new FileInputStream(f));
        HSSFWorkbook wb = new HSSFWorkbook(fs);
        // 读取sheet内容
        HSSFSheet sheet = wb.getSheetAt(0);
``` 
打印的内容有时是每页固定的条目，这时可以使用**google guava** 和**apache commons collections**。
#### 1.google guava

``` 
public void googleCollection() {
		//第一种是使用iterator取出分割好的collections
            Collection<Integer> intCollection = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
			//每3个collerction为一组
            Iterable<List<Integer>> subSets = Iterables.partition(intCollection, 3);
			//取得第一组
            List<Integer> firstPartition = subSets.iterator().next();

        }
		
``` public void googleList () {
		//第二种
            List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7);
            List<List<Integer>> subSets = Lists.partition(intList, 3);

            List<Integer> lastPartition = subSets.get(2);
        }
```
#### 2.	apache commons collections

``` 
public void apacheCollection () {
            List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7);
            List<List<Integer>> subSets = ListUtils.partition(intList, 3);

            List<Integer> lastPartition = subSets.get(2);
        }

```
在应用中一个实现案例,每页打印**固定**7条行项目，底栏不变:

```  int count = 0;
        int size = 0;
        if (item.size() % 7 == 0) {
            size = item.size() / 7;
        } else {
            size = item.size() / 7 + 1;
        }
        List<Map<String, Object>> arrayitem = Lists.newArrayList(item);
        Iterable<List<Map<String, Object>>> subList = Iterables.partition(arrayitem, 7);
        Iterator<List<Map<String, Object>>> iterator = subList.iterator();
        while (iterator.hasNext()) {
            List<Map<String, Object>> printItem = iterator.next();
            // 在相应的单元格进行赋值--header
			 /*
            * 
            */

         	//在相应的单元格进行赋值--item
		 	 /*
            * 
            */

            //底栏
            /*
            * 
            */
	
			//复制下一页
			//先判断是否还有list
            if (count < size - 1) {
				//根据每页行数
                for (int i = 1; i < 17; i++) {
                    if (13 <= i && i <= 16 || i < 6) {
						//复制行信息和value
                        Common.copyRow(wb, sheet.getRow(i), sheet.createRow(16 * (count + 1) + i), true);
                        sheet.getRow(16 * (count + 1) + i).setHeight(sheet.getRow(i).getHeight());
                    } else {
						//仅复制行信息
                        Common.copyRowHightWithoutValue(wb, sheet.getRow(i), sheet.createRow(16 * (count + 1) + i),
                                true);
                        sheet.getRow(16 * (count + 1) + i).setHeight(sheet.getRow(i).getHeight());

                    }
                }
            }

            sheet.setRowBreak(16 + count * 16);
            count++;

        }
        // 设置打印区域
        wb.setPrintArea(0, 0, 10, 0, sheet.getLastRowNum());
		// 修改模板内容导出新模板
        FileOutputStream out = new FileOutputStream(OutXlsxFile);
        wb.write(out);
        out.close();
```
如果使用PDF格式，则需要利用OpenOffice:

```  
// 转换pdf
//考虑到实际生产环境,端口号放在了配置文件properties中
        OpenOfficeConnection connection = new 			   SocketOpenOfficeConnection(Integer.parseInt(PropertiesUtil.getInstance().getPort()));
        connection.connect();
        DocumentConverter converter = new OpenOfficeDocumentConverter(connection);
        File inputFile = new File(OutXlsxFile);
        File outputFile = new File(OutPdfFile);
        converter.convert(inputFile, outputFile);
        connection.disconnect();
```
最后输出文件名:

``` 
		//根据不用的系统
		String[] as = OutPdfFile.split(PropertiesUtil.getInstance().getSeparator());
        String fileName = as[as.length - 1];
		
```
如果是根据内容复制条目信息，则有可能遇到复制行数超过4000行的情况。此时会报**Caused by: java.lang.IllegalStateException**异常，是由于cell styles创建过多造成,此时推荐使用google guava缓存样式。

``` /**
	 * 行复制功能
	 *
	 * @param fromRow
	 * @param toRow
	 * @param copyValueFlag(true时复制value)
	 * @throws Exception
	 */
	@SuppressWarnings("rawtypes")
	public static void copyRow(Workbook wb, Row fromRow, Row toRow, boolean copyValueFlag) throws Exception {
		// toRow.setHeight(fromRow.getHeight());
		for (Iterator cellIt = fromRow.cellIterator(); cellIt.hasNext();) {
			Cell tmpCell = (Cell) cellIt.next();
			Cell newCell = toRow.createCell(tmpCell.getColumnIndex());
			//复制单元格
			CopyCell(wb, tmpCell, newCell, copyValueFlag);
		}
		Sheet worksheet = fromRow.getSheet();
		int total = worksheet.getNumMergedRegions();
		for (int i = 0; i < total; i++) {
			CellRangeAddress cellRangeAddress = worksheet.getMergedRegion(i);
			if (cellRangeAddress.getFirstRow() == fromRow.getRowNum()) {
				CellRangeAddress newCellRangeAddress = new CellRangeAddress(toRow.getRowNum(),
						(toRow.getRowNum() + (cellRangeAddress.getLastRow() - cellRangeAddress.getFirstRow())),
						cellRangeAddress.getFirstColumn(), cellRangeAddress.getLastColumn());
				worksheet.addMergedRegionUnsafe(newCellRangeAddress);
			}
		}
	}
``` /**
	 * @Description: TODO(超过4000时使用的)</br>
	 * @Title: CopyCell </br>
	 * @param @param
	 *            wb
	 * @param @param
	 *            srcCell
	 * @param @param
	 *            distCell
	 * @param @param
	 *            copyValueFlag
	 * @param @throws
	 *            Exception 设定文件</br>
	 * @return void 返回类型</br>
	 * @throws</br>
	 * @author wyang
	 */
	public static void CopyCell(Workbook wb, Cell srcCell, Cell distCell, boolean copyValueFlag) throws Exception {
		//样式缓存
		CellStyle newStyle = CreateCellStyle(wb, srcCell.getCellStyle());
		
		CellStyle srcStyle = srcCell.getCellStyle();
		newStyle.cloneStyleFrom(srcStyle);
		newStyle.setFont(wb.getFontAt(srcStyle.getFontIndex()));
		// 样式
		distCell.setCellStyle(newStyle);
		// 评论
		if (srcCell.getCellComment() != null) {
			distCell.setCellComment(srcCell.getCellComment());
		}
		// 不同数据类型处理
		CellType srcCellType = srcCell.getCellTypeEnum();
		distCell.setCellType(srcCellType);
		if (copyValueFlag) {
			if (srcCellType == CellType.NUMERIC) {
				if (DateUtil.isCellDateFormatted(srcCell)) {
					distCell.setCellValue(srcCell.getDateCellValue());
				} else {
					distCell.setCellValue(srcCell.getNumericCellValue());
				}
			} else if (srcCellType == CellType.STRING) {
				distCell.setCellValue(srcCell.getRichStringCellValue());
			} else if (srcCellType == CellType.BLANK) {

			} else if (srcCellType == CellType.BOOLEAN) {
				distCell.setCellValue(srcCell.getBooleanCellValue());
			} else if (srcCellType == CellType.ERROR) {
				distCell.setCellErrorValue(srcCell.getErrorCellValue());
			} else if (srcCellType == CellType.FORMULA) {
				distCell.setCellFormula(srcCell.getCellFormula());
			} else {
			}
		}
	}
``` /**
	 * @Title: getCellStyleCache </br>
	 * @Description: TODO(样式缓存)</br>
	 * @param @param
	 *            fromStyle
	 * @param @return
	 * @param @throws
	 *            Exception 设定文件</br>
	 * @return CellStyle 返回类型</br>
	 * @throws</br>
	 */
	private static CellStyle getCellStyleCache(final CellStyle fromStyle) throws Exception {
		Cache<Integer, CellStyle> cache = CacheBuilder.newBuilder().maximumSize(10)// 设置大小，条目数
				.expireAfterWrite(20, TimeUnit.SECONDS)// 设置失效时间，创建时间
				.expireAfterAccess(20, TimeUnit.HOURS) // 设置时效时间，最后一次被访问
				.removalListener(new RemovalListener<Object, Object>() { // 移除缓存的监听器
					public void onRemoval(RemovalNotification<Object, Object> notification) {
						System.out.println("有缓存数据被移除了");
					}
				}).build(new CacheLoader<Integer, CellStyle>() { // 通过回调加载缓存
					@Override
					public CellStyle load(Integer name) throws Exception {
						CellStyle newStyle = fromStyle;
						return newStyle;
					}
				});
		// cache.invalidateAll();
		return fromStyle;
		return null;
	}
```
此外还有一些关POI对Row和Region的处理就不一一述说了。