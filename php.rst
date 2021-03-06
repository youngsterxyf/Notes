代码重用
=========

PHP提供了两个非常简单却很有用的语句，它们允许重新使用任何类型的代码。使用一条require()或include()语句，可以将一个文件载入到PHP脚本中。通常，这个文件可以包含任何希望在一个脚本中输入的内容，其中包括PHP语句，文本，HTML标记，PHP函数或PHP类。

**文件扩展名和require语句**

PHP并不会查看所需文件的扩展名。这就意味着，只要不想直接调用这个文件，就可以任意命名该文件。当使用require()语句载入文件时，它会作为PHP文件的一部分被执行。

通常，如果PHP语句放在一个HTML文件(例如，名为page.html的文件)时，它们是不会被处理的。PHP通常用来解析扩展名被定义为.php的文件。但是，如果通过require()语句载入这个page.html，文件内的任何PHP命令都会被处理。因此，可以使用任何扩展名来命名包含文件，但要尽量遵循一个约定，例如将扩展名命名为.inc是一个很好的办法。需要注意的一个问题是，如果扩展名为.inc或一些其他的非标准扩展名的文件保存在Web文档树中，而且用户可以在浏览器中直接载入它们，用户将可以以普通文本的形式查看源代码，因此，将被包含文件保存在文档树之外，或使用标准的文件扩展名是非常重要的。

require()语句和include()语句几乎是等价的。二者的差异在于，当这两个语句调用失败后，require()将给出一个致命错误，而include()只是给出一个警告。

**使用require_once()和include_once()**

require()和include()都有变体函数，分别是require_once()和include_once()，它们的用途是确保一个被包含文件只能被包含一次

类与对象
========

PHP当中对象是按引用传递的，即每个包含对象的变量都持有对象的引用(reference)，而不是整个对象的拷贝。

PHP的引用就是别名，就是两个不同的变量名字指向相同的内容。在php5，一个对象变量已经不再保存整个对象的值，只是保存一个标识符来访问真正的对象内容。当对象作为参数传递，作为结果返回，或者赋值给另外一个变量，另外一个变量跟原来的不是引用的关系，只是他们都保存着同一个标识符的拷贝，这个标识符指向同一个对象的真正内容。

::

	<?php
	class A {
		public $foo = 1;	
	}

	$a = new A;
	$b = $a;	// $a, $b都是同一个标识符的拷贝

	$b->foo = 2;
	echo $a->foo."\n";

	$c = new A;
	$d = $c;	// $c, $d是引用

	$d->foo = 2;
	echo $c->foo."\n";

	$e = new A;
	
	function foo($obj) {
		$obj->foo = 2;
	}

	foo($e);
	echo $e->foo."\n";
	
	?>

一个类可以在声明中用extends关键字继承另一个类的方法和成员。不能扩展多个类，只能继承一个基类。

被继承的方法和成员可以通过用同样的名字重新声明被覆盖，除非父类定义方法时使用了final关键字。可以通过parent::来访问被覆盖的方法或成员。

::

	<?php
	class SimpleClass
	{
		// 成员声明
		public $var = 'a default value';

		// 方法声明
		public function displayVar() {
			echo $this->var;
		}
	}

	class ExtendClass extends SimpleClass
	{
		// Redefine the parent method
		function displayVar()
		{
			echo "Extending class\n"
			parent::displayVar();
		}
	}
	
	$extended = new ExtendClass();
	$extended->displayVar();
	?>

类的变量成员叫做"属性"，或者"字段"，"特征"。属性声明是由关键字public或者protected或者private开头，然后跟一个变量来组成。属性中的变量可以初始化，但是初始化的值必须是常数，这里的常数是指php脚本在编译阶段时就为常数，而不是编译阶段之后在运行阶段运算出的常数。

在类的成员方法里面，可以通过$this->property (property是属性名字)这种方式来访问类的属性，方法，但是要访问类的静态属性或者在静态方法里面却不能使用，而是使用self::$property。

static关键字
^^^^^^^^^^^^

声明类成员或方法为static，就可以不实例化类而直接访问。不能通过一个对象来访问其中的静态成员(静态方法除外)。

