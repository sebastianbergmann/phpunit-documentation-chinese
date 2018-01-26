

.. _risky-tests:

======
有风险的测试
======

在执行测试时，PHPUnit 可以进行一些额外的检查，见下文。

.. _risky-tests.useless-tests:

无用测试
####

PHPUnit 可以更严格对待事实上不测试任何内容的测试。此项检查可以用命令行选项 ``--report-useless-tests`` 或在 PHPUnit 的 XML 配置文件中设置  ``beStrictAboutTestsThatDoNotTestAnything="true"`` 来启用。

在启用本项检查后，如果某个测试未进行任何断言，它将被标记为有风险。仿件对象中的预期和诸如 @expectedException 这样的标注同样视为断言。

.. _risky-tests.unintentionally-covered-code:

意外的代码覆盖
#######

PHPUnit 可以更严格对待意外的代码覆盖。此项检查可以用命令行选项 ``--strict-coverage`` 或在 PHPUnit 的 XML 配置文件中设置 ``beStrictAboutCoversAnnotation="true"`` 来启用。

在启用本项检查后，如果某个带有 @covers 标注的测试执行了未在 @covers 或 @uses 标注中列出的代码，它将被标记为有风险。

.. _risky-tests.output-during-test-execution:

测试执行期间产生的输出
###########

PHPUnit 可以更严格对待测试执行期间产生的输出。 此项检查可以用命令行选项 ``--disallow-test-output`` 或在 PHPUnit 的 XML 配置文件中设置 ``beStrictAboutOutputDuringTests="true"`` 来启用。

在启用本项检查后，如果某个测试产生了输出，例如，在测试代码或被测代码中调用了 print，它将被标记为有风险。

.. _risky-tests.test-execution-timeout:

测试执行时长的超时限制
###########

如果安装了 ``PHP_Invoker`` 包并且 ``pcntl`` 扩展可用，那么可以对测试的执行时长进行限制。此时间限制可以用命令行选项 ``--enforce-time-limit`` 或在 PHPUnit 的 XML 配置文件中设置 ``beStrictAboutTestSize="true"`` 来启用。

带有 ``@large`` 标注的测试如果执行时间超过60秒将视为失败。此超时限制可以通过XML配置文件中的 ``timeoutForLargeTests`` 属性进行配置。

带有 ``@medium`` 标注的测试如果执行时间超过10秒将视为失败。此超时限制可以通过XML配置文件中的 ``timeoutForMediumTests`` 属性进行配置。

没有 ``@medium`` 或 ``@large`` 标注的测试都将视同为带有 ``@small``标注，这类测试如果执行时间超过1秒将视为失败。此超时限制可以通过XML配置文件中的 ``timeoutForSmallTests`` 属性进行配置。

.. _risky-tests.global-state-manipulation:

全局状态篡改
######

PHPUnit 可以更严格对待篡改全局状态的测试。此项检查可以用命令行选项 ``--strict-global-state`` 或在 PHPUnit 的 XML 配置文件中设置 ``beStrictAboutChangesToGlobalState="true"`` 来启用。

