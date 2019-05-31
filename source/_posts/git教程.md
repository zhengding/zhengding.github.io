---
title: git教程
date: 2017-11-20 16:16:01
tags: git
categories: 干货
---

# EAST项目部署常见问题
## 项目启动报错
1. 保证服务器jdk版本为1.8以上
2. 首次部署仅将fitech.war,platform.war和libs.war放入服务器中间件中，防止日志太多，异常不容易排查
2. 查看服务器日志，发现connection异常，检查fitech中的数据库配置是否正确

## 用户登录异常
1. 检查数据脚本都正常执行了，可查看select \* from sysuser是否有数据
2. 访问浏览器控制台，F12，登录一下看看是否有异常，如果是404，检查platform中的fitech.js是否修改成功

## 登录之后系统常见问题
1. 不要随意直接在数据库中修改数据，会导致页面显示有问题，如果实在想操作，请先询问产品开发
2. 系统配置中的制度配置导入时间比较久，耐心等待一会，重复导入可能会导致数据问题，查看是否成功，可在报文配置中查看数据表
\3. 

## ETL数据调度常见问题
> 新平台EAST需要配置3个作业才可以正常运行  
> - 数据补录作业
1. 数据补录系统待上报报文流程存储过程（east.create\_bl\_task|系统内置）
2. 数据补录系统流程开启任务（JAVA任务）
3. EAST收据调度作业
1. EAST待上报报文流程存储过程（east.proc\_ledgerReport|系统内置）  
2. 数据拉取存储过程（客户现场人员编写）  
3. EAST流程开启任务（JAVA任务）
7. EAST报文生成作业
1. EAST上报报文生成任务（JAVA任务）

1. 安装部署手册中的案例都是范例，请根据实际情况进行配置
2. 调度任务有没有成功执行，可以查看控制台日志是否执行成功
3. 数据库连接异常，可查看fitech-etl-1.0.war是否配置成功
4. 出现数组越界，可能是作业中的任务配置有问题，可截图发给开发排查问题
5. ETL会根据配置频度来控制执行状态，例如日频度，则当天只能执行一次，如果想多次执行，则到数据调度监控中重新执行作业，或者执行SQL手动删除ETL调度任务 例如：

```ruby
select * from etl_job_monitor;
select * from ETL_TASK_MONITOR;

delete from ETL_JOB_MONITOR where yxrq = '20171020';
delete from ETL_TASK_MONITOR where yxrq = '20171020';
commit;
```
# EAST项目部署手册
## 服务器环境
- 数据库（如果是oracle需要安装oracle客户端，用作sqluldr2报文生成）
- jdk1.8以上
- 服务器中间件（需要支持jdk1.8）

## 项目说明
> 项目分成4个子服务，由于模块之间有相互调用，需要分先后启动，分别配置

- fitech.war
_平台主服务,处理平台业务逻辑_
- fitech-etl-1.0.war
_ETL调度系统服务，处理数据抽取等调度任务_
- platform.war
_前端主服务_
- libs.war
_前端框架lib包_



## 项目部署
#### 主服务部署配置
- 将fitech.war放入服务中间件
- 修改服务配置文件application-env.properties
![配置文件所在位置][image-1]
- 需要修改的配置
![输入图片说明][image-2] 
 
1. 数据库配置，修改对应IP,端口和监听，以及用户名和密码
2. initPassword是系统创建用户初始化密码的默认密码，根据需求修改
3. template\_path是系统模板存放路径，用于机构，用户等功能的模板下载
4. ledgerRptFIle\_tempPath和ledgerRptFIle\_path是EAST生成报文存放的路径和临时文件生成的存放路径
5. DBurl是sqluldr2报文生成数据链接配置

#### 客户端部署配置
- 将platform.war和libs.war放入服务中间件
- 修改配置文件fitech.js
![放置路径][image-3]
![输入图片说明][image-4]
1. pathcs是配置ETL调度系统地址
2. path是配置主服务地址(包括服务项目名)

