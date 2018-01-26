

.. _textui:

========
命令行测试执行器
========

PHPUnit 命令行测试执行器可通过 :file:`phpunit` 命令调用。下面的代码展示了如何用 PHPUnit 命令行测试执行器来运行测试：

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

上面这个调用例子中，PHPUnit 命令行测试执行器将在当前工作目录中寻找 :file:`ArrayTest.php` 源文件并加载之。而在此源文件中应当能找到 ``ArrayTest`` 测试用例类，此类中的测试将被执行。

对于每个测试的运行，PHPUnit 命令行工具输出一个字符来指示进展：

``.``

    当测试成功时输出。

``F``

    当测试方法运行过程中一个断言失败时输出。

``E``

    当测试方法运行过程中产生一个错误时输出。

``R``

    当测试被标记为有风险时输出（参见:ref:`risky-tests`）。

``S``

    当测试被跳过时输出（参见:ref:`incomplete-and-skipped-tests`）。

``I``

    当测试被标记为不完整或未实现时输出（参见:ref:`incomplete-and-skipped-tests`）。

PHPUnit 区分 *败(failure)*与*错误(error)*。失败指的是被违背了的 PHPUnit 断言，例如一个失败的 ``assertEquals()`` 调用。错误指的是意料之外的异常(exception)或 PHP 错误。这种差异已被证明在某些时候是非常有用的，因为错误往往比失败更容易修复。如果得到了一个非常长的问题列表，那么最好先对付错误，当错误全部修复了之后再试一次瞧瞧还有没有失败。

.. _textui.clioptions:

命令行选项
#####

让我们来瞧瞧以下代码中命令行测试运行器的各种选项：

.. code-block:: bash

    $ phpunit --help
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    Usage: phpunit [options] UnitTest [UnitTest.php]
           phpunit [options] <directory>

    Code Coverage Options:

      --coverage-clover <file>    Generate code coverage report in Clover XML format.
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format.
      --coverage-html <dir>       Generate code coverage report in HTML format.
      --coverage-php <file>       Export PHP_CodeCoverage object to file.
      --coverage-text=<file>      Generate code coverage report in text format.
                                  Default: Standard output.
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format.
      --whitelist <dir>           Whitelist <dir> for code coverage analysis.
      --disable-coverage-ignore   Disable annotations for ignoring code coverage.

    Logging Options:

      --log-junit <file>          Log test execution in JUnit XML format to file.
      --log-teamcity <file>       Log test execution in TeamCity format to file.
      --testdox-html <file>       Write agile documentation in HTML format to file.
      --testdox-text <file>       Write agile documentation in Text format to file.
      --testdox-xml <file>        Write agile documentation in XML format to file.
      --reverse-list              Print defects in reverse order

    Test Selection Options:

      --filter <pattern>          Filter which tests to run.
      --testsuite <name,...>      Filter which testsuite to run.
      --group ...                 Only runs tests from the specified group(s).
      --exclude-group ...         Exclude tests from the specified group(s).
      --list-groups               List available test groups.
      --list-suites               List available test suites.
      --test-suffix ...           Only search for test in files with specified
                                  suffix(es). Default: Test.php,.phpt

    Test Execution Options:

      --dont-report-useless-tests Do not report tests that do not test anything.
      --strict-coverage           Be strict about @covers annotation usage.
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests.
      --disallow-resource-usage   Be strict about resource usage during small tests.
      --enforce-time-limit        Enforce time limit based on test size.
      --disallow-todo-tests       Disallow @todo-annotated tests.

      --process-isolation         Run each test in a separate PHP process.
      --globals-backup            Backup and restore $GLOBALS for each test.
      --static-backup             Backup and restore static attributes for each test.

      --colors=<flag>             Use colors in output ("never", "auto" or "always").
      --columns <n>               Number of columns to use for progress output.
      --columns max               Use maximum number of columns for progress output.
      --stderr                    Write to STDERR instead of STDOUT.
      --stop-on-error             Stop execution upon first error.
      --stop-on-failure           Stop execution upon first error or failure.
      --stop-on-warning           Stop execution upon first warning.
      --stop-on-risky             Stop execution upon first risky test.
      --stop-on-skipped           Stop execution upon first skipped test.
      --stop-on-incomplete        Stop execution upon first incomplete test.
      --fail-on-warning           Treat tests with warnings as failures.
      --fail-on-risky             Treat risky tests as failures.
      -v|--verbose                Output more verbose information.
      --debug                     Display debugging information.

      --loader <loader>           TestSuiteLoader implementation to use.
      --repeat <times>            Runs the test(s) repeatedly.
      --teamcity                  Report test execution progress in TeamCity format.
      --testdox                   Report test execution progress in TestDox format.
      --testdox-group             Only include tests from the specified group(s).
      --testdox-exclude-group     Exclude tests from the specified group(s).
      --printer <printer>         TestListener implementation to use.

    Configuration Options:

      --bootstrap <file>          A "bootstrap" PHP file that is run before the tests.
      -c|--configuration <file>   Read configuration from XML file.
      --no-configuration          Ignore default configuration file (phpunit.xml).
      --no-coverage               Ignore code coverage configuration.
      --no-extensions             Do not load PHPUnit extensions.
      --include-path <path(s)>    Prepend PHP's include_path with given path(s).
      -d key[=value]              Sets a php.ini value.
      --generate-configuration    Generate configuration file with suggested settings.

    Miscellaneous Options:

      -h|--help                   Prints this usage information.
      --version                   Prints the version and exits.
      --atleast-version <min>     Checks that version is greater than min and exits.

