

.. _code-coverage-analysis:

======================
代码覆盖率分析
======================

    *Wikipedia*\ ：

    计算机科学中所说的代码覆盖率是一种用于衡量特定测试套件对程序源代码测试程度的指标。拥有高代码覆盖率的程序相较于低代码低概率的程序而言测试的更加彻底、包含软件 bug 的可能性更低。

在本章中，你将学到 PHPUnit 中与代码覆盖率相关的一切功能。通过这部分功能，能够了解在测试运行过程中执行了生产代码的哪些部分。它使用了 `php-code-coverage <https://github.com/sebastianbergmann/php-code-coverage>`_ 组件，而这个组件又使用了 PHP 的 `Xdebug <https://xdebug.org/>`_ 或 `PCOV <https://github.com/krakjoe/pcov>`_ 扩展或  `PHPDBG <https://www.php.net/manual/en/book.phpdbg.php>`_ 所提供的代码覆盖率功能。

.. admonition:: 注

   如果在运行测试时看到警告信息说没有可用的代码覆盖率驱动，这意味着你正在使用 PHP 命令行二进制文件（``php``）且未加载 Xdebug 或 PCOV。

PHPUnit 可以生成基于 HTML 的代码覆盖率报告，同时也能生成好几种（Clover、Crap4J、PHPUnit）基于 XML 的代码覆盖率信息记录文件。代码覆盖率信息也能以文本格式提供（同时可以输出到 STDOUT）或以 PHP 代码格式输出以供进一步处理。

:ref:`textui`\ 中列出了各种控制代码覆盖率功能的命令行参数供参考，同时\ :ref:`appendixes.configuration.logging`\ 中可以找到其他相关的配置信息。

.. _code-coverage-analysis.metrics:

用于代码覆盖率的软件衡量标准
##################################

目前存在多种软件衡量标准用于衡量代码覆盖率：

*行覆盖率（Line Coverage）*

    *行覆盖率（Line Coverage）*\ 按单个可执行行是否已执行进行计量。

*分支覆盖率（Branch Coverage）*

    *分支覆盖率（Branch Coverage）*\ 按控制结构的分支进行计量。测试套件运行时每个控制结构的布尔表达式求值为 ``true`` 和 ``false`` 各自计为一个分支。

*路径覆盖率（Path Coverage）*

    *路径覆盖率（Path Coverage）*\ 按测试套件运行时函数或者方法内部所经历的执行路径进行计量。一个执行路径指的是从进入函数或方法一直到离开的过程中经过各个分支的特定序列。

*函数与方法覆盖率（Function and Method Coverage）*

    *函数与方法覆盖率（Function and Method Coverage）*\ 按单个函数或方法是否已调用进行计量。仅当函数或方法的所有可执行行全部已覆盖时 php-code-coverage 才将其视为已覆盖。

*类与特质覆盖率（Class and Trait Coverage）*

    *类与特质覆盖率（Class and Trait Coverage）*\ 按单个类或特质的所有方法是否全部已覆盖进行计量。仅当一个类或特质的所有方法全部已覆盖时 php-code-coverage 才将其视为已覆盖。

*变更风险反模式（CRAP）指数（Change Risk Anti-Patterns (CRAP) Index）*

    *变更风险反模式（CRAP）指数（Change Risk Anti-Patterns (CRAP) Index）*\ 是基于代码单元的圈复杂度（cyclomatic complexity）与代码覆盖率计算得出的。不太复杂并具有恰当测试覆盖率的代码将得出较低的 CRAP 指数。可以通过编写测试或重构代码来降低其复杂性的方式来降低 CRAP 指数。

.. _code-coverage-analysis.including-files:

包含文件
###############

为了告诉 PHPUnit 哪些源代码文件要包含在代码覆盖率报告中，必须配置过滤器。可以用\ :ref:`命令行 <textui.clioptions>`\ 选项 ``--coverage-filter`` 或通过配置文件（参见\ :ref:`appendixes.configuration.coverage.include`）来完成。

``includeUncoveredFilesInCodeCoverageReport`` 和 ``processUncoveredFilesForCodeCoverageReport`` 配置设置可用于配置过滤器的使用方式：

- ``includeUncoveredFilesInCodeCoverageReport="false"`` 意味着只有至少有一行已执行代码的文件才会包括在代码覆盖率报告中

- ``includeUncoveredFilesInCodeCoverageReport="true"``\ （默认值）意味着所有文件都会包括在代码覆盖率报告中，即使文件中没有任何一行代码被执行过也一样

