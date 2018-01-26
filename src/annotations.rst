

.. _appendixes.annotations:

==
标注
==

所谓标注，是指某些编程语言中允许加在源代码中的一种特殊格式的语法元数据。PHP 并没有专门的语言特性来支持对源代码进行标注，然而 PHP 社区早已经形成惯例，通过在文档注释块中使用诸如 ``@annotation arguments`` 这样的标签来为源代码加上标注。在 PHP 中，文档注释块是可反射的：可以对函数、方法、类以及属性调用相应级别的反射 API ``getDocComment()`` 方法来获取相应的文档注释块。诸如 PHPUnit 这样的应用程序在运行时用这些信息来配置其行为。

.. admonition:: Note

   PHP中的文档注释块必须以 ``/**`` 开头，以 ``*/`` 结尾。任何其他形式的注释中出现的标注都将被忽略。

本附录列出了 PHPUnit 所支持的所有标注种类。

.. _appendixes.annotations.author:

@author
#######

``@author`` 标注是 ``@group`` 标注（参见 :ref:`appendixes.annotations.group`）的别名，允许基于作者对测试进行过滤。

.. _appendixes.annotations.after:

@after
######

``@after`` 标注用于指明此方法应当在测试用例类中的每个测试方法运行完成之后调用。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures()
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.afterClass:

@afterClass
###########

``@afterClass`` 标注用于指明此静态方法应该于测试类中的所有测试方法都运行完成之后调用，用于清理共享基境。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

全局变量的备份与还原操作可以对某个测试用例类中的所有测试彻底禁用，像这样：

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        // ...
    }

``@backupGlobals`` 标注也可以用在测试方法这一级别。这样可以对备份与还原操作进行更细粒度的配置：

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupGlobals enabled
         */
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

如果指定了 ``@backupStaticAttributes`` 标注，那么将在每个测试之前备份所有已声明的类的静态属性的值，并在测试完成之后全部恢复。它可以用在测试用例类或测试方法级别：

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupStaticAttributes disabled
         */
        public function testThatInteractsWithStaticAttributes()
        {
            // ...
        }
    }

.. admonition:: Note

   受限于 PHP 的内部实现，在某些情况下即使使用了 ``@backupStaticAttributes`` 也可能有个别静态值出现意料外的延续，并污染后继测试。

   详细信息参见 :ref:`fixtures.global-state`。

.. _appendixes.annotations.before:

@before
#######

``@before`` 标注用于指明此方法应当在测试用例类中的每个测试方法开始运行之前调用。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures()
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.beforeClass:

@beforeClass
############

``@beforeClass`` 标注用于指明此静态方法应该于测试类中的所有测试方法都运行完成之后调用，用于建立共享基境。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.codeCoverageIgnore:

@codeCoverageIgnore*
####################

``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart`` and ``@codeCoverageIgnoreEnd`` 标注用于从覆盖率分析中排除掉某些代码行。

用法参见:ref:`code-coverage-analysis.ignoring-code-blocks`。

.. _appendixes.annotations.covers:

@covers
#######

在测试代码中用 ``@covers`` 标注来指明测试方法想要对哪些方法进行测试：

.. code-block:: php

    /**
     * @covers BankAccount::getBalance
     */
    public function testBalanceIsInitiallyZero()
    {
        $this->assertEquals(0, $this->ba->getBalance());
    }

如果提供了此标注，则代码覆盖率信息中只考虑指定的这些方法。

:numref:`appendixes.annotations.covers.tables.annotations`列出了 ``@covers`` 标注的语法。

.. rst-class:: table
.. list-table:: 用于指明测试覆盖哪些方法的标注
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - Annotation （标注）
      - 描述
    * - ``@covers ClassName::methodName``
      - ``指明所标注的测试方法覆盖指定的方法。``
    * - ``@covers ClassName``
      - ``指明所标注的测试方法覆盖给定类的全部方法。``
    * - ``@covers ClassName<extended>``
      - ``指明所标注的测试方法覆盖给定类以及其所有父类与接口的全部方法。``
    * - ``@covers ClassName::<public>``
      - ``指明所标注的测试方法覆盖给定类的所有 public 方法。``
    * - ``@covers ClassName::<protected>``
      - ``指明所标注的测试方法覆盖给定类的所有 protected 方法。``
    * - ``@covers ClassName::<private>``
      - ``指明所标注的测试方法覆盖给定类的所有 private 方法。``
    * - ``@covers ClassName::<!public>``
      - ``指明所标注的测试方法覆盖给定类的所有非 public 方法。``
    * - ``@covers ClassName::<!protected>``
      - ``指明所标注的测试方法覆盖给定类的所有非 protected 方法。``
    * - ``@covers ClassName::<!private>``
      - ``指明所标注的测试方法覆盖给定类的所有非 private 方法。``
    * - ``@covers ::functionName``
      - ``指明所标注的测试方法覆盖给定的全局函数。``

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

