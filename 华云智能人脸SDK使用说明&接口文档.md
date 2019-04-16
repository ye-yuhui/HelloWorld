# *人脸SDK接口文档V1.0.00*
--------------------------------
简介
====

本文档用于说明人脸识别C++
SDK的相关技术内容。主要包括SDK支持授权说明、支持的平台说明、功能列表、开发建议以及历史版本说明等。

授权说明
========

SDK必须授权通过后才能正常使用。开发者需要使用应用账户下的激活码为指定的机器设备获取授权信息。当前支持两种授权方式：

1.  离线授权，开发者使用激活码和机器指纹信息在门户网站上申请获取授权文件，然后将授权文件放到SDK能够访问的路径下。在使用SDK功能前，调用SDK接口设置正确的授权文件路径即可。

2.  联网激活，首次运行时，保证设备能连通公网，将激活码输入SDK，SDK将自动到服务端进行校验激活。

支持平台
========

人脸识别SDK目前支持Linux和Windows系统版本，在每个系统平台上分别支持CPU和GPU运行的版本。SDK提供标准的C++接口，所有SDK版本使用统一的C++接口。

针对不同的系统，推荐的配置要求入下：

Linux
-----

|  项目        |    要求                               |
|--------------|---------------------------------------|
| 系统版本     | Centos7 64bit                          |
| 开发环境     | GCC 4.8.5                              |
| CPU          | 主频 3.00GHz                           |
| 内存         | 8G                                     |
| GPU          | NVIDIA GPU cuda8.0                    |
| 依赖库及版本 | opencv3.4.1 protobuf3.5.1 openssl1.0.2k|

Windows
-------

|  项目        |    要求                               |
|--------------|---------------------------------------|
| 系统版本     | Windows 10 64bit                       |
| 开发环境     | Visual Studio 2015                     |
| CPU          | 主频 3.00GHz                           |
| 内存         | 8G                                     |
| GPU          | NVIDIA GPU cuda8.0                    |
| 依赖库及版本 | opencv3.4.1 protobuf3.5.1 openssl1.0.2k |

功能列表
========

| **功能名称** | **功能简要描述**                                                                            |
|--------------|---------------------------------------------------------------------------------------------|
| 人脸检测     | 检测图片中的人脸，返回人脸个数以及每个人脸的位置和关键点                                    |
| 人脸质量     | 检测图片中人脸的质量                                                                        |
| 人脸关键点   | 检测图片中人脸的关键点，返回106个人脸关键点                                                 |
| 人脸属性     | 检测图片中人脸的属性，包括性别、年龄段、种族、眼镜、微笑、口罩、年龄等                      |
| 人脸眼睛状态 | 检测图片中人脸的眼睛状态，返回左、右眼闭合的概率                                            |
| 人脸嘴巴状态 | 检测图片中人脸的嘴巴状态，返回嘴巴闭合的概率                                                |
| 人脸活体检测 | 通过红外双目相机的可见光和红外人脸图片判断人脸是否为活体                                    |
| 人脸比对     | 比对两个特征值（或两张图片中最大人脸）的相似值                                              |
| 人脸识别     | 在一个人脸库中查找相似度最高的N个人脸，支持创建基于内存的人脸特征库，支持增加、删除人脸特征 |

开发建议
========

1.  实例不是线程安全的，因此不建议多个线程调用一个实例，若无法避免，必须对实例进行加锁；

2.  不要频繁的创建和销毁实例，建议在程序初始化时创建实例，程序退出时销毁实例。

历史版本
========

| **版本号** | **发布日期** | **更新内容**          |
|------------|--------------|-----------------------|
| V1.0.00    | 2019-04-27   | 人脸SDK发布第一个版本 |


数据结构
========

通用定义
--------

### 日志级别

**功能**

定义日志级别。

**声明**

	typedef enum {
	    LL_DEBUG,
	    LL_INFO,
	    LL_WARN,
	    LL_ERROR,
	    LL_FATAL,
	    LL_NOLOG,
	} LOG_LEVEL;


**描述**

| **类型** | **描述**   |
|----------|------------|
| LL_DEBUG | 调试       |
| LL_INFO  | 信息       |
| LL_WARN  | 警告       |
| LL_ERROR | 错误       |
| LL_FATAL | 致命错误   |
| LL_NOLOG | 不输出日志 |

### 日志回调函数

**功能**

定义日志回调函数。

**声明**

	typedef void (*OnLogFunc)(LOG_LEVEL level, const char *data, int len);

**描述**