``phpunit UnitTest``

    运行由 ``UnitTest`` 类提供的测试。这个类应当在 :file:`UnitTest.php` 源文件中声明。

    ``UnitTest`` 这个类必须满足以下二个条件之一：要么它继承自 ``PHPUnit\Framework\TestCase``；要么它提供 ``public static suite()`` 方法，这个方法返回一个 ``PHPUnit_Framework_Test`` 对象，比如，一个 ``PHPUnit_Framework_TestSuite`` 类的实例。

``phpunit UnitTest UnitTest.php``

    运行由 ``UnitTest`` 类提供的测试。这个类应当在指定的源文件中声明。

``--coverage-clover``

    为运行的测试生成带有代码覆盖率信息的 XML 格式的日志文件。更多细节请参见:ref:`logging`。

    请注意，此功能仅当安装了 tokenizer 和 Xdebug 这两个 PHP 扩展后才可用。

``--coverage-crap4j``

    生成 Crap4j 格式的代码覆盖率报告。更多细节请参见:ref:`code-coverage-analysis`。

    请注意，此功能仅当安装了 tokenizer 和 Xdebug 这两个 PHP 扩展后才可用。

``--coverage-html``

    生成 HTML 格式的代码覆盖率报告。更多细节请参见 :ref:`code-coverage-analysis`。

    请注意，此功能仅当安装了 tokenizer 和 Xdebug 这两个 PHP 扩展后才可用。

``--coverage-php``

    生成一个序列化后的 PHP_CodeCoverage 对象，此对象含有代码覆盖率信息。

    请注意，此功能仅当安装了 tokenizer 和 Xdebug 这两个 PHP 扩展后才可用。

``--coverage-text``

    为运行的测试以人们可读的格式生成带有代码覆盖率信息的日志文件或命令行输出。更多细节请参见 :ref:`logging`。

    请注意，此功能仅当安装了 tokenizer 和 Xdebug 这两个 PHP 扩展后才可用。

``--log-junit``

    为运行的测试生成 JUnit XML 格式的日志文件。更多细节请参见 :ref:`logging`。

``--testdox-html`` 和 ``--testdox-text``

    为运行的测试以 HTML 或纯文本格式生成敏捷文档。更多细节请参见 :ref:`other-uses-for-tests`。

``--filter``

    只运行名称与给定模式匹配的测试。如果模式未闭合包裹于分隔符，PHPUnit 将用 ``/`` 分隔符对其进行闭合包裹。

    测试名称将以以下格式之一进行匹配：

    ``TestNamespace\TestCaseClass::testMethod``

        默认的测试名称格式等价于在测试方法内使用 ``__METHOD__`` 魔术常量。

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        当测试拥有数据供给器时，数据的每轮迭代都会将其当前索引附加在默认测试名称结尾处。

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        当测试拥有使用命名数据集的数据供给器时，数据的每轮迭代都会将当前名称附加在默认测试名称结尾处。命名数据集的例子参见:numref:`textui.examples.TestCaseClass.php`。

        .. code-block:: php
            :caption: 命名数据集
            :name: textui.examples.TestCaseClass.php

            <?php
            use PHPUnit\Framework\TestCase;

            namespace TestNamespace;

            class TestCaseClass extends TestCase
            {
                /**
                 * @dataProvider provider
                 */
                public function testMethod($data)
                {
                    $this->assertTrue($data);
                }

                public function provider()
                {
                    return [
                        'my named data' => [true],
                        'my data'       => [true]
                    ];
                }
            }
            ?>

    ``/path/to/my/test.phpt``

        对于 PHPT 测试，其测试名称是文件系统路径。

    有效的过滤器模式例子参见:numref:`textui.examples.filter-patterns`。

    .. code-block:: php
        :caption: 过滤器模式例子
        :name: textui.examples.filter-patterns

    在匹配数据供给器时有一些额外的快捷方式，参见:numref:`textui.examples.filter-shortcuts`。

    .. code-block:: php
        :caption: 过滤器的快捷方式
        :name: textui.examples.filter-shortcuts