#### 创建数据库表
- _启动服务，主服务会自动创建系统表_ 
- [下载脚本][1]
- 执行初始化数据  
- EAST3.0初始化脚本.sql（初始化条线，角色等基础信息）
- EAST脱敏函数.sql（初始化脱敏函数，复制该脚本中的函数依次执行）  
- EAST脱敏公式.sql（初始化脱敏公式）
- 拉链表存储过程.sql（初始化拉链表存储过程，复制该脚本中的存储过程依次执行）
- ETL初始化脚本.sql（初始化ETL基础数据）
- ETL存储过程.sql（ETL存储过程）
- ETD指标建表语句.sql（根据需求执行，免费的10个指标）
- ETD初始化数据.sql（根据需求执行，免费的10个指标）
- 数据补录系统存储过程.sql（数据补录系统存储过程）
- 数据补录初始化脚本.sql（数据补录系统初始化脚本）
- 初始化EAST3.0校验公式（validata\_rule05.sql脚本中有N个变量，根据提示进行输入。输入&&+变量,如：&&CHANGE）
- 将template文件夹下的excel放到application-env.properties配置文件下的template\_path路径下，用于模板下载

#### 启动项目
- 启动服务器中间件
- 打开浏览器访问http://IP:port/platform
- 访问成功，点击登录使用superadmin登录，密码123456进行登录

#### 配置业务制度
- 进入系统配置》制度管理
![输入图片说明][image-5]
- 进入银监标准化业务条线》手工配置
![输入图片说明][image-6]
- 新增一个制度
![输入图片说明][image-7]
- 点击批量载入，选择系统导入文件夹下的EASTtemplate0530.xlsx和fields.xlsx，点击确定，待导入完成后，可生成EAST的58张数据表
![输入图片说明][image-8]
- 进入报文配置功能，查看报文是否生成成功，如果成功可进入操作手册进行机构，用户，角色等业务配置

#### 数据配置
- 系统需要现场实施人员根据客户现场情况创建机构，角色和用户信息
- 机构信息配置
- 角色信息配置
- 用户信息配置
- 流程信息配置（重要，ETL数据调度流程需要此步骤）  
	 [详见用户操作手册][2]

#### 配置ETL调度任务
- 进入业务条线 》 数据调度
![数据调度][image-9]
- 进入‘作业分类’菜单，新建一个‘作业分类’，用于管理系统调度作业
- 进入‘作业管理’菜单，在新建的作业分类下创建调度作业，用于数据调度；例如：
![输入图片说明][image-10]
1. 新增作业；例如：
![输入图片说明][image-11]
> 选择当前调度作业 _目前仅支持shell脚本和存储过程_  
> 配置作业频度之后，只有满足当前配置的启动条件，调度作业才会启动 _shell和存储过程必须要有回调true/false_  

- 进入‘任务管理’菜单，用于配置调度作业所需要的任务；例如：
![输入图片说明][image-12]  
- 选择任务类型新建任务  
1.新建procedure存储过程任务；例如：
![输入图片说明][image-13]  
_所有的存储过程必须要有回调函数true/false;例如：_

```ruby
create or replace procedure testproc(term in VARCHAR2, result OUT varchar)
as
  CURSOR c1 IS
select l.id, t.tablename, i.institutionid
      from ledgerreport l
      left join ledgerrpttemplate t
        on l.ledgerreporttemplate_id = t.id
      left join institution i
        on l.institution_id = i.id
     where l.submitstatetype = '2'
       and l.term = term
       and  i.institutionid='3011';
begin
FOR acct IN c1 LOOP
   update test.t_gx_ygb set reportid = acct.id

 where nbjgh = '3011';
  insert into yjbzh.t_gx_ygb
    select SEQ_FITECH.nextval id,REPORTID,CJRQ,GWBH,YGZT,YXJGMC,ZW,YXJGDM,SFZH,XM,JRXKZH,SSBM,WDH,LXDH,GH,NBJGH,SYNCABLE from test.t_gx_ygb;
    END LOOP;
  commit;
  result := 'true';
exception
  when storage_error then
    result := 'false';
  when others then
    result := 'false';
end testproc;

```


2.新加java调度任务；例如：
![输入图片说明][image-14]
> 新平台EAST需要配置3个作业才可以正常运行  
> - 数据补录作业
1. 数据补录系统待上报报文流程存储过程（east.create\_bl\_task|系统内置）
2. 数据补录系统流程开启任务（JAVA任务）
3. EAST收据调度作业
1. EAST待上报报文流程存储过程（east.proc\_ledgerReport|系统内置）  
2. 数据拉取存储过程（客户现场人员编写）  
3. EAST流程开启任务（JAVA任务）
7. EAST报文生成作业
1. EAST上报报文生成任务（JAVA任务）

