

.. _incomplete-and-skipped-tests:

============================
未完成的测试与跳过的测试
============================

.. _incomplete-and-skipped-tests.incomplete-tests:

未完成的测试
################

开始写新的测试用例类时，可能想从写下空测试方法开始，比如：

.. code-block:: php

    public function testSomething(): void
    {
    }

以此来跟踪需要编写的测试。空测试的问题是 PHPUnit 框架会将它们解读为成功。这种错误解读导致错误报告变得毫无用处——无法分辨出测试是真的成功了还是根本就未编写实现。在未实现的测试中调用 ``$this->fail()`` 同样没啥帮助，因为测试将被解读为失败。这和将未实现的测试解读为成功是一样的错误。

假如把成功的测试视为绿灯、测试失败视为红灯，那么还额外需要黄灯来将测试标记为未完成或尚未实现。``PHPUnit\Framework\IncompleteTest`` 是一个标记接口，用于将测试方法抛出的异常标记为测试未完成或目前尚未实现而导致的结果。``PHPUnit\Framework\IncompleteTestError`` 是这个接口的标准实现。

:numref:`incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php` 展示了一个测试用例类 ``SampleTest``，它有一个测试方法 ``testSomething()``。通过在测试方法中调用便捷方法 ``markTestIncomplete()``（会自动抛出一个 ``PHPUnit\Framework\IncompleteTestError`` 异常）将这个测试标记为未完成。

.. code-block:: php
    :caption: 将测试标记为不完整
    :name: incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SampleTest extends TestCase
    {
        public function testSomething(): void
        {
            // 可选：如果愿意，在这里随便测试点什么。
            $this->assertTrue(true, 'This should already work.');

            // 在这里停止，并将此测试标记为未完成。
            $this->markTestIncomplete(
              'This test has not been implemented yet.'
            );
        }
    }

在 PHPUnit 命令行测试执行器的输出中，未完成的测试记为 ``I``，如下例所示：

.. parsed-literal::

    $ phpunit --verbose SampleTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    I

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 incomplete test:

    1) SampleTest::testSomething
    This test has not been implemented yet.

    /home/sb/SampleTest.php:12
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 1, Incomplete: 1.

:numref:`incomplete-and-skipped-tests.incomplete-tests.tables.api` 列举了用于将测试标记为未完成的 API。

.. rst-class:: table
.. list-table:: 用于不完整的测试的 API
    :name: incomplete-and-skipped-tests.incomplete-tests.tables.api
    :header-rows: 1

    * - 方法
      - 含义
    * - ``void markTestIncomplete()``
      - 将当前测试标记为未完成。
    * - ``void markTestIncomplete(string $message)``
      - 将当前测试标记为未完成，并用 ``$message`` 作为说明信息。

.. _incomplete-and-skipped-tests.skipping-tests:

跳过测试
##############

并非所有测试都能在任何环境中运行。比如说，考虑这样一种情况：一个数据库抽象层，针对其所支持的各种数据库系统有多个不同的驱动程序。针对 MySQL 驱动程序的测试只在 MySQL 服务器可用才能运行。

:numref:`incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php` 展示了一个测试用例类 ``DatabaseTest``，它有一个测试方法 ``testConnection()``。在测试用例类的 ``setUp()`` 模板方法中，检查了 MySQLi 扩展是否可用，并且在扩展不可用时用 ``markTestSkipped()`` 方法来跳过此测试。

.. code-block:: php
    :caption: 跳过测试
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DatabaseTest extends TestCase
    {
        protected function setUp(): void
        {
            if (!extension_loaded('mysqli')) {
                $this->markTestSkipped(
                  'The MySQLi extension is not available.'
                );
            }
        }

        public function testConnection(): void
        {
            // ...
        }
    }

在 PHPUnit 命令行测试执行器的输出中，被跳过的测试记为 ``S``，如下例所示：

.. parsed-literal::

    $ phpunit --verbose DatabaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    S

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 skipped test:

    1) DatabaseTest::testConnection
    The MySQLi extension is not available.

    /home/sb/DatabaseTest.php:9
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 0, Skipped: 1.

:numref:`incomplete-and-skipped-tests.skipped-tests.tables.api` 列举了用于跳过测试的 API。

.. rst-class:: table
.. list-table:: 用于跳过测试的 API
    :name: incomplete-and-skipped-tests.skipped-tests.tables.api
    :header-rows: 1

    * - 方法
      - 含义
    * - ``void markTestSkipped()``
      - 将当前测试标记为已跳过。
    * - ``void markTestSkipped(string $message)``
      - 将当前测试标记为已跳过，并用 ``$message`` 作为说明信息。

.. _incomplete-and-skipped-tests.skipping-tests-using-requires:

用 @requires 来跳过测试
##############################

除了上述方法，还可以用 ``@requires`` 标注来表达测试用例的一些常见前提条件。

.. rst-class:: table
.. list-table:: 可能的 @requires 用法
    :name: incomplete-and-skipped-tests.requires.tables.api
    :header-rows: 1

    * - 类型
      - 可能值
      - 示例
      - 其他示例
    * - ``PHP``
      - 任意 PHP 版本号以及可选的运算符
      - @requires PHP 7.1.20
      - @requires PHP >= 7.2
    * - ``PHPUnit``
      - 任意 PHPUnit 版本号以及可选的运算符
      - @requires PHPUnit 7.3.1
      - @requires PHPUnit < 8
    * - ``OS``
      - 与 `PHP_OS <https://www.php.net/manual/en/reserved.constants.php#constant.php-os>`_ 匹配的正则表达式
      - @requires OS Linux
      - @requires OS WIN32|WINNT
    * - ``OSFAMILY``
      - 任意 `OS family <https://www.php.net/manual/en/reserved.constants.php#constant.php-os-family>`_
      - @requires OSFAMILY Solaris
      - @requires OSFAMILY Windows
    * - ``function``
      - 任意 `function_exists <https://www.php.net/function_exists>`_ 的有效参数
      - @requires function imap_open
      - @requires function ReflectionMethod::setAccessible
    * - ``extension``
      - 任意扩展名以及可选的版本号和可选的运算符
      - @requires extension mysqli
      - @requires extension redis >= 2.2.0

PHP、PHPUnit 和扩展的版本约束支持以下运算符：``<``、``<=``、``>``、``>=``、``=``、``==``、``!=``、``<>``。

版本是用 PHP 的 `version_compare <https://www.php.net/version_compare>`_ 函数进行比较的。除了其他事情之外，这意味着 ``=`` 和 ``==`` 运算符只能用于完整的 ``X.Y.Z`` 版本号，只用 ``X.Y`` 是不行的。

.. code-block:: php
    :caption: 用 @requires 跳过测试
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseClassSkippingTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @requires extension mysqli
     */
    final class DatabaseTest extends TestCase
    {
        /**
         * @requires PHP >= 5.3
         */
        public function testConnection(): void
        {
            // 测试需要 mysqli 扩展，并且要求 PHP >= 5.3
        }

        // ... 其他需要 mysqli 扩展的测试
    }

如果使用了某种在特定版本的 PHP 下无法编译的语法，请参考\ :ref:`appendixes.configuration.testsuites`\ 中 XML 配置信息里关于版本依赖的部分。