为了兼容PHP4，如果没有指定"可见性",属性和方法默认为public。

由于静态方法不需要通过对象即可调用，所以伪变量$this在静态方法中不可用。

静态属性不可以由对象通过->操作符来访问。

就像其他所有的PHP静态变量一样，静态属性只能被初始化为一个字符值或一个常量，不能使用表达式。所以你可以把静态属性初始化为整型或数组，但不能指向另一个变量或函数返回值，也不能指向一个对象。

::

	<?php
	class Foo
	{
		public static $my_static = 'foo';
		
		public function staticValue() {
			return self::$my_static;		
		}
	}

	class Bar extends Foo
	{
		public function fooStatic() {
			return parent::$my_static;
		}
	}

	print Foo::$my_static . "\n";
	
	$foo = new Foo();
	print $foo->staticValue() . "\n";
	?>

类常量
^^^^^^^

可以在类中使用const关键字定义常量。常量的值始终保持不变。在定义和使用常量的时候不需要使用$符号。

常量的值必须是一个定值，不能是变量，类属性或其他操作(如函数调用)的结果。

PHP5.3之后，可以用一个变量来动态调用类。但该变量的值不能为关键字self, parent或static。

::

	<?php
	class MyClass
	{
		const constant = 'constant value';
		
		function showContent() {
			echo self::constant . "\n";
		}
	}

	echo MyClass::constant . "\n";

	$classname = "MyClass";
	echo $classname::constant . "\n";	// PHP 5.3之后

	$class = newMyClass();
	$class->showConstant();

	echo $class::constant . "\n"
	?>


自动加载对象
^^^^^^^^^^^^

很多开发者写面向对象的应用程序时对每个类的定义建立一个PHP源文件。一个很大的烦恼是不得不在每个脚本(每个类一个文件)开头写一个长长的的包含文件列表。

在PHP5中，不再需要这样了。可以定义一个__autoload函数，它会在试图使用尚未定义的类时自动调用。通过调用此函数，脚本引擎在PHP出错失败前有了最后一个机会加载所需的类。

*在__autoload函数中抛出的异常不能被catch语句块捕获并导致致命错误。*

::

	<?php
	function __autoload($class_name) {
		require_once $class_name . '.php';
	}

	$obj = new MyClass1();
	$obj2 = new MyClass2();
	?>

本例尝试分别从MyClass1.php和MyClass2.php文件中加载MyClass1和MyClass2类。

构造函数和析构函数
^^^^^^^^^^^^^^^^^

抽象类
^^^^^^^^

PHP5支持抽象类和抽象方法。抽象类不能直接实例化，必须继承该抽象类，然后再实例化子类。抽象类中至少要包含一个抽象方法。如果类方法被声明为抽象的，那么其中就不能包括具体的功能实现。

继承一个抽象类的时候，子类必须实现抽象类中的所有抽象方法；另外，这些方法的可见性必须和抽象类中一样(或者更为宽松)。如果抽象类中某个抽象方法被声明为protected，那么子类中实现的方法就应该声明为protected或者public，而不能定义为private。

::

	<?php
	abstract class AbstractClass
	{
		// 强制要求子类定义这些方法
		abstract protected function getValue();
		abstract protected function prefixValue($prefix);

		// 普通方法(非抽象方法)
		public function printOut() {
			print $this->getValue() . "\n";
		}
	}

	class ConcreteClass1 extends AbstractClass
	{
		protected function getValue()
		{
			return "ConcreteClass1";
		}
		
		public function prefixValue($prefix) {
			return "{$prefix}ConcreteClass1";		
		}
	}

	class ConcreteClass2 extends AbstractClass
	{
		public function getValue() {
			return "ConcreteClass2";
		}

		public function prefixValue($prefix) {
			return "{$prefix}ConcreteClass2";
		}
	}

	$class1 = new ConcreteClass1;
	$class1->printOut();
	echo $class1->prefixValue('FOO_') . "\n";

	$class2 = new ConcreteClass2;
	$class2->printOut();
	echo $class2->prefixValue('FOO_') . "\n";
	?>

