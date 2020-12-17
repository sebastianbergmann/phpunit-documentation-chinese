

.. _extending-phpunit:

=================
扩展 PHPUnit
=================

可以用多种方式对 PHPUnit 进行扩展，使编写测试更容易，以及对运行测试所得到的反馈进行定制。扩展 PHPUnit 时，一般从这些点入手：

.. _extending-phpunit.PHPUnit_Framework_TestCase:

PHPUnit\\Framework\\TestCase 的子类
#####################################

将自定义的断言和工具方法写在 ``PHPUnit\Framework\TestCase`` 的一个抽象子类中，然后从这个抽象子类派生你的测试用例类。这是扩展 PHPUnit 的最容易的方法。

.. _extending-phpunit.custom-assertions:

编写自定义断言
#######################

编写自定义断言时，最佳实践是遵循 PHPUnit 自有断言的实现方式。正如\ :numref:`extending-phpunit.examples.Assert.php` 中所示，``assertTrue()`` 方法只是对 ``isTrue()`` 和 ``assertThat()`` 方法的封装：``isTrue()`` 创建了一个匹配器对象，将其传递给 ``assertThat()`` 进行评定。

.. code-block:: php
    :caption: PHPUnit\\Framework\\Assert 类的 assertTrue() 和 isTrue() 方法
    :name: extending-phpunit.examples.Assert.php

    <?php declare(strict_types=1);
    namespace PHPUnit\Framework;

    use PHPUnit\Framework\Constraint\IsTrue;

    abstract class Assert
    {
        // ...

        public static function assertTrue($condition, string $message = ''): void
        {
            static::assertThat($condition, static::isTrue(), $message);
        }

        // ...

        public static function isTrue(): IsTrue
        {
            return new IsTrue;
        }

        // ...
    }

:numref:`extending-phpunit.examples.IsTrue.php` 展示了 ``PHPUnit\Framework\Constraint\IsTrue`` 是如何扩展针对匹配器对象（或约束）的抽象基类 ``PHPUnit\Framework\Constraint`` 的。

.. code-block:: php
    :caption: PHPUnit\\Framework\Constraint\\IsTrue 类
    :name: extending-phpunit.examples.IsTrue.php

    <?php declare(strict_types=1);
    namespace PHPUnit\Framework\Constraint;

    use PHPUnit\Framework\Constraint;

    final class IsTrue extends Constraint
    {
        public function toString(): string
        {
            return 'is true';
        }

        protected function matches($other): bool
        {
            return $other === true;
        }
    }

在实现 ``assertTrue()`` 和 ``isTrue()`` 方法及 ``PHPUnit\Framework\Constraint\IsTrue`` 类时所付出的努力带来了一些好处，``assertThat()`` 能够自动负责起断言的评定与任务簿记（例如为了统计目的而对其进行计数）工作。此外， ``isTrue()`` 方法还可以在配置仿件对象时用来作为匹配器。

.. _extending-phpunit.TestRunner:

扩展测试执行器
########################

PHPUnit 的测试执行器可以通过注册实现了一个或多个以下接口的对象来进行扩展：

- ``AfterIncompleteTestHook``
- ``AfterLastTestHook``
- ``AfterRiskyTestHook``
- ``AfterSkippedTestHook``
- ``AfterSuccessfulTestHook``
- ``AfterTestErrorHook``
- ``AfterTestFailureHook``
- ``AfterTestWarningHook``
- ``AfterTestHook``
- ``BeforeFirstTestHook``
- ``BeforeTestHook``

每个“hook”（表示上面列出的各个接口）都代表一个可能在测试执行过程中发生的事件。

有关如何在 PHPUnit 的 XML 配置中注册扩展的详细信息，参见 :ref:`appendixes.configuration.extensions`。

:numref:`extending-phpunit.examples.TestRunnerExtension` 展示了一个实现了 ``BeforeFirstTestHook`` 和 ``AfterLastTestHook`` 的扩展：

.. code-block:: php
    :caption: 测试执行器扩展示例
    :name: extending-phpunit.examples.TestRunnerExtension

    <?php declare(strict_types=1);
    namespace Vendor;

    use PHPUnit\Runner\BeforeFirstTestHook;
    use PHPUnit\Runner\AfterLastTestHook;

    final class MyExtension implements BeforeFirstTestHook, AfterLastTestHook
    {
        public function executeBeforeFirstTest(): void
        {
            // 运行第一个测试之前调用
        }

        public function executeAfterLastTest(): void
        {
            // 运行完最后一个测试之后调用
        }
    }

配置扩展
----------------------

假定扩展接受配置值，则你可以对 PHPUnit 扩展进行配置。

:numref:`extending-phpunit.examples.TestRunnerConfigurableExtension` 展示了如何通过为扩展类添加 ``__constructor()`` 定义让扩展可配置：

.. code-block:: php
    :caption: 带有构造函数的测试执行器扩展
    :name: extending-phpunit.examples.TestRunnerConfigurableExtension

    <?php declare(strict_types=1);
    namespace Vendor;

    use PHPUnit\Runner\BeforeFirstTestHook;
    use PHPUnit\Runner\AfterLastTestHook;

    final class MyConfigurableExtension implements BeforeFirstTestHook, AfterLastTestHook
    {
        protected $config_value_1 = '';

        protected $config_value_2 = 0;

        public function __construct(string $value1 = '', int $value2 = 0)
        {
            $this->config_value_1 = $config_1;
            $this->config_value_2 = $config_2;
        }

        public function executeBeforeFirstTest(): void
        {
            if (strlen($this->config_value_1) {
                echo 'Testing with configuration value: ' . $this->config_value_1;
            }
        }

        public function executeAfterLastTest(): void
        {
            if ($this->config_value_2 > 10) {
                echo 'Second config value is OK!';
            }
        }
    }

要通过 XML 给扩展输入配置，必须更新 XML 配置文件的 ``extensions`` 段来让其拥有配置值，如\ :numref:`extending-phpunit.examples.TestRunnerConfigurableExtensionConfig` 中所示：

.. code-block:: xml
    :caption: 测试执行器扩展配置
    :name: extending-phpunit.examples.TestRunnerConfigurableExtensionConfig

    <extensions>
        <extension class="Vendor\MyUnconfigurableExtension" />
        <extension class="Vendor\MyConfigurableExtension">
            <arguments>
                <string>Hello world!</string>
                <int>15</int>
            </arguments>
        </extension>
    </extensions>

有关如何使用 ``arguments`` 配置的详细信息，参见 :ref:`appendixes.configuration.extensions.extension.arguments`。

请记住：所有配置都是可选的，因此要确保你的配置要么要有健全的默认值，要么它就应当在缺失配置的时候禁用自身。