``@coversDefaultClass`` 标注用于指定一个默认的命名空间或类名，这样就不用在每个 ``@covers`` 标注中重复长名称。参见:numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`。

.. code-block:: php
    :caption: 用 @coversDefaultClass 缩短标注
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething()
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }
    ?>

.. _appendixes.annotations.coversNothing:

@coversNothing
##############

在测试代码中用 ``@coversNothing`` 标注来指明所标注的测试用例不需要记录任何代码覆盖率信息。

这可以用于集成测试。例子可参见:ref:`code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php`。

这个标注可以用在类级别或者方法级别，并且会覆盖掉任何 ``@covers`` 标注。

.. _appendixes.annotations.dataProvider:

@dataProvider
#############

测试方法可以接受任意参数。这些参数可以由数据供给器方法（:ref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`中的 ``provider()``）提供。所要使用的数据供给器方法用 ``@dataProvider`` 标注来指定。

更多细节参见:ref:`writing-tests-for-phpunit.data-providers`。

.. _appendixes.annotations.depends:

@depends
########

PHPUnit支持对测试方法之间的显式依赖关系进行声明。这种依赖关系并不是定义在测试方法的执行顺序中，而是允许生产者(producer)返回一个测试基境(fixture)的实例，并将此实例传递给依赖于它的消费者(consumer)们。:ref:`writing-tests-for-phpunit.examples.StackTest2.php`展示了如何用 ``@depends`` 标注来表达测试方法之间的依赖关系。

更多细节参见:ref:`writing-tests-for-phpunit.test-dependencies`。

.. _appendixes.annotations.expectedException:

@expectedException
##################