接口
^^^^^

使用接口(interface)，可以指定某个类必须实现哪些方法，但不需要定义这些方法的具体内容。

可以通过interface来定义一个接口，就像定义一个标准的类一样，但其中定义所有的方法都是空的。

接口中定义的所有方法都必须是public，这是接口的特性。

要实现一个接口，可以使用implements操作符。类中必须实现接口中定义的所有方法，否则会报一个fatal错误。如果要实现多个接口，可以用逗号来分隔多个接口的名称。

*实现多个接口时，接口中的方法不能重名。*

*接口也可以继承，通过使用extends操作符。*

接口中也可以定义常量。接口常量和类常量的使用完全相同。它们都是定值，不能被子类或子接口修改。

重载
^^^^^

PHP所提供的"重载"(overloading)是指动态地"创建"类属性和方法。通过魔术方法(magic methods)来实现的。

所有的重载方法都必须被声明为public。

对象迭代
^^^^^^^^^^

PHP5提供了一种迭代(iteration)对象的功能，就像使用数组那样，可以通过foreach来遍历对象中的属。默认情况下，在外部迭代只能得到外部可见的属性的值。

::

    <?php
    class MyClass
    {
        public $var1 = 'value 1';
        public $var2 = 'value 2';
        public $var3 = 'value 3';

        protected $protected = 'protected var';
        private $private = 'private var';

        function iterateVisible() {
            echo "MyClass::iterateVisible:\n";
            foreach($this as $key => $value) {
                print "$key => $value\n";
            }
        }
    }

    $class = new MyClass();

    foreach($class as $key = > $value) {
        print "$key => $value\n";
    }

    echo "\n";

    $class->iterateVisible();
    ?>

如上所示，foreach遍历了所有可见的属性。你也可以通过实现PHP5自带的Iterator接口来实现迭代。使用Iterator接口可以让对象自行决定如何迭代自己。

::

    <?php
    class MyIterator implements Iterator
    {
        private $var = array();

        public function __construct($array)
        {
            if (is_array($array)) {
                $this->var = $array;
            }
        }

        public function rewind() {
            echo "rewinding\n";
            reset($this->var);
        }

        public function current() {
            $var = current($this->var);
            echo "current: $var\n";
            return $var;
        }

        public function key() {
            $var = key($this->var);
            echo "key: $var\n";
            return $var;
        }

        public function valid() {
            $var = $this->current() != false;
            echo "valid: {$var}\n";
            return $var;
        }
    }

    $values = array(1, 2, 3);
    $it = new MyIterator($values);

    foreach ($it as $a => $b) {
        print "$a: $b\n";
    }
    ?>

设计模式
^^^^^^^^^

**工厂模式**

工厂模式(Factory)允许你在代码执行时实例化对象。它之所以被称为工厂模式是因为它负责"生产"对象。工厂方法的参数是你要生成的对象对应的名称。

::

    <?php
    class Example
    {
        // The parameterized factory method
        public static function factory($type)
        {
            if (include_once 'Drivers/' . $type . '.php') {
                $classname = 'Driver_' . $type;
            } else {
                throw new Exception ('Driver not found');
            }
        }
    }
    ?>

按上面的方式可以动态加载drivers。如果Example类是一个数据库抽象类，那么可以这样来生成MySQLhe SQLite驱动对象。

::

    <?php
    // Load a MySQL Driver
    $mysql = Example::factory('MySQL');

    // Load a SQLite Driver
    $sqlite = Example::factory('SQLite');
    ?>

**单例**

单例模式(Singleton)用于为一个类生成一个唯一的对象。最常用的地方是数据库连接。使用单例模式生成一个对象后，该对象可以被其它众多对象所使用。

::

    <?php
    class Example
    {
        // 保存类实例在此属性中
        private static $instance;

        // 构造方法声明为private，防止直接创建对象
        private function __construct()
        {
            echo 'I am constructed';
        }

        // singleton方法
        public static function singleton()
        {
            if (!isset(self::$instance)) {
                $c = __CLASS__;
                self::$instance = new $c;
            }

            return self::$instance;
        }

        // Example类的普通方法
        public function bark()
        {
            echo 'Woof!';
        }

        // 阻止用户复制对象实例
        public function __clone()
        {
            trigger_error('Clone is not allowed.', E_USER_ERROR);
        }
    }
    ?>

