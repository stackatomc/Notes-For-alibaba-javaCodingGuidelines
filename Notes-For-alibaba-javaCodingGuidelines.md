# 《码出高效 阿里巴巴Java开发手册 终极版1.3.0》阅读笔记

标签：Java

---

## 简记重要内容和问题解决情况

- 前言
- 一、编程规约
	- （一）命名风格
		- 3 类名使用驼峰形式UpperCamelCase风格，除DO/BO/DTO/VO/AO
		- 4 方法命、参数名、成员变量、局部变量使用lowerCamelCase风格
		- 5 常量命名全部大写，单词间用下划线隔开，力求予以表达完整清除，不要嫌名字长。如MAX_STOCK_COUNT，反例MAX_COUNT
		- 6 抽象类命名用Abstract或Base开头；异常类命名使用Exception结尾；测试类命名以要测试的类的名称开头，以Test结尾
		- 7 中括号正确写法在数组类型后面。特别注意：String[] args，反例String args[]
		- 8 POJO类中的布尔类型，前面不要使用is，否则部分框架解析会引起序列化错误。如定义维基本数据类型Boolean isDeleted的属性，它的方法也是isDeleted()，RPC框架在反向解析的时候，“以为”对应的属性名称事deleted，导致属性获取不到，进而抛出异常。
			- 拓展：参考[Spring框架 boolean类型命名is开头会引起序列化错误](https://blog.csdn.net/qq_31145141/article/details/71597608)Spring框架也存在这种序列化错误
		- 9 包名统一使用小写，点分隔符之间有且仅有一个自然于一的英文单词。包名统一使用单数形式，但是类名如果有复数含义，类名可以使用复数形式。如com.alibaba.open.util，类名为MessageUtils(此规则参考Spring的框架结构)
		- 11 代码自解释，自定义编程元素在命名时，要尽量完整的单词组合来表达其义。如从远程仓库拉去代码的类命名为PullCodeFromRemoteRepository，反例int a对变量随意命名方式
		- 12 如果模块、借口、类、方法使用了设计模式，在命名时体现除具体模式，将设计模式体现在名字中，有利于阅读者快速理解架构设计理念。如public class OrderFactory;public class LoginProxy;public ClassResourceObserver;
		- 13 接口中的方法和属性不要加任何修饰符号(包括public)，并加上有效的Javadoc注释。尽量不要再接口里定义变量，如果一定要定义变量，肯定与接口方法相关，并且是整个应用的基础常量。如接口方法void f();接口基础常量String COMPANY="ALIBABA";
			- 拓展1：Java8中接口可自带default实现，即对所有实现类都有价值的默认实现
			- 拓展2：参考`<Java8 in action>`书中，解决多个接口实现与父类继承时出现方法冲突的三条原则
				- 三条原则之一 类中的方法优先级最高。类或父类优先级高于任何声明为默认方法的优先级
				- 二 如果第一条无法判断，那么子接口的优先级更高，如果C继承A和B，而且B继承A所以B更具体，所以C选择B的实现
				- 三 若还是无法判断，如同时继承多个接口或者。该类必须显示覆盖和调用期望的方法，如B.super.hello();
				- 特殊情况之一 还有一种“菱形问题”，如D实现BC接口，而且BC均继承接口A，则均为A接口的一个默认实现方法，会调用该方法，不会出现冲突 
				- 二 父类中显示将该方法定义为抽象方法，此时参考三，需要子类显示覆盖和调用
				- 三 应用场景，如可避免很多空模板实现，不必要实现接口每个方法，对提供默认实现接口方法可以不显示声明，但对未提供默认实现的接口方法必须全部显式提供实现方法。另外，IDEA对接口中有默认实现和无的小图标不一样
				```
				public interface A{
					default void hello(){
						System.out.println("hello A");
					}
				}
				```
		- 14 接口和实现类的命名两套规则
			- 1) Service和DAO类，基于SOA面向服务的理念，暴露出来的服务一定是接口，内部的实现类用Impl的后缀与接口区别
			- 2) 如果是形容能力的接口名称，取对应的形容词做接口名(通常是-able的形式)。如AbstractTranslator实现Translatable
		- 15 枚举类名建议带上Enum后缀，枚举成员需要全大写，单词间用下划线隔开。枚举其实就是特殊的常量类，且构造方法被默认强制是私有。如，枚举类名ProcessStatusEnum.
		- 16 各层明明规约
			- A) Service/DAO层方法命名规约
				- 1 获取单个对象的方法用get作前缀
				- 2 获取多个对象的方法用list作前缀
				- 3 获取统计值的方法用count作前缀
				- 4 插入的方法用save/insert作前缀
				- 5 删除的方法用remove/delete作前缀
				- 6 修改的方法用update作前缀
			- B) 领域模型命名规约
				- 1 数据对象：xxxDO，xxx即为数据表名
				- 2 数据传输对象：xxxDTO，xxx为业务领域相关的名称
				- 3 展示对象：xxxVO，xxx一般为网页名称
				- 4 POJO是DO/DTO/BO/VO的统称，禁止命名为xxxPOJO
			- 简记
				- PO(persistant object)：持久对象，与数据库表字段存在映射关系，本身还有部分业务逻辑的处理，但不包含任何对数据库的操作
				- DO(Domain Object)：有形或无形的对象，一般与数据库表字段对应
				- VO：视图层对象
				- TO：用于业务间传输
				- DTO：用于展示层和业务层传输
				- POJO：单纯的javabean，仅与数据库表字段映射，且不含有业务处理
				- DAO：业务处理对象
				- BO：业务对象
				- QUERY：查询对象
			- 参考博客
				- [实体类（VO，DO，DTO）的划分](https://blog.csdn.net/wangxin1982314/article/details/51954264/)
				- [如何理解《阿里巴巴Java开发手册》中提到的 DO/DTO/BO/VO/QUERY ](https://zhidao.baidu.com/question/1673864517316886227.html)
				- VO->DTO->DO->DAO->PO
	- （二）常量定义
		- 1 不允许任何魔法值（即未经定义的常量，直接出现在代码中）
		- 2 long或Long赋初始值时，使用大写的L，不能是小写的l，小写容易被跟数字1混淆，造成误解
		- 3 不要使用一个常量类维护所有常量，按常量功能进行归类，分开维护.如，缓存相关的放在类CacheConsts下，系统配置相关常量放在ConfigConsts下
		- 4 常量的复用层次有五层：跨应用共享常量、应用内共享常量、子宫城内共享常量、包内共享常量、类内共享常量
			- 跨应用共享常量：放置在二方库中，通常是client.jar中的constant目录下
			- 应用内共享常量：放置在一方库中，通常是modules中的constant目录下（易懂变量如代码自解释YES，当a和b分别命名static final为YES="yes"/"y";会导致线上问题，应设置为应用内共享常量）
			- 子工程内部共享常量：即在当前子工程的constant目录下 
			- 包内共享常量：即在当前包下dandudeconstant目录下
			- 类内共享常量：直接在类 内部private static final定义
		- 5 枚举类可带有延伸属性。如public class Enum{MONDAY(1),TUESDAY(2),WEDWESDAY(3),THURSDAY(4),FRIDAY(5),SATURDAY(6),SUNDAY(7);}
			- 拓展：参考[Java枚举](https://www.jianshu.com/p/bcef3be6c7ae)和《Java编程思想》5.9枚举类型，关于枚举类的构造方法参数和switch结合运用
	- （三）代码格式
		- 1 大括号的使用约定，如果是空实现，不需要换行，写为{}，如果非空
			- 1) 左大括号前不换行，后换行
			- 2) 右大括号前换行，右大括号后如果还有else等代码则不换行，表示终止的有大括号后必须换行
		- 2 左小括号和字符之间不出现空格；同样，有右括号和字符之间也不出现空格。如if(a == b)
		- 3 if/for/while/switch/do等保留字与括号之间都必须加空格
		- 4 任何二目、三目运算符的左右两边都需要加一个空格。运算符包括赋值运算符=、逻辑运算符&&、加减乘除符号等
		- 5 采用4个空格缩进，禁止使用tab字符。说明，如果使用tab缩进，必须设置1个tab为4个空格，IDEA设置tab为4个空格时请勿勾选 Use tab character;而在eclipse中，必须勾选insert spaces for tabs
		```
		public static void main(String[] args)空格{
		    // 缩进4个空格
		    String say = "hello";
			// 运算符的左右必须有一个空格
			int flag=0;
			// 关键字if和括号之间必须有一个空格，括号内的f与左括号，0与右括号不需要空格
			if (flag == 0) {
				System.out.println(say);
			}
			
			// 左大括号前加空格且不换行，左大括号后换行
			if (flag == 1) {
				System.out.println("world");
				//右大括号前换行，右大括号后育else不换行
			}空格else空格{
				System.out.println("ok");
				// 在右大括号后直接结束，则必须换行
			}
		}
		```
		- 6 注释的双斜线与注释内容之间有且仅有一个空格，且不用为其换行。如// 注释内容
		- 7 单行字符数限制不超过120个，超出需要换行，遵循如下原则：
			- 1) 第二行相对第一行缩进4个空格，从第三行开始，不再继续缩进
			- 2) 运算符与下文一起换行
			- 3) 方法调用的点符号与下文一起换行
			- 4) 方法调用时，多个参数，需要换行时，在逗号后进行
			- 5) 在括号前不要换行
		- 8 方法参数在定义和传入时，多个参数都好后边必须家空格。如method(a, b, c);
		- 9 IDEA的text file encoding设置为UTF-8，IDEA中文件的换行符使用Unix格式，不要使用Windows格式
			- 参考博客[编程规范之tab设置4空格和回车设置Unix(idea)](https://blog.csdn.net/drdongshiye/article/details/78811044)
			- 回车设置位置：Settings/Editor/Code Style/Line separator.选择 Unix and OS X(\n)
			- tab设置位置：Settings/Editor/Code Style/Java/Tab and Indents下不勾选Use tab character
		- 11 方法体内的执行与剧组、变量的定义语句组、不同的业务逻辑之间或者不同的语义之间插入一个空行。相同业务逻辑和予以之间不需要插入空行。说明：没有必要插入多个空行进行隔开
	- (四)OOP规约
		- OO 说明：OO，Object Oriented，面向对象
		- 1 避免通过一个类的对象引用访问此类的静态变量或静态方法，无调增加编译器解析成本直接用类名来访问即可
		- 2 所有的覆写方法，必须加@Override注解。如，getObject()与get0bject()方法加@Override可以准确判断是否覆盖成功。另外，如果在抽象类中对方法签名进行修改其实现类会马上编译报错
		- 3 相同参数类型，相同业务含义，才可以使用Java的可变参数，避免使用Obejct。说明，可变参数必须放置在参数列表的最后，提倡尽量不用可变参数编程。如public User getUser(String type,  Integer... ids) {...}
		- 4 外部正在调用或者二房库依赖的接口，不允许修改方法签名，避免对接口调用方产生影响。接口过时必须加@Deprecated注解，并清晰地说明采用的新接口或者新服务是什么
		- 5 不能使用过时的类或者方法。作为调用方来说，有义务去考证过时方法的新实现是什么
		- 6 Objectdeequals方法容易抛空指针异常，应使用常量或确定优质的对象来第哦啊用equals。如"test".equals(object);反例object.equals("test");另外，推荐使用java.util.Objects#equals(JDK7引入的工具类)
		```
		java.util.Objects#equals源码如下
		public static boolean equals(Object a, Object b) {

        return (a == b) || (a != null && a.equals(b));
		
    	}
		```
		- 7 所有的相同类型的包装类对象之间值的比较，全部使用equals方法比较。说明，对于Integer var=？在-128至127范围内的赋值（8位），Integer都是在IntegerCache.cache产生，会复用已有对象，这个区间内的Integer值可以直接使用==进行判断，但是这个区间之外的所有数据，都会在对上产生，并不会复用已有对象，这是一个大坑，所以推荐使用equals方法进行判断
			- 拓展：关于-128~127的范围，参考回答 [有符号字符型取值范围为什么是-128-127？](https://zhidao.baidu.com/question/1667734501528485787.html)
	>根据补码的几条规定即可推出上述结论： 
1 若二进制每位全为0,则表示数0 
2 
若最高位（即符号位）为0,表示正数 
3 若最高位为1, 
表示是负数，而该负数的绝对值是多少呢？将每个二进制位（包括符号位）取反加1，得到一个二进制数，将该数看成无符号数，其值就是上述负数的绝对值。 
例如，二进制的 10000000 的最高位为1, 所以它表示的是负数。是负的多少呢？我们将其八位全部取反，得到01111111, 
然后加1，得到10000000. 将该数看作无符号数，值为128, 故计算机中的10000000表示的是-128
最高位（即符号位）为1的8位有符号数有128个，故可表示128个负数；最高位为0的8位有符号数有128个，但全0的那个表示数0，所以总共只能表示127个正整数 
	- 8 关于基本数据类型与包装数据类型的使用标准如下：
		- 1) 所有的POJO类属性必须使用包装数据类型
		- 2) RPC方法的返回值和参数必须使用包装数据类型
		- 3) 所有的局部变量使用基本数据类型
			- 拓展：RPC(Remote Procedure Call Protocol),远程过程调用。随着集中式架构向分布式架构的转变，应用系统之间的服务调用与通讯问题成为了首要解决的需求。而RPC 的主要目标就是为了让构建分布式计算（应用）变得更加简单，在提供强大的远程调用能力时不损失本地调用的语义简洁性。 为实现该目标，RPC 框架需提供一种透明调用机制让使用者不必显示的区分本地调用和远程调用。常用的分布式RPC框架有：dubbo、motan、rpcx、gRPC、thrift等等。
		- 说明:POJO类属性没有初值是提醒使用者在需要使用时，必须自己显示地进行赋值，任何NPE问题，或则和入库检查，都由使用者来保证
		- 如数据库的查询结果可能是null，因为自动拆用，用基本数据类型接受有NPE风险。反例比如显示成交总额涨跌情况，即正负x%，x为基本数据类型，调用地RPC服务，调用不成功是，返回的是默认值，页面显示为0%，这个是不合理的，应该显示成中华先。所以包含数据类型的null值可以表示额外的信息，如：远程调用失败，异常退出
	- 9 定义DO/DTO/VO等POJO类时，不要任何属性默认值。反例，POJO类的getCreate默认值为new Date();但是这个属性在数据抓取时并没有置入具体数值，在更新其他字段时又附带了更新了此字段，导致创建时间被修改为当前时间
	- 10 序列化类新增属性时，请不要修改serialVersionUID字段，避免反序列失败；如果完全不兼容升级，避免反序列化混乱，那么请修改serialVersionUID值。说明，注意不同的serialVersionUID会导致序列化时异常。
		- 