:ref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`展示了如何用 ``@expectedException`` 标注来测试被测代码中是否抛出了异常。

更多细节参见:ref:`writing-tests-for-phpunit.exceptions`。

.. _appendixes.annotations.expectedExceptionCode:

@expectedExceptionCode
######################

将 ``@expectedExceptionCode`` 标注与 ``@expectedException`` 联合使用，可以对抛出异常的代码作出断言，这样可以缩小具体异常的范围。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException     MyException
         * @expectedExceptionCode 20
         */
        public function testExceptionHasErrorcode20()
        {
            throw new MyException('Some Message', 20);
        }
    }

为了方便测试并减少冗余，可以用"``@expectedExceptionCode ClassName::CONST``"这样的语法将指定类常量作为 ``@expectedExceptionCode``

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
          * @expectedException     MyException
          * @expectedExceptionCode MyClass::ERRORCODE
          */
        public function testExceptionHasErrorcode20()
        {
          throw new MyException('Some Message', 20);
        }
    }
    class MyClass
    {
        const ERRORCODE = 20;
    }

.. _appendixes.annotations.expectedExceptionMessage:

@expectedExceptionMessage
#########################

``@expectedExceptionMessage`` 标注的运作方式类似于 ``@expectedExceptionCode`` ，用它可以对异常的错误讯息作出断言。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException        MyException
         * @expectedExceptionMessage Some Message
         */
        public function testExceptionHasRightMessage()
        {
            throw new MyException('Some Message', 20);
        }
    }

预期讯息可以是异常讯息的子串。在只需要断言传入的特定名称或参数确实出现于异常中时这个特性很有用，这样就无需在测试中关注完整的异常讯息。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException        MyException
          * @expectedExceptionMessage broken
          */
         public function testExceptionHasRightMessage()
         {
             $param = "broken";
             throw new MyException('Invalid parameter "'.$param.'".', 20);
         }
    }

为了方便测试同时减少冗余，可以用"``@expectedExceptionMessage ClassName::CONST``"这样的语法将指定类常量作为 ``@expectedExceptionMessage``。在:ref:`appendixes.annotations.expectedExceptionCode`中可以看到范例。

.. _appendixes.annotations.expectedExceptionMessageRegExp:

@expectedExceptionMessageRegExp
###############################

预期讯息也可以通过 ``@expectedExceptionMessageRegExp`` 标注以正则表达式来指定。当无法用子串来完成对给定讯息的匹配时，这种方式就非常有用了。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException              MyException
          * @expectedExceptionMessageRegExp /Argument \d+ can not be an? \w+/
          */
         public function testExceptionHasRightMessage()
         {
             throw new MyException('Argument 2 can not be an integer');
         }
    }

.. _appendixes.annotations.group:

@group
######

测试可以用 ``@group`` 标注来标记为属于一个或多个组，就像这样：

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething()
        {
        }

        /**
         * @group regresssion
         * @group bug2204
         */
        public function testSomethingElse()
        {
        }
    }

测试可以基于组来选择性的执行，使用命令行测试执行器的 ``--group`` and ``--exclude-group`` 选项，或者使用对应的 XML 配置文件指令。

.. _appendixes.annotations.large:

@large
######

``@large`` 标注是 ``@group large`` 的别名。

如果安装了 ``PHP_Invoker`` 组件包并启用了严格模式，一个执行时间超过60秒的大型(large)测试将视为失败。这个超时限制可以通过 XML 配置文件的 ``timeoutForLargeTests`` 属性进行配置。

.. _appendixes.annotations.medium:

@medium
#######

``@medium`` 标注是 ``@group medium`` 的别名。中型(medium)测试不能依赖于标记为 ``@large`` 的测试。

如果安装了 ``PHP_Invoker`` 组件包并启用了严格模式，一个执行时间超过10秒的中型(medium)测试将视为失败。这个超时限制可以通过 XML 配置文件的 ``timeoutForMediumTests`` 属性进行配置。

.. _appendixes.annotations.preserveGlobalState:

@preserveGlobalState
####################

在单独的进程中运行测试时，PHPUnit 会尝试保持来自父进程的全局状态（通过在父进程序列化全局状态然后在子进程反序列化的方式）。这当父进程包含非可序列化的全局内容时可能会导致问题。为了修正这种问题，可以用 ``@preserveGlobalState`` 标注来禁止 PHPUnit 保持全局状态。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

.. _appendixes.annotations.requires:

@requires
#########

``@requires`` 标注用于在常规前提条件（例如 PHP 版本或所安装的扩展）不满足时跳过测试。

完整的可能用法以及例子见:ref:`incomplete-and-skipped-tests.requires.tables.api`

.. _appendixes.annotations.runTestsInSeparateProcesses:

@runTestsInSeparateProcesses
############################

指明单个测试类内的所有测试要各自运行在独立的 PHP 进程中。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    class MyTest extends TestCase
    {
        // ...
    }

*注意：*:ref:`appendixes.annotations.preserveGlobalState` 默认情况下，PHPUnit 会尝试通过在父进程序列化全局状态然后在子进程反序列化的方式在子进程中保持来自父进程的全局状态。这当父进程包含非可序列化的全局内容时可能会导致问题。关于如何修正此问题的信息参见:ref:`appendixes.annotations.preserveGlobalState`。

.. _appendixes.annotations.runInSeparateProcess:

@runInSeparateProcess
#####################

明某个测试要运行在独立的 PHP 进程中。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

*注意：*:ref:`appendixes.annotations.preserveGlobalState` 默认情况下，PHPUnit 会尝试通过在父进程序列化全局状态然后在子进程反序列化的方式在子进程中保持来自父进程的全局状态。这当父进程包含非可序列化的全局内容时可能会导致问题。关于如何修正此问题的信息参见:ref:`appendixes.annotations.preserveGlobalState`。

.. _appendixes.annotations.small:

@small
######

``@small`` 标注是 ``@group small`` 的别名。小型(small)测试不能依赖于标记为 ``@medium`` 或 ``@large`` 的测试。

如果安装了 ``PHP_Invoker`` 组件包并启用了严格模式，一个执行时间超过1秒的小型(small)测试将会视为失败。这个超时限制可以通过 XML 配置文件的 ``timeoutForSmallTests`` 属性进行配置。

.. admonition:: Note

   需要启用运行时间限制的测试必须显式地标注为 ``@small``、``@medium`` 或 ``@large``。

.. _appendixes.annotations.test:

@test
#####

除了用 ``test`` 作为测试方法名称的前缀外，还可以在方法的文档注释块中用 ``@test`` 标注来将其标记为测试方法。

.. code-block:: php

    /**
     * @test
     */
    public function initialBalanceShouldBe0()
    {
        $this->assertEquals(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

.. code-block:: php

.. _appendixes.annotations.ticket:

@ticket
#######

.. code-block:: php

.. _appendixes.annotations.uses:

@uses
#####

``@uses`` 标注用来指明那些将会在测试中执行到但同时又不打算让其被测试所覆盖的代码。在对代码单元进行测试时所必须的值对象就是个很好的例子。

.. code-block:: php

    /**
     * @covers BankAccount::deposit
     * @uses   Money
     */
    public function testMoneyCanBeDepositedInAccount()
    {
        // ...
    }

在严格覆盖模式中，意外覆盖的代码将导致测试判定为失败，这个标注就显得特别有用。关于严格覆盖模式的更多信息，参见:ref:`risky-tests.unintentionally-covered-code`。

