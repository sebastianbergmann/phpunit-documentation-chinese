

.. _writing-tests-for-phpunit:

=============
编写 PHPUnit 测试
=============

:numref:`writing-tests-for-phpunit.examples.StackTest.php`展示了如何用 PHPUnit 编写测试来对 PHP 数组操作进行测试。本例介绍了用 PHPUnit 编写测试的基本惯例与步骤：

#.

   针对类 ``Class`` 的测试写在类 ``ClassTest``中。

#.

   ``ClassTest``（通常）继承自 ``PHPUnit\Framework\TestCase``。

#.

   测试都是命名为 ``test*`` 的公用方法。

   也可以在方法的文档注释块(docblock)中使用 ``@test`` 标注将其标记为测试方法。

#.

   在测试方法内，类似于 ``assertEquals()``（参见 :ref:`appendixes.assertions`）这样的断言方法用来对实际值与预期值的匹配做出断言。

.. code-block:: php
    :caption: 用 PHPUnit 测试数组操作
    :name: writing-tests-for-phpunit.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testPushAndPop()
        {
            $stack = [];
            $this->assertEquals(0, count($stack));

            array_push($stack, 'foo');
            $this->assertEquals('foo', $stack[count($stack)-1]);
            $this->assertEquals(1, count($stack));

            $this->assertEquals('foo', array_pop($stack));
            $this->assertEquals(0, count($stack));
        }
    }
    ?>

    *Martin Fowler*:

    当你想把一些东西写到 ``print`` 语句或者调试表达式中时，别这么做，将其写成一个测试来代替。

.. _writing-tests-for-phpunit.test-dependencies:

测试的依赖关系
#######

    *Adrian Kuhn et. al.*:

    单元测试主要是作为一种良好实践来编写的，它能帮助开发人员识别并修复 bug、重构代码，还可以看作被测软件单元的文档。要实现这些好处，理想的单元测试应当覆盖程序中所有可能的路径。一个单元测试通常覆盖一个函数或方法中的一个特定路径。但是，测试方法并不一定非要是一个封装良好的独立实体。测试方法之间经常有隐含的依赖关系暗藏在测试的实现方案中。

PHPUnit支持对测试方法之间的显式依赖关系进行声明。这种依赖关系并不是定义在测试方法的执行顺序中，而是允许生产者(producer)返回一个测试基境(fixture)的实例，并将此实例传递给依赖于它的消费者(consumer)们。

-

  生产者(producer)，是能生成被测单元并将其作为返回值的测试方法。

-

  消费者(consumer)，是依赖于一个或多个生产者及其返回值的测试方法。

:numref:`writing-tests-for-phpunit.examples.StackTest2.php`展示了如何用 ``@depends`` 标注来表达测试方法之间的依赖关系。

.. code-block:: php
    :caption: 用 ``@depends`` 标注来表达依赖关系
    :name: writing-tests-for-phpunit.examples.StackTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        public function testEmpty()
        {
            $stack = [];
            $this->assertEmpty($stack);

            return $stack;
        }

        /**
         * @depends testEmpty
         */
        public function testPush(array $stack)
        {
            array_push($stack, 'foo');
            $this->assertEquals('foo', $stack[count($stack)-1]);
            $this->assertNotEmpty($stack);

            return $stack;
        }

        /**
         * @depends testPush
         */
        public function testPop(array $stack)
        {
            $this->assertEquals('foo', array_pop($stack));
            $this->assertEmpty($stack);
        }
    }
    ?>

在上例中，第一个测试， ``testEmpty()``，创建了一个新数组，并断言其为空。随后，此测试将此基境作为结果返回。第二个测试，``testPush()``，依赖于 ``testEmpty()`` ，并将所依赖的测试之结果作为参数传入。最后，``testPop()`` 依赖于 ``testPush()``。

.. admonition:: Note

   默认情况下，生产者所产生的返回值将“原样”传递给相应的消费者。这意味着，如果生产者返回的是一个对象，那么传递给消费者的将是一个指向此对象的引用。如果需要传递对象的副本而非引用，则应当用 @depends clone 替代 @depends。

为了快速定位缺陷，我们希望把注意力集中于相关的失败测试上。这就是为什么当某个测试所依赖的测试失败时，PHPUnit 会跳过这个测试。通过利用测试之间的依赖关系，缺陷定位得到了改进，如:numref:`writing-tests-for-phpunit.examples.DependencyFailureTest.php`中所示。