这样我们就能得到一个独一无二的Example类的对象。

::

    <?php

    // 这个写法会出错，因为构造方法被声明为private
    $test = new Example;

    // 下面将得到Example类的单例对象
    $test = Example::singleton();
    $test->bark();

    // 复制对象将导致一个E_USER_ERROR.
    $test_clone = clone $test;
    ?>

Final关键字
^^^^^^^^^^^^

PHP5新增了一个final关键字。如果父类中的方法被声明为final，则子类无法覆盖该方法；如果一个类被声明为final，则不能被继承。

对象复制
^^^^^^^^^

在多数情况下，我们并不需要完全复制一个对象来获得其中属性。但有一个情况下确实需要：如果你有一个GTK窗口对象，该对象持有窗口相关的资源。你可能会复制一个新的窗口，保持所有属性与原来的窗口相同，但必须是一个新的对象(因为如果不是新的对象，那么一个窗口中的改变就会影响到另一个窗口)。还有一种情况：如果对象A中保存着对象B的引用，当你复制对象A时，你想其中使用的对象不再是对象B而是对象B的一个副本，那么你必须得到对象A的一个副本。

对象复制可以通过clone关键字来完成(如果可能，这将调用对象的__clone()方法)。对象中的__clone()方法不能被直接调用。

::

    $copy_of_object = clone $object;

当对象被复制后，PHP5会对对象的所有属性执行一个浅复制(shallow copy)。所有的引用属性仍然会是一个指向原来的变量的引用。

对象比较
^^^^^^^^^^

当使用对比操作符(==)比较两个对象变量时，比较的原则是：如果两个对象的属性和属性值都相等，而且两个对象是同一个类的实例，那么这两个对象变量相等。

而如果使用全等操作符(===)，这两个对象变量一定要指向某个类的同一个实例(即同一个对象)。

字符串的连接
^^^^^^^^^^^^^^

字符串连接符---点号(.)，可以将几段文本连接成一个字符串。通常，当使用echo命令向浏览器发送输出时，将使用这个连接符。这可以用来避免编写多个echo命令。

对于任何非数组变量，也可以将变量写入到一个由双引号引用起来的字符串中：

::

    echo "$tireqty tires<br />";

这个语句等价于：

::

    echo $tireqty.' tires<br />';

这两种格式都是有效的，而且使用任何一种格式都只是个人爱好问题。

用一个字符串的内容代替一个变量的操作就是插补(interpolation)。 **请注意，插补操作只是双引号引用的字符串的特性之一。** 不能像这样将一个变量名称放置在一个由单引号引用的字符串中。

::

    echo '$tireqty tires<br />';

该代码将"$tireqty tires<br />"发送给浏览器。

在双引号中，变量名称将被变量值所替代，而在单引号中，变量名称，或者其他任何文件都会不经修改而发送给浏览器。

可变变量
^^^^^^^^^^^^

可变变量允许我们动态地改变一个变量的名称。工作原理是用一个变量的值作为另一个变量的名称。例如：

::

    $varname = 'tireqty';

那么：

::
    
    $$varname = 5;

等价于：

::

    $tireqty = 5;

声明和使用常量
^^^^^^^^^^^^^^^^^^

使用define函数来定义常量。常量的名称通常都是由大写字母组成的。

常量与变量之间的一个重要不同点在于引用一个常量的时候，它前面并没有$符号。如果要使用一个常量的值，只需要使用其名称就可以了。

除了可以自己定义常量外，PHP还预定义了许多常量。了解这些常量的简单方法就是运行phpinfo()命令：

::

    phpinfo();

这个命令将给出一个PHP预定义常量和变量的列表，以及其他有用的信息。

变量和常量的另一个差异在于常量只可以保存布尔值，整数，浮点数或字符串数据。

理解变量的作用域
^^^^^^^^^^^^^^^^^^^