_作业任务为Java任务的实例如下_
- EAST流程开启任务（POST请求）  
	\`\`\`ruby
类名：com.fitechsoft.util.HttpRequest
方法名：sendPost
URL：http://IP:端口/项目名/task/startProcess/
```
- EAST上报报文生成任务（GET请求）  
```ruby
类名：com.fitechsoft.util.HttpRequest
方法名：sendGet
URL：http://IP:端口/项目名/ledgerReportFile/reportFileGenerated/create
```
- 数据补录系统流程开启任务（POST请求）
```ruby
类名：com.fitechsoft.util.HttpRequest
方法名：sendPost
URL:http://IP:端口/项目名/accountTask/startProcess/
```

- 进入‘作业管理’ 将任务管理中创建的任务配置到作业中
- 选择你要维护的作业，点击作业维护
![输入图片说明][image-15]
2. 添加新步骤，将任务配置到作业管理中
![输入图片说明][image-16]

#### 部署ETL调度任务
- 将fitech-etl-1.0.war放入应用服务器中间件中
- 修改ETL系统配置
![输入图片说明][image-17]
- config.properties 修改ETL调度任务配置数据库
![输入图片说明][image-18]
- config1.properties 修改ETL调度任务存储过程数据表配置
![输入图片说明][image-19]
- 启动服务，控制台正常启动即可，操作员登录系统，查看代办任务是否有数据，有数据即可
# Home
1. [新平台activiti参数说明][3]
2. [git安装教程][4]
3. [新平台开发手册][5]
# git安装教程
### 安装介质
- Git-1.9.5-preview20150319.exe
- TortoiseGit-1.8.14.0-64bit.msi
- Tor4toiseGit-LanguagePack-1.8.14.0-64bit-zh\_CN.msi  
	[下载][6]

### 基本配置
1. 配置SSH密钥
![配置SSH密钥][image-20]
2. 找到生成的git授权KEY,路径默认生成在 _C:\Users\rui\.ssh_
![git授权KEY][image-21]
3. 配置TortoiseGit
![输入图片说明][image-22]
![输入图片说明][image-23]
![输入图片说明][image-24]

### 常用语法
- 查看当前git仓库获取和提交服务地址
``` ruby
git remote -v
```
- 查看所有分支
``` ruby
git branch
```
- 创建分支
``` ruby
git branch dev
```
- 删除分支
``` ruby
git branch -d dev
```
- 切换分支
``` ruby
git checkout dev
```
- 创建并切换
``` ruby
git checkout -b dev
```
- 查看工作区修改文件
``` ruby
git status
```
- 查看修改内容
``` ruby
git diff
```
- 添加修改到缓存区
``` ruby
git add ***
git add -A
```
- 版本提交
``` ruby
git commit -m "branch test"
```
- 合并分支dev到当前分支
``` ruby
git merge dev
```
- 指定定远程版本更新到本地指定版本
``` ruby
git pull <远程主机名> <远程分支名>:<本地分支名>
git pull origin dev:master
```
- 将当前分支提交到远程分支
``` ruby
git push <远程主机名> <远程分支名>
git push origin dev
```
- 切换git地址
``` ruby
1. git remote rm origin
2. git remote add origin git@github.com:yuquan0821/demo.git
3. git push origin
```
 **GIT教程** 
- [网络教程][7]

# oracle数据导出工具sqluldr2配置
### 安装步骤
1. 由于sqluldr2是基于oracle客户端的一个插件，则需要在平台服务端安装oracle\_client
2. 下载sqluldr2.bin（根据操作系统自行判定版本）到$ORACLE\_HOME的bin目录，重命名为sqluldr2.bin
3. 修改oracle安装$HOME目录下.bash\_profile 增加如下环境变量
```ruby
export LD_LIBRARY_PATH=$ORACLE_HOME/bin:$ORACLE_HOME/lib:/lib:/usr/lib 
```
4. 执行 sqluldr2.bin 执行成功即可
# 新平台activiti参数说明
### activiti配置
1. 新建bpmn流程
2. 绘制流程图，具体画法网上有
3. 设置流程参数
	- 配置流程图ID ; name ; namespaces属性（自定义）;
![输入图片说明][image-25]
	- 配置启动监听（该监听为自定义，平台已经初始化一个，无特殊需求可直接使用）
![输入图片说明][image-26]
	- 配置任务节点属性
	  1. 配置ID和name(此ID有特殊用处)
