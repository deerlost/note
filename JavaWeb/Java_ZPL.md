---
title: Java 两种打印方式--JPL
tags: JavaWeb，打印,JPL,ZPL
grammar_cjkRuby: true
---
**wyoung**

## 前言 ##
另一种关于Java常用的打印功能是JPL，也就是常用的条形码和二维码打印，这里简称标签打印。
### 2.使用JPL打印文件 ###

``` 
public String mdPrint(List<Map> dataList, String type) {
        String templateType = "";    //如果标签需要输出EXCEL文件.则读取标签模版
        @SuppressWarnings("rawtypes")
        List<Map> listInputData = new ArrayList<Map>();
        Boolean flag = false;
        for (Map<String, String> data : dataList) {
			//类型判断
            //批次二维码打印
            if (type.equals(Constant.TYPE_1)) {
                try {
                    flag = this.batchPrintForCU(data);
                    if (flag) {
                        flag = false;
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
                //仓位标签
                //    listInputData.add(setMapToPalletLabel(data));
                templateType = Constant.TEMPLATE_LABEL_BATCH;
            }
            //序列号二维码打印
            if (type.equals(Constant.TYPE_2)) {
                try {
                    flag = this.SNNumberPrintForCU(data);
                    if (flag) {
                        flag = false;
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
                //包装物SN标签
                //    listInputData.add(setMapToPackegeLabel(data));
                templateType = Constant.TEMPLATE_LABEL_POSITION;
            }
            //打包码打印
            if (type.equals(Constant.TYPE_3)) {
                try {
                    flag = this.packagePrintForCU(data);
                    if (flag) {
                        flag = false;
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
                //仓位标签
                // listInputData.add(setMapToPositionLabel(data));
                templateType = Constant.TEMPLATE_LABEL_POSITION;
            }
        }
        //   HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.currentRequestAttributes()).getRequest();
        //  String fileName = this.printLabelFor(dataList, request.getServletContext().getRealPath(templateType), new CULabelImpl(), type);
        return null;
```
调用打印机的部分：

``` 
 private boolean batchPrintForCU(Map<String, String> data) {
        ZplPrint zplPrint = new ZplPrint();

        zplPrint.zplInit(data.get("print_name"), 640);

        // String content = Constant.ANDROID_TYPE_1 + data.get("content");
        String content = data.get("content");

        // 左右 上下  大小
        zplPrint.setQRcode("280", "40", "2", "6", content);

        String[] array = data.get("content").split(",");

        int[] xy = new int[]{200, 300};
        xy = zplPrint.setLabelValue(xy, 0, "编号", array[0]);

        int[] z = new int[]{200, 340};
        xy = zplPrint.setLabelValue(z, 0, "描述", array[1]);
        /*int[] z = new int[]{100, 300};
        z = zplPrint.setLabelValue(z, 0, "描述", data.get("content"));*/


        String zplString = zplPrint.getZplString();

        System.out.println("ZPL完整代码 : " + zplString);

        return zplPrint.createPrintJob(zplString);
    }
```
公司使用的是斑马打印机，关于打印内容的生成需要用到ZPL.

