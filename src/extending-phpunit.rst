

.. _extending-phpunit:

==========
扩展 PHPUnit
==========

可以用多种方式对 PHPUnit 进行扩展，使编写测试更容易，以及对运行测试所得到的反馈进行定制。扩展 PHPUnit 时，一般从这些点入手：

.. _extending-phpunit.PHPUnit_Framework_TestCase:

PHPUnit\\Framework\\TestCase 的子类
################################

将自定义的断言和工具方法写在 ``PHPUnit\Framework\TestCase`` 的一个抽象子类中，然后从这个抽象子类派生你的测试用例类。这是扩展 PHPUnit 的最容易的方法。

.. _extending-phpunit.custom-assertions:

编写自定义断言
#######

编写自定义断言时，最佳实践是遵循 PHPUnit 自有断言的实现方式。正如 :numref:`extending-phpunit.examples.Assert.php`中所示，``assertTrue()`` 方法只是对 ``isTrue()`` 和 ``assertThat()`` 方法的外包覆：``isTrue()`` 创建了一个匹配器对象，将其传递给 ``assertThat()`` 进行评定。

.. code-block:: php
    :caption: PHPUnit_Framework_Assert 类的 assertTrue() 与 isTrue() 方法
    :name: extending-phpunit.examples.Assert.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class PHPUnit_Framework_Assert
    {
        // ...

        /**
         * 断言某个条件为真。
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit_Framework_AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * 返回一个 PHPUnit_Framework_Constraint_IsTrue 匹配器对象
         *
         * @return PHPUnit_Framework_Constraint_IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit_Framework_Constraint_IsTrue;
        }

        // ...
    }?>

:numref:`extending-phpunit.examples.IsTrue.php`展示了 ``PHPUnit_Framework_Constraint_IsTrue`` 是如何扩展针对匹配器对象（或约束）的抽象基类 ``PHPUnit_Framework_Constraint`` 的。

.. code-block:: php
    :caption: PHPUnit_Framework_Constraint_IsTrue  类
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class PHPUnit_Framework_Constraint_IsTrue extends PHPUnit_Framework_Constraint
    {
        /**
         * 对参数 $other 进行约束评定。如果符合约束，
         * 返回 TRUE，否则返回 FALSE。
         *
         * @param mixed $other Value or object to evaluate.
         * @return bool
         */
        public function matches($other)
        {
            return $other === true;
        }

        /**
         * 返回代表此约束的字符串。
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }?>

在实现 ``assertTrue()`` 和 ``isTrue()`` 方法及 ``PHPUnit_Framework_Constraint_IsTrue`` 类时所付出的努力带来了一些好处，``assertThat()`` 能够自动负责起断言的评定与任务簿记（例如为了统计目的而对其进行计数）工作。此外， ``isTrue()`` 方法还可以在配置仿件对象时用来作为匹配器。

.. _extending-phpunit.PHPUnit_Framework_TestListener:

实现 PHPUnit\\Framework\\TestListener
###################################

:numref:`extending-phpunit.examples.SimpleTestListener.php`展示了 ``PHPUnit\Framework\TestListener`` 接口的一个简单实现。

.. code-block:: php
    :caption: 简单的测试监听器
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Error while running test '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit_Framework_Test $test, PHPUnit_Framework_AssertionFailedError $e, $time)
        {
            printf("Test '%s' failed.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is incomplete.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is deemed risky.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' has been skipped.\n", $test->getName());
        }

        public function startTest(PHPUnit_Framework_Test $test)
        {
            printf("Test '%s' started.\n", $test->getName());
        }

        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' started.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' ended.\n", $suite->getName());
        }
    }
    ?>

:numref:`extending-phpunit.examples.BaseTestListener.php`展示了如何从抽象类 ``PHPUnit_Framework_BaseTestListener`` 派生子类，这个抽象类为所有接口方法提供了空白实现，这样你就只需要指定那些在你的使用情境下有意义的接口方法。

.. code-block:: php
    :caption: 使用测试监听器基类
    :name: extending-phpunit.examples.BaseTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ShortTestListener extends PHPUnit_Framework_BaseTestListener
    {
        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }
    }
    ?>

在:ref:`appendixes.configuration.test-listeners`中可以看到如何配置 PHPUnit 来将测试监听器附加到测试执行过程上。

.. _extending-phpunit.PHPUnit_Framework_Test:

实现 PHPUnit_Framework_Test
#########################

``PHPUnit_Framework_Test`` 接口是比较狭义的，十分容易实现。举例来说，你可以自行为 ``PHPUnit_Framework_Test`` 编写一个类似于 ``PHPUnit\Framework\TestCase`` 的实现来运行*数据驱动测试*。

:numref:`extending-phpunit.examples.DataDrivenTest.php`展示了一个数据驱动的测试用例类，对来自 CSV 文件内的值进行比较。这个文件内的每个行看起来类似于 ``foo;bar``，第一个值是期望值，第二个值则是实际值。

.. code-block:: php
    :caption: 一个数据驱动的测试
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit_Framework_Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit_Framework_TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit_Framework_TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit_Framework_Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit_Framework_AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === null) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit_TextUI_TestRunner::run($test);
    ?>

.. code-block:: bash

    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.