![输入图片说明][image-27]
	  2. 配置指派角色和督办角色，
	    - 变量规则：${任务节点ID_assignee};${任务节点ID_supervise}
![输入图片说明][image-28]
	  3. 设置任务节点参数
	    - argname: 路由KEY，固定为input
	    - argvalues: 页面按钮参数，几个路由跳转几个参数，可自定义
	    - handleEntry 当前任务节点页面路由地址
	    - completeEntry 暂时为空，预留
	    - isAssign:是否支持任务转派， ${任务节点ID_isAssign}
![输入图片说明][image-29]
	  4. 路由扭转控制参数
	    - 设定页面跳转路由参数${input=='refuse'}
	    - 说明 input:为步骤三中argname 的参数，无明显需求暂统一定义为input
	    - 说明'refuse' 为步骤三中argvalues参数中定义的value，指定下一节点跳转
![输入图片说明][image-30]
# 新平台开发手册
### 开发环境
- 开发工具IDE：IntelliJ IDEA/Eclipse
- 数据库：mysql/oracle
- JDK:1.8
- 版本管理工具：GIT
- 项目管理工具：MAVEN

### 开发规范
- 项目包名:com.fitech.功能名.项目名
- 项目类名:
	- 领域模型：根据领域模型实际意义创建（能用英文标识的必须使用英文，无法使用英文标识的用拼音首字母创建类名，首字母必须大写)
	- 持久化层：领域模型名+Repository
	- 业务层：
		1. 接口：领域模型名+Service
		2. 实现：领域模型名+ServiceImpl

- 注释:
- 类名注释：当前类的中文描述+作者  
	![类名注释][image-31]
- 接口名注释：当前接口作用+入参说明+出参说明  
	![接口注释][image-32]
- 业务代码注释：每10行必须有至少有一个注释，业务复杂的3行内要有注释，判断条件（if）必须有注释
- 预留接口，预留逻辑必须使用//TODO进行标记，并且加上备
- 如果修改业务逻辑代码，在修改处添加备注，标识修改原因，修改时间和修改人  
	![修改说明][image-33]

- 测试用例
- 每个模块有独立的测试用例配置文件，放置位置在  
	![测试用例配置文件][image-34]
- 每个业务逻辑添加单元测试，测试通过再提交代码  
	![测试用例][image-35]

- GIT代码管理
- 仓库中默认版本master，是我们开发版本
- 本地更新后，创建本地开发版本dev   (git checkout -b dev)
- 本地开发完成并测试通过后提交本地仓库
- 切换版本到master （git checkout master）
- 更新最新的开发版本
- 合并DEV版本到master （git merge dev）
- 确定无冲突之后提交远程仓库

### 技术说明
- domain说明  
	![domain][image-36]
- 添加注释@entity
- 主体继承FBSubject,客体继承FBObject
- 每个字段属性添加@Description("角色名称")注解，方便后期功能扩充
- service说明  
	![service][image-37]
- 添加@Service注解，加入spring容器管理
- 添加@ServiceTrace注解，系统会自动管理service接口日志，记录到logback  
	![输入图片说明][image-38]
- 接口的增删改操作需要捕获reporsitory发生的异常，然后throw出appException异常，这个是自定义异常，统一处理系统异常，记录logback,可自定义入参，第一个是异常码，可自定义，第二个是系统异常，可自定义;
- 对于一个服务接口有2个及以上“增”“删”“改”操作的，需要添加事务@Transactional
- controller说明  
	![controller][image-39]
- 添加控制层上添加@RequestMapping ，定位当前接口位置，便于后期权限控制  
	![输入图片说明][image-40]
- controller回调GenericResult\<?\>封装回调函数，里面有当前请求是否成功，成功状态，异常状态，异常码，数据集对象等，系统回封装成json返回前端;
- controller中需要记录用户的操作日志，可以只对增删改操作记录，查询根据具体情况记录，addOperateLog第一个为日志信息，自定义，第二个是请求上下文对象，系统需要根据请求上下文获取登录用户信息和IP等;
- 操作日志需要在finally中记录，防止系统异常没有记录;
- war 说明
![war ][image-41]
1. Activiti.cfg.xml：activiti流程引擎配置
2. applicationContext-jpa.xml：配置持久化层springdata配置
3. applicationContext.xml：spring相关配置
4. Security.cfg.xml：权限框架springsecutity相关配置
5. secutityAcl.cfg.xml：数据权限acl相关配置
6. Spring-mvc.xml
7. Springbatch-content
8. Application-evn.properties:数据源配置及其他的一些参数配置
9. Logback.xml：配置系统日志，
> 注：可根据具体需求进行配置修改
### 开发注意事项
1. clean install -Dmaven.test.skip=true 模块编译
2. jetty:run 项目执行
### 数据库版本切换
- 目前系统支持mysql和Oracle数据库，由于制度报文生成接口是由不同数据库语法编写所以在切换数据库的同时要修改对应的数据库方言配置
- 修改pom.xml  
	![输入图片说明][image-42]
