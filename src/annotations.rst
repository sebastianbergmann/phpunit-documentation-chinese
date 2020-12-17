

.. _appendixes.annotations:

===========
标注
===========

所谓标注，是指某些编程语言中允许加在源代码中的一种特殊格式的语法元数据。PHP 并没有专门的语言特性来支持对源代码进行标注，然而 PHP 社区早已经形成惯例，通过在文档注释块中使用诸如 ``@annotation arguments`` 这样的标签来为源代码加上标注。在 PHP 中，文档注释块是可反射的：可以对函数、方法、类以及属性调用相应级别的反射 API ``getDocComment()`` 方法来获取相应的文档注释块。诸如 PHPUnit 这样的应用程序在运行时用这些信息来配置其行为。


.. admonition:: 注

   PHP中的文档注释块必须以 ``/**`` 开头，以 ``*/`` 结尾。任何其他形式的注释中出现的标注都将被忽略。

本附录列出了 PHPUnit 所支持的所有标注种类。

.. _appendixes.annotations.author:

@author
#######

``@author`` 标注是 ``@group`` 标注（参见 :ref:`appendixes.annotations.group`\ ）的别名，允许基于作者对测试进行过滤。

.. _appendixes.annotations.after:

@after
######

``@after`` 标注用于指明此方法应当在测试用例类中的每个测试方法运行完成之后调用。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures(): void
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.afterClass:

@afterClass
###########

``@afterClass`` 标注用于指明此静态方法应该于测试类中的所有测试方法都运行完成之后调用，用于清理共享基境。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures(): void
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

PHPUnit 可选地允许在每个测试之前备份所有全局与超全局变量，并在每个测试结束后还原这些备份。

可以在类级别使用 ``@backupGlobals enabled`` 标注来对本测试用例类中的所有测试启用此操作：

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals enabled
     */
    final class MyTest extends TestCase
    {
        // ...
    }

