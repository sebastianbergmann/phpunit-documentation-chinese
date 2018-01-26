

.. _code-coverage-analysis:

=======
代码覆盖率分析
=======

    *Wikipedia*:

    计算机科学中所说的代码覆盖率是一种用于衡量特定测试套件对程序源代码测试程度的指标。拥有高代码覆盖率的程序相较于低代码低概率的程序而言测试的更加彻底、包含软件 bug 的可能性更低。

在本章中，你将学到 PHPUnit 中与代码覆盖率相关的一切功能。通过这部分功能，能够了解在测试运行过程中执行了生产代码的哪些部分。它使用了 `PHP_CodeCoverage <https://github.com/sebastianbergmann/php-code-coverage>`_ 组件，而这个组件又使用了 PHP 的 `Xdebug <http://xdebug.org/>`_ 扩展所提供的代码覆盖率功能。

.. admonition:: Note

   Xdebug 不随 PHPUnit 分发。如果在运行测试时收到了 Xdebug 扩展未加载的提示，就意味着 Xdebug 未安装或者未正确配置。在使用 PHPUnit 的代码覆盖率分析功能之前，需要阅读下 `Xdebug 安装指南 <http://xdebug.org/docs/install>`_。

PHPUnit 可以生成基于 HTML 的代码覆盖率报告，同时也能生成好几种（Clover、Crap4J、PHPUnit）基于XML的代码覆盖率信息记录文件。代码覆盖率信息也能以文本格式提供（同时可以输出到STDOUT）或以PHP代码格式输出以供进一步处理。

:ref:`textui`中列出了各种控制代码覆盖率功能的命令行参数供参考，同时:ref:`appendixes.configuration.logging`中可以找到其他相关的配置信息。

.. _code-coverage-analysis.metrics:

用于代码覆盖率的软件衡量标准
##############

目前存在多种软件衡量标准用于衡量代码覆盖率：

*行覆盖率(Line Coverage)*

    *行覆盖率(Line Coverage)*按单个可执行行是否已执行到进行计量。

*函数与方法覆盖率(Function and Method Coverage)*

    *函数与方法覆盖率(Function and Method Coverage)*按单个函数或方法是否已调用到进行计量。仅当函数或方法的所有可执行行全部已覆盖时 PHP_CodeCoverage 才将其视为已覆盖。

*类与特质覆盖率(Class and Trait Coverage)*

    *类与特质覆盖率(Class and Trait Coverage)*按单个类或特质的所有方法是否全部已覆盖进行计量。仅当一个类或性状的所有方法全部已覆盖时 PHP_CodeCoverage 才将其视为已覆盖。

*Opcode 覆盖率(Opcode Coverage)*

    *Opcode 覆盖率*按函数或方法对应的每条 opcode 在运行测试套件时是否执行到进行计量。一行（PHP的）代码通常会编译得到多条 opcode。进行行覆盖率计量时，只要其中任何一条 opcode 被执行就视为此行已覆盖。

*分支覆盖率(Branch Coverage)*

    *分支覆盖率(Branch Coverage)*按控制结构的分支进行计量。测试套件运行时每个控制结构的布尔表达式求值为 ``true`` 和 ``false`` 各自计为一个分支。

*路径覆盖率(Path Coverage)*

    *路径覆盖率(Path Coverage)*按测试套件运行时函数或者方法内部所经历的执行路径进行计量。一个执行路径指的是从进入函数或方法一直到离开的过程中经过各个分支的特定序列。

*变更风险反模式(CRAP)指数(Change Risk Anti-Patterns (CRAP) Index)*

    *变更风险反模式(CRAP)指数(Change Risk Anti-Patterns (CRAP) Index)*是基于代码单元的圈复杂度(cyclomatic complexity)与代码覆盖率计算得出的。不太复杂并具有恰当测试覆盖率的代码将得出较低的CRAP指数。可以通过编写测试或重构代码来降低其复杂性的方式来降低CRAP指数。

.. admonition:: Note

   目前 PHP_CodeCoverage 尚不支持 *Opcode覆盖率*、*分支覆盖率* 及 *路径覆盖率*。

.. _code-coverage-analysis.whitelisting-files:

