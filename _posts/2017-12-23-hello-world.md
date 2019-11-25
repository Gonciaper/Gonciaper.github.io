---
permalink: >-
  https://gonciaper.netlify.com/admin/#/collections/post/entries/2017-12-23-hello-world
display: normal
title: PHP八大设计模式
tags: PHP
date: '2019-11-01 19:59:00 +08:00'
comment: true
layout: post
component: PHP
---
### 设计模式

单例模式解决的是如何在整个项目中创建唯一对象实例的问题，工厂模式解决的是如何不通过 new 建立实例对象的方法。

#### 单例模式

1.  $_instance 必须声明为静态的私有变量
2.  构造函数和析构函数必须声明为私有, 防止外部程序 new 类从而失去单例模式的意义
3.  getInstance() 方法必须设置为公有的, 必须调用此方法 以返回实例的一个引用
4.  :: 操作符只能访问静态变量和静态函数
5.  new 对象都会消耗内存
6.  使用场景: 最常用的地方是数据库连接。
7.  使用单例模式生成一个对象后， 该对象可以被其它众多对象所使用。
8.  私有的__clone() 方法防止克隆对象

单例模式，使某个类的对象仅允许创建一个。构造函数 private 修饰，   
申明一个 static getInstance 方法，在该方法里创建该对象的实例。如果该实例已经存在，则不创建。比如只需要创建一个数据库连接。

#### 工厂模式

工厂模式，工厂方法或者类生成对象，而不是在代码中直接 new。   
使用工厂模式，可以避免当改变某个类的名字或者方法之后，在调用这个类的所有的代码中都修改它的名字或者参数。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 Test1.php
 2 <?php
 3 class Test1{
 4     static function test(){
 5         echo __FILE__;
 6     }
 7 }
 8 
 9 Factory.php