* 内置超级全局变量可以在脚本的任何地方使用和可见。
* 常量，一旦被声明，将可以在全局可见;也就是说，他们可以在函数内外使用。
* 在一个脚本中声明的全局变量在整个脚本中是可见的，但不是在函数内部。
* 函数内部使用的变量声明为全局变量时，其名称要与全局变量名称一致。
* 在函数内部创建并被声明为静态的变量无法在函数外部可见，但是可以在函数的多次执行过程中保持该值。
* 在函数内部创建的变量对函数来说是本地的，而当函数终止时，该变量也就不存在了。

特殊的比较操作符
^^^^^^^^^^^^^^^^^^

恒等操作符===(三个等于号)。只有当恒等操作符两边的操作数相等并且具有相同的数据类型时，其返回值才为true。例如，0=='0'将为true，但是0==='0'就不是true，因为左边的0是一个整数，而另一个0则是一个字符串。

其他操作符
^^^^^^^^^^^^^^

* 错误抑制操作符@可以在任何表达式前面使用，即任何有值的或者可以计算出值的表达式前

* 执行操作符实际上是一对操作符，它是一对反向单引号(``)。反向单引号不是一个单引号---通常，它与~位于键盘的相同位置。PHP将试着将反向单引号之间的命令当作服务器端的命令行来执行。表达式的值就是命令的执行结果。

* 类型操作符：instanceof。instanceof操作符允许检查一个对象是否是特定类的实例。


-----

print和echo都不是真正的函数，但是都可以以带有参数的函数形式进行调用。二者都可以当作一个操作符：只要将要显示的字符串放置在echo或print关键字之后。以函数形式调用print将使其返回一个值(1)。如果希望在一个更复杂的表达式中生成输出，这个功能可能是有用的，但是print要比echo的速度慢。

使用可变函数
^^^^^^^^^^^^^^^^^

PHP有一个函数库，这个函数库允许我们使用不同的方法来操作和测试变量。

* 测试和设置变量类型：大部分的可变函数都是用来测试一个函数的类型的。PHP中有两个最常见的函数，分别是gettype()和settype()。

* PHP还提供了一些特定类型的测试函数。每一个函数都使用一个变量作为其参数，并且返回true或false。这些函数是:
  is_array()
  is_double(), is_float(), is_real() (所有都是相同的函数)
  is_long, is_int(), is_integer() (所有都是相同的函数)
  is_string()
  is_object()
  is_resource()
  is_null
  is_scalar() --- 检查该变量是否是标量，也就是，一个整数，布尔值，字符串或浮点数
  is_numeric() --- 检查该变量是否是任何类型的数字或数字字符串
  is_callable() --- 检查该变量是否是有效的函数名称

* 测试变量状态。
  PHP有几个函数可以用来测试变量的状态，第一个函数就是isset()。这个函数需要一个变量名称作为参数，如果这个变量存在则返回true，否则返回false。也可以传递一个由逗号间隔的变量列表，如果所有变量都被设置了，isset()函数将返回true。
  也可以使用与isset()函数相对应的unset()函数来销毁一个变量。
  函数empty()可以用来检查一个变量是否存在，以及它的值是否为非空和非0，相应的返回值为true或false。

switch语句
^^^^^^^^^^^^

在switch语句中，只要条件值是一个简单的数据类型(整型，字符串或浮点型)，条件就可以具有任意多个不同的值。必须提供一个case语句来处理每一个条件值，并且提供对应的动作代码。此外，还应该有一个默认的case条件来处理没有提供任何特定值的情况。

switch语句和if或elseif语句的行为有所不同。如果没有专门使用花括号来声明一个语句块，if语句只能影响一条语句。而switch语句刚好相反。当switch语句中的特定case被匹配时，PHP将执行该case下的代码，直至遇到break语句。如果没有break语句，switch将执行这个case以下所有值为true的case中的代码。当遇到一个break语句时，才会执行switch后面的语句。

使用数组
===========

数组排序
^^^^^^^^^^

