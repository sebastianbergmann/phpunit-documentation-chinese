

.. _fixtures:

===============
基境（fixture）
===============

在编写测试时，最费时的部分之一是编写代码来将整个场景设置成某个已知的状态，并在测试结束后将其复原到初始状态。这个已知的状态称为测试的\ *基境（fixture）*\ 。

在\ :ref:`writing-tests-for-phpunit.examples.StackTest.php`\ 中，基境就是存储在 ``$stack`` 变量中的数组。然而，绝大多数时候基境均远比一个简单数组要复杂，用于建立基境的代码量也会随之增长。测试的真正内容就被淹没于建立基境带来的干扰中。当编写多个需要类似基境的测试时这个问题就变得更糟糕了。如果没有来自于测试框架的帮助，就不得不在写每一个测试时都将建立基境的代码重复一次。

PHPUnit 支持共享建立基境的代码。在运行某个测试方法前，会调用一个名叫 ``setUp()`` 的模板方法。``setUp()`` 是创建测试所用对象的地方。当测试方法运行结束后，不管是成功还是失败，都会调用另外一个名叫 ``tearDown()`` 的模板方法。``tearDown()`` 是清理测试所用对象的地方。

在\ :ref:`writing-tests-for-phpunit.examples.StackTest2.php`\ 中，我们在测试之间运用生产者-消费者关系来共享基境。这并非总是预期的方式，甚至有时是不可能的。\ :numref:`fixtures.examples.StackTest.php` 展示了另外一个编写测试 ``StackTest`` 的方式。在这个方式中，不再重用基境本身，而是重用建立基境的代码。首先声明一个实例变量，``$stack``，用来替代方法内的局部变量。然后把 ``array`` 基境的建立放到 ``setUp()`` 方法中。最后，从测试方法中去除冗余代码，在 ``assertSame()`` 断言方法中使用新引入的实例变量 ``$this->stack`` 替代方法内的局部变量 ``$stack``。

.. code-block:: php
    :caption: 用 setUp() 来创建堆栈基境
    :name: fixtures.examples.StackTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StackTest extends TestCase
    {
        private $stack;

        protected function setUp(): void
        {
            $this->stack = [];
        }

        public function testEmpty(): void
        {
            $this->assertTrue(empty($this->stack));
        }

        public function testPush(): void
        {
            array_push($this->stack, 'foo');

            $this->assertSame('foo', $this->stack[count($this->stack)-1]);
            $this->assertFalse(empty($this->stack));
        }

        public function testPop(): void
        {
            array_push($this->stack, 'foo');

            $this->assertSame('foo', array_pop($this->stack));
            $this->assertTrue(empty($this->stack));
        }
    }

测试类的每个测试方法都会运行一次 ``setUp()`` 和 ``tearDown()`` 模板方法（同时，每个测试方法都是在一个全新的测试类实例上运行的）。

另外，``setUpBeforeClass()`` 与 ``tearDownAfterClass()`` 模板方法将分别在测试用例类的第一个测试运行之前和测试用例类的最后一个测试运行之后调用。

下面这个例子中展示了测试用例类中所有可用的模板方法。

