---
title: Apidoc生成Restful web Api文档
date: 2019-07-14 15:16:50
tags:
    - Apidoc
    - Api文档
toc: true
---
## Apidoc生成Restful web Api文档

> 工具名称：apiDoc
> Git地址：[https://github.com/apidoc/apidoc](https://github.com/apidoc/apidoc)
> 项目地址：[http://apidocjs.com/](http://apidocjs.com/)
> 样例项目：[http://apidocjs.com/example_basic/](http://apidocjs.com/example_basic/)

#### 一、安装apidoc模块

> apidoc是nodejs模块，github上有安装和使用说明。

```linux
# npm install apidoc -g
```

#### 二、在代码文件夹的根目录下，创建apidoc.json文件

```json
{
  "name": "xx平台",
  "version": "0.1.0",
  "description": "为您提供优质的数据服务",
  "title": "xx平台",
  "url" : "http://127.0.0.1:8080/demo",
  "template":{
    "forceLanguage":"zh-cn"
  }
}

```

> 配置属性如下: 
> name：项目名称 
> version：项目版本 
> description：项目介绍 
> title：浏览器显示的标题内容 
> url：endpoints的前缀，例如[https://api.github.com/v1](https://api.github.com/v1)
> sampleUrl：如果设置了，则在api文档中出现一个测试用的from表单 header 
> title：导航文字包含header.md文件 
> filename：markdown-file 文件名 footer 
> title：导航文字包含header.md文件 
> filename：markdown-file 文件名 
>
> order：用于配置输出 api-names/group-names 排序，在列表中的将按照列表中的顺序排序，不在列表中的名称将自动显示

#### 三、在代码文件夹的根目录下，创建build.bat

```
apidoc -i e:/nodejs/myapp/ -o e:/nodejs/myapp/apidoc/
```

```
apidoc -f ".*\\.js$" -f ".*\\.java$" -i myapp/ -o apidoc/ -t mytemplate/
```

> 参数说明
>
> -f 文件过滤
> -i 代码文件夹
> -o 输出Api文档的路径
> -t 使用模板文件的路径，可以自定义输出的模板
>
> tips:
> 参数名称加[name]，表示可选参数。

#### 四、参数解释说明

##### @api

> **@api {method} path [title]**

> ​ method：请求方法，[参见](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) 
> ​ path：请求路径 
> ​ title(可选)：标题

Example:

```json
/**
 *@api {get} /user/:id
 */
```

##### @apiDefine

>  **@apiDefine name [title] [description]**

> 定义重复内容，供其他模块引用
>
> ​ name：重复模块名 
> ​ title（可选）：标题 
> ​ description（可选）：说明

Example1:

```json
/**
*@apiDefine MyError
*@apiError UserNotFound The <code>id</code> of the User was not found.
*/
/**
*@api {get} /user/:id
*@apiUse MyError
*/
```

Example2:

```json
/**
*@apiDefine admin User access only
*This optional description belong to to the group admin.
*/
/**
*@api {get} /user/:id
*@apiPermission admin
*/
```

##### @apiDescription

>  **@apiDescription text**

> 设置多行说明
>
> ​ text：多行说明内容

Example

```json
/**
*@apiDescription This is the Description.
*It is multiline capable.
*
*Last line of Description.
*/
```

##### @apiError

> **@apiError [(group)] [{type}] field [description]**

>  请求错误参数
>
> ​ （group）（可选）：参数将以这个名称分组，不设置的话，默认是Error 4xx 
> ​ {type}（可选）：返回值类型，例如：{Boolean}, {Number}, {String}, {Object}, {String[]} 
> ​ field：返回值字段名称 
> ​ descriptionoptional（可选）：返回值字段说明

Example:

```json
/**
*@api {get} /user/:id
*@apiError UserNotFound The <code>id</code> of the User was not found.
*/
```

##### @apiErrorExample

> **@apiErrorExample [{type}] [title] example**

> 错误返回信息样例。
>
> ​ type（可选）：返回内容的格式 
> ​ title（可选）：标题 
> ​ example：样例，可以是多行文本

Example:

```json
/**
    *@api {get} /user/:id
    *@apiErrorExample {json} Error-Response:
    *HTTP/1.1 404 Not Found
    *{
        *"error": "UserNotFound"
    *}
*/
```

##### @apiExample

> **@apiExample [{type}] title example**



> 请求Api的测试样例
>
> ​ {type}（可选）：请求方式类型 
> ​ title：标题 
> ​ example：样例，可以是多行文本

Example:

```json
/**
*@api {get} /user/:id
*@apiExample {curl} Example usage:
*curl -i http://localhost/user/4711
*/
```

##### @apiGroup

> **@apiGroup name**

> 定义Api的分组
>
> ​ name：组名称，也是导航的标题

Example:

```json
/**
*@api {get} /user/:id
*@apiGroup User
*/
```

##### @apiHeader

> **@apiHeader [(group)] [{type}] [field=defaultValue] [description]**

> 定义head参数
>
> ​ (group)（可选）：分组 
> ​ {type}（可选）：类型，例如：{Boolean}, {Number}, {String}, {Object}, {String[]} 
> ​ field：变量名 
> ​ [field]：定义变量，并标记变量是可选项 
> ​ =defaultValue（可选）：默认值 
> ​ description（optional）：变量说明

Examples:

```json
/**
*@api {get} /user/:id
*@apiHeader {String} access-key Users unique access-key.
*/
```

##### @apiHeaderExample

> **@apiHeaderExample [{type}] [title]  example**

> head参数样例
>
> ​ {type}（可选）：请求方式类型 
> ​ title：标题 
> ​ example：样例，可以是多行文本

Example:

```jason
/**
*@api {get} /user/:id
*@apiHeaderExample {json} Header-Example:
*{
*"Accept-Encoding": "Accept-Encoding: gzip, deflate"
*}
*/@apiIgnore
```

##### @apiIgnore [hint]

> 忽略不发布到文档
>
> ​ hint（可选）：不发布的原因

Example:

```jason
/**
*@apiIgnore Not finished Method
*@api {get} /user/:id
*/@apiName
```

##### @apiName name

> 定义api名称
>
> ​ name：api名称

Example:

```jason
/**
    *@api {get} /user/:id
    *@apiName GetUser
*/
```

##### @apiParam

> **@apiParam [(group)] [{type}] [field=defaultValue] [description]**

> 定义请求Api的参数
>
> ​ (group)（可选）：分组 
> ​ {type}（可选）：类型，例如：{Boolean}, {Number}, {String}, {Object}, {String[]} 
> ​ {type{size}}（可选）：类型限定长度，例如：{string{..5}} 限定最大长度为5个字符 
> ​ {string{2..5}} 限定最短2个字符，最长5个字符 
> ​ {number{100-999}} 限定数字在100-999之间 
> ​ {type=allowedValues}（可选）：类型限定值，例如：{string=”small”}限定只允许传递small字符， 
> ​ {string=”small”,”huge”} 限定只允许传递small或huge字符， 
> ​ {number=1,2,3,99} 限定只允许传1，2，3，99这四个数字 
> ​ field：变量名 
> ​ [field]：定义变量，并标记变量是可选项 =defaultValue（可选）：默认值 
> ​ description（optional）：变量说明

Examples:

```jason
/**
    *@api {get} /user/:id
    *@apiParam {Number} id Users unique ID.
*/
```

```jason
/**
*@api {post} /user/
*@apiParam {String} [firstname]  Optional Firstname of the User.
*@apiParam {String} lastname     Mandatory Lastname.
*@apiParam {String} country="DE" Mandatory with default value "DE".
*@apiParam {Number} [age=18]     Optional Age with default 18.
*@apiParam (Login) {String} pass Only logged in users can post this.
*In generated documentation a separate
*"Login" Block will be generated.
*/
```

##### @apiParamExample

>  @apiParamExample [{type}] [title] example

> 请求参数样例
>
> ​ {type}（可选）：请求方式类型 
> ​ title：标题 
> ​ example：样例，可以是多行文本

Example:

```jason
/**
    *@api {get} /user/:id
    *@apiParamExample {json} Request-Example:
    *{
        *"id": 4711
    *}
*/
```

##### @apiPermission

> **@apiPermission name**

> 定义接口权限
>
> ​ name：权限名称

Example:

```jason
/**
    *@api {get} /user/:id
    *@apiPermission none
*/
```

##### @apiSampleRequest

> **@apiSampleRequest url**

> 设置测试请求form，如果在apidoc.json或package.json中设置过了sampleUrl，则默认请求地址为，sampleUrl+apipath，设置这个标签则重写测试请求路径
>
> ​ url：测试地址， 
> ​ 样例 
> ​ @apiSampleRequest http://www.example.com 改变测试地址 
> ​ @apiSampleRequest /my_test_path 在apipath中加前缀 
> ​ @apiSampleRequest off 不显示测试请求from

Examples: 

> 默认请求的地址是http://api.github.com/user/:id

> Configuration parameter sampleUrl: "http://api.github.com"

```jason
/**
    *@api {get} /user/:id
*/
```

> 需要将测试地址修改为http://test.github.com/some_path/user/:id，则
>
> Configuration parameter sampleUrl: "http://api.github.com"

```jason
/**
    *@api {get} /user/:id
    *@apiSampleRequest http://test.github.com/some_path/
*/
```

> 当地址需要请求http://api.github.com/test/user/:id 则
>
> Configuration parameter sampleUrl: "http://api.github.com"

```jason
/**
    *@api {get} /user/:id
    *@apiSampleRequest /test
*/
```

> 不需要测试请求from Configuration parameter sampleUrl: "http://api.github.com"

```jason
/**
    *@api {get} /user/:id
    *@apiSampleRequest off
*/
```

> 没有设置sampleUrl时需要显示测试请求from，并设置请求http://api.github.com/some_path/user/:id

> Configuration parameter sampleUrl is not set

```jason
/**
    *@api {get} /user/:id
    *@apiSampleRequest http://api.github.com/some_path/
*/
```

##### @apiSuccess

> **@apiSuccess [(group)] [{type}] field [description]**

> 请求成功返回的参数
>
> ​ (group）（可选）：参数将以这个名称分组，不设置的话，默认是Error 4xx 
> ​ {type}（可选）：返回值类型，例如：{Boolean}, {Number}, {String}, {Object}, {String[]} 
> ​ field：返回值字段名称 
> ​ descriptionoptional（可选）：返回值字段说明

Example:

```jason
/**
    *@api {get} /user/:id
    *@apiSuccess (200) {String} firstname Firstname of the User.
    *@apiSuccess (200) {String} lastname  Lastname of the User.
*/
```

##### @apiSuccessExample

> **@apiSuccessExample [{type}] [title] example**

>  请求成功返回数据样例
>
> ​ {type}（可选）：请求方式类型 
> ​ title：标题 
> ​ example：样例，可以是多行文本

Example:

```jason
/**
    *@api {get} /user/:id
    *@apiSuccessExample {json} Success-Response:
    *HTTP/1.1 200 OK
    *{
        *"firstname": "John",
        *"lastname": "Doe"
    *}
*/
```

##### @apiUse

> **@apiUse name**

> 使用在@apiDefine中定义的内容
>
> ​ name：在@apiDefine定义的name

Example:

```jason
/**
    *@apiDefine MySuccess
    *@apiSuccess {string} firstname The users firstname.
    *@apiSuccess {number} age The users age.
*/
/**
    *@api {get} /user/:id
    *@apiUse MySuccess
*/
```

##### @apiVersion

> **@apiVersion version**

> 设定Api的版本号
>
> ​ version：版本号，格式(major.minor.patch)

Example:

```jason
/**
    *@api {get} /user/:id
    *@apiVersion 1.6.2
*/
```

#### 五、参考示例

```java
/**
     * @apiDefine name 名称匹配
     */

    /**
     * @apiDefine pub_params 
     * @apiParam {string} accesskey 授权凭证
     * @apiParam {string} timestamp 时间戳
     * @apiParam {string} random 随机数
     * @apiParam {string} digest 签名
     */

    /**
     * @api {POST} /bank/api/basicQuery.json 根据地址查询
     * @apiGroup name
     * @apiName 根据地址查询
     * @apiVersion 1.0.0
     * @apiHeader {String} ContentType=application/json;charset=UTF-8 ContentType
     * @apiHeader {String} Accept=application/json;charset=UTF-8 Accept
     *
     * @apiUse pub_params
     * 
     * @apiParam {string} name 名称
     * @apiParam {string} [province] 省份
     * @apiParam {string} [city] 城市
     * 
     * @apiParamExample {json} paramExample:
     * {
            * "accesskey":"xxxxxxxxxxxxxxxxxxxxxx",
            * "timestamp":"1552302151",
            * "random":"xxxxxxxxxxxxxxxxxxxxxx",
            * "digest":"xxxxxxxxxxxxxxxxxxxxxx",
            * "message":"{\"name\":\"北部工业区\",\"province\":\"河北\",\"city\":\"岛市\",\"pageNumber\":1,\"pageSize\":10}"
        * }
     *
     * @apiSuccess {string} responseCode 状态码：0.成功、-1.非法请求、-2.签名错误、-51.未知错误、
     * @apiSuccess {string} responseMessage 响应提示信息
     * @apiSuccess {string} bank_type x行
     * @apiSuccess {string} city 城市
     * @apiSuccess {string} code 号
     * @apiSuccess {string} name 名称
     * @apiSuccess {string} province 省份
     * 
     * @apiSuccessExample  {json} Response (success):
     * {
     *     "accesskey": "xxxxxxxxxxxxxxxxxxxxxx",
     *     "secretKey": null,
     *     "timestamp": "20190307062545",
     *     "random": "xxxxxxxxxxxxxxxxxxxxxx",
     *     "digest": "xxxxxxxxxxxxxxxxxxxxxx",
     *     "responseCode": 0,
     *     "responseMessage": "成功"
     * }
     * @apiErrorExample Error-Response:
     * {
     *     "accesskey": "xxxxxxxxxxxxxxxxxxxxxx",
     *     "secretKey": null,
     *     "timestamp": "20190307062545",
     *     "random": "xxxxxxxxxxxxxxxxxxxxxx",
     *     "digest": "xxxxxxxxxxxxxxxxxxxxxx",
     *     "responseCode": -1,
     *     "responseMessage": "非法请求"
     * }
     */

```

```jason
/**
 * @api {POST} /register 注册用户
 * @apiGroup Users
 * @apiVersion 0.0.1
 * @apiDescription 用于注册用户
 * @apiParam {String} account 用户账户名 
 * @apiParam {String} password 密码
 * @apiParam {String} mobile 手机号
 * @apiParam {int} vip = 0  是否注册Vip身份 0 普通用户 1 Vip用户
 * @apiParam {String} [recommend] 邀请码
 * @apiParamExample {json} 请求样例：
 *                ?account=sodlinken&password=11223344&mobile=13739554137&vip=0&recommend=
 * @apiSuccess (200) {String} msg 信息
 * @apiSuccess (200) {int} code 0 代表无错误 1代表有错误
 * @apiSuccessExample {json} 返回样例:
 *                {"code":"0","msg":"注册成功"}
 */

 /**
 * @api {POST} /login 用户登录
 * @apiGroup Users
 * @apiVersion 0.0.1
 * @apiDescription 用于用户登录
 * @apiParam {String} userName 用户名
 * @apiParam {String} password 密码
 * @apiParamExample {json} 请求样例：
 *                ?userName=张三&password=11223344
 * @apiSuccess (200) {String} msg 信息
 * @apiSuccess (200) {String} code 0 代表无错误 1代表有错误
 * @apiSuccess (200) {String} user 用户信息
 * @apiSuccess (200) {String} userId 用户id
 * @apiSuccessExample {json} 返回样例:
 *                {"code":"0","msg":"登录成功","userId":"fe6386d550bd434b8cd994b58c3f8075"}
 */

 /**
 * @api {GET} /users/:id 获取用户信息
 * @apiGroup Users
 * @apiVersion 0.0.1
 * @apiDescription 获取用户信息
 * @apiSuccess (200) {String} msg 信息
 * @apiSuccess (200) {int} code 0 代表无错误 1代表有错误
 * @apiSuccess (200) {String} name 真实姓名
 * @apiSuccess (200) {String} mobile 手机号
 * @apiSuccess (200) {String} birthday 生日
 * @apiSuccess (200) {String} email 邮箱
 * @apiSuccess (200) {String} summary 简介
 * @apiSuccess (200) {String} recommendCode 我的推荐码
 * @apiSuccess (200) {String} idCardNo 身份证号
 * @apiSuccess (200) {String} memberState 会员状态 0普通用户 1VIP 2账户冻结
 * @apiSuccess (200) {String} address 家庭住址 
 * @apiSuccess (200) {String} money 账户现金
 * @apiSuccessExample {json} 返回样例:
 * {
 *   "code": 0,
 *   "msg": "",
 *   "name": "真实姓名",
 *   "mobile": 15808544477,
 *   "birthday": "1990-03-05",
 *   "email": "slocn@gamil.com",
 *   "summary": "简介",
 *   "recommendCode": "我的推荐码",
 *   "idCardNo": "身份证号",
 *   "memberState": 1,
 *   "address": "家庭住址",
 *   "money": "30.65"
 * }
 */


 /**
 * @api {POST} /users/:id 修改(完善)用户信息
 * @apiGroup Users
 * @apiVersion 0.0.1
 * @apiDescription 修改(完善)用户信息
 * @apiParam (200) {String} [name] 真实姓名
 * @apiParam (200) {String} [mobile] 手机号
 * @apiParam (200) {String} [birthday] 生日
 * @apiParam (200) {String} [email] 邮箱
 * @apiParam (200) {String} [summary] 简介
 * @apiParam (200) {String} [idCardNo] 身份证号
 * @apiParam (200) {String} [address] 家庭住址
 * @apiSuccess (200) {String} msg 信息
 * @apiSuccess (200) {int} code 0 代表无错误 1代表有错误
 * @apiSuccessExample {json} 返回样例:
 *                {"code":"0","msg":"修改成功"}
 */
```

> ***@apiParam*  *@apiSuccess***带有层级关系的样式 如：res.*

```java
/**
     * @api {get} /rest/area/getAreasByCode 行政区域查询
     * @apiDescription 根据行政编码获取行政区域,0获取省级行政区域
     * @apiName getAreasByCode
     * @apiGroup area
     * @apiVersion 1.0.0
     *
     * @apiParam {String} code 行政编码
     *
     * @apiSampleRequest /rest/area/getAreasByCode
     * @apiUse token_msg
     * @apiUse success_msg
     * @apiSuccess (success 2000) {String}   res.id    标识码
     * @apiSuccess (success 2000) {String}   res.name    行政地区名称
     * @apiSuccess (success 2000) {String}   res.code    行政编码
     * @apiSuccess (success 2000) {String}   res.prevCode    上级行政编码
     * @apiSuccess (success 2000) {String}   res.allName    全称
     *
     */
    @RequestMapping("/getAreasByCode")
    @ResponseBody
    public RestResponse getAreasByCode(String code){
        return new RestResponse(areaService.findAreaByPrevCode(code));
    }
```

```java
/**
     * @api {get} /rest/role/find 角色列表查询
     * @apiDescription  综合角色查询
     * @apiName find
     * @apiGroup role
     * @apiVersion 1.0.0
     *
     * @apiUse token_msg
     * @apiParam {String}  [page]     当前第几页
     * @apiParam {String}  [pageSize]    每页显示多少条数据,当该参数为0时表示不分页，查询全部
     * @apiParam {String}  [name]    角色名称
     * @apiParam {String}  [code]       角色代码
     *
     * @apiSampleRequest /rest/role/find
     * @apiUse success_msg
     * @apiSuccess (success 2000) {Long}      res.total    总条数
     * @apiSuccess (success 2000) {Array}    res.results    结果集
     * @apiSuccess (success 2000) {String}    res.results.id    角色id
     * @apiSuccess (success 2000) {String}    res.results.name    角色名称
     * @apiSuccess (success 2000) {String}    res.results.code        角色代码
     * @apiSuccess (success 2000) {String}    res.results.remark        角色描述
     * @apiSuccess (success 2000) {String}    res.results.createTime        创建时间
     * @apiSuccess (success 2000) {String}    res.results.updateTime        更新时间
     * @apiSuccess (success 2000) {String}    res.results.sort        排序编号
     * @apiSuccess (success 2000) {String}    res.results.isSuper        是否超级管理员
     */
    @RequestMapping("/find")
    @ResponseBody
    public RestResponse find(String name,String code,String page,String pageSize){
        return new RestResponse(rsp);
    }
```

> 表单上传示例

```java
/**
     * @api {post} /rest/user/create 新建用户
     * @apiDescription  新建用户
     * @apiName create
     * @apiGroup user
     * @apiVersion 1.0.0
     *
     * @apiUse token_msg
     * @apiParam {formData}  [iconFile]     头像
     * @apiParam {String}  loginName     登录名
     * @apiParam {String}  pwd     密码
     * @apiParam {String}  orgId     机构id
     * @apiParam {String}  [roleId]     角色id
     * @apiParam {String}  name    用户名
     * @apiParam {String}  [jobCode]    职务 1:科长 2：主任 3：科员 4：。。。
     * @apiParam {String}  [jobType]    职业性质 1：全职 2：兼职
     * @apiParam {String}  [sex]    性别 1：男 2：女
     * @apiParam {String}  phone    手机号
     * @apiParam {String}   idCard   身份证号
     * @apiParam {String}  birthday  出生日期
     * @apiParam {String}  [address]    住址
     * @apiParam  {String} [contactUser] 紧急联系人
     * @apiParam {String}  [contactPhone] 紧急联系电话
     * @apiParam  {String} [sex]    性别  1:男  2：女
     *
     * @apiSampleRequest /rest/user/create
     * @apiUse success_msg
     *
     * @apiUse USER_EMPTY_NAME
     * @apiUse USER_EMPTY_LOGIN_NAME
     * @apiUse USER_EMPTY_PWD
     * @apiUse USER_EMPTY_ORG
     * @apiUse USER_EMPTY_ROLE
     * @apiUse REGISTER_PHONE_EXIST
     * @apiUse REGISTER_USERNAME_EXIST
     * @apiUse USER_IDCARD_EXIST
     * @apiUse REGISTER_ORG_UNEXIST
     * @apiUse BASE_UPLOAD_FAIL
     * @apiUse BASE_SAVE_FAIL
     * */
    @RequestMapping("/create")
    @ResponseBody
    public RestResponse create(@RequestParam("iconFile") CommonsMultipartFile[] files
                                ,String loginName,String pwd,String orgId,String roleId,String name,
                               String jobCode,String jobType,String sex,String phone,String idCard,String birthday,String address,String contactUser,String contactPhone){
        return new RestResponse(rest);
    }
```