**目前0.0.1-SNAPSHOT版本对应mysql;0.0.2-SNAPSHOT版本对应oracle**
2. 修改配置文件applicationContext-jpa.xml，改为数据库所对应的接口实现类  
	![输入图片说明][image-43]
3. 如果需要新加SQL实现函数，则在对应的系统模块中添加，例如  
	![输入图片说明][image-44]  
	实现自己的业务逻辑即可，然后在applicationContext-jpa.xml添加相应配置加入spring管理容器

[1]:	https://gitee.com/mpmpal_cr/document/attach_files/download?i=96428&u=http://files.git.oschina.net/group1/M00/01/F4/PaAvDFnExYiAZBwYAAbJgGShKGQ898.rar?token=2fc273a040d2eabf77c760f50eff721b&ts=1506067947&attname=%E6%96%B0%E5%B9%B3%E5%8F%B0%E8%84%9A%E6%9C%AC.rar
[2]:	https://gitee.com/mpmpal_cr/document/attach_files/download?i=98255&u=http://files.git.oschina.net/group1/M00/02/09/PaAvDFnbQGuALm5OAGF7R5HNSdg735.doc?token=a118c827b961e80c623f752ed78eb4d8&ts=1507541099&attname=PD-%E7%A7%91%E8%9E%8D%E7%BB%BC%E5%90%88%E7%BB%9F%E8%AE%A1%E5%B9%B3%E5%8F%B0%E4%BA%A7%E5%93%81-%E7%94%A8%E6%88%B7%E6%93%8D%E4%BD%9C%E6%89%8B%E5%86%8C_V1.0_20170928.doc
[3]:	https://gitee.com/mpmpal_cr/document/wikis/pages?title=%E6%96%B0%E5%B9%B3%E5%8F%B0activiti%E5%8F%82%E6%95%B0%E8%AF%B4%E6%98%8E&parent=
[4]:	https://gitee.com/mpmpal_cr/document/wikis/pages?title=git%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B&parent=
[5]:	https://gitee.com/mpmpal_cr/document/wikis/pages?title=%E6%96%B0%E5%B9%B3%E5%8F%B0%E5%BC%80%E5%8F%91%E6%89%8B%E5%86%8C&parent=
[6]:	http://gitee.com/mpmpal_cr/document/attach_files/download?i=102945&u=http://files.git.oschina.net/group1/M00/02/3F/PaAvDFn_1fiAEkFDAocAFD5NBjw242.rar?token=3d3e93341027b8450a17c0d4f33afc20&ts=1509938681&attname=Git%2CTortoiseGit%E5%AE%89%E8%A3%85.rar
[7]:	http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000