.. code-block:: php
    :caption: 展示所有可用模板方法的示例
    :name: fixtures.examples.TemplateMethodsTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class TemplateMethodsTest extends TestCase
    {
        public static function setUpBeforeClass(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function setUp(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function assertPreConditions(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public function testOne(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(true);
        }

        public function testTwo(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(false);
        }

        protected function assertPostConditions(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function tearDown(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public static function tearDownAfterClass(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function onNotSuccessfulTest(Throwable $t): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            throw $t;
        }
    }

.. parsed-literal::

    $ phpunit TemplateMethodsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    TemplateMethodsTest::setUpBeforeClass
    TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testOne
    TemplateMethodsTest::assertPostConditions
    TemplateMethodsTest::tearDown
    .TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testTwo
    TemplateMethodsTest::tearDown
    TemplateMethodsTest::onNotSuccessfulTest
    FTemplateMethodsTest::tearDownAfterClass

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) TemplateMethodsTest::testTwo
    Failed asserting that <boolean:false> is true.
    /home/sb/TemplateMethodsTest.php:30

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _fixtures.more-setup-than-teardown:

setUp() 多、tearDown() 少
############################

理论上说，``setUp()`` 和 ``tearDown()`` 是精确对称的，但是实践中并非如此。实际上，只有在 ``setUp()`` 中分配了诸如文件或套接字之类的外部资源时才需要实现 ``tearDown()`` 。如果 ``setUp()`` 中只创建纯 PHP 对象，通常可以略过 ``tearDown()``。不过，如果在 ``setUp()`` 中创建了大量对象，你可能想要在 ``tearDown()`` 中 ``unset()`` 指向这些对象的变量，这样它们就可以被垃圾回收机制回收掉。对测试用例对象的垃圾回收动作则是不可预知的。

.. _fixtures.variations:

变体
##########

如果拥有两个测试，它们的基境建立工作略有不同，该怎么办？有两种可能：

-

  如果两个 ``setUp()`` 代码仅有微小差异，把有差异的代码内容从 ``setUp()`` 移到测试方法内。

-

  如果两个 ``setUp()`` 是确实不一样，那么需要另外一个测试用例类。参考基境建立工作的不同之处来命名这个类。

.. _fixtures.sharing-fixture:

基境共享
###############

有几个好的理由来在测试之间共享基境，但是大部分情况下，在测试之间共享基境的需求都源于某个未解决的设计问题。

一个有实际意义的多测试间共享基境的例子是数据库连接：只登录数据库一次，然后重用此连接，而不是每个测试都建立一个新的数据库连接。这样能加快测试的运行。

:numref:`fixtures.sharing-fixture.examples.DatabaseTest.php` 中用 ``setUpBeforeClass()`` 和 ``tearDownAfterClass()`` 模板方法来分别在测试用例类的第一个测试之前和最后一个测试之后连接与断开数据库。

.. code-block:: php
    :caption: 在同一个测试套件内的不同测试之间共享基境
    :name: fixtures.sharing-fixture.examples.DatabaseTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DatabaseTest extends TestCase
    {
        private static $dbh;

        public static function setUpBeforeClass(): void
        {
            self::$dbh = new PDO('sqlite::memory:');
        }

        public static function tearDownAfterClass(): void
        {
            self::$dbh = null;
        }
    }

需要反复强调的是：在测试之间共享基境会降低测试的价值。潜在的设计问题是对象之间并非松散耦合。如果解决掉潜在的设计问题并使用桩件（stub）（参见\ :ref:`test-doubles`\）来编写测试，就能达成更好的结果，而不是在测试之间产生运行时依赖并错过改进设计的机会。

.. _fixtures.global-state:

全局状态
############

`使用单件（singleton）的代码很难测试。 <http://googletesting.blogspot.com/2008/05/tott-using-dependancy-injection-to.html>`_\ 使用全局变量的代码也一样。通常情况下，欲测代码和全局变量之间会强烈耦合，并且其创建无法控制。另外一个问题是，一个测试对全局变量的改变可能会破坏另外一个测试。

在 PHP 中，全局变量是这样运作的：

-

  全局变量 ``$foo = 'bar';`` 实际上是存储为 ``$GLOBALS['foo'] = 'bar';`` 的。

-

  ``$GLOBALS``\ 这个变量是一种被称为\ *超全局*\ 变量的变量。

-

  超全局变量是一种在任何变量作用域中都总是可用的内建变量。

-

  在函数或者方法的变量作用域中，要访问全局变量 ``$foo``，可以直接访问 ``$GLOBALS['foo']``，或者用 ``global $foo;`` 来创建一个引用全局变量的局部变量。

除了全局变量，类的静态属性也是一种全局状态。

在版本 6 之前，默认情况下，PHPUnit 用一种更改全局变量与超全局变量(``$GLOBALS``、\ ``$_ENV``、\ ``$_POST``、\ ``$_GET``、\ ``$_COOKIE``、\ ``$_SERVER``、\ ``$_FILES``、\ ``$_REQUEST``)不会影响到其他测试的方式来运行所有测试。

在版本 6 中，默认情况下 PHPUnit 不再对全局变量和超全局变量进行这种备份与恢复的操作。可以用 ``--globals-backup`` 选项或在 XML 配置文件中用 ``backupGlobals="true"`` 将其激活。

通过用 ``--static-backup`` 选项或在 XML 配置文件中设置 ``backupStaticAttributes="true"``，可以将此隔离扩展到类的静态属性。

.. admonition:: 注

   对全局变量和类的静态属性的备份与还原操作使用了 ``serialize()`` 与 ``unserialize()``。

   某些类的实例对象（比如 ``PDO``）无法序列化，因此如果把这样一个对象存放在比如说 ``$GLOBALS`` 数组内时，备份操作就会出问题。

在 :ref:`appendixes.annotations.backupGlobals` 中所讨论的 ``@backupGlobals`` 标注可以用来控制对全局变量的备份与还原操作。另外，还可以提供一个全局变量的名单，名单中的全局变量将被排除于备份与还原操作之外，就像这样：

.. code-block:: php

    final class MyTest extends TestCase
    {
        protected $backupGlobalsExcludeList = ['globalVariable'];

        // ...
    }

.. admonition:: 注

   在方法（例如 ``setUp()`` 方法）内对 ``$backupGlobalsBlacklist`` 属性进行设置是无效的。

在 :ref:`appendixes.annotations.backupStaticAttributes` 中提到的 ``@backupStaticAttributes`` 标注可以用于在每个测试之前备份所有已声明类的静态属性值并在其后恢复。

它所处理的并不只是测试类自身，而是在测试开始时已声明的所有类。它只作用于静态类属性，不作用于函数内声明的静态变量。

.. admonition:: 注

   只有启用了 ``@backupStaticAttributes`` 的测试方法才会在方法之前执行此操作。如果在此之前运行的某个没有启用 ``@backupStaticAttributes`` 的测试方法改变了静态属性的值，那么被备份及还原的将会是这个改变后的值——而非初始声明时提供的默认值。PHP 并不额外记录任何静态变量的声明时提供的初始默认值。

   同样的情况也发生于测试内部新加载/声明的类的静态属性上。它们也无法在测试结束之后复原为声明时提供的原始默认值，因为无从得知这些默认值。这些被修改过的值会泄漏到后继测试中。

   对单元测试而言，推荐在 ``setUp()`` 中显式的重置测试中使用到的静态属性（最好同时在 ``tearDown()`` 中执行重置，这样就保证不会影响到后继的测试）。

可以提供名单来将静态属性从备份与还原操作中排除出去：

.. code-block:: php

    final class MyTest extends TestCase
    {
        protected $backupStaticAttributesExcludeList = [
            'className' => ['attributeName']
        ];

        // ...
    }

.. admonition:: 注

   在方法（例如 ``setUp()`` 方法）内对 ``$backupStaticAttributesExcludeList`` 属性进行设置是无效的。