``@backupGlobals`` 标注也可以用在测试方法这一级别。这样可以对备份与还原操作进行更细粒度的配置：

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals enabled
     */
    final class MyTest extends TestCase
    {
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }

        /**
         * @backupGlobals disabled
         */
        public function testThatDoesNotInteractWithGlobalVariables(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

PHPUnit 可选地允许在每个测试之前备份所有已声明类的静态属性，并在每个测试结束后还原这些备份。

可以在类级别使用 ``@backupStaticAttributes enabled`` 标注来对本测试用例类中的所有测试启用此操作：

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    final class MyTest extends TestCase
    {
        // ...
    }

``@backupStaticAttributes`` 标注也可以用在测试方法这一级别。这样可以对备份与还原操作进行更细粒度的配置：

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends TestCase
    {
        public function testThatInteractsWithStaticAttributes(): void
        {
            // ...
        }

        /**
         * @backupStaticAttributes disabled
         */
        public function testThatDoesNotInteractWithStaticAttributes(): void
        {
            // ...
        }
    }

.. admonition:: 注

   受限于 PHP 的内部实现，在某些情况下即使使用了 ``@backupStaticAttributes`` 也可能有个别静态值出现意料外的延续，并污染后继测试。

   细节参见\ :ref:`fixtures.global-state`。

.. _appendixes.annotations.before:

@before
#######

``@before`` 标注用于指明此方法应当在测试用例类中的每个测试方法开始运行之前调用。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures(): void
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.beforeClass:

@beforeClass
############

``@beforeClass`` 标注用于指明此静态方法应该于测试类中的所有测试方法都运行完成之后调用，用于建立共享基境。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures(): void
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.codeCoverageIgnore:

@codeCoverageIgnore*
####################

``@codeCoverageIgnore``\ 、\ ``@codeCoverageIgnoreStart`` 和 ``@codeCoverageIgnoreEnd`` 标注用于从覆盖率分析中排除掉某些代码行。

用法参见\ :ref:`code-coverage-analysis.ignoring-code-blocks`\ 。

.. _appendixes.annotations.covers:

@covers
#######

在测试代码中用 ``@covers`` 标注来指明想要对代码的哪些部分进行测试：

.. code-block:: php

    /**
     * @covers \BankAccount
     */
    public function testBalanceIsInitiallyZero(): void
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

如果提供，这将有效地过滤代码覆盖率报告，将其限制为仅包括所指代码部分中的已执行部分。这将确保代码只在有针对它的专用测试覆盖的情况下才会被标记为已覆盖，而如果它被针对其他类的测试间接使用则并不会标记为已覆盖，从而避免代码覆盖范围的误报。


此标注可以添加给测试类的文档注释块，也可以添加给单个测试方法的文档注释块。推荐的方法是将此标注添加给测试类的文档注释块而不是测试方法的文档注释块。

如果\ :ref:`配置文件 <appendixes.configuration>`\ 中的 ``forceCoversAnnotation`` 配置选项设置为 ``true``\ ，则每个测试方法都必须拥有相应的 ``@covers`` 标注（无论是在测试类还是单个测试方法上）。

:numref:`appendixes.annotations.covers.tables.annotations` 展示了 ``@covers`` 标注的语法。
:ref:`code-coverage-analysis.specifying-covered-parts`\ 这部分有关于使用此标注的更长一些的示例。

请注意，此标注要求用完全限定类名（FQCN，fully-qualified class name）。为了让读者更容易理解，推荐写上开头的反斜杠（虽然此标注并不要求如此也能正常运行）。

.. rst-class:: table
.. list-table:: 用于指明测试覆盖哪些方法的标注
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - 标注
      - 描述
    * - ``@covers ClassName::methodName``\ （不推荐）
      - 指明所标注的测试方法覆盖指定的方法。
    * - ``@covers ClassName``\ （推荐）
      - 指明所标注的测试方法覆盖给定类的全部方法。
    * - ``@covers ClassName<extended>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类以及其所有父类的全部方法。
    * - ``@covers ClassName::<public>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类的所有 public 方法。
    * - ``@covers ClassName::<protected>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类的所有 protected 方法。
    * - ``@covers ClassName::<private>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类的所有 private 方法。
    * - ``@covers ClassName::<!public>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类的所有非 public 方法。
    * - ``@covers ClassName::<!protected>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类的所有非 protected 方法。
    * - ``@covers ClassName::<!private>``\ （不推荐）
      - 指明所标注的测试方法覆盖给定类的所有非 private 方法。
    * - ``@covers ::functionName``\ （推荐）
      - 指明所标注的测试方法覆盖给定的全局函数。

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

``@coversDefaultClass`` 标注用于指定一个默认的命名空间或类名，这样就不用在每个 ``@covers`` 标注中重复长名称。参见\ :numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`\ 。

请注意，此标注要求用完全限定类名（FQCN，fully-qualified class name）。为了让读者更容易理解，推荐写上开头的反斜杠（虽然此标注并不要求如此也能正常运行）。

.. code-block:: php
    :caption: 用 @coversDefaultClass 来缩短标注
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    final class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething(): void
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }

.. _appendixes.annotations.coversNothing:

@coversNothing
##############

在测试代码中用 ``@coversNothing`` 标注来指明所标注的测试用例不需要记录任何代码覆盖率信息。

这可以用于集成测试。例子可参见\ :ref:`code-coverage-analysis.specifying-covered-parts.examples.GuestbookIntegrationTest.php`。

这个标注可以用在类级别或者方法级别，并且会覆盖掉所有 ``@covers`` 标注。

.. _appendixes.annotations.dataProvider:

@dataProvider
#############

测试方法可以接受任意参数。这些参数由一个或多个数据供给器方法（在\ :ref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`\ 中，是 ``provider()`` 方法）提供。用 ``@dataProvider`` 标注来指定要使用的数据供给器方法。

更多细节，参见\ :ref:`writing-tests-for-phpunit.data-providers`。

.. _appendixes.annotations.depends:

@depends
########

PHPUnit 支持对测试方法之间的显式依赖关系进行声明。这种依赖关系并不是定义在测试方法的执行顺序中，而是允许生产者（producer）返回一个测试基境（fixture）的实例，并将此实例传递给依赖于它的消费者（consumer）们。\ :ref:`writing-tests-for-phpunit.examples.StackTest2.php`\ 展示了如何用 ``@depends`` 标注来表达测试方法之间的依赖关系。

更多细节，参见\ :ref:`writing-tests-for-phpunit.test-dependencies`。

.. _appendixes.annotations.doesNotPerformAssertions:

@doesNotPerformAssertions
#########################

防止不执行任何断言的测试被视为有风险。

.. _appendixes.annotations.group:

@group
######

测试可以用 ``@group`` 标注来标记为属于一个或多个组，就像这样：

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething(): void
        {
        }

        /**
         * @group regression
         * @group bug2204
         */
        public function testSomethingElse(): void
        {
        }
    }