[image-1]:	https://git.oschina.net/uploads/images/2017/0922/142804_a619079d_1102706.png "图片1.png"
[image-2]:	https://git.oschina.net/uploads/images/2017/0922/142905_7ad5bb2f_1102706.png "图片2.png"
[image-3]:	https://git.oschina.net/uploads/images/2017/0922/145015_b3e45198_1102706.png "图片3.png"
[image-4]:	https://git.oschina.net/uploads/images/2017/0922/145035_e6b445d0_1102706.png "图片4.png"
[image-5]:	https://git.oschina.net/uploads/images/2017/0922/173322_c8830ec5_1102706.png "5.png"
[image-6]:	https://git.oschina.net/uploads/images/2017/0922/173652_6afc04b1_1102706.png "6.png"
[image-7]:	https://git.oschina.net/uploads/images/2017/0922/173722_7a2ae350_1102706.png "7.png"
[image-8]:	https://git.oschina.net/uploads/images/2017/0922/173755_dfcbb075_1102706.png "8.png"
[image-9]:	https://git.oschina.net/uploads/images/2017/0925/095221_0b8c5fd0_1102706.png "9.png"
[image-10]:	https://git.oschina.net/uploads/images/2017/0925/100741_ab5c641f_1102706.png "10.png"
[image-11]:	https://git.oschina.net/uploads/images/2017/0925/101135_d4af3a5b_1102706.png "11.png"
[image-12]:	https://git.oschina.net/uploads/images/2017/0925/113253_f27a4429_1102706.png "12.png"
[image-13]:	https://git.oschina.net/uploads/images/2017/0925/142839_6cf3572f_1102706.png "13.png"
[image-14]:	https://git.oschina.net/uploads/images/2017/0925/144829_27d05efd_1102706.png "14.png"
[image-15]:	https://git.oschina.net/uploads/images/2017/0925/151904_7d519dcc_1102706.png "15.png"
[image-16]:	https://git.oschina.net/uploads/images/2017/0925/151948_2459f713_1102706.png "16.png"
[image-17]:	https://git.oschina.net/uploads/images/2017/0925/153251_aa9e251f_1102706.png "17.png"
[image-18]:	https://git.oschina.net/uploads/images/2017/0925/155627_3d96691e_1102706.png "18.png"
[image-19]:	https://git.oschina.net/uploads/images/2017/0925/155636_08b12be3_1102706.png "19.png"
[image-20]:	https://git.oschina.net/uploads/images/2017/0830/143511_7e9c1265_1102706.png "clipboard.png"
[image-21]:	https://git.oschina.net/uploads/images/2017/0830/143750_9390d834_1102706.png "clipboard.png"
[image-22]:	https://git.oschina.net/uploads/images/2017/0830/143856_de604f92_1102706.png "clipboard.png"
[image-23]:	https://git.oschina.net/uploads/images/2017/0830/144125_814b18db_1102706.png "clipboard.png"
[image-24]:	https://git.oschina.net/uploads/images/2017/0830/144156_8ca52887_1102706.png "clipboard.png"
[image-25]:	https://git.oschina.net/uploads/images/2017/0809/141007_8649cc82_1102706.png "1.png"
[image-26]:	https://git.oschina.net/uploads/images/2017/0809/141102_62ed4ba4_1102706.png "2.png"
[image-27]:	https://git.oschina.net/uploads/images/2017/0809/141225_c541dcca_1102706.png "3.png"
[image-28]:	https://git.oschina.net/uploads/images/2017/0809/141253_9b67ac6b_1102706.png "4.png"
[image-29]:	https://git.oschina.net/uploads/images/2017/0809/141342_3d544a50_1102706.png "5.png"
[image-30]:	https://git.oschina.net/uploads/images/2017/0809/141411_478f2512_1102706.png "6.png"
[image-31]:	https://git.oschina.net/uploads/images/2017/0830/152051_747dcf5e_1102706.png "图片1.png"
[image-32]:	https://git.oschina.net/uploads/images/2017/0830/152154_1cf6e0b1_1102706.png "图片2.png"
[image-33]:	https://git.oschina.net/uploads/images/2017/0830/152322_a4b7aeb4_1102706.png "图片3.png"
[image-34]:	https://git.oschina.net/uploads/images/2017/0830/152917_14e372ff_1102706.png "QQ截图20170830152553.png"
[image-35]:	https://git.oschina.net/uploads/images/2017/0830/152948_01102b7d_1102706.png "图片4.png"
[image-36]:	https://git.oschina.net/uploads/images/2017/0830/154423_a15e4a4c_1102706.png "图片5.png"
[image-37]:	https://git.oschina.net/uploads/images/2017/0830/160045_4a882daf_1102706.png "图片6.png"
[image-38]:	https://git.oschina.net/uploads/images/2017/0830/160703_aa9ffc2f_1102706.png "图片7.png"
[image-39]:	https://git.oschina.net/uploads/images/2017/0830/162413_b14e447d_1102706.png "图片8.png"
[image-40]:	https://git.oschina.net/uploads/images/2017/0830/163109_0c571fe1_1102706.png "图片9.png"
[image-41]:	https://git.oschina.net/uploads/images/2017/0830/163455_5036a5e5_1102706.png "图片10.png"
[image-42]:	https://git.oschina.net/uploads/images/2017/0830/164510_7b159611_1102706.png "图片11.png"
[image-43]:	https://git.oschina.net/uploads/images/2017/0830/164601_eb23eff4_1102706.png "图片12.png"
[image-44]:	https://git.oschina.net/uploads/images/2017/0830/164642_efb5c1a0_1102706.png "图片13.png"