| **字段名** | **类型**     | **说明**     |
|------------|--------------|--------------|
| level,     | [LOG_LEVEL](#日志级别)    | 日志级别     |
| data       | const char\* | 日志信息指针 |
| len        | int          | 日志信息长度 |

### 返回码

**功能**

定义返回码。

**声明**

	typedef enum {
	    RET_OK, 
	    RET_AUTH_FAILED, 
	    RET_INVALID_INPUT_PARAM, 
	    RET_UNSUPPORT_IMG_FORMAT, 
	    RET_INVALID_INPUT_IMG, 
	    RET_SET_CONFIG_FAILED, 
	    RET_OTHERS_ERROR, 
	} RET_CODE;


**描述**

| **类型**                 | **描述**         |
|--------------------------|------------------|
| RET_OK                   | 成功             |
| RET_AUTH_FAILED          | 鉴权失败         |
| RET_INVALID_INPUT_PARAM  | 无效的输入参数   |
| RET_UNSUPPORT_IMG_FORMAT | 不支持的图片格式 |
| RET_INVALID_INPUT_IMG    | 无效的输入图片   |
| RET_SET_CONFIG_FAILED    | 设置配置失败     |
| RET_OTHERS_ERROR         | 其他错误         |

### 许可模式

**功能**

定义许可模式。

**声明**

	typedef enum {
	    AUTH_SOFTDOG,
	    AUTH_OFFLINE,
	    AUTH_ONLINE_ACTIVATE,
	} AUTH_MODE;


**描述**

| **类型**             | **描述**   |
|----------------------|------------|
| AUTH_SOFTDOG         | 加密狗授权 |
| AUTH_OFFLINE         | 离线授权   |
| AUTH_ONLINE_ACTIVATE | 联网激活   |

### 授权信息

**功能**

定义授权信息结构。

**声明**

	typedef struct {
	    PSTRING licenseFile;
	    struct {
	        PSTRING activateCode;
	        PSTRING host;
	        int port;
	    } online;
	} AUTH_INFO;


**描述**

| **字段名**          | **类型** | **说明**                 |
|---------------------|----------|--------------------------|
| licenseFile         | [PSTRING](#字符串)  | 授权文件路径，离线授权必 |
| online.activateCode | [PSTRING](#字符串)  | 激活码                   |
| online.host         | [PSTRING](#字符串)  | 服务器地址，选填         |
| online.port         | int      | 服务器端口，选填         |

基础数据结构
------------

### 数据缓冲区

**功能**

定义数据缓冲区。

**声明**

	template<typename _T>
	struct Buffer {
	    _T* data; 
	    int capacity; 
	    int len; 
	};


**描述**

| **字段名** | **类型** | **说明**       |
|------------|----------|----------------|
| data       | \_T\*    | 数据缓冲区指针 |
| capacity   | int      | 数据缓冲区容量 |
| len        | int      | 数据长度       |

### 坐标点

**功能**

定义坐标点。

**声明**

	typedef struct {
	    float x;
	    float y;
	} POINT;


**描述**

| **字段名** | **类型** | **说明**      |
|------------|----------|---------------|
| x          | float    | 坐标点x轴的值 |
| y          | float    | 坐标点y轴的值 |

### 矩形框

**功能**

定义矩形框。

**声明**

	typedef struct RECT_ {
	    int x;
	    int y;
	    int width;
	    int height;
	} FACE_RECT;


**描述**

| **字段名** | **类型** | **说明**            |
|------------|----------|---------------------|
| x          | int      | 矩形框左上角x坐标值 |
| y          | int      | 矩形框左上角y坐标值 |
| width      | int      | 矩形框宽            |
| height     | int      | 矩形框高            |

### 特征值

**功能**

定义特征值。

**声明**

	typedef Buffer<float> FACE_FEATURE;

**描述**

| **别名** | **类型** | **说明**   |
|------------|----------|------------|
| FACE_FEATURE | Buffer<float>   | 特征向量 |

### 字符串

**功能**

定义字符串。

**声明**

	typedef struct PSTRING_ {
	    char* data;
	    int size;
	} PSTRING;


**描述**

| **字段名** | **类型** | **说明**   |
|------------|----------|------------|
| data       | char\*   | 字符串地址 |
| size       | int      | 字符串长度 |

图像数据
--------

### 图像格式

**功能**

定义图像格式。

**声明**

	typedef enum {
	    IMG_BGR24,
	    IMG_RGB24,
	    IMG_RGB32,
	} IMG_FORMAT;


**描述**

| **类型**  | **描述**       |
|-----------|----------------|
| IMG_BGR24 | 三通道BGR图像  |
| IMG_RGB24 | 三通道RGB图像  |
| IMG_RGB32 | 四通道RGBA图像 |

### 原始图像信息

**功能**

定义原始图像信息。

**声明**

	typedef struct {
	    IMG_FORMAT format;
	    IMG_BUFFER buffer;
	    int width;
	    int height;
	} IMG;


**描述**

| **字段名** | **类型**   | **说明**                         |
|------------|------------|----------------------------------|
| format     | [IMG_FORMAT](#图像格式) | 图像格式                         |
| buffer     | IMG_BUFFER | 图像数据 Buffer\<unsigned char\> |
| width      | int        | 图像数据宽度                     |
| height     | int        | 图像数据高度                     |

### 对齐人脸图像

**功能**

定义对齐人脸图像信息。

**声明**

	typedef struct {
	    IMG_BUFFER buffer;
	    int width;
	    int height;
	    int channel;
	} ALIGNED_IMG;


**描述**

| **字段名** | **类型**   | **说明**                                 |
|------------|------------|------------------------------------------|
| buffer     | IMG_BUFFER | 对齐后的图像数据 Buffer\<unsigned char\> |
| width      | int        | 对齐后的图像宽度                         |
| height     | int        | 对齐后的图像高度                         |
| channel    | int        | 对齐后的图像通道                         |

人脸关键点
----------

### 人脸关键点

**功能**

定义人脸关键点。

**声明**

	#define HWN_MAX_KEYS_NUM    128       ///< 默认关键点最大个数
	typedef struct {
	    int num;
	    POINT point[HWN_MAX_KEYS_NUM];
	} FACE_KEYS;


**描述**

| **字段名** | **类型** | **说明**       |
|------------|----------|----------------|
| num        | int      | 实际关键点个数 |
| point[]    | [POINT](#坐标点)    | 关键点         |

人脸检测
--------

### 人脸检测参数

**功能**

定义人脸检测输入参数。

**声明**

	typedef struct {
	    int minFaceSize;
	    int maxFaceSize;
	} FD_PARAM;


**描述**

| **字段名**  | **类型** | **说明**     |
|-------------|----------|--------------|
| minFaceSize | int      | 最小人脸尺寸 |
| maxFaceSize | int      | 最大人脸尺寸 |

### 人脸检测结果

**功能**

定义人脸检测结果。

**声明**

	typedef struct {
	    int faceId;
	    FACE_RECT rect;
	    FACE_KEYS keys;
	} FACE_INFO;


**描述**

| **字段名** | **类型**  | **说明**   |
|------------|-----------|------------|
| faceId     | int       | 人脸ID     |
| rect       | [FACE_RECT](#矩形框) | 人脸矩形框 |
| keys       | [FACE_KEYS](#人脸关键点) | 人脸关键点 |

人脸属性
--------

### 性别

**功能**

定义性别。

**声明**

	typedef enum {
	    GD_FEMALE,
	    GD_MALE,
	    GD_UNKOWN,
	} GENDER;


**描述**

| **类型**  | **描述** |
|-----------|----------|
| GD_FEMALE | 女性     |
| GD_MALE   | 男性     |
| GD_UNKOWN | 未知     |

### 年龄段

**功能**

定义年龄段。

**声明**

	typedef enum {
	    AG_BABY,
	    AG_PRETEEN,
	    AG_TEENAGER,
	    AG_ADULT,
	    AG_MID_AGED,
	    AG_MID_OLD_AGED,
	    AG_AGED,
	    AG_UNKOWN,
	} AGE_GROUP;


**描述**

| **类型**        | **描述** |
|-----------------|----------|
| AG_BABY         | 幼年     |
| AG_PRETEEN      | 少年     |
| AG_TEENAGER     | 青年     |
| AG_ADULT        | 壮年     |
| AG_MID_AGED     | 中年     |
| AG_MID_OLD_AGED | 中老年   |
| AG_AGED         | 老年     |
| AG_UNKOWN,      | 未知     |

### 种族

**功能**

定义种族。

**声明**

	typedef enum {
	    RC_YELLOW,
	    RC_BLACK,
	    RC_WHITE,
	    RC_UNKOWN,
	} RACE;


**描述**

| **类型**  | **描述** |
|-----------|----------|
| RC_YELLOW | 黄种人   |
| RC_BLACK  | 黑人     |
| RC_WHITE  | 白人     |
| RC_UNKOWN | 未知     |

### 眼镜类型

**功能**

定义眼镜类型。

**声明**

	typedef enum {
	    GL_NO,
	    GL_YES,
	    GL_SUN,
	    GL_UNKOWN,
	} GLASSES;


**描述**

| **类型**  | **描述** |
|-----------|----------|
| GL_NO     | 无眼镜   |
| GL_YES    | 带眼镜   |
| GL_SUN    | 带墨镜   |
| GL_UNKOWN | 未知     |

### 人脸属性结构

**功能**

定义人脸属性结构。

**声明**

	typedef struct {
	    GENDER gender;
	    AGE_GROUP ageGroup;
	    RACE race;
	    GLASSES glasses;
	    int smile;
	    int mask;
	    int age;
	} FACE_ATTR;


**描述**

| **字段名** | **类型**  | **说明**          |
|------------|-----------|-------------------|
| gender     | [GENDER](#性别)    | 性别              |
| ageGroup   | [AGE_GROUP](#年龄段) | 年龄段            |
| race       | [RACE](#种族)      | 种族              |
| glasses    | [GLASSES](#眼镜类型)   | 眼镜类型          |
| smile      | int       | 微笑: 0 no; 1 yes |
| mask       | int       | 口罩: 0 no; 1 yes |
| age        | int       | 年龄              |

人脸状态
--------

### 眼睛状态

**功能**

定义人脸眼睛状态。

**声明**

	typedef struct {
	    float left;
	    float right;
	} FACE_EYES_STATE;


**描述**

| **字段名** | **类型** | **说明**       |
|------------|----------|----------------|
| left       | float    | 左眼闭合的概率 |
| right      | float    | 右眼闭合的概率 |

### 嘴巴状态

**功能**

定义人脸嘴巴状态。

**声明**

	typedef struct {
	    float state;
	} FACE_MOUTH_STATE;


**描述**

| **字段名** | **类型** | **说明**       |
|------------|----------|----------------|
| state      | float    | 嘴巴闭合的概率 |

人脸识别
--------

### 人脸识别结果

**功能**

定义人脸识别结果。

**声明**

	typedef struct {
	    int index;
	    float score;
	    void* data;
	} FACE_RECOG_RESULT;


**描述**

| **字段名** | **类型** | **说明**     |
|------------|----------|--------------|
| index      | int      | 人脸特征索引 |
| score      | float    | 人脸相似度   |
| data       | void\*   | 用户数据     |

通用接口
========

获取版本信息
------------

**接口功能**

获取SDK版本信息。

**接口声明**

	const char* getVersion();

**参数描述**

无

**返回值**

| **类型**     | **描述** |
|--------------|----------|
| const char\* | 版本信息 |

**示例代码**

	void TEST_GetVersion()
	{
	    printf("SDK Version: %s\n", HWN::Face::getVersion());
	}

获取机器指纹信息
----------------

**接口功能**

获取机器指纹信息。

**接口声明**

	const char* getMachineId();

**参数描述**

无

**返回值**

| **类型**     | **描述**     |
|--------------|--------------|
| const char\* | 机器指纹信息 |

**示例代码**

	void TEST_GetMachineId()
	{
	    printf("MachineId: %s\n", HWN::Face::getMachineId());
	}

设置授权信息
------------

**接口功能**

设置授权信息。

**接口声明**

	RET_CODE setAuthInfo(AUTH_MODE mode, const AUTH_INFO\* info);

**参数描述**

| **字段名** | **类型**          | **说明**     |
|------------|-------------------|--------------|
| mode       | [AUTH_MODE](#许可模式)         | 授权许可模式 |
| info       | const [AUTH_INFO](#授权信息)\* | 授权许可信息 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

**示例代码**
	
	void TEST_Auth()
	{
	    HWN::Face::AUTH_MODE mode = HWN::Face::AUTH_OFFLINE;
	    // HWN::Face::AUTH_MODE mode = HWN::Face::AUTH_ONLINE_ACTIVATE;
	    HWN::Face::AUTH_INFO info;
	    if (HWN::Face::AUTH_OFFLINE == mode)
	    {
	        info.licenseFile.data = (char*)”HwnLicense.lic”;
	        info.licenseFile.size = strlen(“HwnLicense.lic”);
	    }
	    else if (HWN::Face::AUTH_ONLINE_ACTIVATE == mode)
	    {
	        info.online.activateCode.data = (char*)”9fDJklKme1tuIsEQ”;
	        info.online.activateCode.size = strlen(”9fDJklKme1tuIsEQ”);
	        info.online.host.data = NULL;
	        info.online.host.size = 0;
	        info.online.port = 0;
	    }
	    HWN::Face::RET_CODE ret = HWN::Face::setAuthInfo(mode, &info);
	    if (HWN::Face::RET_OK != ret)
	    {
	        printf("auth failed, code: %d, msg: %s\n", ret, HWN::Face::getRetMsg(ret));
	    }
	    else
	    {
	        printf("auth init success!\n");
	    }
	}



人脸检测
========

创建人脸检测实例
----------------

**接口功能**

创建人脸检测实例。

**接口声明**

	static IFaceDetection* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**         | **描述**                   |
|------------------|----------------------------|
| IFaceDetection\* | 人脸检测实例，失败返回NULL |

销毁人脸检测实例
----------------

**接口功能**

销毁人脸检测实例。

**接口声明**

	static void destroy(IFaceDetection* instance);

**参数描述**

| **字段名** | **类型**         | **说明**     |
|------------|------------------|--------------|
| instance   | IFaceDetection\* | 人脸检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取默认参数信息
----------------

**接口功能**

获取默认参数信息。

**接口声明**

	RET_CODE getDefaultParam(FD_PARAM* param);

**参数描述**

| **字段名** | **类型**   | **说明**                        |
|------------|------------|---------------------------------|
| param      | [FD_PARAM](#人脸检测参数)\* | [OUT]人脸检测参数，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取当前参数信息
----------------

**接口功能**

获取当前参数信息。

**接口声明**

	RET_CODE getParam(FD_PARAM* param);

**参数描述**

| **字段名** | **类型**   | **说明**                        |
|------------|------------|---------------------------------|
| param      | [FD_PARAM](#人脸检测参数)\* | [OUT]人脸检测参数，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

设置参数
--------

**接口功能**

设置参数信息。

**接口声明**

	RET_CODE setParam(const FD_PARAM* param);

**参数描述**

| **字段名** | **类型**         | **说明**     |
|------------|------------------|--------------|
| param      | const [FD_PARAM](#人脸检测参数)\* | 人脸检测参数 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

人脸检测
--------

**接口功能**

人脸检测。

**接口声明**

	RET_CODE detect(IMG* img, FACE_INFO* faceList, int size, int* faceNum, bool isVideo = false);

**参数描述**

| **字段名** | **类型**    | **说明**                                                            |
|------------|-------------|---------------------------------------------------------------------|
| img        | [IMG](#原始图像信息)\*       | 待检测图片信息                                                      |
| faceList   | [FACE_INFO](#人脸检测结果)\* | [OUT]人脸检测结果数组，外部分配内存                                 |
| size       | int         | 人脸检测结果数组大小                                                |
| faceNum    | Int\*       | [OUT]检测到的人脸数                                                 |
| isVideo    | bool        | 是否是视频流，视频流的情况下，将isVideo设置为true，能够提升检测速度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_Detect(std::string imgPath)
	{
	    IFaceDetection* faceDetection = IFaceDetection::create(0);
	
	    do {
	        cv::Mat oriImgMat = cv::imread(imgPath); // load image
	        IMG img;
	        img.format = IMG_BGR24;
	        img.buffer = IMG_BUFFER(oriImgMat.data,
	                                oriImgMat.total() * oriImgMat.channels(),
	                                oriImgMat.total() * oriImgMat.channels());
	        img.width = oriImgMat.cols;
	        img.height = oriImgMat.rows;
	        FACE_INFO faceList[10];
	        int faceNum = 0;
	        RET_CODE ret = faceDetection->detect(&img, faceList, 10, &faceNum);
	        if (RET_OK != ret)
	        {
	            printf("face detect failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("face detect success\n");
	    } while (0);
	
	    IFaceDetection::destroy(faceDetection);
	}


人脸对齐
========

创建人脸对齐检测实例
--------------------

**接口功能**

创建人脸对齐检测实例。

**接口声明**

	static IFaceAlignment* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**         | **描述**                       |
|------------------|--------------------------------|
| IFaceAlignment\* | 人脸对齐检测实例，失败返回NULL |

销毁人脸对齐检测实例
--------------------

**接口功能**

销毁人脸对齐检测实例。

**接口声明**

	static void destroy(IFaceAlignment* instance);

**参数描述**

| **字段名** | **类型**         | **说明**         |
|------------|------------------|------------------|
| instance   | IFaceAlignment\* | 人脸对齐检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取对齐人脸尺寸
----------------

**接口功能**

获取对齐后的人脸图像尺寸。

**接口声明**

	int getAlignedSize();

**参数描述**

无

**返回值**

| **类型** | **描述**             |
|----------|----------------------|
| int      | 对齐后的人脸图像尺寸 |

获取人脸对齐
------------

**接口功能**

获取归一化人脸。

**接口声明**

	RET_CODE getAlignedFace(IMG* img, FACE_KEYS* keys, ALIGNED_IMG* alignedImg, bool padding = false);

**参数描述**

| **字段名** | **类型**      | **说明**                                           |
|------------|---------------|----------------------------------------------------|
| img        | [IMG](#原始图像信息)\*   | 待检测图片信息                               |
| keys       | [FACE_KEYS](#人脸关键点)\*   | 人脸关键点                                         |
| alignedImg | [ALIGNED_IMG](#对齐人脸图像)\* | [OUT]人脸对齐图片，外部分配内存                    |
| padding    | bool          | 若为true，则在图像变换的时候采用周边的像素进行补充 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_Alignment(IMG* img, FACE_KEYS* keys)
	{
	    IFaceAlignment* faceAlignment = IFaceAlignment::create(0);
	
	    do
	    {
	        int alignedSize = faceAlignment->getAlignedSize();
	        ALIGNED_IMG alignedImg;
	        alignedImg.buffer.data = new unsigned char[alignedSize];
	        alignedImg.buffer.capacity = alignedSize;
	        alignedImg.buffer.len = 0;
	        RET_CODE ret = faceAlignment->getAlignedFace(img, keys, &alignedImg);
	        if (RET_OK != ret)
	        {
	            printf("face align failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            if (NULL != alignedImg.buffer.data) {
	                delete [] alignedImg.buffer.data;
	                alignedImg.buffer.data = NULL;
	            }
	            break;
	        }
	        printf("face alignment success\n");
	    } while (0);
	
	    IFaceAlignment::destroy(faceAlignment);
	}


人脸质量
========

创建人脸质量检测实例
--------------------

**接口功能**

创建人脸质量检测实例。

**接口声明**

	static IFaceQuality* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**       | **描述**                       |
|----------------|--------------------------------|
| IFaceQuality\* | 人脸质量检测实例，失败返回NULL |

销毁人脸质量检测实例
--------------------

**接口功能**

销毁人脸质量检测实例。

**接口声明**

	static void destroy(IFaceQuality* instance);

**参数描述**

| **字段名** | **类型**       | **说明**         |
|------------|----------------|------------------|
| instance   | IFaceQuality\* | 人脸质量检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取人脸质量
------------

**接口功能**

获取人脸质量。

**接口声明**

	RET_CODE getFaceQuality(IMG* img, FACE_RECT* rect, float* quality);

**参数描述**

| **字段名** | **类型**    | **说明**                            |
|------------|-------------|-------------------------------------|
| img        | [IMG](#原始图像信息)\*       | 待检测图片信息                      |
| rect       | [FACE_RECT](#矩形框)\* | 人脸框                              |
| quality    | float\*     | [OUT]人脸质量检测结果，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_Quality(IMG* img, FACE_RECT* rect)
	{
	    IFaceQuality* faceQuality = IFaceQuality::create(0);
	
	    do {
	        float quality = 0.0;
	        RET_CODE ret = faceQuality->getFaceQuality(img, rect, &quality);
	        if (RET_OK != ret)
	        {
	            printf("face detect failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("get face quality success! quality: %f\n", quality);
	    } while (0);
	
	    IFaceQuality::destroy(faceQuality);
	}


人脸关键点
==========

创建人脸关键点检测实例
----------------------

**接口功能**

创建人脸关键点检测实例。

**接口声明**

	static IFaceKeyPoints* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**         | **描述**                         |
|------------------|----------------------------------|
| IFaceKeyPoints\* | 人脸关键点检测实例，失败返回NULL |

销毁人脸关键点检测实例
----------------------

**接口功能**

销毁人脸关键点检测实例。

**接口声明**

	static void destroy(IFaceKeyPoints* instance);

**参数描述**

| **字段名** | **类型**         | **说明**           |
|------------|------------------|--------------------|
| instance   | IFaceKeyPoints\* | 人脸关键点检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取人脸关键点
--------------

**接口功能**

获取人脸关键点。

**接口声明**

	RET_CODE getFaceKeyPoints(IMG* img, FACE_RECT* rect, FACE_KEYS* keys);

**参数描述**

| **字段名** | **类型**    | **说明**                              |
|------------|-------------|---------------------------------------|
| img        | [IMG](#原始图像信息)\*       | 待检测图片信息                        |
| rect       | [FACE_RECT](#矩形框)\* | 人脸框                                |
| keys       | [FACE_KEYS](#人脸关键点)   | [OUT]人脸关键点检测结果，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_KeyPoints(IMG* img, FACE_RECT* rect)
	{
	    IFaceKeyPoints* faceKeyPoints = IFaceKeyPoints::create(0);
	
	    do
	    {
	        FACE_KEYS keys;
	        RET_CODE ret = faceKeyPoints->getFaceKeyPoints(img, rect, &keys);
	        if (RET_OK != ret)
	        {
	            printf("face keys failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("face keypoints success\n");
	    } while (0);
	
	    IFaceKeyPoints::destroy(faceKeyPoints);
	}


人脸属性
========

创建人脸属性检测实例
--------------------

**接口功能**

创建人脸属性检测实例。

**接口声明**

	static IFaceAttribute* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**         | **描述**                       |
|------------------|--------------------------------|
| IFaceAttribute\* | 人脸属性检测实例，失败返回NULL |

销毁人脸属性检测实例
--------------------

**接口功能**

销毁人脸属性检测实例。

**接口声明**

	static void destroy(IFaceAttribute* instance);

**参数描述**

| **字段名** | **类型**         | **说明**         |
|------------|------------------|------------------|
| instance   | IFaceAttribute\* | 人脸属性检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取人脸属性
------------

**接口功能**

获取人脸属性。

**接口声明**

	RET_CODE getFaceAttribute(ALIGNED_IMG* alignedImg, FACE_ATTR* attr);

**参数描述**

| **字段名** | **类型**      | **说明**                            |
|------------|---------------|-------------------------------------|
| alignedImg | [ALIGNED_IMG](#对齐人脸图像)\* | 人脸对齐图片                        |
| attr       | [FACE_ATTR](#人脸属性结构)\*   | [OUT]人脸属性检测结果，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_Attributes(ALIGNED_IMG* alignedImg)
	{
	    IFaceAttribute* faceAttribute = IFaceAttribute::create(0);
	
	    do
	    {
	        FACE_ATTR faceAttr;
	        RET_CODE ret = faceAttribute->getFaceAttribute(alignedImg, &faceAttr);
	        if (RET_OK != ret)
	        {
	            printf("face attr failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("face attribute success\n");
	    } while (0);
	
	    IFaceAttribute::destroy(faceAttribute);
	}


人脸眼睛状态
============

创建人脸眼睛状态检测实例
------------------------

**接口功能**

创建人脸眼睛状态检测实例。

**接口声明**

	static IFaceEyesState* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**         | **描述**                           |
|------------------|------------------------------------|
| IFaceEyesState\* | 人脸眼睛状态检测实例，失败返回NULL |

销毁人脸眼睛状态检测实例
------------------------

**接口功能**

销毁人脸眼睛状态检测实例。

**接口声明**

	static void destroy(IFaceEyesState* instance);

**参数描述**

| **字段名** | **类型**         | **说明**             |
|------------|------------------|----------------------|
| instance   | IFaceEyesState\* | 人脸眼睛状态检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取人脸眼睛状态
----------------

**接口功能**

获取人脸眼睛状态。

**接口声明**

	RET_CODE getFaceEyesState(IMG* img, FACE_KEYS* keys, FACE_EYES_STATE* eyesState);

**参数描述**

| **字段名** | **类型**           | **说明**                                |
|------------|--------------------|-----------------------------------------|
| img        | [IMG](#原始图像信息)\*              | 待检测图片信息                          |
| keys       | [FACE_KEYS](#人脸关键点)\*        | 人脸关键点信息                          |
| eyesState  | [FACE_EYES_STATE](#眼睛状态) \* | [OUT]人脸眼睛状态检测结果，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_EyesState(IMG* img, FACE_KEYS* keys)
	{
	    IFaceEyesState* faceEyesState = IFaceEyesState::create(0);
	
	    do
	    {
	        FACE_EYES_STATE eyesState;
	        RET_CODE ret = faceEyesState->getFaceEyesState(img, keys, &eyesState);
	        if (RET_OK != ret)
	        {
	            printf("eyes state failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("get face eyes state success! eyes close probability, left: %f, right: %f\n", eyesState.left, eyesState.right);
	    }
	    while (0);
	
	    IFaceEyesState::destroy(faceEyesState);
	}


人脸嘴巴状态
============

创建人脸嘴巴状态检测实例
------------------------

**接口功能**

创建人脸嘴巴状态检测实例。

**接口声明**

	static IFaceMouthState* create(int deviceId = 0)

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**          | **描述**                           |
|-------------------|------------------------------------|
| IFaceMouthState\* | 人脸嘴巴状态检测实例，失败返回NULL |

销毁人脸嘴巴状态检测实例
------------------------

**接口功能**

销毁人脸嘴巴状态检测实例。

**接口声明**

	static void destroy(IFaceMouthState* instance);

**参数描述**

| **字段名** | **类型**          | **说明**             |
|------------|-------------------|----------------------|
| instance   | IFaceMouthState\* | 人脸嘴巴状态检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取人脸嘴巴状态
----------------

**接口功能**

获取人脸嘴巴状态。

**接口声明**

	RET_CODE getFaceMouthState(IMG* img, FACE_KEYS* keys, FACE_MOUTH_STATE* mouthState);

**参数描述**

| **字段名** | **类型**            | **说明**                                |
|------------|---------------------|-----------------------------------------|
| img        | [IMG](#原始图像信息)\*               | 待检测图片信息                          |
| keys       | [FACE_KEYS](#人脸关键点)\*         | 人脸关键点信息                          |
| mouthState | [FACE_MOUTH_STATE](#嘴巴状态) \* | [OUT]人脸嘴巴状态检测结果，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_MouthState(IMG* img, FACE_KEYS* keys)
	{
	    IFaceMouthState* faceMouthState = IFaceMouthState::create(0);
	
	    do
	    {
	        FACE_MOUTH_STATE mouthState;
	        RET_CODE ret = faceMouthState->getFaceMouthState(img, keys, &mouthState);
	        if (RET_OK != ret)
	        {
	            printf("mouth state failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("get face mouth state success! mouth close probability: %f\n", mouthState.state);
	    }
	    while (0);
	
	    IFaceMouthState::destroy(faceMouthState);
	}


人脸活体检测
============

创建人脸活体检测实例
--------------------

**接口功能**

创建人脸活体检测实例。

**接口声明**

	static IFaceLive* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**    | **描述**                       |
|-------------|--------------------------------|
| IFaceLive\* | 人脸活体检测实例，失败返回NULL |

销毁人脸活体检测实例
--------------------

**接口功能**

销毁人脸活体检测实例。

**接口声明**

	static void destroy(IFaceLive* instance);

**参数描述**

| **字段名** | **类型**    | **说明**         |
|------------|-------------|------------------|
| instance   | IFaceLive\* | 人脸活体检测实例 |

**返回值**

无

设置配置文件路径
----------------

**接口功能**

设置配置文件路径。

**接口声明**

	RET_CODE setConfigPath(const char* configPath, int len);

**参数描述**

| **字段名** | **类型**     | **说明**               |
|------------|--------------|------------------------|
| configPath | const char\* | 配置文件路径字符串指针 |
| len        | int          | 配置文件路径字符串长度 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取默认参数信息
----------------

**接口功能**

获取默认参数信息。

**接口声明**

	RET_CODE getDefaultParam(FD_PARAM* param)

**参数描述**

| **字段名** | **类型**   | **说明**                        |
|------------|------------|---------------------------------|
| param      | [FD_PARAM](#人脸检测参数)\* | [OUT]人脸检测参数，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

获取当前参数信息
----------------

**接口功能**

获取当前参数信息。

**接口声明**

	RET_CODE getParam(FD_PARAM* param)

**参数描述**

| **字段名** | **类型**   | **说明**                        |
|------------|------------|---------------------------------|
| param      | [FD_PARAM](#人脸检测参数)\* | [OUT]人脸检测参数，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

设置参数
--------

**接口功能**

设置参数信息。

**接口声明**

	RET_CODE setParam(const FD_PARAM* param)

**参数描述**

| **字段名** | **类型**         | **说明**     |
|------------|------------------|--------------|
| param      | const [FD_PARAM](#人脸检测参数)\* | 人脸检测参数 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

人脸活体检测
------------

**接口功能**

获取人脸活体检测结果。

**接口声明**

	RET_CODE detectLive(IMG* infrImg, FACE_RECT* rect, bool* live);

**参数描述**

| **字段名** | **类型**    | **说明**                            |
|------------|-------------|-------------------------------------|
| infrImg    | [IMG](#原始图像信息)\*       | 红外图片信息                        |
| rect       | [FACE_RECT](#矩形框)\* | 自然光图片人脸框                    |
| live       | bool\*      | [OUT]人脸活体检测结果，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_LiveDetect(std::string infrImgPath, std::string natuImgPath)
	{
	    IFaceDetection* faceDetection = IFaceDetection::create(0);
	    IFaceLive* faceLive = IFaceLive::create(0);
	
	    do
	    {
	        /// detect natural light image face
	        cv::Mat natuImgMat = cv::imread(natuImgPath);
	        IMG natuImg;
	        natuImg.format = IMG_BGR24;
	        natuImg.buffer = IMG_BUFFER(natuImgMat.data,
	                                    natuImgMat.total() * natuImgMat.channels(),
	                                    natuImgMat.total() * natuImgMat.channels());
	        natuImg.width = natuImgMat.cols;
	        natuImg.height = natuImgMat.rows;
	        FACE_INFO faceList[10];
	        int faceNum = 0;
	        RET_CODE ret = faceDetection->detect(&natuImg, faceList, 10, &faceNum);
	        if (RET_OK != ret)
	        {
	            printf("face detect failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	
	        /// live detect
	        cv::Mat infrImgMat = cv::imread(infrImgPath);
	        IMG infrImg;
	        infrImg.format = IMG_BGR24;
	        infrImg.buffer = IMG_BUFFER(infrImgMat.data,
	                                   infrImgMat.total() * infrImgMat.channels(),
	                                   infrImgMat.total() * infrImgMat.channels());
	        infrImg.width = infrImgMat.cols;
	        infrImg.height = infrImgMat.rows;
	        bool live;
	        RET_CODE ret2 = faceLive->detectLive(&infrImg, &(faceList[0].rect), &live);
	        if (RET_OK != ret2)
	        {
	            printf("live detect failed, code: %d, msg: %s\n", ret2, getRetMsg(ret2));
	            break;
	        }
	        printf("get live detect success! >>> %s <<<\n", live ? "Living" : "Fake");
	    }
	    while (0);
	
	    IFaceDetection::destroy(faceDetection);
	    IFaceLive::destroy(faceLive);
	}


人脸特征提取
============

创建人脸特征提取实例
--------------------

**接口功能**

创建人脸特征提取实例。

**接口声明**

	static IFaceFeature* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**       | **描述**                       |
|----------------|--------------------------------|
| IFaceFeature\* | 人脸特征提取实例，失败返回NULL |

销毁人脸特征提取实例
--------------------

**接口功能**

销毁人脸特征提取实例。

**接口声明**

	static void destroy(IFaceFeature* instance);

**参数描述**

| **字段名** | **类型**       | **说明**         |
|------------|----------------|------------------|
| instance   | IFaceFeature\* | 人脸特征提取实例 |

**返回值**

无

获取人脸特征维数
----------------

**接口功能**

获取人脸特征维数。

**接口声明**

	int getFeatureDim();

**参数描述**

无

**返回值**

| **类型** | **描述**     |
|----------|--------------|
| int      | 人脸特征维数 |

人脸特征提取
------------

**接口功能**

提取人脸特征。

**接口声明**

	RET_CODE getFaceFeature(ALIGNED_IMG* alignedImg, FACE_FEATURE* feature);

**参数描述**

| **字段名** | **类型**       | **说明**                    |
|------------|----------------|-----------------------------|
| alignedImg | [ALIGNED_IMG](#对齐人脸图像)\*  | 人脸对齐图片                |
| feature    | [FACE_FEATURE](#特征值)\* | [OUT]人脸特征，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_EyesState(ALIGNED_IMG* alignedImg, FACE_FEATURE* feature)
	{
	    IFaceFeature* faceFeature = IFaceFeature::create(0);
	
	    do
	    {
	        int dim = faceFeature->getFeatureDim();
	        feature->data = new float[dim];
	        feature->capacity = dim;
	        feature->len = 0;
	        RET_CODE ret = faceFeature->getFaceFeature(alignedImg, feature);
	        if (RET_OK != ret)
	        {
	            printf("face feature failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("get face feature success! feature dim: %d\n", feature->len);
	    }
	    while (0);
	
	    IFaceFeature::destroy(faceFeature);
	}


人脸比对
========

创建人脸比对实例
----------------

**接口功能**

创建人脸比对实例。

**接口声明**

	static IFaceCompare* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**       | **描述**                   |
|----------------|----------------------------|
| IFaceCompare\* | 人脸比对实例，失败返回NULL |

销毁人脸比对实例
----------------

**接口功能**

销毁人脸比对实例。

**接口声明**

	static void destroy(IFaceCompare\* instance);

**参数描述**

| **字段名** | **类型**       | **说明**     |
|------------|----------------|--------------|
| instance   | IFaceCompare\* | 人脸比对实例 |

**返回值**

无

人脸特征比对
------------

**接口功能**

获取人脸特征比对结果。

**接口声明**

	RET_CODE compare(FACE_FEATURE* feature1, FACE_FEATURE* feature2, float* score);

**参数描述**

| **字段名** | **类型**       | **说明**                          |
|------------|----------------|-----------------------------------|
| feature1   | [FACE_FEATURE](#特征值)\* | 人脸特征1                         |
| feature2   | [FACE_FEATURE](#特征值)\* | 人脸特征2                         |
| score      | float\*        | [OUT]人脸比对相似度，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

人脸图片比对
------------

**接口功能**

获取人脸图片比对结果。

**接口声明**

	RET_CODE compare(IMG* img1, IMG* img2, float* score);

**参数描述**

| **字段名** | **类型** | **说明**                          |
|------------|----------|-----------------------------------|
| img1       | [IMG](#原始图像信息)\*    | 人脸图片1                         |
| img2       | [IMG](#原始图像信息)\*    | 人脸图片2                         |
| score      | float\*  | [OUT]人脸比对相似度，外部分配内存 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_Compare(FACE_FEATURE* feature1, FACE_FEATURE* feature2)
	{
	    IFaceCompare* faceCompare = IFaceCompare::create(0);
	
	    do
	    {
	        float score = 0;
	        RET_CODE ret = faceCompare->compare(feature1, feature2, &score);
	        if (RET_OK != ret)
	        {
	            printf("face compare failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("face compare success! score: %f\n", score);
	    }
	    while (0);
	
	    IFaceFeature::destroy(faceFeature);
	}
	
	void TEST_ImgCompare(IMG* img1, IMG* img2)
	{
	    IFaceCompare* faceCompare = IFaceCompare::create(0);
	
	    do
	    {
	        float score = 0;
	        RET_CODE ret = faceCompare->compare(img1, img2, &score);
	        if (RET_OK != ret)
	        {
	            printf("face compare failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("face compare success! score: %f\n", score);
	    }
	    while (0);
	
	    IFaceFeature::destroy(faceFeature);
	}


人脸识别
========

创建人脸识别实例
----------------

**接口功能**

创建人脸识别实例。

**接口声明**

	static IFaceRecognition* create(int deviceId = 0);

**参数描述**

| **字段名** | **类型** | **说明**                               |
|------------|----------|----------------------------------------|
| deviceId   | int      | 当使用GPU时表示GPU编号, 否则忽略该参数 |

**返回值**

| **类型**           | **描述**                   |
|--------------------|----------------------------|
| IFaceRecognition\* | 人脸识别实例，失败返回NULL |

销毁人脸识别实例
----------------

**接口功能**

销毁人脸识别实例。

**接口声明**

	static void destroy(IFaceRecognition* instance);

**参数描述**

| **字段名** | **类型**           | **说明**     |
|------------|--------------------|--------------|
| instance   | IFaceRecognition\* | 人脸识别实例 |

**返回值**

无

初始化特征数据库
----------------

**接口功能**

初始化特征数据库。

**接口声明**

	RET_CODE initDatabase(int featureDim, int capacity);

**参数描述**

| **字段名** | **类型** | **说明**       |
|------------|----------|----------------|
| featureDim | int      | 特征值维数     |
| capacity   | int      | 特征数据库容量 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

设置特征数据库容量
------------------

**接口功能**

设置特征数据库容量。

**接口声明**

	RET_CODE setCapacity(int capacity, bool reserve = true);

**参数描述**

| **字段名** | **类型** | **说明**       |
|------------|----------|----------------|
| capacity   | int      | 特征数据库容量 |
| reserve    | bool     | 是否保留原数据 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

添加人脸特征
------------

**接口功能**

添加人脸特征到数据库。

**接口声明**

	RET_CODE addFaceFeature(FACE_FEATURE* feature, int* index, void* data = NULL);

**参数描述**

| **字段名** | **类型**       | **说明**                        |
|------------|----------------|---------------------------------|
| feature    | [FACE_FEATURE](#特征值)\* | 人脸特征                        |
| index      | int\*          | [OUT]人脸特征索引，外部分配内存 |
| data       | void\*         | 用户数据                        |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

删除人脸特征
------------

**接口功能**

从数据库中删除人脸特征。

**接口声明**

	RET_CODE delFaceFeature(int index);

**参数描述**

| **字段名** | **类型** | **说明**     |
|------------|----------|--------------|
| index      | int      | 人脸特征索引 |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

人脸识别
--------

**接口功能**

人脸识别。

**接口声明**

	RET_CODE recognize(FACE_FEATURE* feature, int topN, FACE_RECOG_RESULT* resultList, int size, int* resultNum);

**参数描述**

| **字段名** | **类型**            | **说明**                            |
|------------|---------------------|-------------------------------------|
| feature    | [FACE_FEATURE](#特征值)\*      | 人脸特征                            |
| topN       | int                 | 最多返回最高相似度人脸的个数        |
| resultList | [FACE_RECOG_RESULT](#人脸识别结果)\* | [OUT]人脸识别结果数组，外部分配内存 |
| size       | int                 | 人脸识别结果数组大小                |
| resultNum  | int\*               | 人脸识别结果个数                    |

**返回值**

| **类型** | **描述** |
|----------|----------|
| [RET_CODE](#返回码) | 返回码   |

示例代码
--------

	using namespace HWN::Face;
	
	void TEST_Recognition(FACE_FEATURE* featureList, int size, FACE_FEATURE* feature)
	{
	    IFaceRecognition* faceRecognition = IFaceRecognition::create(0);
	
	    do
	    {
	        // Allocate Database
	        IFaceFeature* faceFeature = IFaceFeature::create(0);
	        int featureDim = faceFeature->getFeatureDim();
	        IFaceFeature::destroy(faceFeature);
	        faceRecognition->initDatabase(featureDim, size);
	        //faceRecognition->setCapacity(10);
	
	        for (int i = 0; i < size; i++)
	        {
	            int index = 0;
	            faceRecognition->addFaceFeature(featureList[i], &index);
	        }
	
	        FACE_RECOG_RESULT resultList[4];
	        int resultNum;
	        RET_CODE ret = faceRecognition->recognize(feature, 4, resultList, 4, &resultNum);
	        if (RET_OK != ret)
	        {
	            printf("face recognize failed, code: %d, msg: %s\n", ret, getRetMsg(ret));
	            break;
	        }
	        printf("face recognition success! result num: %d\n", resultNum);
	        for (int i = 0; i < resultNum; i++)
	        {
	            printf("index[%d] score[%f]\n", resultList[i].index, resultList[i].score);
	        }
	    }
	    while (0);
	
	    IFaceRecognition::destroy(faceRecognition);
	}