将文件列入白名单
########

为了告诉 PHPUnit 哪些源代码文件要包含在代码覆盖率报告中，必须配置*白名单*。可以用命令行选项 ``--whitelist`` 或通过配置文件（参见 :ref:`appendixes.configuration.whitelisting-files`）来完成。

可以在 PHPUnit 的配置信息中设置 ``addUncoveredFilesFromWhitelist="true"`` 来将白名单中包含的所有文件全部加入到代码覆盖率报告中（参见:ref:`appendixes.configuration.whitelisting-files`）。这样可以把完全没有测试到的文件也一并包含到报告中。如果需要知道这些未被覆盖文件中有哪些行是可执行的，需要同时在 PHPUnit 的配置信息中设置 ``processUncoveredFilesFromWhitelist="true"``（参见:ref:`appendixes.configuration.whitelisting-files`）。

.. admonition:: Note

   请注意，当设置了 ``processUncoveredFilesFromWhitelist="true"`` 时将对源代码文件进行载入，这在某些情况下可能导致问题，比如，源代码文件包含有处于类或者函数作用域之外的代码。

.. _code-coverage-analysis.ignoring-code-blocks:

略过代码块
#####

有时，一些代码块是无法对其进行测试的，因此希望在代码覆盖率分析中忽略它们。在 PHPUnit 中可以用 ``@codeCoverageIgnore``、``@codeCoverageIgnoreStart`` 与 ``@codeCoverageIgnoreEnd`` 标注来做到这点，如:numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php`中所示。

.. code-block:: php
    :caption: 使用 ``@codeCoverageIgnore``、``@codeCoverageIgnoreStart`` 与 ``@codeCoverageIgnoreEnd`` 标注
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    class Foo
    {
        public function bar()
        {
        }
    }

    class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo()
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore
    ?>

代码中被忽略掉的行（用标注标记为忽略）将会计为已执行（如果它们是可执行的），并且不会在代码覆盖情况中被高亮标记。

.. _code-coverage-analysis.specifying-covered-methods:

指明要覆盖的方法
########

``@covers`` 标注（参见 :ref:`appendixes.annotations.covers.tables.annotations`）可以用在测试代码中来指明测试方法想要对哪些方法进行测试。如果提供了这个信息，则只有指定方法的代码覆盖率信息会被统计。 :numref:`code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php`展示了一个例子。

.. code-block:: php
    :caption: 在测试中指明欲覆盖哪些方法
    :name: code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class BankAccountTest extends TestCase
    {
        protected $ba;

        protected function setUp()
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero()
        {
            $this->assertEquals(0, $this->ba->getBalance());
        }

        /**
         * @covers BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative()
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertEquals(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2()
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertEquals(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::getBalance
         * @covers BankAccount::depositMoney
         * @covers BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney()
        {
            $this->assertEquals(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertEquals(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertEquals(0, $this->ba->getBalance());
        }
    }
    ?>

同时，可以用 ``@coversNothing`` 标注来指明一个测试不覆盖*任何*方法（参见:ref:`appendixes.annotations.coversNothing`）。这可以在编写集成测试时用于确保代码覆盖全部来自单元测试。

.. code-block:: php
    :caption: 指明测试不欲覆盖任何方法
    :name: code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class GuestbookIntegrationTest extends PHPUnit_Extensions_Database_TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );

            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");

            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _code-coverage-analysis.edge-cases:

边缘情况
####

本节中展示了一些值得注意的边缘情况，在这些边缘情况中可能出现令人迷惑的代码覆盖率信息。

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    // 因为覆盖率是“基于行”而不是基于语句的，
    // 每行只会有一种覆盖状态
    if (false) this_function_call_shows_up_as_covered();

    // 由于代码覆盖率的内部工作方式，这两行显得很特殊。
    // 这一行会显示为非可执行
    if (false)
        // 这一行会显示为已覆盖，
        // 实际上是上一行的 if 语句的覆盖信息显示在这了！
        will_also_show_up_as_covered();

    // 要避免这种情况，必须使用大括号
    if (false) {
        this_call_will_never_show_up_as_covered();
    }
    ?>


