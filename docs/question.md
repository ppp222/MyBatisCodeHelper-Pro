## 如何生成的时候返回主键id？

首先要确保主键是自动生成的，比如 mysql主键 设置为 autoIncrement
oracle 主键添加序列和触发器 (用java类生成建表语句可以自动生成好)
(2.7.6支持配置oracle序列)
然后 数据库在生成的时候 填写了 useGeneratedKey为你的主键字段名
之后在生成的接口会有一个insert方法 比如  insert(User user)  生成的xml中的insert 语句 会有
useGeneratedKey = true
在执行完插入语句后 可以使用 user.getId() 来获取生成的id

## 生成代码出现 can't find xml file for namespace xxxx
先查看xml中的namespace是否与接口一致，如果一致， 查看是否安装了其他冲突插件如FreeMybatisPlugin,MybatisX,MybatisPlugin(mybatisLogPlugin不冲突),如果有请卸卸掉 然后使用 invalidate Cache and restart. https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/invalidateCacheAndRestart.png
如果还没有发现问题，请查看下 接口和xml 是否在同一个module，module是否有依赖关系，如果在同一个module还有问题，请联系我


## mybatis generator没有生成service接口 (2.6版本可以生成)

一般spring项目中service接口没啥用 参考 https://www.zhihu.com/question/296829023/answer/521249348

如果实在要接口 可以使用Intellij自带的一键导出接口 https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/extractInterface.png

## 我不想要xml的注释怎么处理

通过数据库生成时，xml里面会有一系列 @Mbg generated的这种注释，这个注释的目的是让人知道这个是自动生成的，并且可以在数据库添加了字段重新生成的时候 只会更新 xml中标记了这些注释的语句，所以建议还是生成注释比较好

如果还是不想要注释 可以在数据库生成的时候 有一个 genComment选项 把它关闭就不会生成了

## 数据库加减字段后如何重新生成 
1.首先通过数据库生成的时候选中 genComment(默认选中) 会生成一些自带方法，请不要修改这些自动生成的方法 这些方法会在第二次生成的时候给新方法覆盖掉
2.数据库加减或修改字段重新从数据库生成下即可，不会覆盖掉你自己加的自定义方法这些 xml mapper接口 service 都会自动合并好

## 如何进行逻辑删除

目前在生成代码的时候还没有提供默认生成逻辑删除的语句 
可以先通过方法名来进行生成
可以写 updateIsDeleteById 来生成 通过主键逻辑删除

## xml param 中的 jdbcType 有啥用

https://stackoverflow.com/questions/18645820/is-jdbctype-necessary-in-a-mybatis-mapper

部分数据库在插入 删除 更新的时候 如果传的是null值 需要添加jdbcType  加上jdbcType是更安全的做法

经测试 mysql不需要 oracle需要 

## 怎么生成 if test 怎么换图标 方法名生成的代码怎么弄到service中

设置里面可以配置 

![setting](https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/settings.png)


## 方法名生成sql时 出现 please check with your xml resultMap id:  dose it contain all the property of resultMap 怎么处理

出现该异常的原因是 xml中的resultMap 和 resultMap type中 对应的实体的属性不一致  xml的resultMap中缺少了一些字段

由于方法名生成sql 需要知道实体里面 每个属性 对应的数据库字段名

要么在 resultMap中添加缺少的属性  或者 在java实体中 将这个属性 设置为 transient

有一些java实体中 会有一些属性是从其他表查出来的  比如 Blog类 里面有一个 List<Commnet> 

这种情况 用 transient 也不合适， 建议更改继承关系 不要把List<Comment>放到 Blog中  而是加一个 BlogWithComment的类 继承 Blog类。

请不要更改自动生成的BaseResultMap.

## 数据库 添加字段后 如何生成

直接使用 mybatis generator 重新生成即可 会保留接口和xml中不是自动生成的方法


## mybatis在写sql时数据库字段没有自动提示
使用Intellij高级版 需要配置idea自带的database 在配置的时候记得填上数据库名

## 使用模版如cd co ${ ignore sql报错Range Marker Error
出现提示后请用tab键 而不是enter键来进行补全，这个是IDEA的一个bug 以后看看

## 从表生成代码只有两个insert方法
请检查表中是否有主键，如果有主键请刷新IDEA的数据库 直到下图这种
![tableNoPrimaryKey](https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/tableNoPrimaryKey.png)

## sql标签中的字段标红 sql标签中的sql 由于不是完整的sql，无法进行检测和代码补全，插件引入了 @sql 注释，在注释中把sql的前缀和后缀填写进去，可保证sql标签中的sql无误
![sqlTagNoError](https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/sqlTagNoError.gif)

## choose when语句 没有自动提示，标红

请添加ignore注释 参考
![chooseWhenAutoComplete](https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/chooseWhenAutoComplete.gif)

## 使用${}的sql会标红

由于$中可以使用任意的字符串，无法解析具体的sql是啥，插件没有将${}加入sql解析，可以在${}的后面添加一个注释代表真正可能的sql，比如引用常量的时候，需要把常量的值放到注释中
，这样$不会标红并且后面的sql也能正确识别

(2.7.6支持一键生成对应的sql)
![AddSqlAfter$](https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/AddSqlAfter$.gif)

## 数据库表是tinyint(1)生成了boolean类型

mysql tinyint(1)与boolean是一个含义，不想生成boolean请使用tinyint(4)


## 怎么去掉sql显示中的黄色背景
![yellowBackGround](https://coding.net/u/gejun123456/p/MyBatisCodeHelper-Pro/git/raw/master/screenshots/yellowBackGround.png)