``` 
public class ZplPrint {

	private static final Logger logger = LoggerFactory.getLogger(ZplPrint.class);

	private PrintService printService = null;// 打印机服务
	private String printerURI = null;// 打印机完整路径

	private int cnCharSize = 25; // 中文字符尺寸
	private int enCharSizeX = 15; // 英文字符尺寸
	private int enCharSizeY = 25; // 英文字符尺寸

	private int lineSep = 15;
	private int width = 762; // 打印纸宽度 x

	private String content = "";

	/**
	 * 初始化方法
	 * @param printerURI 打印机路径
	 */
	private void init(String printerURI) {
		this.printerURI = printerURI;
		PrintService[] services = PrintServiceLookup.lookupPrintServices(null, null);
		if (services != null && services.length > 0) {
			for (PrintService service : services) {
				if (printerURI.equals(service.getName())) {
					printService = service;
					break;
				}
			}
		}
		if (printService == null) {
			System.out.println("没有找到打印机：[" + printerURI + "]");
			// 循环出所有的打印机
			if (services != null && services.length > 0) {
				logger.info("可用的打印机列表：");
				for (PrintService service : services) {
					logger.info("[" + service.getName() + "]");
				}
			}
		} else {
			logger.info("找到打印机：[" + printerURI + "]");
			logger.info("打印机名称：[" + printService.getAttribute(PrinterName.class).getValue() + "]");
		}
	}

	/**
	 * 打印功能
	 * @param zpl 完整的ZPL
	 */
	public boolean createPrintJob(String zpl) {
		if (printService == null) {
			logger.error("打印出错：没有找到打印机：[" + printerURI + "]");
			return false;
		}
		DocPrintJob job = printService.createPrintJob();
		byte[] by = null;
		try {
			by = zpl.getBytes("GB18030");
		} catch (UnsupportedEncodingException e1) {
			e1.printStackTrace();
		}
		DocFlavor flavor = DocFlavor.BYTE_ARRAY.AUTOSENSE;
		Doc doc = new SimpleDoc(by, flavor, null);
		try {
			job.print(doc, null);
			logger.info("已打印");
			return true;
		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}

	/**
	 * 设置宽度
	 * @param width
	 */
	private void setWidth(int width) {
		this.width = width;
	}

	/**
	 * 设置二维码
	 * @param qrx
	 * @param qry
	 * @param content
	 * @return
	 */
	public void setQRcode(String qrx,String qry,String type,String size,String qrValue){
		String QRcode = "^FO%s,%s^BQ,%s,%s^FDQA,${data}^FS";
		QRcode = String.format(QRcode, qrx, qry,type,size);
		QRcode = QRcode.replace("${data}", qrValue);
		content = content +  QRcode  + "^FWR";
	}

	public void getQRcode(String path,String str,String qrx,String qry,String filename,String x,String y){
		String QRcode = "~DY%s,A,P,%s^FO%s,%s^XGR:%s,%s,%s^FS ";
		QRcode = String.format(QRcode, path,str,qrx, qry,filename,x,y);
		content = content +  QRcode;
	}

	/**
	 * 设置一维码
	 * @param qrx
	 * @param qry
	 * @param type
	 * @param size
	 * @param codeValue
	 */
	public void setCode128(String qrx,String qry,String type,String size,String codeValue) {
		String code128 = "^FO%s,%s^BY%s^BCN,%s,N,N,A^FD${data}^FS";
		code128 = String.format(code128, qrx, qry,type,size);
		code128 = code128.replace("${data}", codeValue);
		content = content +  code128 + "^FWR";
	}

	/**
	 * 打印参数接入
	 * @date 2017年10月31日
	 * @author 高海涛
	 */
	public void zplInit(String printName ,int width) {
		this.init(printName);
		this.setWidth(width);

	}

	/**
	 * 设置标签值
	 *
	 * @param p
	 * @param xy
	 * @param label1
	 * @param value1
	 * @return
	 */
	public int[] setLabelValue(int[] xy,int c, String label, String value) {
		int initX= xy[0];
		xy = this.setText(label, xy);
		xy[0] = initX+ 50 + c;
		xy = this.setText(":", xy);
		xy[0] = initX + 70 + c;
		xy = this.setText(value, xy);
		xy[0] = initX;
		xy[1] = xy[1] +  cnCharSize + lineSep;
		return xy;
	}


	/**
	 * 获取完整的ZPL
	 *
	 * @return
	 */
	public String getZplString() {
		return Constant.ZPL_BEGIN + content +"^CI0^PQ1" + Constant.ZPL_END;
	}
	public String getZpl() {
		return Constant.ZPL_BEGIN + content + Constant.ZPL_END;
	}




	/**
	 * 判断字符串中英文
	 * @param ch
	 * @return
	 */
	private boolean checkChar(char ch) {
		if ((ch + "").getBytes().length == 1) {
			return false; // 英文
		} else {
			return true;// 中文
		}
	}

	/**
	 * 设置文本
	 * @param str
	 * @param xy
	 * @return
	 */
	private int[] setText(String str, int[] xy) {
		int x = xy[0];
		int y = xy[1];
		if (str != null) {
			char[] charArray = str.toCharArray();
			int initX = x;
			for (int i = 0; i < charArray.length;i++) {
				char c = charArray[i];
				if (checkChar(c)) {
					setChar(String.valueOf(c), x, y, true);
					x = x + cnCharSize;
				} else {
					setChar(String.valueOf(c), x, y, false);
					x = x + enCharSizeX;
				}

				if (x >= width) {
					x = initX;
					y = y + cnCharSize + lineSep;
				}
			}
		}

		return new int[] { x, y };
	}

	/**
	 * 字符(包含数字)
	 * @param str 字符串
	 * @param x x坐标
	 * @param y y坐标
	 * @param cn 是否为中文
	 */
	private void setChar(String str, int x, int y, boolean cn) {
		if (cn) {
			content = content + "^CI14^FO" + x + "," + y + "^A1N," + cnCharSize + "," + cnCharSize + "^FD" + str + "^FS";
		} else {
			content = content + "^CI0^FO" + x + "," + y + "^A1N," + enCharSizeY + "," + enCharSizeX + "^FD" + str + "^FS";
		}

	}

}

```