也可以对测试类使用 ``@group`` 标注。它会被“继承”到此测试类的所有方法上。

 测试可以基于组来选择性的执行，使用命令行测试执行器的 ``--group`` 和 ``--exclude-group`` 选项，或者使用对应的 XML 配置文件指令。

.. _appendixes.annotations.large:

@large
######

``@large`` 标注是 ``@group large`` 的别名。

如果安装了 ``PHP_Invoker`` 组件包并启用了严格模式，一个执行时间超过60秒的大型（large）测试将视为失败。这个超时限制可以通过 XML 配置文件的 ``timeoutForLargeTests`` 属性进行配置。

.. _appendixes.annotations.medium:

@medium
#######

``@medium`` 标注是 ``@group medium`` 的别名。中型（medium）测试不能依赖于标记为 ``@large`` 的测试。

如果安装了 ``PHP_Invoker`` 组件包并启用了严格模式，一个执行时间超过10秒的中型（medium）测试将视为失败。这个超时限制可以通过 XML 配置文件的 ``timeoutForMediumTests`` 属性进行配置。

.. _appendixes.annotations.preserveGlobalState:

@preserveGlobalState
####################

在单独的进程中运行测试时，PHPUnit 会尝试保持来自父进程的全局状态（通过在父进程序列化全局状态然后在子进程反序列化的方式）。这当父进程包含非可序列化的全局内容时可能会导致问题。为了修正这种问题，可以用 ``@preserveGlobalState`` 标注来禁止 PHPUnit 保持全局状态。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.requires:

@requires
#########

``@requires`` 标注用于在常规前提条件（例如 PHP 版本或所安装的扩展）不满足时跳过测试。

完整的可能用法以及例子见\ :ref:`incomplete-and-skipped-tests.requires.tables.api`

.. _appendixes.annotations.runTestsInSeparateProcesses:

@runTestsInSeparateProcesses
############################

指明单个测试类内的所有测试要各自运行在独立的 PHP 进程中。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    final class MyTest extends TestCase
    {
        // ...
    }

*注意：*\ 默认情况下，PHPUnit 会尝试通过在父进程序列化全局状态然后在子进程反序列化的方式在子进程中保持来自父进程的全局状态。这当父进程包含非可序列化的全局内容时可能会导致问题。关于如何修正此问题的信息参见 :ref:`appendixes.annotations.preserveGlobalState`。

.. _appendixes.annotations.runInSeparateProcess:

@runInSeparateProcess
#####################

指明某个测试要运行在独立的 PHP 进程中。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess(): void
        {
            // ...
        }
    }

*注意：*\ 默认情况下，PHPUnit 会尝试通过在父进程序列化全局状态然后在子进程反序列化的方式在子进程中保持来自父进程的全局状态。这当父进程包含非可序列化的全局内容时可能会导致问题。关于如何修正此问题的信息参见 :ref:`appendixes.annotations.preserveGlobalState`。

.. _appendixes.annotations.small:

@small
######