10 <?php
11 class Factory{
12     /*
13      * 如果某个类在很多的文件中都new ClassName()，那么万一这个类的名字
14      * 发生变更或者参数发生变化，如果不使用工厂模式，就需要修改每一个PHP
15      * 代码，使用了工厂模式之后，只需要修改工厂类或者方法就可以了。
16      */
17     static function createDatabase(){
18         $test = new Test1();
19         return $test;
20     }
21 }
22 
23 Test.php
24 <?php
25 spl_autoload_register('autoload1');
26 
27 $test = Factory::createDatabase();
28 $test->test();
29 function autoload1($class){
30     $dir  = __DIR__;
31     $requireFile = $dir."\\".$class.".php";
32     require $requireFile;
33 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

![](https://images2018.cnblogs.com/blog/1009472/201804/1009472-20180411233545229-1881933869.png)

#### 注册模式

注册模式，解决全局共享和交换对象。已经创建好的对象，挂在到某个全局可以使用的数组上，在需要使用的时候，直接从该数组上获取即可。将对象注册到全局的树上。任何地方直接去访问。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 <?php
 2 
 3 class Register
 4 {
 5     protected static  $objects;
 6     function set($alias,$object)//将对象注册到全局的树上
 7     {
 8         self::$objects[$alias]=$object;//将对象放到树上
 9     }
10     static function get($name){
11         return self::$objects[$name];//获取某个注册到树上的对象
12     }
13     function _unset($alias)
14     {
15         unset(self::$objects[$alias]);//移除某个注册到树上的对象。
16     }
17 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

#### 适配器模式

将各种截然不同的函数接口封装成统一的 API。   
PHP 中的数据库操作有 MySQL,MySQLi,PDO 三种，可以用适配器模式统一成一致，使不同的数据库操作，统一成一样的 API。类似的场景还有 cache 适配器，可以将 memcache,redis,file,apc 等不同的缓存函数，统一成一致。   
首先定义一个接口 (有几个方法，以及相应的参数)。然后，有几种不同的情况，就写几个类实现该接口。将完成相似功能的函数，统一成一致的方法。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 接口 IDatabase
2 <?php
3 namespace IMooc;
4 interface IDatabase
5 {
6     function connect($host, $user, $passwd, $dbname);
7     function query($sql);
8     function close();
9 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 MySQL
 2 <?php
 3 namespace IMooc\Database;
 4 use IMooc\IDatabase;
 5 class MySQL implements IDatabase
 6 {
 7     protected $conn;
 8     function connect($host, $user, $passwd, $dbname)
 9     {
10         $conn = mysql_connect($host, $user, $passwd);
11         mysql_select_db($dbname, $conn);
12         $this->conn = $conn;
13     }
14 
15     function query($sql)
16     {
17         $res = mysql_query($sql, $this->conn);
18         return $res;
19     }
20 
21     function close()
22     {
23         mysql_close($this->conn);
24     }
25 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 MySQLi
 2 <?php
 3 namespace IMooc\Database;
 4 use IMooc\IDatabase;
 5 class MySQLi implements IDatabase
 6 {
 7     protected $conn;
 8 
 9     function connect($host, $user, $passwd, $dbname)
10     {
11         $conn = mysqli_connect($host, $user, $passwd, $dbname);
12         $this->conn = $conn;
13     }
14 
15     function query($sql)
16     {
17         return mysqli_query($this->conn, $sql);
18     }
19 
20     function close()
21     {
22         mysqli_close($this->conn);
23     }
24 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 PDO
 2 <?php
 3 namespace IMooc\Database;
 4 use IMooc\IDatabase;
 5 class PDO implements IDatabase
 6 {
 7     protected $conn;
 8     function connect($host, $user, $passwd, $dbname)
 9     {
10         $conn = new \PDO("mysql:host=$host;db, $user, $passwd);
11         $this->conn = $conn;
12     }
13 function query($sql)
14     {
15         return $this->conn->query($sql);
16     }
17 
18     function close()
19     {
20         unset($this->conn);
21     }
22 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

通过以上案例，PHP 与 MySQL 的数据库交互有三套 API，在不同的场景下可能使用不同的 API，那么开发好的代码，换一个环境，可能就要改变它的数据库 API，那么就要改写所有的代码，使用适配器模式之后，就可以使用统一的 API 去屏蔽底层的 API 差异带来的环境改变之后需要改写代码的问题。

#### 策略模式

策略模式，将一组特定的行为和算法封装成类，以适应某些特定的上下文环境。   
eg：假如有一个电商网站系统，针对男性女性用户要各自跳转到不同的商品类目，并且所有的广告位展示不同的广告。在传统的代码中，都是在系统中加入各种 if else 的判断，硬编码的方式。如果有一天增加了一种用户，就需要改写代码。使用策略模式，如果新增加一种用户类型，只需要增加一种策略就可以。其他所有的地方只需要使用不同的策略就可以。   
首先声明策略的接口文件，约定了策略的包含的行为。然后，定义各个具体的策略实现类。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 UserStrategy.php
 2 <?php
 3 /*
 4  * 声明策略文件的接口，约定策略包含的行为。
 5  */
 6 interface UserStrategy
 7 {
 8     function showAd();
 9     function showCategory();
10 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 FemaleUser.php
 2 <?php
 3 require_once 'Loader.php';
 4 class FemaleUser implements UserStrategy
 5 {
 6     function showAd(){
 7         echo "2016冬季女装";
 8     }
 9     function showCategory(){
10         echo "女装";
11     }
12 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 MaleUser.php
 2 <?php
 3 require_once 'Loader.php';
 4 class MaleUser implements UserStrategy
 5 {
 6     function showAd(){
 7         echo "IPhone6s";
 8     }
 9     function showCategory(){
10         echo "电子产品";
11     }
12 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 Page.php//执行文件
 2 <?php
 3 require_once 'Loader.php';
 4 class Page
 5 {
 6     protected $strategy;
 7     function index(){
 8         echo "AD";
 9         $this->strategy->showAd();
10         echo "<br>";
11         echo "Category";
12         $this->strategy->showCategory();
13         echo "<br>";
14     }
15     function setStrategy(UserStrategy $strategy){
16         $this->strategy=$strategy;
17     }
18 }
19 
20 $page = new Page();
21 if(isset($_GET['male'])){
22     $strategy = new MaleUser();
23 }else {
24     $strategy = new FemaleUser();
25 }
26 $page->setStrategy($strategy);
27 $page->index();

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

执行结果图： 

![](https://images2018.cnblogs.com/blog/1009472/201804/1009472-20180411235931617-2111845980.png)

![](https://images2018.cnblogs.com/blog/1009472/201804/1009472-20180411235939863-1854572548.png)

 总结：   
通过以上方式，可以发现，在不同用户登录时显示不同的内容，但是解决了在显示时的硬编码的问题。如果要增加一种策略，只需要增加一种策略实现类，然后在入口文件中执行判断，传入这个类即可。实现了解耦。   
实现依赖倒置和控制反转 _（有待理解）_   
通过接口的方式，使得类和类之间不直接依赖。在使用该类的时候，才动态的传入该接口的一个实现类。如果要替换某个类，只需要提供一个实现了该接口的实现类，通过修改一行代码即可完成替换。

#### 观察者模式

1：观察者模式 (Observer)，当一个对象状态发生变化时，依赖它的对象全部会收到通知，并自动更新。   
2：场景: 一个事件发生后，要执行一连串更新操作。传统的编程方式，就是在事件的代码之后直接加入处理的逻辑。当更新的逻辑增多之后，代码会变得难以维护。这种方式是耦合的，侵入式的，增加新的逻辑需要修改事件的主体代码。   
3：观察者模式实现了低耦合，非侵入式的通知与更新机制。 


定义一个事件触发抽象类。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 EventGenerator.php
 2 <?php
 3 require_once 'Loader.php';
 4 abstract class EventGenerator{
 5     private $observers = array();
 6     function addObserver(Observer $observer){
 7         $this->observers[]=$observer;
 8     }
 9     function notify(){
10         foreach ($this->observers as $observer){
11             $observer->update();
12         }
13     }
14 }

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

定义一个观察者接口

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
Observer.php
<?php
require_once 'Loader.php';
interface Observer{
    function update();//这里就是在事件发生后要执行的逻辑
}

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 <?php
 2 //一个实现了EventGenerator抽象类的类，用于具体定义某个发生的事件
 3 require 'Loader.php';
 4 class Event extends EventGenerator{
 5     function triger(){
 6         echo "Event<br>";
 7     }
 8 }
 9 class Observer1 implements Observer{
10     function update(){
11         echo "逻辑1<br>";
12     }
13 }
14 class Observer2 implements Observer{
15     function update(){
16         echo "逻辑2<br>";
17     }
18 }
19 $event = new Event();
20 $event->addObserver(new Observer1());
21 $event->addObserver(new Observer2());
22 $event->triger();
23 $event->notify();

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

当某个事件发生后，需要执行的逻辑增多时，可以以松耦合的方式去增删逻辑。也就是代码中的红色部分，只需要定义一个实现了观察者接口的类，实现复杂的逻辑，然后在红色的部分加上一行代码即可。这样实现了低耦合。

#### 原型模式

原型模式（对象克隆以避免创建对象时的消耗）   
1：与工厂模式类似，都是用来创建对象。   
2：与工厂模式的实现不同，原型模式是先创建好一个原型对象，然后通过 clone 原型对象来创建新的对象。这样就免去了类创建时重复的初始化操作。   
3：原型模式适用于大对象的创建，创建一个大对象需要很大的开销，如果每次 new 就会消耗很大，原型模式仅需要内存拷贝即可。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
Canvas.php
<?php
require_once 'Loader.php';
class Canvas{
private $data;
function init($width = 20, $height = 10)
    {
        $data = array();
        for($i = 0; $i < $height; $i++)
        {
            for($j = 0; $j < $width; $j++)
            {
                $data[$i][$j] = '*';
            }
        }
        $this->data = $data;
    }
function rect($x1, $y1, $x2, $y2)
    {
        foreach($this->data as $k1 => $line)
        {
            if ($x1 > $k1 or $x2 < $k1) continue;
           foreach($line as $k2 => $char)
            {
              if ($y1>$k2 or $y2<$k2) continue;
                $this->data[$k1][$k2] = '#';
            }
        }
    }

    function draw(){
        foreach ($this->data as $line){
            foreach ($line as $char){
                echo $char;
            }
            echo "<br>;";
        }
    }
}

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
 1 Index.php
 2 <?php
 3 require 'Loader.php';
 4 $c = new Canvas();
 5 $c->init();
 6 / $canvas1 = new Canvas();
 7 // $canvas1->init();
 8 $canvas1 = clone $c;//通过克隆，可以省去init()方法，这个方法循环两百次
 9 //去产生一个数组。当项目中需要产生很多的这样的对象时，就会new很多的对象，那样
10 //是非常消耗性能的。
11 $canvas1->rect(2, 2, 8, 8);
12 $canvas1->draw();
13 echo "-----------------------------------------<br>";
14 // $canvas2 = new Canvas();
15 // $canvas2->init();
16 $canvas2 = clone $c;
17 $canvas2->rect(1, 4, 8, 8);
18 $canvas2->draw();

```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

执行结果：

![](https://images2018.cnblogs.com/blog/1009472/201804/1009472-20180412002250788-449235848.png)

#### 装饰器模式

1：装饰器模式，可以动态的添加修改类的功能   
2：一个类提供了一项功能，如果要在修改并添加额外的功能，传统的编程模式，需要写一个子类继承它，并重写实现类的方法   
3：使用装饰器模式，仅需要在运行时添加一个装饰器对象即可实现，可以实现最大额灵活性。

转自：https://blog.csdn.net/flitrue/article/details/52614599

如有侵权，请联系删除。
