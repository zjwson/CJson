# 一.cJSON详解

## 1.1 JSON介绍

JSON：JavaScript对象表示法（JavaScript Object Notation）。是一种轻量级的数据交换格式。它基于ECMAScript的一个子集。JSON采用完全独立于语言的文本格式，但是也使用了类似C语音家族的习惯（包括C、C++、C#、Java、JavaScript、Perl、Python等）。这些特性使JSON成为理想的数据交换语言。易于人阅读和编写，同时也易于机器解析和生成（一般用于提升网络传输速率）。

JSON解析器和JSON库支持许多不同的编程语言。JSON文本格式在语法上与创建JavaScript对象的代码相同。由于这种相似性，无需解释器，JavaScript程序能够使用内建的eval()函数，用JSON数据来生成原生的JavaScript对象。

JSON是存储和交换文本信息的语法，类似XML。但JSON比XML更小、更快、更易解析；且JSON具有自我描述性、语法简单，易于理解。

## 1.2 JSON语法

JSON语法是JavaScript对象表示法语法的子集：

- 数据在键/值对中；
- 数据由逗号分隔；
- 花括号保存对象，也称一个文档对象；
- 方括号保存数组，每个数组成员用逗号隔开，并且每个数组成员可以是文档对象或者数组或者键值对。

JSON的三种语法：

- 键/值对key:value，用半角冒号分割。比如"name":"Faye"。
- 文档对象JSON对象写在花括号中，可以包含多个键/值对。比如{"name":"Faye", "address":"北京"}。
- 数组JSON数组在方括号中书写：数组成员可以是对象、值，也可以是数组(只要有意义)。比如{"love": ["乒乓球","高尔夫","斯诺克","羽毛球","LOL","撩妹"]}。

JSON的值可以是：数字（整数或浮点数）、字符串（在双引号中）、逻辑值（true或false）、数组（在方括号中）、对象（在花括号中）、null。

JSON结构有两种：

- 对象：对象在js中表示为“{}”括起来的内容，数据结构为{key：value,key：value,...}的键值对的结构，在面向对象的语言中，key为对象的属性，value为对应的属性值，所以很容易理解，取值方法为 对象.key 获取属性值；
- 数组：数组在js中是中括号“[]”括起来的内容，数据结构为["java","javascript","vb",...]，取值方式和所有语言中一样，使用索引获取。

经过对象、数组2种结构就可以组合成复杂的数据结构了。

# 二、CJSON介绍

cJSON是一个仅有一个.h文件，一个.c文件组成的JSON解析器，它是由纯C(ANSI C89)实现的，跨平台性较好。cJSON是采用链表存储的。

cJSON库在使用的时候只需要如下两步：将cJSON.c(或者库文件)和cJSON.h添加到项目中即可；如果在命令行中进行链接还需要加上-lm表示链接math库。

## 2.1 源码获取