.. code-block:: php
    :caption: 利用测试之间的依赖关系
    :name: writing-tests-for-phpunit.examples.DependencyFailureTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyFailureTest extends TestCase
    {
        public function testOne()
        {
            $this->assertTrue(false);
        }

        /**
         * @depends testOne
         */
        public function testTwo()
        {
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose DependencyFailureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FS

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) DependencyFailureTest::testOne
    Failed asserting that false is true.

    /home/sb/DependencyFailureTest.php:6

    There was 1 skipped test:

    1) DependencyFailureTest::testTwo
    This test depends on "DependencyFailureTest::testOne" to pass.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

测试可以使用多个 ``@depends`` 标注。PHPUnit 不会更改测试的运行顺序，因此你需要自行保证某个测试所依赖的所有测试均出现于这个测试之前。

拥有多个 ``@depends`` 标注的测试，其第一个参数是第一个生产者提供的基境，第二个参数是第二个生产者提供的基境，以此类推。参见:numref:`writing-tests-for-phpunit.examples.MultipleDependencies.php`

.. code-block:: php
    :caption: 有多重依赖的测试
    :name: writing-tests-for-phpunit.examples.MultipleDependencies.php

    <?php
    use PHPUnit\Framework\TestCase;

    class MultipleDependenciesTest extends TestCase
    {
        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         */
        public function testConsumer()
        {
            $this->assertEquals(
                ['first', 'second'],
                func_get_args()
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose MultipleDependenciesTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...

    Time: 0 seconds, Memory: 3.25Mb

    OK (3 tests, 3 assertions)

.. _writing-tests-for-phpunit.data-providers:

数据供给器
#####

测试方法可以接受任意参数。这些参数由数据供给器方法（在 :numref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`中，是 ``additionProvider()`` 方法）提供。用 ``@dataProvider`` 标注来指定使用哪个数据供给器方法。

数据供给器方法必须声明为 ``public``，其返回值要么是一个数组，其每个元素也是数组；要么是一个实现了 ``Iterator`` 接口的对象，在对它进行迭代时每步产生一个数组。每个数组都是测试数据集的一部分，将以它的内容作为参数来调用测试方法。

.. code-block:: php
    :caption: 使用返回数组的数组的数据供给器
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                [0, 0, 0],
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 matches expected 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

当使用到大量数据集时，最好逐个用字符串键名对其命名，避免用默认的数字键名。这样输出信息会更加详细些，其中将包含打断测试的数据集所对应的名称。

.. code-block:: php
    :caption: 使用带有命名数据集的数据供给器
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest1.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return [
                'adding zeros'  => [0, 0, 0],
                'zero plus one' => [0, 1, 1],
                'one plus zero' => [1, 0, 1],
                'one plus one'  => [1, 1, 3]
            ];
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set "one plus one" (1, 1, 3)
    Failed asserting that 2 matches expected 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: 使用返回迭代器对象的数据供给器
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    require 'CsvFileIterator.php';

    class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd($a, $b, $expected)
        {
            $this->assertEquals($expected, $a + $b);
        }

        public function additionProvider()
        {
            return new CsvFileIterator('data.csv');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit DataTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 ('1', '1', '3')
    Failed asserting that 2 matches expected '3'.

    /home/sb/DataTest.php:11

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: CsvFileIterator 类
    :name: writing-tests-for-phpunit.data-providers.examples.CsvFileIterator.php

    <?php
    use PHPUnit\Framework\TestCase;

    class CsvFileIterator implements Iterator {
        protected $file;
        protected $key = 0;
        protected $current;

        public function __construct($file) {
            $this->file = fopen($file, 'r');
        }

        public function __destruct() {
            fclose($this->file);
        }

        public function rewind() {
            rewind($this->file);
            $this->current = fgetcsv($this->file);
            $this->key = 0;
        }

        public function valid() {
            return !feof($this->file);
        }

        public function key() {
            return $this->key;
        }

        public function current() {
            return $this->current;
        }

        public function next() {
            $this->current = fgetcsv($this->file);
            $this->key++;
        }
    }
    ?>

如果测试同时从 ``@dataProvider`` 方法和一个或多个 ``@depends`` 测试接收数据，那么来自于数据供给器的参数将先于来自所依赖的测试的。来自于所依赖的测试的参数对于每个数据集都是一样的。参见:numref:`writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php`

.. code-block:: php
    :caption: 在同一个测试中组合使用 @depends 和 @dataProvider
    :name: writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DependencyAndDataProviderComboTest extends TestCase
    {
        public function provider()
        {
            return [['provider1'], ['provider2']];
        }

        public function testProducerFirst()
        {
            $this->assertTrue(true);
            return 'first';
        }

        public function testProducerSecond()
        {
            $this->assertTrue(true);
            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         * @dataProvider provider
         */
        public function testConsumer()
        {
            $this->assertEquals(
                ['provider1', 'first', 'second'],
                func_get_args()
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit --verbose DependencyAndDataProviderComboTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 3.50Mb

    There was 1 failure:

    1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
    Array (
    -    0 => 'provider1'
    +    0 => 'provider2'
    1 => 'first'
    2 => 'second'
    )

    /home/sb/DependencyAndDataProviderComboTest.php:31

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. admonition:: Note

   如果一个测试依赖于另外一个使用了数据供给器的测试，仅当被依赖的测试至少能在一组数据上成功时，依赖于它的测试才会运行。使用了数据供给器的测试，其运行结果是无法注入到依赖于此测试的其他测试中的。

.. admonition:: Note

   所有的数据供给器方法的执行都是在对 ``setUpBeforeClass`` 静态方法的调用和第一次对 ``setUp`` 方法的调用之前完成的。因此，无法在数据供给器中使用创建于这两个方法内的变量。这是必须的，这样 PHPUnit 才能计算测试的总数量。

.. _writing-tests-for-phpunit.exceptions:

对异常进行测试
#######

:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`展示了如何用 ``@expectException`` 标注来测试被测代码中是否抛出了异常。

.. code-block:: php
    :caption: 使用 expectException() 方法
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        public function testException()
        {
            $this->expectException(InvalidArgumentException::class);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Expected exception InvalidArgumentException

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

除了 ``expectException()`` 方法外，还有 ``expectExceptionCode()``、``expectExceptionMessage()`` 和 ``expectExceptionMessageRegExp()`` 方法可以用于为被测代码所抛出的异常建立预期。

或者，也可以用 ``@expectedException``、``@expectedExceptionCode``、``@expectedExceptionMessage`` 和 ``@expectedExceptionMessageRegExp`` 标注来为被测代码所抛出的异常建立预期。:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php`展示了一个范例。

.. code-block:: php
    :caption: 使用 @expectedException 标注
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExceptionTest extends TestCase
    {
        /**
         * @expectedException InvalidArgumentException
         */
        public function testException()
        {
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ExceptionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Expected exception InvalidArgumentException

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.errors:

对 PHP 错误进行测试
############

默认情况下，PHPUnit 将测试在执行中触发的 PHP 错误、警告、通知都转换为异常。利用这些异常，就可以，比如说，预期测试将触发 PHP 错误，如:numref:`writing-tests-for-phpunit.exceptions.examples.ErrorTest.php`所示。

.. admonition:: Note

   PHP 的 ``error_reporting`` 运行时配置会对 PHPUnit 将哪些错误转换为异常有所限制。如果在这个特性上碰到问题，请确认 PHP 的配置中没有抑制想要测试的错误类型。

.. code-block:: php
    :caption: 用 @expectedException 来预期 PHP 错误
    :name: writing-tests-for-phpunit.exceptions.examples.ErrorTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExpectedErrorTest extends TestCase
    {
        /**
         * @expectedException PHPUnit\Framework\Error
         */
        public function testFailingInclude()
        {
            include 'not_existing_file.php';
        }
    }
    ?>

.. code-block:: bash

    $ phpunit -d error_reporting=2 ExpectedErrorTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .

    Time: 0 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

``PHPUnit\Framework\Error\Notice`` 和 ``PHPUnit\Framework\Error\Warning`` 分别代表 PHP 通知与 PHP 警告。

.. admonition:: Note

   对异常进行测试是越明确越好的。对太笼统的类进行测试有可能导致不良副作用。因此，不再允许用 ``@expectedException`` 或 ``setExpectedException()`` 对 ``Exception`` 类进行测试。

如果测试依靠会触发错误的 PHP 函数，例如 ``fopen`` ，有时候在测试中使用错误抑制符会很有用。通过抑制住错误通知，就能对返回值进行检查，否则错误通知将会导致抛出 ``PHPUnit\Framework\Error\Notice``。
.. code-block:: php
    :caption: 对会引发PHP 错误的代码的返回值进行测试
    :name: writing-tests-for-phpunit.exceptions.examples.TriggerErrorReturnValue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ErrorSuppressionTest extends TestCase
    {
        public function testFileWriting() {
            $writer = new FileWriter;
            $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
        }
    }
    class FileWriter
    {
        public function write($file, $content) {
            $file = fopen($file, 'w');
            if($file == false) {
                return false;
            }
            // ...
        }
    }

    ?>

.. code-block:: bash

    $ phpunit ErrorSuppressionTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .

    Time: 1 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

如果不使用错误抑制符，此测试将会失败，并报告 ``fopen(/is-not-writeable/file): failed to open stream: No such file or directory``。

.. _writing-tests-for-phpunit.output:

对输出进行测试
#######

有时候，想要断言（比如说）某方法的运行过程中生成了预期的输出（例如，通过 ``echo`` 或 ``print``）。``PHPUnit\Framework\TestCase`` 类使用 PHP 的 `输出缓冲 <http://www.php.net/manual/en/ref.outcontrol.php>`_ 特性来为此提供必要的功能支持。

:numref:`writing-tests-for-phpunit.output.examples.OutputTest.php`展示了如何用 ``expectOutputString()`` 方法来设定所预期的输出。如果没有产生预期的输出，测试将计为失败。

.. code-block:: php
    :caption: 对函数或方法的输出进行测试
    :name: writing-tests-for-phpunit.output.examples.OutputTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class OutputTest extends TestCase
    {
        public function testExpectFooActualFoo()
        {
            $this->expectOutputString('foo');
            print 'foo';
        }

        public function testExpectBarActualBaz()
        {
            $this->expectOutputString('bar');
            print 'baz';
        }
    }
    ?>

.. code-block:: bash

    $ phpunit OutputTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) OutputTest::testExpectBarActualBaz
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

:numref:`writing-tests-for-phpunit.output.tables.api`中列举了用于对输出进行测试的各种方法。

.. rst-class:: table
.. list-table:: 用于对输出进行测试的方法
    :name: writing-tests-for-phpunit.output.tables.api
    :header-rows: 1

    * - 方法
      - 含义
    * - ``void expectOutputRegex(string $regularExpression)``
      - 设置输出预期为输出应当匹配正则表达式 ``$regularExpression``。
    * - ``void expectOutputString(string $expectedString)``
      - 设置输出预期为输出应当与 ``$expectedString`` 字符串相等。
    * - ``bool setOutputCallback(callable $callback)``
      - 设置回调函数，用来做诸如将实际输出规范化之类的动作。
    * - ``string getActualOutput()``
      - 获取实际输出。

.. admonition:: Note

   在严格模式下，本身产生输出的测试将会失败。

.. _writing-tests-for-phpunit.error-output:

错误相关信息的输出
#########

当有测试失败时，PHPUnit 全力提供尽可能多的有助于找出问题所在的上下文信息。

.. code-block:: php
    :caption: 数组比较失败时生成的错误相关信息输出
    :name: writing-tests-for-phpunit.error-output.examples.ArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayDiffTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [1, 2,  3, 4, 5, 6],
                [1, 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayDiffTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayDiffTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 1
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

在这个例子中，数组中只有一个值不同，但其他值也都同时显示出来，以提供关于错误发生的位置的上下文信息。

当生成的输出很长而难以阅读时，PHPUnit 将对其进行分割，并在每个差异附近提供少数几行上下文信息。

.. code-block:: php
    :caption: 长数组比较失败时生成的错误相关信息输出
    :name: writing-tests-for-phpunit.error-output.examples.LongArrayDiffTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class LongArrayDiffTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2,  3, 4, 5, 6],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LongArrayDiffTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LongArrayDiffTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
         13 => 2
    -    14 => 3
    +    14 => 33
         15 => 4
         16 => 5
         17 => 6
     )

    /home/sb/LongArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.error-output.edge-cases:

边缘情况
====

当比较失败时，PHPUnit 为输入值建立文本表示，然后以此进行对比。这种实现导致在差异指示中显示出来的问题可能比实际上存在的多。

这种情况只出现在对数组或者对象使用 assertEquals 或其他“弱”比较函数时。

.. code-block:: php
    :caption: 当使用弱比较时在生成的差异结果中出现的边缘情况
    :name: writing-tests-for-phpunit.error-output.edge-cases.examples.ArrayWeakComparisonTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayWeakComparisonTest extends TestCase
    {
        public function testEquality() {
            $this->assertEquals(
                [1, 2, 3, 4, 5, 6],
                ['1', 2, 33, 4, 5, 6]
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ArrayWeakComparisonTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayWeakComparisonTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    +    0 => '1'
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayWeakComparisonTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

在这个例子中，第一个索引项中的 ``1`` and ``'1'`` 在报告中被视为不同，虽然 assertEquals 认为这两个值是匹配的。