``--testsuite``

    只运行名称与给定模式匹配的测试套件。

``--group``

    只运行来自指定分组（可以多个）的测试。可以用 ``@group`` 标注为测试标记其所属的分组。

    ``@author`` 标注是 ``@group`` 的一个别名，允许按作者来筛选测试。

``--exclude-group``

    排除来自指定分组（可以多个）的测试。可以用 ``@group`` 标注为测试标记其所属的分组。

``--list-groups``

    列出所有有效的测试分组。

``--test-suffix``

    只查找文件名以指定后缀（可以多个）结尾的测试文件。

``--report-useless-tests``

    更严格对待事实上不测试任何内容的测试。详情参见 :ref:`risky-tests`。

``--strict-coverage``

    更严格对待意外的代码覆盖。详情参见 :ref:`risky-tests`。

``--strict-global-state``

    更严格对待全局状态篡改。详情参见 :ref:`risky-tests`。

``--disallow-test-output``

    更严格对待测试执行期间产生的输出。详情参见:ref:`risky-tests`。

``--disallow-todo-tests``

    不执行文档注释块中含有 ``@todo`` 标注的测试。

``--enforce-time-limit``

    根据测试规模对其加上执行时长限制。详情参见:ref:`risky-tests`。

``--process-isolation``

    每个测试都在独立的PHP进程中运行。

``--no-globals-backup``

    不要备份并还原 $GLOBALS。更多细节请参见:ref:`fixtures.global-state`。

``--static-backup``

    备份并还原用户定义的类中的静态属性。更多细节请参见:ref:`fixtures.global-state`。

``--colors``

    使用彩色输出。Windows下，用 `ANSICON <https://github.com/adoxa/ansicon>`_ 或 `ConEmu <https://github.com/Maximus5/ConEmu>`_。

    本选项有三个可能的值：

    -

      ``never``: 完全不使用彩色输出。当未使用 ``--colors`` 选项时，这是默认值。

    -

      ``auto``: 如果当前终端不支持彩色、或者输出被管道输出至其他命令、或输出被重定向至文件时，不使用彩色输出，其余情况使用彩色。

    -

      ``always``: 总是使用彩色输出，即使当前终端不支持彩色、输出被管道输出至其他命令、或输出被重定向至文件。

    当使用了 ``--colors`` 选项但未指定任何值时，将选择 ``auto`` 做为其值。

``--columns``

    定义输出所使用的列数。如果将其值定义为 ``max``，则使用当前终端所支持的最大列数。

``--stderr``

    选择输出到 ``STDERR`` 而非 ``STDOUT``.

``--stop-on-error``

    首次错误出现后停止执行。

``--stop-on-failure``

    首次错误或失败出现后停止执行。

``--stop-on-risky``

    首次碰到有风险的测试时停止执行。

``--stop-on-skipped``

    首次碰到跳过的测试时停止执行。

``--stop-on-incomplete``

    首次碰到不完整的测试时停止执行。

``--verbose``

    输出更详尽的信息，例如不完整或者跳过的测试的名称。

``--debug``

    输出调试信息，例如当一个测试开始执行时输出其名称。

``--loader``

    指定要使用的 ``PHPUnit_Runner_TestSuiteLoader`` 实现。

    标准的测试套件加载器将在当前工作目录和 PHP 的 ``include_path`` 配置指令中指定的每个目录内查找源文件。诸如 ``Project_Package_Class`` 这样的类名对应的源文件名为 :file:`Project/Package/Class.php`。

``--repeat``

    将测试重复运行指定次数。

``--testdox``

    将测试进度以敏捷文档方式报告。更多细节请参见 :ref:`other-uses-for-tests`。

``--printer``

    指定要使用的结果输出器(printer)。输出器类必须扩展 ``PHPUnit_Util_Printer`` 并且实现 ``PHPUnit\Framework\TestListener`` 接口。

``--bootstrap``

    在测试前先运行一个 "bootstrap" PHP 文件。

``--configuration``, ``-c``

    从 XML 文件中读取配置信息。更多细节请参见:ref:`appendixes.configuration`。

    如果 :file:`phpunit.xml` 或 :file:`phpunit.xml.dist` （按此顺序）存在于当前工作目录并且*未*使用 ``--configuration``，将自动从此文件中读取配置。

``--no-configuration``

    忽略当前工作目录下的 :file:`phpunit.xml` 与 :file:`phpunit.xml.dist`。

``--include-path``

    向 PHP 的 ``include_path`` 开头添加指定路径（可以多个）。

``-d``

    设置指定的 PHP 配置选项的值。

.. admonition:: Note

   请注意，从 4.8 开始，选项不能放在参数之后。