在GitHub上的地址为：[git@github.com](https://links.jianshu.com/go?to=mailto%3Agit%40github.com):zjwson/CJson.git。通过clone获取：

```ruby
$ git clone git@github.com:zjwson/CJson.git
```

## 2.2 安装与移植

安装和编译cJSON的只需要五步(cmake)：



```bash
mkdir build
cd build
cmake ..
make
main install
```

By default it installs the headers /usr/local/include/cjson and the libraries to /usr/local/lib. It also installs files for pkg-config to make it easier to detect and use an existing installation of CMake. And it installs CMake config files, that can be used by other CMake based projects to discover the library.

也可通过在cmake命令执行时，传入-DCMAKE_INSTALL_PREFIX=/yourdir/修改指定的安装目录。常用的可配置选项如下：



```jsx
* `-DENABLE_CJSON_TEST=On`: Enable building the tests. (on by default)
* `-DENABLE_CJSON_UTILS=On`: Enable building cJSON_Utils. (off by default)
* `-DENABLE_TARGET_EXPORT=On`: Enable the export of CMake targets. Turn off if it makes problems. (on by default)
* `-DENABLE_CUSTOM_COMPILER_FLAGS=On`: Enable custom compiler flags (currently for Clang, GCC and MSVC). Turn off if it makes problems. (on by default)
* `-DENABLE_VALGRIND=On`: Run tests with [valgrind](http://valgrind.org). (off by default)
* `-DENABLE_SANITIZERS=On`: Compile cJSON with [AddressSanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizer) and [UndefinedBehaviorSanitizer](https
://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html) enabled (if possible). (off by default)
* `-DENABLE_SAFE_STACK`: Enable the [SafeStack](https://clang.llvm.org/docs/SafeStack.html) instrumentation pass. Currently only works with the Clang compiler. (off
 by default)
* `-DBUILD_SHARED_LIBS=On`: Build the shared libraries. (on by default)
* `-DBUILD_SHARED_AND_STATIC_LIBS=On`: Build both shared and static libraries. (off by default)
* `-DCMAKE_INSTALL_PREFIX=/usr`: Set a prefix for the installation.
* `-DENABLE_LOCALES=On`: Enable the usage of localeconv method. ( on by default )
* `-DCJSON_OVERRIDE_BUILD_SHARED_LIBS=On`: Enable overriding the value of `BUILD_SHARED_LIBS` with `-DCJSON_BUILD_SHARED_LIBS`.
```

比如要为Linux发布一个cJSON包，可以这样：



```bash
mkdir build
cd build
cmake .. -DENABLE_CJSON_UTILS=On -DENABLE_CJSON_TEST=Off -DCMAKE_INSTALL_PREFIX=/usr
make
make DESTDIR=$pkgdir install
```

如果是编译嵌入式版本的gflags，还需要在ccmake ..步骤中修改所有对应的交叉编译工具，并且系统中已经通过PATH指定了交叉编译环境。

# 三、cJSON数据结构与接口

整体来讲，cJSON库的数据结构和函数命名接口非常规范，基本上一看就懂，非常容易理解。

## 3.1 数据结构定义



```cpp
/* cJSON Types: */
#define cJSON_Invalid (0)
#define cJSON_False  (1 << 0)
#define cJSON_True   (1 << 1)
#define cJSON_NULL   (1 << 2)
#define cJSON_Number (1 << 3)
#define cJSON_String (1 << 4)
#define cJSON_Array  (1 << 5)
#define cJSON_Object (1 << 6)
#define cJSON_Raw    (1 << 7) /* raw json */

#define cJSON_IsReference 256
#define cJSON_StringIsConst 512

/* The cJSON structure: */
typedef struct cJSON
{
    /* next/prev allow you to walk array/object chains. Alternatively, use GetArraySize/GetArrayItem/GetObjectItem */
    struct cJSON *next;
    struct cJSON *prev;
    /* An array or object item will have a child pointer pointing to a chain of the items in the array/object. */
    struct cJSON *child;

    /* The type of the item, as above. */
    int type;

    /* The item's string, if type==cJSON_String  and type == cJSON_Raw */
    char *valuestring;
    /* writing to valueint is DEPRECATED, use cJSON_SetNumberValue instead */
    int valueint;
    /* The item's number, if type==cJSON_Number */
    double valuedouble;

    /* The item's name string, if this item is the child of, or is in the list of subitems of an object. */
    char *string;
} cJSON;
```

next、prev用于遍历数组或对象链的前向后向链表指针；child指向数组或对象的孩子节点；type是value的类型；valuestring是字符串值；valueint是整数值；valuedouble是浮点数值；string是key的名字。还是非常容易理解的。

## 3.2 cJSON_Parse



```csharp
/* Supply a block of JSON, and this returns a cJSON object you can interrogate. */
CJSON_PUBLIC(cJSON *) cJSON_Parse(const char *value);
```

作用：将一个JSON字符串，按照cJSON结构体的结构序列化整个数据包，并在堆中开辟一块内存存储cJSON结构体。

返回值：成功返回一个指向内存块中的cJSON的指针，失败返回NULL。

## 3.3 cJSON_Delete



```cpp
/* Delete a cJSON entity and all subentities. */
CJSON_PUBLIC(void) cJSON_Delete(cJSON *c);
```

作用：释放位于堆中cJSON结构体内存。

返回值：无

注意：在使用cJSON_Parse()获取cJSON指针后，若不再使用了，则需要调用cJSON_Delete()对其释放，否则会导致内存泄漏。

## 3.4 cJSON_Print



```cpp
/* Render a cJSON entity to text for transfer/storage. */
CJSON_PUBLIC(char *) cJSON_Print(const cJSON *item);
/* Render a cJSON entity to text for transfer/storage without any formatting. */
CJSON_PUBLIC(char *) cJSON_PrintUnformatted(const cJSON *item);
```

作用：将cJSON数据解析成JSON字符串，并在堆中开辟一块char*的内存空间存储JSON字符串。cJSON_PrintUnformatted()与cJSON_Print()类似，只是打印输出不带格式，而只是一个字符串数据。

返回值：成功返回一个char*指针该指针指向位于堆中JSON字符串，失败返回NULL。

注意：通过cJSON_Print()可以将cJSON链表中所有的cJSON对象打印出来，但是需要手动去释放对应的资源：free(char *)。

## 3.5 cJSON_Version



```cpp
/* returns the version of cJSON as a string */
CJSON_PUBLIC(const char*) cJSON_Version(void);
```

作用：获取当前使用的cJSON库的版本号。

返回值：返回一个字符串数据。

## 3.6 cJSON_GetArrayItem



```csharp
/* Get item "string" from object. Case insensitive. */
CJSON_PUBLIC(cJSON *) cJSON_GetObjectItem(const cJSON * const object, const char * const string);
```

作用：从object的cJSON链中寻找key为string的cJSON对象。

返回值：成功返回一个指向cJSON类型的结构体指针，失败返回NULL。

与cJSON_GetObjectItem()类似的接口还有(相信不用解释就能看明白)：



```cpp
/* Returns the number of items in an array (or object). */
CJSON_PUBLIC(int) cJSON_GetArraySize(const cJSON *array);
/* Retrieve item number "index" from array "array". Returns NULL if unsuccessful. */
CJSON_PUBLIC(cJSON *) cJSON_GetArrayItem(const cJSON *array, int index);
CJSON_PUBLIC(cJSON *) cJSON_GetObjectItemCaseSensitive(const cJSON * const object, const char * const string);
CJSON_PUBLIC(cJSON_bool) cJSON_HasObjectItem(const cJSON *object, const char *string);
/* Check if the item is a string and return its valuestring */
CJSON_PUBLIC(char *) cJSON_GetStringValue(cJSON *item);
```

## 3.7 类型判断接口

下面的接口用于判断具体cJSON指针指向的item类型：



```cpp
/* These functions check the type of an item */
CJSON_PUBLIC(cJSON_bool) cJSON_IsInvalid(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsFalse(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsTrue(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsBool(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsNull(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsNumber(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsString(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsArray(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsObject(const cJSON * const item);
CJSON_PUBLIC(cJSON_bool) cJSON_IsRaw(const cJSON * const item);
```

## 3.8 创建cJSON对象接口

下面的接口用于创建指定类型的cJSON数据结构对象：



```cpp
/* raw json */
CJSON_PUBLIC(cJSON *) cJSON_CreateRaw(const char *raw);
CJSON_PUBLIC(cJSON *) cJSON_CreateArray(void);
CJSON_PUBLIC(cJSON *) cJSON_CreateObject(void);
/* These calls create a cJSON item of the appropriate type. */
CJSON_PUBLIC(cJSON *) cJSON_CreateNull(void);
CJSON_PUBLIC(cJSON *) cJSON_CreateTrue(void);
CJSON_PUBLIC(cJSON *) cJSON_CreateFalse(void);
CJSON_PUBLIC(cJSON *) cJSON_CreateBool(cJSON_bool boolean);
CJSON_PUBLIC(cJSON *) cJSON_CreateNumber(double num);
CJSON_PUBLIC(cJSON *) cJSON_CreateString(const char *string);
/* These utilities create an Array of count items. */
CJSON_PUBLIC(cJSON *) cJSON_CreateIntArray(const int *numbers, int count);
CJSON_PUBLIC(cJSON *) cJSON_CreateFloatArray(const float *numbers, int count);
CJSON_PUBLIC(cJSON *) cJSON_CreateDoubleArray(const double *numbers, int count);
CJSON_PUBLIC(cJSON *) cJSON_CreateStringArray(const char **strings, int count);
```

## 3.9 添加cJSON对象到链表

下面的接口用于将创建的cJSON对象添加到链表中：



```cpp
/* Append item to the specified array/object. */
CJSON_PUBLIC(void) cJSON_AddItemToArray(cJSON *array, cJSON *item);
CJSON_PUBLIC(void) cJSON_AddItemToObject(cJSON *object, const char *string, cJSON *item);
```

## 3.10 从cJSON对象中删除一个cJSON对象

下面的接口用于从现有的cJSON链表中删除一个对象：



```csharp
/* Remove/Detatch items from Arrays/Objects. */
CJSON_PUBLIC(cJSON *) cJSON_DetachItemViaPointer(cJSON *parent, cJSON * const item);
CJSON_PUBLIC(cJSON *) cJSON_DetachItemFromArray(cJSON *array, int which);
CJSON_PUBLIC(void) cJSON_DeleteItemFromArray(cJSON *array, int which);
CJSON_PUBLIC(cJSON *) cJSON_DetachItemFromObject(cJSON *object, const char *string);
CJSON_PUBLIC(cJSON *) cJSON_DetachItemFromObjectCaseSensitive(cJSON *object, const char *string);
CJSON_PUBLIC(void) cJSON_DeleteItemFromObject(cJSON *object, const char *string);
CJSON_PUBLIC(void) cJSON_DeleteItemFromObjectCaseSensitive(cJSON *object, const char *string);
```

## 3.11 创建并添加到链表

下面的接口用于创建并添加cJSON对象到指定的链表（简化操作）：



```csharp
/* Helper functions for creating and adding items to an object at the same time.
 * They return the added item or NULL on failure. */
CJSON_PUBLIC(cJSON*) cJSON_AddNullToObject(cJSON * const object, const char * const name);
CJSON_PUBLIC(cJSON*) cJSON_AddTrueToObject(cJSON * const object, const char * const name);
CJSON_PUBLIC(cJSON*) cJSON_AddFalseToObject(cJSON * const object, const char * const name);
CJSON_PUBLIC(cJSON*) cJSON_AddBoolToObject(cJSON * const object, const char * const name, const cJSON_bool boolean);
CJSON_PUBLIC(cJSON*) cJSON_AddNumberToObject(cJSON * const object, const char * const name, const double number);
CJSON_PUBLIC(cJSON*) cJSON_AddStringToObject(cJSON * const object, const char * const name, const char * const string);
CJSON_PUBLIC(cJSON*) cJSON_AddRawToObject(cJSON * const object, const char * const name, const char * const raw);
CJSON_PUBLIC(cJSON*) cJSON_AddObjectToObject(cJSON * const object, const char * const name);
CJSON_PUBLIC(cJSON*) cJSON_AddArrayToObject(cJSON * const object, const char * const name);
```

# 四、使用实例

## 4.1 简单解析一个JSON数据



```cpp
#include <stdio.h>
#include <stdlib.h>
#include "cjson/cJSON.h"

char text[] = "{\"timestamp\":\"2019-03-03 08:45:57\", \"value\":1}";

int main(int argc, const char *argv[])
{
    cJSON *json, *json_value, *json_timestamp;

    json = cJSON_Parse(text);
    if(NULL == json)
    {
        printf("Error before: [%s]\n", cJSON_GetErrorPtr());
        return -1;
    }

    json_value = cJSON_GetObjectItem(json, "value");
    if(json_value->type == cJSON_Number)
    {
        printf("value: %d\n", json_value->valueint);
    }

    json_timestamp = cJSON_GetObjectItem(json, "timestamp");
    if(json_timestamp->type == cJSON_String)
    {
        printf("%s\n", json_timestamp->valuestring);
    }

    cJSON_Delete(json);

    return 0;
}
```

输出结果如下：



```csharp
$ ./parse 
value: 1
2019-03-03 08:45:57
```

## 4.2 下面是一个按不同要求解析json的方法



```cpp
#include <stdio.h>
#include <stdlib.h>
#include "cjson/cJSON.h"

void printJson(cJSON * root)//以递归的方式打印json的最内层键值对
{
    int i = 0;
    for(; i<cJSON_GetArraySize(root); i++)   //遍历最外层json键值对
    {
        cJSON * item = cJSON_GetArrayItem(root, i);        
        if(cJSON_Object == item->type)      //如果对应键的值仍为cJSON_Object就递归调用printJson
            printJson(item);
        else                                //值不为json对象就直接打印出键和值
        {
            printf("%s->", item->string);
            printf("%s\n", cJSON_Print(item));
        }
    }
}

int main()
{
    char * jsonStr = "{\"semantic\":{\"slots\":{\"name\":\"张三\"}}, \"rc\":0, \"operation\":\"CALL\", \"service\":\"telephone\", \"text\":\"打电话给张三\"}";
    cJSON * root = NULL;
    cJSON * item = NULL;//cjson对象

    root = cJSON_Parse(jsonStr);     
    if (!root) 
    {
        printf("Error before: [%s]\n",cJSON_GetErrorPtr());
    }
    else
    {
        printf("%s\n", "有格式的方式打印Json:");           
        printf("%s\n\n", cJSON_Print(root));
        printf("%s\n", "无格式方式打印json：");
        printf("%s\n\n", cJSON_PrintUnformatted(root));

        printf("%s\n", "一步一步的获取name 键值对:");
        printf("%s\n", "获取semantic下的cjson对象:");
        item = cJSON_GetObjectItem(root, "semantic");//
        printf("%s\n", cJSON_Print(item));
        printf("%s\n", "获取slots下的cjson对象");
        item = cJSON_GetObjectItem(item, "slots");
        printf("%s\n", cJSON_Print(item));
        printf("%s\n", "获取name下的cjson对象");
        item = cJSON_GetObjectItem(item, "name");
        printf("%s\n", cJSON_Print(item));

        printf("%s:", item->string);   //看一下cjson对象的结构体中这两个成员的意思
        printf("%s\n", item->valuestring);
                        

        printf("\n%s\n", "打印json所有最内层键值对:");
        printJson(root);
    }
    return 0;    
}
```

运行输出：



```rust
有格式的方式打印Json:
{
    "semantic": {
        "slots":    {
            "name": "张三"
        }
    },
    "rc":   0,
    "operation":    "CALL",
    "service":  "telephone",
    "text": "打电话给张三"
}

无格式方式打印json：
{"semantic":{"slots":{"name":"张三"}},"rc":0,"operation":"CALL","service":"telephone","text":"打电话给张三"}

一步一步的获取name 键值对:
获取semantic下的cjson对象:
{
    "slots":    {
        "name": "张三"
    }
}
获取slots下的cjson对象
{
    "name": "张三"
}
获取name下的cjson对象
"张三"
name:张三

打印json所有最内层键值对:
name->"张三"
rc->0
operation->"CALL"
service->"telephone"
text->"打电话给张三"
```

## 4.3 下面实例用于创建一个JSON



```cpp
#include <stdio.h>
#include <stdlib.h>
#include "cjson/cJSON.h"

int main(void)
{
    char *cjson_str = NULL;
    cJSON * root =  cJSON_CreateObject();
    cJSON * item =  cJSON_CreateObject();
    cJSON * next =  cJSON_CreateObject();

    cJSON_AddItemToObject(root, "rc", cJSON_CreateNumber(0));//根节点下添加
    cJSON_AddItemToObject(root, "operation", cJSON_CreateString("CALL"));
    cJSON_AddItemToObject(root, "service", cJSON_CreateString("telephone"));
    cJSON_AddItemToObject(root, "text", cJSON_CreateString("打电话给张三"));
    cJSON_AddItemToObject(root, "semantic", item);//root节点下添加semantic节点
    cJSON_AddItemToObject(item, "slots", next);//semantic节点下添加item节点
    cJSON_AddItemToObject(next, "name", cJSON_CreateString("张三"));//添加name节点

    cjson_str = cJSON_Print(root);
    printf("first json:\n%s\n", cjson_str);
    free(cjson_str);

    cJSON_AddStringToObject(next, "number", "13423452334");
    cJSON_AddNumberToObject(next, "age", 35);
    cJSON_AddBoolToObject(next, "man", 1);

    cjson_str = cJSON_Print(root);
    printf("second json:\n%s\n", cjson_str);
    free(cjson_str);

    cJSON_Delete(root);

    return 0;
}
```

运行输出：



```bash
first json:
{
    "rc":   0,
    "operation":    "CALL",
    "service":  "telephone",
    "text": "打电话给张三",
    "semantic": {
        "slots":    {
            "name": "张三"
        }
    }
}
second json:
{
    "rc":   0,
    "operation":    "CALL",
    "service":  "telephone",
    "text": "打电话给张三",
    "semantic": {
        "slots":    {
            "name": "张三",
            "number":   "13423452334",
            "age":  35,
            "man":  true
        }
    }
}
```

## 4.4 下面实例用于创建一个Array的JSON



```cpp
#include <stdio.h>
#include <stdlib.h>
#include "cjson/cJSON.h"

char *makeArray(int iSize)
{
    int i;
    char *out = NULL;

    cJSON *root = cJSON_CreateArray();
    if(NULL == root)
    {
        printf("create json array failed!");
        return NULL;
    }

    for(i = 0; i < iSize; i++)
    {
        cJSON_AddNumberToObject(root, "hehe", i + 1);
    }

    out = cJSON_Print(root);
    cJSON_Delete(root);
    return out;
}

void parseArray(char *pJson)
{
    int i = 0;
    int iSize = 0;
    cJSON *root = NULL;

    if(NULL == pJson)
        return;

    root = cJSON_Parse(pJson);
    if(NULL == root)
        return;

    iSize = cJSON_GetArraySize(root);
    for(i = 0; i < iSize;i++)
    {
        cJSON *psub = cJSON_GetArrayItem(root, i);
        if(NULL == psub)
            continue;
        printf("value[%2d] : %d\n", i, psub->valueint);
    }

    cJSON_Delete(root);
}

int main(void)
{
    char *json = NULL;
    
    json = makeArray(10);
    printf("create json: %s\n", json);
    parseArray(json);
    free(json);

    return 0;
}
```

执行运行结果如下：



```css
create json: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
value[ 0] : 1
value[ 1] : 2
value[ 2] : 3
value[ 3] : 4
value[ 4] : 5
value[ 5] : 6
value[ 6] : 7
value[ 7] : 8
value[ 8] : 9
value[ 9] : 10
```