* 使用sort()函数：sort()函数是区分字母大小写的。所有的大写字母都在小写字母的前面。所以'A'小于'Z'，而'Z'小于'a'。
  该函数的第二个参数是可选的。这个可选参数可以传递SORT_REGULAR（默认值），SORT_NUMERIC或SORT_STRING。指定排序类型的功能是非常有用，例如，当要比较可能包含有数字2和12的字符串时。从数字角度看，2要小于12，但是作为字符串，'12'却要小于'2'。

* 使用asort()函数和ksort()函数对相关数组排序：函数asort()根据数组的每个元素值进行排序，函数ksort()则是按关联数组的关键字排序。

* 反向排序：sort(), asort()和ksort()每个都有一个对应的反向排序的函数(rsort(), arsort()和krsort())，可以将数组按降序排序。 

对数组进行重新排序
^^^^^^^^^^^^^^^^^^^^^

* 函数shuffle()将数组各元素进行随机排序。

* 函数array_reverse()给出一个原来数组的反向排序。

从文件载入数组
^^^^^^^^^^^^^^^^^^

可以使用file()函数将整个文件载入到一个数组中。文件中的每行则成为数组中的一个元素。

字符串操作与正则表达式
========================

字符串格式化
^^^^^^^^^^^^^^^

通常，在使用用户输入的字符串(通常来自HTML表单界面)之前，必须对它们进行整理。

* 字符串整理：chop()，ltrim()，rtrim()和trim()。整理字符串的第一步是清理字符串中多余的空格。虽然这一步操作不是必需的，但如果要将字符串存入一个文件或数据库中，或者将它和别的字符串进行比较，这就是非常有用的。

  - trim()函数可以除去字符串开始位置和结束位置的空格，并将结果字符串返回。默认情况下，除去的字符是换行符和回车符(\n和\r)，水平和垂直制表符(\t和\x0B)，字符串结束符(\0)和空格。除了这个默认的过滤字符列表外，也可以在该函数的第二个参数中提供要过滤的特殊字符。

* 格式化字符串以便显示。

  - 使用HTML格式化：nl2br()函数。nl2br()函数将字符串作为输入函数，用XHTML中的<br />标记代替字符串中的换行符。

  - 为打印输出而格式化字符串：printf()，sprintf()

* 格式化字符串以便存储：addslashes()和stripslashes()。

用字符串函数连接和分割字符串
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 使用函数explode()，implode()和join()

* 使用substr()函数：允许我们访问一个字符串给定起点和终点的子字符串。

字符串的比较
^^^^^^^^^^^^^^^

* 字符串的排序：strcmp()，strcasecmp()和strnatcmp()可用于字符串的排序。

  - strcmp()函数的原型为：int strcmp(string str1, string str2)。该函数需要两个进行比较的参数字符串。如果这两个字符串相等，该函数就返回0,如果按字典顺序str1在str2后面(大于str2)就返回一个正数，否则返回一个负数。这个函数是区分大小写的。

  - 函数strcasecmp()除了不区分大小写之外，其他和strcmp()一样。

  - 函数strnatcmp()及与之对应的不区分大小写的strnatcasecmp()函数是在PHP4中新添加的。这两个函数按"自然顺序"比较字符串，所谓自然顺序是按人们习惯的顺序进行排序。

* 使用strlen()函数测试字符串的长度。


Sessions
=========

每个Session都是存储在服务器设定目录中的唯一文件，目录的位置由php.ini中的session.save_path控制。

session有效期内所有键/值对数据组合成一个关联数组，经过序列化后存储在该文件的内部结构中，一般来讲，这个有效期会持续到客户端浏览器关闭。要开始一个Session，无论怎样，你都要使用session_start函数。

超全局变量
============

REQUEST数组，是一个包含各类请求数据的数组，即$_COOKIE，$_GET，$_POST。美中不足的是，数组中键名必须唯一，否则对于$_REQUEST['lname']这样的取值来说不能确定是来自前三者中的哪一个。

你可以通过php.ini中的配置指令variables_order来控制整个环境中的超全局数组。我的服务器上配置的是GPC，即PHP在加载后依次建立的超全局数组是GET，POST和COOKIE。如果你删除了其中的一个字母，保存后重启你的web服务器，则那个字母所代表的超全局变量就不会自动建立。
