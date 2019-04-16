

#紫为云行人SDK 文档
---
### 修订历史

编号  | 修订内容描述  |   修订时间 | 修订后的版本号 |修订人| 批准人
-------|--------------|-----------|-----------------|-------|---------------------
1|初稿 |2019.04.11 |v1.0.00|  |
---

## 行人SDK使用说明
---

### 简介

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本文档用于说明行人C++SDK的相关技术内容。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要包括SDK授权说明、功能列表、开发建议以及历史版本说明等。

---
### 授权说明

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SDK须授权通过后才能正常使用。开发者需要使用应用账户下的激活码为指定的机器设备获取授权信息。当前支持两种授权方式：

1.  离线授权，开发者使用激活码和机器指纹信息在门户网站上申请获取授权文件，然后将授权文件放到SDK能够访问的路径下。在使用SDK功能前，调用SDK接口设置正确的授权文件路径即可。

2.  联网激活，首次运行时，保证设备能连通公网，将激活码输入SDK，SDK将自动到服务端进行校验激活。
---

### 支持平台
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;行人SDK目前支持Linux平台GPU版本。SDK提供标准的C++接口，**最低支持C++11**

###推荐硬件配置

|--------------|---------------------------|
| 系统版本     | Centos7 64bit             |
| 开发环境     | GCC 4.8.5                 |
| CPU          | 主频 3.00GHz              |
| 内存         | 8G                        |
| GPU          | NVIDIA GPU cuda8.0        |

 

###功能列表