- ``processUncoveredFilesForCodeCoverageReport="false"``\ （默认值）意味着没有已执行代码行的文件会被加入到代码覆盖率报告中（如果设置了 ``includeUncoveredFilesInCodeCoverageReport="true"``），但它并不会被 PHPUnit 加载，因此也不会对其进行分析来获取正确的可执行代码行信息

- ``processUncoveredFilesForCodeCoverageReport="true"`` 意味着没有已执行代码行的文件会被 PHPUnit 加载，从而也能对其进行分析来获取正确的可执行代码行信息

.. admonition:: 注

   请注意，当设置了 ``processUncoveredFilesForCodeCoverageReport="true"`` 时将对源代码文件进行载入，这在某些情况下可能导致问题，比如，源代码文件包含有处于类或者函数作用域之外的代码。

.. _code-coverage-analysis.ignoring-code-blocks:

忽略代码块
####################

有时，一些代码块是无法对其进行测试的，因此希望在代码覆盖率分析中忽略它们。在 PHPUnit 中可以用 ``@codeCoverageIgnore``、``@codeCoverageIgnoreStart`` 与 ``@codeCoverageIgnoreEnd`` 标注来做到这点，如\ :numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php` 中所示。

.. code-block:: php
    :caption: 使用 ``@codeCoverageIgnore``、``@codeCoverageIgnoreStart`` 和 ``@codeCoverageIgnoreEnd`` 标注
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    final class Foo
    {
        public function bar(): void
        {
        }
    }

    final class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo(): void
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore

代码中被忽略掉的行（用标注标记为忽略）将会计为已执行（如果它们是可执行的），并且不会在代码覆盖情况中被高亮标记。

.. _code-coverage-analysis.specifying-covered-parts:

指明覆盖的代码部分
#############################

``@covers`` 标注（参见\ :ref:`annotation documentation <appendixes.annotations.covers.tables.annotations>`\ ）可以用在测试代码中来指明测试类（或测试方法）想要对哪些代码部分进行测试。如果提供了这个信息，则可以有效过滤代码覆盖率报告，仅包含所指定的代码部分中的已执行代码。:numref:`code-coverage-analysis.specifying-covered-parts.examples.InvoiceTest.php` 展示了一个例子。


.. admonition:: 注

    如果用 ``@covers`` 标注指定了一个方法吗，那么只有所指方法会被视为已覆盖，这个方法所调用的方法不会视为已覆盖。因此，如果用\ *提取方法*\ 重构了已覆盖的方法，则需要添加相应的 ``@covers`` 标注。这就是推荐将此标注用在类作用域而非方法作用域的原因。

.. code-block:: php
    :caption: 指明了要覆盖的类的测试类
    :name: code-coverage-analysis.specifying-covered-parts.examples.InvoiceTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @covers \Invoice
     * @uses \Money
     */
    final class InvoiceTest extends TestCase
    {
        private $invoice;

        protected function setUp(): void
        {
            $this->invoice = new Invoice;
        }

        public function testAmountInitiallyIsEmpty(): void
        {
            $this->assertEquals(new Money, $this->invoice->getAmount());
        }
    }

.. code-block:: php
    :caption: 指明了要覆盖哪个方法的测试
    :name: code-coverage-analysis.specifying-covered-parts.examples.BankAccountTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class BankAccountTest extends TestCase
    {
        private $ba;

        protected function setUp(): void
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers \BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero(): void
        {
            $this->assertSame(0, $this->ba->getBalance());
        }

        /**
         * @covers \BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative(): void
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers \BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2(): void
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers \BankAccount::getBalance
         * @covers \BankAccount::depositMoney
         * @covers \BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney(): void
        {
            $this->assertSame(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertSame(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

同时，可以用 ``@coversNothing`` 标注来指明一个测试不覆盖\ *任何*\ 方法（参见\ :ref:`appendixes.annotations.coversNothing`）。这可以在编写集成测试时用于确保代码覆盖全部来自单元测试。

.. code-block:: php
    :caption: 指明应当不覆盖任何方法的测试
    :name: code-coverage-analysis.specifying-covered-parts.examples.GuestbookIntegrationTest.php

    <?php declare(strict_types=1);
    use PHPUnit\DbUnit\TestCase

    final class GuestbookIntegrationTest extends TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry(): void
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

.. _code-coverage-analysis.edge-cases:

边缘情况
##########

本节中展示了一些值得注意的边缘情况，在这些边缘情况中可能出现令人迷惑的代码覆盖率信息。

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    // 因为是“基于行”的而非基于语句的覆盖率
    // 一行始终只能有一种覆盖状态
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