``@small`` 标注是 ``@group small`` 的别名。小型（small）测试不能依赖于标记为 ``@medium`` 或 ``@large`` 的测试。

如果安装了 ``PHP_Invoker`` 组件包并启用了严格模式，一个执行时间超过 1 秒的小型（small）测试将会视为失败。这个超时限制可以通过 XML 配置文件的 ``timeoutForSmallTests`` 属性进行配置。

.. admonition:: 注

   需要启用运行时间限制的测试必须显式地标注为 ``@small``、``@medium`` 或 ``@large``。

.. _appendixes.annotations.test:

@test
#####

除了用 ``test`` 作为测试方法名称的前缀外，还可以在方法的文档注释块中用 ``@test`` 标注来将其标记为测试方法。

.. code-block:: php

    /**
     * @test
     */
    public function initialBalanceShouldBe0(): void
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

指定生成敏捷文档句子时使用的替换描述。

测试类和测试方法都可以应用 ``@testdox`` 标注。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @testdox A bank account
     */
    final class BankAccountTest extends TestCase
    {
        /**
         * @testdox has an initial balance of zero
         */
        public function balanceIsInitiallyZero(): void
        {
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

.. admonition:: 注

   在 PHPUnit 7.0 之前（由于标注解析中的一个 bug），使用 ``@testdox`` 标注也会激活 ``@test`` 标注的行为。

如果将 ``@testdox`` 标注在方法级别和 ``@dataProvider`` 联用，可以在替换描述中将方法参数用作占位符。

.. code-block:: php

    /**
     * @dataProvider additionProvider
     * @testdox Adding $a to $b results in $expected
     */
    public function testAdd($a, $b, $expected)
    {
        $this->assertSame($expected, $a + $b);
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

.. _appendixes.annotations.testWith:

@testWith
#########

除了实现一个方法并将之与 ``@dataProvider`` 联用外，你也可以用 ``@testWith`` 标注来定义数据集。

数据集由一个或多个元素组成。要定义具有多个元素的数据集，每个元素都要定义在单独一行中。数据集的每个元素都必须是以 JSON 格式定义的数组。

参见\ :ref:`writing-tests-for-phpunit.data-providers`\ 来学习更多关于传递数据集合给测试的信息。

.. code-block:: php

    /**
     * @testWith ["test", 4]
     *           ["longer-string", 13]
     */
    public function testStringLength(string $input, int $expectedLength): void
    {
        $this->assertSame($expectedLength, strlen($input));
    }

以 JSON 格式表示的对象会转换为关联数组。

.. code-block:: php

    /**
     * @testWith [{"day": "monday", "conditions": "sunny"}, ["day", "conditions"]]
     */
    public function testArrayKeys(array $array, array $keys): void
    {
        $this->assertSame($keys, array_keys($array));
    }

.. _appendixes.annotations.ticket:

@ticket
#######

``@ticket`` 标注是 ``@group`` 标注（参见 :ref:`appendixes.annotations.group`\ ）的别名，允许基于事务 ID 对测试进行过滤。

.. _appendixes.annotations.uses:

@uses
#####

``@uses`` 标注用来指明那些将会在测试中执行到但同时又不打算让其被测试所覆盖的代码。在对代码单元进行测试时所必须的值对象就是个很好的例子。

.. code-block:: php

    /**
     * @covers \BankAccount
     * @uses   \Money
     */
    public function testMoneyCanBeDepositedInAccount(): void
    {
        // ...
    }

:numref:`code-coverage-analysis.specifying-covered-parts.examples.InvoiceTest.php` 展示了另一个示例。

在严格覆盖模式中，意外覆盖的代码将导致测试判定为失败，这个标注就比较有用，另外它也有助于阅读代码。关于严格覆盖模式的更多信息，参见\ :ref:`risky-tests.unintentionally-covered-code`。

请注意，此标注要求用完全限定类名（FQCN，fully-qualified class name）。为了让读者更容易理解，推荐写上开头的反斜杠（虽然此标注并不要求如此也能正常运行）。