| **功能名称** | **功能简要描述**                                                                            |
|--------------|---------------------------------------------------------------------------------------------|
| 行人检测     | 检测图片中的行人，返回行人区域(先对输入输入图片）、行人背景区域，以及检测耗时|
|   行人属性分析   | 检测输入行人图片的属性|
| 行人特征提取   |  提取输入行人图片的特征值|
| 行人图片比对（1:1）| 比对输入行人图片的相似度|
| 行人特征比对（1:1）| 比对比对输入行人特征的相似度|

### SDK目录结构

|--- libVisionPedestrianSDK.so&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//sdk so库 
|--- build_version.xml&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//依赖版本信息   
|--- include
|&nbsp;&nbsp;&nbsp;&nbsp;|--- packet
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|--- HwnPacket.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//缓存结构
|&nbsp;&nbsp;&nbsp;&nbsp;|---IPedAttrbuteDetector.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人属性检测接口定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- IPedDetector.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人检测接口定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- IPedFeature.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人特征接口定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- IPedRecongnition.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人识别接口定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- Pedestrian.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//全局接口定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- PedImage.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人图片结构定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- PedPersonAttribute.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人属性定义
|&nbsp;&nbsp;&nbsp;&nbsp;|--- PedResult.h&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//行人检测结果结构定义
|&nbsp;&nbsp;&nbsp;&nbsp;|---Demo&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//SDK使用示例


###SDK使用步骤
* 1.在官方网站下载行人C++ SDK压缩包;

* 2.引用 SDK中include下的头文件,注意aip命名空间下的类和方法;

* 3.编译工程时需添加 C++11 支持 (gcc/clang 添加编译参数 -std=c++11), 添加build_version.xml依赖第三方库需要的链接参数.

###API使用说明
* SDK 中包括 行人检测、行人属性检测、行人特征三个接口；

* 接口的使用步骤包括：创建接口实例->设置算法模型加载路径->初始化接口实例 -> 算法执行

* 算法执行是线程安全的，支持同一实例多线程调用；

* 算法实例的初始化过程会加载算法模型，该过程比较耗时，用户需要提前将算法实例初始化好，需要执行算法直接调用执行接口

* 一个算法实例占用资源较大，用户需要避免创建过多的算法实例，尽量复用.

---

## 行人SDK接口说明
---

### 启动行人SDK
**接口声明**


`RET_CODE pedestrianSDKSetup(PedAuthMode authMode,const PedAuthInfo& authInfo)`

**参数**

字段名                        | 类型   | 说明 
-------------------------------|--------|---
authMode| [PedAuthMode](#PedAuthMode)|授权方式
authInfo| const [PedAuthInfo](#PedAuthInfo)&|sdk 授权认证信息

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---

### 设置算法执行模式
**接口声明**

  
`RET_CODE setRunMode()`

**参数**
  &nbsp;&nbsp; 无


**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---
### 设置GPU 设备编号
**接口声明**
&nbsp;&nbsp;
  
`RET_CODE setGPUDeviceId(int deviceId = 0)`

**参数**

字段名                        | 类型   | 说明 
-------------------------------|--------|---
deviceId|int|当使用GPU时表示GPU编号, 否则忽略该参数;默认值为0

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

**说明**
&nbsp;&nbsp;该接口只对GPU版本的sdk有效

---

### 获取sdk 信息
**接口声明**

`PedSdkInfo getSdkInfo()`

**参数**
  &nbsp;&nbsp; 无


**返回值**

 类型   | 说明  
--------|---
[PedSdkInfo](#PedSdkInfo)| 行人SDK基础信息
---
### 获取sdk 信息
**接口声明**

`RET_CODE setLogOutput(LOG_LEVEL logLevel,OnLogFunc func)`

**参数**

  字段名                        | 类型   | 说明 
-------------------------------|--------|---
logLevel|LOG_LEVEL|日志级别
func|OnLogFunc|日志回调接口


**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 


---

### 获取RET_CODE 错误信息
**接口声明**

`const char* getRetCodeStr(RET_CODE code)`

**参数**

字段名                        | 类型   | 说明 
-------------------------------|--------|---
code| [RET_CODE](#ret_code)|错误码


**返回值**

 类型   | 说明  
--------|---
const char*| 错误码对应的信息

---

### 行人检测

#### 创建行人检测实例
**接口声明**


`static std::shared_ptr<IPedDetector> create()`

**参数**
  &nbsp;&nbsp; 无


**返回值**

 类型   | 说明  
--------|---
std::shared_ptr<IPedDetector>|行人检测实例 
---
#### 设置行人检测算法模型路径
**接口声明**


`RET_CODE setModelPath(const std::string& path)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
path|const std::string&|模型路径



**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---

#### 初始化行人检测算法
**接口声明**
 

`RET_CODE init()`

**参数**

 &nbsp;&nbsp; 无

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---
#### 检测行人
**接口声明**
 

`RET_CODE detect(PedRecgImage* image, PedDectedPedRet* pDectedRet)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
image|[PedRecgImage*](#PedRecgImage)|输入图片信息
pDectedRet|[PedDectedPedRet*](#PedDectedPedRet)|输出检测结果

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---
### 行人属性检测

#### 创建行人属性检测实例
**接口声明**

`static std::shared_ptr<IPedAttrbuteDetector> create()`

**参数 **
  &nbsp;&nbsp; 无


**返回值**

 类型   | 说明  
--------|---
std::shared_ptr<[IPedAttrbuteDetector]_>|行人属性检测实例 
---
#### 设置行人属性检测算法模型路径
**接口声明**

`RET_CODE setModelPath(const std::string&)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
path|const std::string&|模型路径



**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---

#### 初始化行人属性检测算法
**接口声明**

`RET_CODE init()`

**参数**

 &nbsp;&nbsp; 无

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---
#### 检测行人特征
**接口声明**

`RET_CODE detect(PedRecgImage* image, PedDetectedAttrRet* pDetectedRet)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
image|[PedRecgImage*](#PedRecgImage)|输入图片信息
pDetectedRet|[PedDetectedAttrRet*](#PedDetectedAttrRet)|输出检测属性结果

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

### 行人特征

####创建行人特征算法实例
**接口声明**

`static std::shared_ptr<IPedFeature> create()`

**参数**
  &nbsp;&nbsp; 无


**返回值**

 类型   | 说明  
--------|---
std::shared_ptr<IPedFeature_>|行人特征检测实例 
---
#### 设置行人特征算法模型路径
**接口声明**

`RET_CODE setModelPath(const std::string&)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
path|const std::string&|模型路径



**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---

#### 初始化行人特征算法
**接口声明**

`RET_CODE init()`

**参数**

 &nbsp;&nbsp; 无

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---
#### 提取行人特征
**接口声明**

###  

`RET_CODE extractFeature(PedRecgImage* image, PedDetectedFeatureRet* pPedVectRet)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
image|[PedRecgImage*](#PedRecgImage)|输入图片信息
pDetectedRet|[PedDetectedFeatureRet*](#PedDetectedFeatureRet)|输出行人特征

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

#### 行人比对
**接口声明**

`RET_CODE calSimilarity(PedRecgImage* srcImage,PedRecgImage* dstImage,double& similarity)`

**参数**

 
 字段名                        | 类型   | 说明 
-------------------------------|--------|---
srcImage|[PedObjFeature](#PedObjFeature)*|输入对比图片
dstImage|[PedObjFeature](#PedObjFeature)*|输出对比图片
similarity|double&|输出对比相似度

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

#### 行人特征比对
**接口声明**

###  

`RET_CODE calSimilarity(PedObjFeature* srcFeature,PedObjFeature* dstFeature,double& similarity)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
srcFeature|[PedObjFeature](#PedObjFeature)*|输入对比特征
dstFeature|[PedObjFeature](#PedObjFeature)*|输出对比特征
similarity|double&|输出对比相似度

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

### 行人识别


#### 创建行人识别实例
**接口声明**

`static std::shared_ptr<IPedFeature> create()`

**参数**
  &nbsp;&nbsp; 无


**返回值**

 类型   | 说明  
--------|---
std::shared_ptr<IPedFeature_>|行人行人识别实例 
---
#### 设置行人识别算法模型路径
**接口声明**

`RET_CODE setModelPath(const std::string&)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
path|const std::string&|模型路径



**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---

#### 初始化行人识别算法
**接口声明**

`RET_CODE init()`

**参数**

 &nbsp;&nbsp; 无

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 

---
#### 提取行人特征
**接口声明**

`RET_CODE detect(PedRecgImage* recgImage, int evalType,PedRecgRet* pRecgRet)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
recgImage|[PedRecgImage*](#PedRecgImage)|输入图片信息
evalType|int|[调用类型](#PedEvalType),组合方式
pRecgRet|[PedRecgRet*](#PedRecgRet)|输出行人特征

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

#### 行人比对
**接口声明**

###  

`RET_CODE calSimilarity(PedRecgImage* srcImage,PedRecgImage* dstImage,double& similarity)`

**参数**

 
 字段名                        | 类型   | 说明 
-------------------------------|--------|---
srcImage|[PedObjFeature](#PedObjFeature)*|输入对比图片
dstImage|[PedObjFeature](#PedObjFeature)*|输出对比图片
similarity|double&|输出对比相似度

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

#### 行人特征比对
**接口声明**


`RET_CODE calSimilarity(PedObjFeature* srcFeature,PedObjFeature* dstFeature,double& similarity)`

**参数**

 字段名                        | 类型   | 说明 
-------------------------------|--------|---
srcFeature|[PedObjFeature](#PedObjFeature)*|输入对比特征
dstFeature|[PedObjFeature](#PedObjFeature)*|输出对比特征
similarity|double&|输出对比相似度

**返回值**

 类型   | 说明  
--------|---
[RET_CODE](#ret_code)|错误码 
---

---

## 数据结构
---

### <span id="PedAuthMode">授权方式定义</span>
**定义**
```c
typedef enum {
    AUTH_UNKNOW = 0,
    LOCAL_AUTH,      
    ONLINE_AUTH 
}PedAuthMode;
```

**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
AUTH_UNKNOW | unknown
LOCAL_AUTH |本地授权
ONLINE_AUTH |联网授权
---

### <span id="PedSdkInfo">行人sdk信息</span>
**定义**
```c
typedef struct PedestrianSdkInfo {
    std::string version;
    std::string runMode;
}PedSdkInfo;
```

**字段说明**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
version |std::string  | sdk 版本信息
runMode|std::string | sdk 运行模式

---

### <span id="PedOnlineAuthInfo">联网授权信息</span>
**定义**
```c
typedef struct PedestrianOnlineAuthInfo {
    std::string activeCode;       
    std::string host;             
    int port;                     
}PedOnlineAuthInfo;
```

**字段说明**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
activeCode |std::string  | 激活码
host|std::string | 在线鉴权服务器地址，选填
port|int | 服务器端口，选填

---
### <span id="PedAuthInfo">sdk授权信息结构</span>
**定义**
```c
typedef struct PedestrianAuthInfo {
    std::string         localLicense;  
    PedOnlineAuthInfo   onlineAuthInfo;
}PedAuthInfo;
```

**字段说明**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
localLicense |std::string  | 本地授权文件
onlineAuthInfo|[PedOnlineAuthInfo](#PedOnlineAuthInfo) | 在线授权信息

---
### <span id="PedEvalType">调用类型定义</span>
**定义**
```c
typedef enum {
	PED_EVAL_UNKNOWN = 0,           
	PED_DETECT_PED = 1,				
	PED_DETECT_ATTR = 1 << 1, 
	PED_EXTRACT_FEATURE = 1 << 2
}PedEvalType;
```

**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
PED_EVAL_UNKNOWN | unknown
PED_DETECT_PED |行人检测
PED_DETECT_ATTR |行人属性检测
PED_EXTRACT_FEATURE |行人特征提取
---

---

### <span id="IMG_FORMAT">图片格式</span>
**定义**
```c
typedef enum {
	HWN_IMG_BGR24 = 1
} IMG_FORMAT;
```
**说明**
仅支持BGR24, 若格数不满足开发者需要进行转换

---

### <span id="Image">图片</span>
**定义**

```c
typedef struct Image {

	Image():format(HWN_IMG_BGR24),pszData(nullptr),nLen(0),nWidth(1920),nHeight(1080) {
	}

	IMG_FORMAT			format;				
	void* 				pszData;			
	uint32_t			nLen;				
	uint32_t			nWidth;			
	uint32_t			nHeight;		
} Image;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
format |[IMG_FORMAT](#IMG_FORMAT)  | 图片格式
pszData|void* | 数据指针
nLen  | uint32_t| 数据长度
nWidth | uint32_t| 图片宽
nHeight | uint32_t| 图片高
---

### <span id="ImageRect">图片区域矩形框</span>
**定义**

```c
typedef struct ImageRect {
  
	ImageRect():nX(0),nY(0),nW(0),nH(0) {}
	ImageRect(uint32_t x,uint32_t y,uint32_t w,uint32_t h) {
		nX = x,nY = y,nW = w,nH = h;
	}

	uint32_t 	nX;
	uint32_t 	nY;
	uint32_t 	nW;
	uint32_t	nH;
}ImageRect;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
nX |uint32_t  | 矩形框左上角x坐标值
nY|uint32_t | 矩形框左上角y坐标值
nW  | uint32_t| 矩形框宽
nH | uint32_t| 矩形框高

---

### <span id="PedImageExtra">图片扩展信息</span>
**定义**

```c
typedef struct PedImageExtra {
	PedImageExtra():imageDec(""),imageId(""),nStride(1920),frameId(0),channel(3) {
	}

	std::string			imageDec;		
	std::string			imageId;		

	uint32_t			nStride;			
	uint32_t			frameId;			
	uint32_t			channel;			
} PedImageExtra;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
imageDec |std::string  | 图片描述,用户自定义
imageId|std::string | 图片Id
nStride  | uint32_t| 预留
frameId | uint32_t| 帧Id
channel | uint32_t| 通道数

---

### <span id="PedImage">行人图片</span>
**定义**

```c
typedef struct PedImage {
	Image				image;				
	PedImageExtra		imageExtra;
}PedImage;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
image |[Image](#Image)  | 图片信息
imageExtra|[PedImageExtra](#PedImageExtra) | 扩展信息

---

### <span id="PedRecgImage">行人识别图片</span>
**定义**

```c
typedef struct PedRecongnitionImage {
	PedImage			pedImage;			
	ImageRect		   region; 			
	void 			   *pContext;		
} PedRecgImage;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
pedImage |[PedImage](#PedImage)  | 图片
region|[ImageRect](#ImageRect) | 待检测区域
pContext|*void | 用户上下文
---

---

### <span id="PedObjectAttr">行人属性</span>
**定义**

```c
typedef struct PedObjectAttr {
	PedObjectAttr():hair(Ped_Hair_Unknown),gender(Ped_Gender_Unknown),age(Ped_AGE_Unknown),
					accessory(0),upClothing(0),lowClothing(0),footWear(0),carrying(0) {}

	PersonHair				hair;			   
	PersonGender			  gender;			 
	PersonAge				 age;			    
	uint32_t				  accessory;		
	uint32_t				  upClothing;		
	uint32_t				  lowClothing;	
	uint32_t          		footWear;
	uint32_t				  carrying;		   
}PedObjectAttr;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
hair |PersonHair  | [PersonHair](#PersonHair) attribute
gender|PersonGender | [PersonGender](#PersonGender) attribute
age|PersonAge | [PersonAge](#PersonAge) attribute 
accessory|uint32_t | 由[PersonAge](#PersonAge) attribute 组合
upClothing|uint32_t | 由[PersonUpClothing](#PersonUpClothing) attribute组合
lowClothing|uint32_t | 由[PersonLowClothing](#PersonLowClothing) attribute组合
footWear|uint32_t | 由[PersonFootWear](#PersonFootWear) attribute组合
carrying|uint32_t | 由[PersonCarrying](#PersonCarrying) attribute组合
---

### <span id="PedObjFeature">行人对象特征</span>
**定义**

```c
typedef struct PedObjectFeature {
	PedObjectFeature():pValue(nullptr),nLen(0) {
	}

	float 			*pValue;		
	uint32_t		  nLen;			
	HwnPacket		 valuePacket;
}PedObjFeature;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
pValue |float*  | 特征向量指针，用户不需要管理
nLen|uint32_t | 特征向量大小
valuePacket|[HwnPacket](#HwnPacket) | 存储特征向量缓存

---

### <span id="PedDetectedObject">行人对象信息</span>
**定义**

```c
typedef struct PedDetectedObject {
	PedObjFeature			objFeature;	
	PedObjectAttr			attribute;	
	ImageRect				region;			
}PedDetectedObjRet;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
objFeature |[PedObjFeature](#PedObjFeature) | 行人特征
attribute|[PedObjectAttr](#PedObjectAttr) | 行人属性
region|[ImageRect](#ImageRect) | 行人在待检测图片中的区域

---

### <span id="PedDetectedPed">检测到的行人</span>
**定义**

```c
typedef struct PedDetectedPed {
	ImageRect		region;					
}PedDetectedPed;
```
**字段描述 **

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
region |[ImageRect](#ImageRect) | 行人在待检测图片中的区域位置

---

### <span id="PedDetectedObjGroup">行人对象组</span>
**定义**

```c
typedef std::list<PedDetectedObjRet> PedDetectedObjList;

typedef struct PedDetectedObjectGroup {
	PedDetectedObjList	objects;	
	uint32_t			  nObject;			
	ImageRect			 bgRegion;			
	PedImageExtra         imageExtra;
}PedDetectedObjGroup;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
objects |PedDetectedObjList | 检测行人对象信息
nObject |uint32_t | 行人对象个数
bgRegion |[ImageRect](#ImageRect) | 行人对象组的背景区域
imageExtra |[PedImageExtra](#PedImageExtra) | 原图片扩展信息
---

### <span id="PedRecgRet">行人识别结果</span>
**定义**

```c
typedef struct PedRecongnitionResult {
	PedDetectedObjGroup		objGroup;			
	uint32_t				elapsedTime;			
} PedRecgRet;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
objGroup |[PedDetectedObjGroup](#PedDetectedObjGroup) | 检测到的对象组
elapsedTime |uint32_t | 耗时(ms)
---

### <span id="PedDetectedAttrRet">行人属性</span>
**定义**

```c
typedef struct PedDetectedAttributeResult {
	PedObjectAttr		attribute;		
	uint32_t			elapsedTime;		
}PedDetectedAttrRet;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
attribute |[PedObjectAttr](#PedObjectAttr) | 属性
elapsedTime |uint32_t | 耗时(ms)
---

### <span id="PedDetectedFeatureRet">行人特征</span>
**定义**

```c
typedef struct PedDetectedFeatureResult {
	PedObjFeature		objFeature;		
	uint32_t			elapsedTime;		
}PedDetectedFeatureRet;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
objFeature |[PedObjFeature](#PedObjFeature)* | 行人特征
elapsedTime |uint32_t | 耗时(ms)
---

### <span id="PedDectedPedRet">行人检测结果</span>
**定义**

```c
typedef std::list<PedDetectedPed>  PedDectPedList;

typedef struct PedDetectedPedResult {
	PedDectPedList		pedList;			
	uint32_t			nPedestrian;			
	ImageRect			bgRegion;				
	uint32_t			elapsedTime;		
}PedDectedPedRet;
```
**字段描述**

字段名|类型                       | 描述                                
-----------|---------------------|---------------------------------------------
pedList |PedDectPedList | 检测结果
nPedestrian |uint32_t | 行人个数
bgRegion |[ImageRect](#ImageRect) | 背景区域
elapsedTime |uint32_t | 耗时(ms)
---

---


### <span id="PersonAge">行人属性-年龄段</span> 
**定义**
```c
typedef enum PersonAge {
    Ped_AGE_Unknown,         
    Ped_AGE_Less30,          
    Ped_AGE_30To40,          
    Ped_AGE_40To60,          
    Ped_AGE_Larger60         
} PersonAge;
```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_AGE_Unknown |未知
Ped_AGE_Less30 | 小于30岁
Ped_AGE_30To40 | 30～40岁
Ped_AGE_40To60 | 40～60岁
Ped_AGE_Larger60 |大于60岁
---

### <span id="PersonCarrying">行人属性-携带物</span> 
**定义**
```c
typedef enum PersonCarrying {
    Ped_Carrying_UNKNOWN,          
    Ped_Carrying_MessengerBag = 1, 
    Ped_Carrying_Nothing = 2,      
    Ped_Carrying_Backpack = 4,     
    Ped_Carrying_PlasticBags = 8,  
    Ped_Carrying_Other,            
} PersonCarrying;
```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_Carrying_UNKNOWN |未知
Ped_Carrying_MessengerBag |斜挎包
Ped_Carrying_Nothing | 无
Ped_Carrying_Backpack | 背包
Ped_Carrying_PlasticBags | 塑料袋
Ped_Carrying_Other | 其他
---

### <span id="PersonAccessory">行人属性-饰品</span> 
**定义**
```c
typedef enum PersonAccessory {
    Ped_Accessory_UNKNOWN,                      
    Ped_Accessory_Hat = 1,                      
    Ped_Accessory_Muffler = 2,                  
    Ped_Accessory_Nothing = 4,                  
    Ped_Accessory_Sunglasses = 8,               
} PersonAccessory;
```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_Accessory_UNKNOWN |未知
Ped_Accessory_Hat | 帽子
Ped_Accessory_Muffler | 围巾
Ped_Accessory_Nothing | 无
Ped_Accessory_Sunglasses | 太阳镜
---

### <span id="PersonFootWear">行人属性-鞋子</span> 
**定义**
```c
typedef enum PersonFootWear {
    Ped_FootWear_UNKNOWN,                       
    Ped_FootWear_Leather_Shoes = 1,             
    Ped_FootWear_Sandals = 2,                   
    Ped_FootWear_Shoes = 4,                     
    Ped_FootWear_Sneaker = 8,                   
} PersonFootWear;
```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_FootWear_UNKNOWN |未知
Ped_FootWear_Leather_Shoes | 皮鞋
Ped_FootWear_Sandals | 凉鞋
Ped_FootWear_Shoes | 普通鞋子
Ped_FootWear_Sneaker | 运动鞋
---

### <span id="PersonLowClothing">行人属性-下身服饰</span> 
**定义**
```c
typedef enum PersonLowClothing {
    Ped_Low_Cloth_UNKNOWN,                   
    Ped_Low_Jeans = 1,                       
    Ped_Low_Shorts = 2,                      
    Ped_Low_ShortSkirt = 4,                   
    Ped_Low_Trousers = 8,                     
    Ped_Low_Casual = 0x200,                    
    Ped_Low_Formal = 0x201,                    
} PersonLowClothing;
```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_Low_Cloth_UNKNOWN |未知
Ped_Low_Jeans | 牛仔裤
Ped_Low_Shorts | 短裤
Ped_Low_ShortSkirt | 短裙
Ped_Low_Trousers | 普通裤子
Ped_Low_Casual | 休闲裤
Ped_Low_Formal | 西裤
---

### <span id="PersonGender">行人属性-性别</span> 
**定义**
```c
typedef enum PersonGender {
    Ped_Gender_Unknown,                
    Ped_Gender_Male,                   
    Ped_Gender_Female,                 
} PersonGender;
```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_Gender_Unknown |未知
Ped_Gender_Male | 男性
Ped_Gender_Female | 女性
---

### <span id="PersonHair">行人属性-头发</span> 
**定义**
```c
typedef enum PersonHair {
    Ped_Hair_Unknown,                  
    Ped_Hair_Long,                     
    Ped_Hair_Short,                    
} PersonHair;

```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_Hair_Unknown |未知
Ped_Hair_Long | 长发
Ped_Hair_Short | 短发
---

### <span id="PersonUpClothing">行人属性-上身服饰</span> 
**定义**
```c
typedef enum PersonUpClothing {
    Ped_Up_Cloth_Unknown
    Ped_Up_ShortSleeve = 1,         
    Ped_Up_Jacket = 2,              
    Ped_Up_Logo = 4,                
    Ped_Up_Plaid = 8,               
    Ped_Up_ThinStripes = 16,        
    Ped_Up_Tshirt = 32,             
    Ped_Up_Other = 64,              
    Ped_Up_VNeck = 128,             
    Ped_Up_Casual = 1 << 8,         
    Ped_Up_Formal = 1 << 9,         
} PersonUpClothing;

```
**字段说明**

字段                         | 描述                                
--------------------------------|---------------------------------------------
Ped_up_Cloth_UNKNOWN |未知
Ped_Up_ShortSleeve | 短袖
Ped_Up_Jacket | 夹克
Ped_Up_Logo | Logo
Ped_Up_Plaid | 格子衫
Ped_Up_ThinStripes | 细条纹
Ped_Up_Tshirt | T恤
Ped_Up_Other | 其他
Ped_Up_VNeck | 高领
Ped_Up_Casual | 休闲
Ped_Up_Formal | 西服
---

---

##  <span id="ret_code">错误码</span>

**错误码说明**

错误码                         | 描述                              
--------------------------------|---------------------------------------------
RET_SUCCESS            | 成功
RET_SDK_NOT_SETUP            | sdk 没有setup
RET_SDK_VERSION_INVALID            | sdk 版本无效
RET_LICENSE_EXPIRE            | license 过期
RET_LOAD_LICENSE_FAILED            | 加载sdk license 失败
RET_INIT_ALG_FAILED            | 初始化算法失败
RET_DECRYPT_ALG_MODEL_ERROR            | 解密算法模型失败
RET_NOT_INIT_ALG            | 算法未初始化
RET_INIT_CACHE_FAILED            | 初始化缓存失败
RET_INPUT_ARGS_NULL            | 输入参数为NULL
RET_INPUT_IMAGE_NULL            | 输入图片为NULL
RET_PATH_ARGS_ERROR            | 路径参数错误
RET_NOT_FOUND_ALG_MODEL            | 没有找到算法模型
RET_MODULE_NULL            | 算法模型为NULL
RET_NONSUPPORT_IMG_FORMAT            | 图片格式不支持
RET_IMAGE_ERROR            | 图片错误
RET_REGION_ERROR            | 区域错误
RET_NO_PED_DETECTED            | 没有检测到行人
RET_INPUT_FEATURE_ERROR            | 输入行人特征错误
RET_NO_DETECTED_FEAT            | 没有提取到行人特征
RET_FEATURE_SIZE_ERROR            |特征向量大小不一致
RET_CACHE_NOT_ENOUGH            | 缓存不够
RET_NOT_FOUND_GPU            | 没有找到GPU
RET_NOT_FOUND_GPU_DEVICE            | 没有找到GPU设备

---