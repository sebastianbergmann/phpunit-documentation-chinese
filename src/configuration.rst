

.. _appendixes.configuration:

==========================
XML 配置文件
==========================

.. _appendixes.configuration.phpunit:

``<phpunit>`` 元素
#########################

.. _appendixes.configuration.phpunit.backupGlobals:

``backupGlobals`` 属性
-------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

PHPUnit 可选地允许在每个测试之前备份所有全局与超全局变量，并在每个测试结束后还原这些备份。

此属性为所有测试配置此操作，可以在测试用例类和测试方法级别用 ``@backupGlobals`` 标注来覆盖此配置。

.. _appendixes.configuration.phpunit.backupStaticAttributes:

``backupStaticAttributes`` 属性
----------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

PHPUnit 可选地允许在每个测试之前备份所有已声明类的静态属性，并在每个测试结束后还原这些备份。

此属性为所有测试配置此操作，可以在测试用例类和测试方法级别用 ``@backupStaticAttributes`` 标注来覆盖此配置。

.. _appendixes.configuration.phpunit.bootstrap:

``bootstrap`` 属性
---------------------------

此属性配置在执行测试之前加载的引导脚本。此脚本通常仅注册用于加载被测代码的自动加载器回调函数。

.. _appendixes.configuration.phpunit.cacheResult:

``cacheResult`` 属性
-----------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置测试结果的缓存。该缓存是某些其他功能正常工作所必须的。

.. _appendixes.configuration.phpunit.cacheResultFile:

``cacheResultFile`` 属性
---------------------------------

此属性配置测试结果缓存（见上文）储存在哪个文件中。

.. _appendixes.configuration.phpunit.colors:

``colors`` 属性
------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置 PHPUnit 的输出是否使用彩色。

将此属性设置为 ``true`` 等效于使用 ``--colors=auto`` 命令行选项。

将此属性设置为 ``false`` 等效于使用 ``--colors=never`` 命令行选项。

.. _appendixes.configuration.phpunit.columns:

``columns`` 属性
-------------------------

可能值：整数或字符串 ``max``\ （默认值：\ ``80``）

此属性配置进度输出所用的列数。

如果定义了 ``max`` 作为其值，则列数为当前终端的最大值。

.. _appendixes.configuration.phpunit.convertDeprecationsToExceptions:

``convertDeprecationsToExceptions`` 属性
-------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置是否将被测代码所触发的 ``E_DEPRECATED`` 和 ``E_USER_DEPRECATED`` 事件转换为异常（并将此测试标记为错误）。

.. _appendixes.configuration.phpunit.convertErrorsToExceptions:

``convertErrorsToExceptions`` 属性
-------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置是否将被测代码所触发的 ``E_ERROR`` 和 ``E_USER_ERROR`` 事件转换为异常（并将此测试标记为错误）。

.. _appendixes.configuration.phpunit.convertNoticesToExceptions:

``convertNoticesToExceptions`` 属性
--------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置是否将被测代码所触发的 ``E_STRICT``、``E_NOTICE`` 和 ``E_USER_NOTICE`` 事件转换为异常（并将此测试标记为错误）。

.. _appendixes.configuration.phpunit.convertWarningsToExceptions:

``convertWarningsToExceptions`` 属性
---------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置是否将被测代码所触发的 ``E_WARNING`` 和 ``E_USER_WARNING`` 事件转换为异常（并将此测试标记为错误）。

.. _appendixes.configuration.phpunit.forceCoversAnnotation:

``forceCoversAnnotation`` 属性
---------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置的是在没有 :ref:`@covers <appendixes.annotations.covers>` 标注时，测试是否要标记为有风险（参见\ :ref:`risky-tests.unintentionally-covered-code`）。

.. _appendixes.configuration.phpunit.printerClass:

``printerClass`` 属性
------------------------------

默认值：``PHPUnit\TextUI\ResultPrinter``

此属性配置的是一个类名，这个类是 ``PHPUnit\TextUI\ResultPrinter`` 或者是扩展自 ``PHPUnit\TextUI\ResultPrinter`` 的类。会使用此类的一个实例对象来打印进度和测试结果。

.. _appendixes.configuration.phpunit.printerFile:

``printerFile`` 属性
-----------------------------

此属性可用于配置 ``printerClass`` 所配置的类的声明所在源代码文件，以防此类无法自动加载的情况。

.. _appendixes.configuration.phpunit.processIsolation:

``processIsolation`` 属性
----------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当对每个测试都用单独的 PHP 进程来运行以提高隔离度。

.. _appendixes.configuration.phpunit.stopOnError:

``stopOnError`` 属性
-----------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“错误（error）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.stopOnFailure:

``stopOnFailure`` 属性
-------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“失败（failure）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.stopOnIncomplete:

``stopOnIncomplete`` 属性
----------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“未完成（incomplete）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.stopOnRisky:

``stopOnRisky`` 属性
-----------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“有风险（risky）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.stopOnSkipped:

``stopOnSkipped`` 属性
-------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“跳过（skipped）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.stopOnWarning:

``stopOnWarning`` 属性
-------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“警告（warning）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.stopOnDefect:

``stopOnDefect`` 属性
------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应当在第一个以“错误（error）”、“失败（failure）”，“有风险（risky）”或“警告（warning）”状态结束的测试之后停止测试套件的执行。

.. _appendixes.configuration.phpunit.failOnRisky:

``failOnRisky`` 属性
-----------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置的是在所有测试都成功但有部分测试被标记为有风险（risky）时，PHPUnit 测试执行器在退出时是否应当使用指示失败的 sell 退出码。

.. _appendixes.configuration.phpunit.failOnWarning:

``failOnWarning`` 属性
-------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置的是在所有测试都成功但有部分测试有警告（warning）时，PHPUnit 测试执行器在退出时是否应当使用指示失败的 sell 退出码。

.. _appendixes.configuration.phpunit.beStrictAboutChangesToGlobalState:

``beStrictAboutChangesToGlobalState`` 属性
---------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置当被测代码（或测试代码）操纵全局状态时，PHPUnit 是否应将测试标记为有风险（risky）。

.. _appendixes.configuration.phpunit.beStrictAboutOutputDuringTests:

``beStrictAboutOutputDuringTests`` 属性
------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置当被测代码（或测试代码）打印输出时，PHPUnit 是否应将测试标记为有风险（risky）。

.. _appendixes.configuration.phpunit.beStrictAboutResourceUsageDuringSmallTests:

``beStrictAboutResourceUsageDuringSmallTests`` 属性
------------------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置的是当标注为 ``@small`` 的测试调用对 ``resource`` 变量进行操作的 PHP 内建函数或方法时，PHPUnit 是否应将其标记为有风险（risky）。

.. _appendixes.configuration.phpunit.beStrictAboutTestsThatDoNotTestAnything:

``beStrictAboutTestsThatDoNotTestAnything`` 属性
---------------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置在测试未执行任何断言（预期也算在内）时，PHPUnit 是否应将其标记为有风险（risky）。

.. _appendixes.configuration.phpunit.beStrictAboutTodoAnnotatedTests:

``beStrictAboutTodoAnnotatedTests`` 属性
-------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置在测试有 ``@todo`` 标注时，PHPUnit 是否应将其标记为有风险。

.. _appendixes.configuration.phpunit.beStrictAboutCoversAnnotation:

``beStrictAboutCoversAnnotation`` 属性
-----------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置在测试执行未使用 ``@covers`` 或 ``@uses`` 指定的代码时，PHPUnit 是否应将其标记为有风险。

.. _appendixes.configuration.phpunit.enforceTimeLimit:

``enforceTimeLimit`` 属性
----------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应实施时间限制。

.. _appendixes.configuration.phpunit.defaultTimeLimit:

``defaultTimeLimit`` 属性
----------------------------------

可能值：整数（默认值：``0``）

此属性配置默认时间限制（以秒为单位）。

.. _appendixes.configuration.phpunit.timeoutForSmallTests:

``timeoutForSmallTests`` 属性
--------------------------------------

可能值：整数（默认值：``1``）

此属性配置标注为 ``@small`` 的测试的时间限制（以秒为单位）。

.. _appendixes.configuration.phpunit.timeoutForMediumTests:

``timeoutForMediumTests`` 属性
---------------------------------------

可能值：整数（默认值：``10``）

此属性配置标注为 ``@medium`` 的测试的时间限制（以秒为单位）。

.. _appendixes.configuration.phpunit.timeoutForLargeTests:

``timeoutForLargeTests`` 属性
--------------------------------------

可能值：整数（默认值：``60``）

此属性配置标注为 ``@large`` 的测试的时间限制（以秒为单位）。

.. _appendixes.configuration.phpunit.testSuiteLoaderClass:

``testSuiteLoaderClass`` 属性
--------------------------------------

默认值：``PHPUnit\Runner\StandardTestSuiteLoader``

此属性配置的是一个类名，这个类必须实现 ``PHPUnit\Runner\TestSuiteLoader`` 接口。会用此类的一个实例对象来加载测试套件。

.. _appendixes.configuration.phpunit.testSuiteLoaderFile:

``testSuiteLoaderFile`` 属性
-------------------------------------

此属性可用于配置 ``testSuiteLoaderClass`` 所配置的类的声明所在源代码文件，以防此类无法自动加载的情况。

.. _appendixes.configuration.phpunit.defaultTestSuite:

``defaultTestSuite`` 属性
----------------------------------

此属性配置默认测试套件名称。

.. _appendixes.configuration.phpunit.verbose:

``verbose`` 属性
-------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应打印更详细的输出。

.. _appendixes.configuration.phpunit.stderr:

``stderr`` 属性
------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置 PHPUnit 是否应将其输出打印到 ``stderr`` 而不是 ``stdout``。

.. _appendixes.configuration.phpunit.reverseDefectList:

``reverseDefectList`` 属性
-----------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应以逆序打印不成功的测试。

.. _appendixes.configuration.phpunit.registerMockObjectsFromTestArgumentsRecursively:

``registerMockObjectsFromTestArgumentsRecursively`` 属性
-----------------------------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应对用 ``@depends`` 批注从一个测试传递到另一测试的数组和对象图进行递归扫描以查找仿件对象。

.. _appendixes.configuration.phpunit.extensionsDirectory:

``extensionsDirectory`` 属性
-------------------------------------

当使用 ``phpunit.phar`` 时，此属性可用于配置一个目录，这个目录中的所有 * .phar 文件都会被加载作为PHPUnit 测试执行器的扩展。

.. _appendixes.configuration.phpunit.executionOrder:

``executionOrder`` 属性
--------------------------------

可能值：``default``、``defects``、``depends``、``no-depends``、``duration``、``random``、``reverse``、``size``

可以使用多个值。这些值之间需要用 ``,`` 分割。

此属性配置测试的执行顺序。

.. _appendixes.configuration.phpunit.resolveDependencies:

``resolveDependencies`` 属性
-------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

此属性配置是否应解决测试之间（用 ``@depends`` 注解表示）的依赖关系。

.. _appendixes.configuration.phpunit.testdox:

``testdox`` 属性
-------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否以 TestDox 格式打印输出。

``noInteraction`` 属性
-------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置在使用 TestDox 格式时，比如说，进度是否应当是动画。

.. _appendixes.configuration.testsuites:

``<testsuites>`` 元素
############################

父元素：``<phpunit>``

此元素是一个或多个 ``<testsuite>`` 元素的根元素，用于配置需要执行的测试。

.. _appendixes.configuration.testsuites.testsuite:

``<testsuite>`` 元素
---------------------------

父元素：``<testsuites>``

``<testsuite>`` 元素必须拥有 ``name`` 属性，可以有一个或多个 ``<directory>`` 及 ``<file>`` 子元素，分别代表需要搜索测试的目录及元素。

.. code-block:: xml

    <testsuites>
      <testsuite name="unit">
        <directory>tests/unit</directory>
      </testsuite>

      <testsuite name="integration">
        <directory>tests/integration</directory>
      </testsuite>

      <testsuite name="edge-to-edge">
        <directory>tests/edge-to-edge</directory>
      </testsuite>
    </testsuites>

可以用 ``phpVersion`` 和 ``phpVersionOperator`` 属性来指定 PHP 版本需求：

.. code-block:: xml

    <testsuites>
      <testsuite name="unit">
        <directory phpVersion="8.0.0" phpVersionOperator=">=">tests/unit</directory>
      </testsuite>
    </testsuites>

在上面的示例中，仅当 PHP 版本至少为 8.0.0 时，才会将 ``tests/unit`` 目录中的测试添加到测试套件中。``phpVersionOperator`` 属性是可选的，默认为 ``>=``。

.. _appendixes.configuration.coverage:

``<coverage>`` 元素
##########################

父元素：``<phpunit>``

``<coverage>`` 元素及其子元素可用于配置代码覆盖率：

.. code-block:: xml

    <coverage cacheDirectory="/path/to/directory"
              includeUncoveredFiles="true"
              processUncoveredFiles="true"
              pathCoverage="false"
              ignoreDeprecatedCodeUnits="true"
              disableCodeCoverageIgnore="true">
        <!-- ... -->
    </coverage>

``cacheDirectory`` 属性
--------------------------------

可能值：字符串

当收集并处理代码覆盖率数据时，将执行静态代码分析以改善有关覆盖代码的推理。这是一项昂贵的操作，而其结果可以缓存。设置 ``cacheDirectory`` 属性后，静态分析结果将缓存在指定目录中。

``includeUncoveredFiles`` 属性
---------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``true``）

当设置为 ``true`` 时，所有配置为代码覆盖率分析需要考虑的源代码文件都将包含在代码覆盖率报告中。这包括测试运行时并未执行的源代码文件。

``processUncoveredFiles`` 属性
---------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

当设置为 ``true`` 时，所有配置为代码覆盖率分析需要考虑的源代码文件都将被处理。这包括测试运行时并未执行的源代码文件。

``ignoreDeprecatedCodeUnits`` 属性
-------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置代码覆盖率是否应忽略标注为 ``@deprecated`` 的代码单元。

``pathCoverage`` 属性
------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

设置为 ``false`` 时，将仅收集、处理和报告行覆盖率数据。

设置为 ``true`` 时，将收集、处理和报告行覆盖率、分支覆盖率和路径覆盖率数据。这需要支持路径覆盖率的代码覆盖率驱动程序。目前只有 Xdebug 实现了路径覆盖率。

``disableCodeCoverageIgnore`` 属性
-------------------------------------------

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

此属性配置是否应忽略 ``@codeCoverageIgnore*`` 批注。

.. _appendixes.configuration.coverage.include:

The ``<include>`` Element
-------------------------

父元素：``<coverage>``

配置要包含在代码覆盖率报告中的文件集合。

.. code-block:: xml

    <include>
        <directory suffix=".php">src</directory>
    </include>

上面示例指示 PHPUnit 在代码覆盖率报告中要包含在 ``src`` 目录及其子目录中的所有带 ``.php`` 后缀的源代码文件。


.. _appendixes.configuration.coverage.exclude:

``<exclude>`` 元素
-------------------------

父元素：``<coverage>``

配置要从代码覆盖率报告中排除的文件集合。

.. code-block:: xml

    <include>
        <directory suffix=".php">src</directory>
    </include>

    <exclude>
        <directory suffix=".php">src/generated</directory>
        <file>src/autoload.php</file>
    </exclude>

上面示例指示 PHPUnit 在代码覆盖率报告中要包含在 ``src`` 目录及其子目录中的所有带 ``.php`` 后缀的源代码文件，但要排除 ``src/generated`` 目录及其子目录中的所有带 ``.php`` 后缀的文件以及 ``src/autoload.php`` 文件。


.. _appendixes.configuration.coverage.directory:

``<directory>`` 元素
---------------------------

父元素：``<include>``、``<exclude>``

配置要包含在代码覆盖率报告中或从代码覆盖率报告中排除的目录及其子目录。

``prefix`` 属性
************************

可能值：字符串

配置基于前缀的过滤器，该过滤器将应用于目录及其子目录中的文件名。

``suffix`` 属性
************************

可能值：string（默认值：``'.php'``）

配置基于后缀的过滤器，该过滤器将应用于目录及其子目录中的文件名。

``phpVersion`` 属性
****************************

可能值：字符串

配置基于用来运行当前 PHPUnit 进程的 PHP 运行时版本的过滤器。

``phpVersionOperator`` 属性
************************************

可能值：``'<'``、``'lt'``、``'<='``、``'le'``、``'>'``、``'gt'``、``'>='``、``'ge'``、``'=='``、``'='``、``'eq'``, ``'!='``, ``'<>'``, ``'ne'``\ （默认值：``'>='``）

配置基于用来运行当前 PHPUnit 进程的 PHP 运行时版本的过滤器的 ``version_compare()`` 操作所用的比较运算符。


.. _appendixes.configuration.coverage.file:

``<file>`` 元素
----------------------

父元素：``<include>``、``<exclude>``

配置要包含在代码覆盖率报告中或从代码覆盖率报告中排除的文件。


.. _appendixes.configuration.coverage.report:

``<report>`` 元素
------------------------

父元素：``<coverage>``

配置要生成的代码覆盖率报告。

.. code-block:: xml

    <report>
        <clover outputFile="clover.xml"/>
        <crap4j outputFile="crap4j.xml" threshold="50"/>
        <html outputDirectory="html-coverage" lowUpperBound="50" highLowerBound="90"/>
        <php outputFile="coverage.php"/>
        <text outputFile="coverage.txt" showUncoveredFiles="false" showOnlySummary="true"/>
        <xml outputDirectory="xml-coverage"/>
    </report>


.. _appendixes.configuration.coverage.report.clover:

``<clover>`` 元素
************************

父元素：``<report>``

配置 Clover XML 格式的代码覆盖率报告。

``outputFile`` 属性
++++++++++++++++++++++++++++

可能值：字符串

Clover XML 报告写入的文件。


.. _appendixes.configuration.coverage.report.crap4j:

``<crap4j>`` 元素
************************

父元素：``<report>``

配置 Crap4J XML 格式的代码覆盖率报告。

``outputFile`` 属性
++++++++++++++++++++++++++++

可能值：字符串

Crap4J XML 报告写入的文件。

``threshold`` 属性
+++++++++++++++++++++++++++

可能值：integer（默认值：``50``）


.. _appendixes.configuration.coverage.report.html:

``<html>`` 元素
**********************

父元素：``<report>``

配置 HTML 格式的代码覆盖率报告。

``outputDirectory`` 属性
+++++++++++++++++++++++++++++++++

HTML 报告写入的目录。

``lowUpperBound`` 属性
+++++++++++++++++++++++++++++++

可能值：integer（默认值：``50``）

应当被视为“低覆盖率”的上限。

``highLowerBound`` 属性
++++++++++++++++++++++++++++++++

可能值：整数（默认值：``90``）

应当被视为“高覆盖率”的下限。


.. _appendixes.configuration.coverage.report.php:

``<php>`` 元素
*********************

父元素：``<report>``

配置 PHP 格式的代码覆盖率报告。

``outputFile`` 属性
++++++++++++++++++++++++++++

可能值：字符串

PHP 报告写入的文件。


.. _appendixes.configuration.coverage.report.text:

``<text>`` 元素
**********************

父元素：``<report>``

配置文本格式的代码覆盖率报告。

``outputFile`` 属性
++++++++++++++++++++++++++++

可能值：字符串

文本报告写入的文件。

``showUncoveredFiles`` 属性
++++++++++++++++++++++++++++++++++++

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）

``showOnlySummary`` 属性
+++++++++++++++++++++++++++++++++

可能值：``true`` 或 ``false``\ （默认值：\ ``false``）


.. _appendixes.configuration.coverage.report.xml:

``<xml>`` 元素
*********************

父元素：``<report>``

配置 PHPUnit XML 格式的代码覆盖率报告。

``outputDirectory`` 属性
+++++++++++++++++++++++++++++++++

可能值：字符串

PHPUnit XML 报告写入的目录。


.. _appendixes.configuration.logging:

``<logging>`` 元素
#########################

父元素：``<phpunit>``

``<logging>`` 元素及其子元素可用于配置测试执行期间的日志记录。

.. code-block:: xml

    <logging>
        <junit outputFile="junit.xml"/>
        <teamcity outputFile="teamcity.txt"/>
        <testdoxHtml outputFile="testdox.html"/>
        <testdoxText outputFile="testdox.txt"/>
        <testdoxXml outputFile="testdox.xml"/>
        <text outputFile="logfile.txt"/>
    </logging>


.. _appendixes.configuration.logging.junit:

``<junit>`` 元素
-----------------------

父元素：``<logging>``

配置 JUnit XML 格式的测试结果日志文件。

``outputFile`` 属性
****************************

可能值：字符串

JUnit XML 格式的测试结果日志写入的文件。


.. _appendixes.configuration.logging.teamcity:

``<teamcity>`` 元素
--------------------------

父元素：``<logging>``

配置 TeamCity 格式的测试结果日志文件。

``outputFile`` 属性
****************************

可能值：字符串

TeamCity 格式的测试结果日志写入的文件。


.. _appendixes.configuration.logging.testdoxHtml:

``<testdoxHtml>`` 元素
-----------------------------

父元素：``<logging>``

配置 TestDox HTML 格式的测试结果日志文件。

``outputFile`` 属性
****************************

可能值：字符串

TestDox HTML 格式的测试结果日志写入的文件。


.. _appendixes.configuration.logging.testdoxText:

``<testdoxText>`` 元素
-----------------------------

父元素：``<logging>``

配置 TestDox 文本格式的测试结果日志文件。

``outputFile`` 属性
****************************

可能值：字符串

TestDox 文本格式的测试结果日志写入的文件。


.. _appendixes.configuration.logging.testdoxXml:

``<testdoxXml>`` 元素
----------------------------

父元素：``<logging>``

配置 TestDox XML 格式的测试结果日志文件。

``outputFile`` 属性
****************************

可能值：字符串

TestDox XML 格式的测试结果日志写入的文件。


.. _appendixes.configuration.logging.text:

``<text>`` 元素
----------------------

父元素：``<logging>``

配置文本格式的测试结果日志文件。

``outputFile`` 属性
****************************

可能值：字符串

文本格式的测试结果日志写入的文件。


.. _appendixes.configuration.groups:

``<groups>`` 元素
########################

父元素：``<phpunit>``

``<groups>`` 元素及其 ``<include>``、``<exclude>``、``<group>`` 子元素用于从带有 ``@group`` 标注（相关文档参见 :ref:`appendixes.annotations.group`）的测试中选择需要运行（或不运行）的分组。

.. code-block:: xml

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

上面的示例等效于以 ``--group name --exclude-group name`` 调用  PHPUnit 测试执行器。

.. _appendixes.configuration.testdoxGroups:

``<testdoxGroups>`` 元素
###############################

父元素：``<phpunit>``

... <待完成> ...

.. _appendixes.configuration.listeners:

``<listeners>`` 元素
###########################

父元素：``<phpunit>``

``<listeners>`` 元素及其 ``<listener>`` 子元素可用于在测试执行期间附加额外的测试监听器。

.. _appendixes.configuration.listeners.listener:

``<listener>`` 元素
--------------------------

父元素：``<listeners>``

.. code-block:: xml

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

以上 XML 配置对应于将 ``$listener`` 对象（见下文）附到测试执行过程上。

.. code-block:: php

    $listener = new MyListener(
        ['Sebastian'],
        22,
        'April',
        19.78,
        null,
        new stdClass
    );

.. admonition:: 注

    请注意，``PHPUnit\Framework\TestListener`` 接口已废弃，将在以后删除。应该改用测试执行器扩展来替代测试监听器。

.. _appendixes.configuration.extensions:

``<extensions>`` 元素
############################

父元素：``<phpunit>``

``<extensions>`` 元素及其 ``<extension>`` 子元素可用于注册测试执行器扩展。

.. _appendixes.configuration.extensions.extension:

``<extension>`` 元素
---------------------------

父元素：``<extensions>``

.. code-block:: xml

    <extensions>
        <extension class="Vendor\MyExtension"/>
    </extensions>

.. _appendixes.configuration.extensions.extension.arguments:

``<arguments>`` 元素
***************************

父元素：``<extension>``

``<arguments>`` 元素可用于配置单个 ``<extension>``。

接受类型的元素的列表，该列表用于配置各个扩展。参数会按照在配置中定义的顺序被传递给扩展类的 ``__constructor`` 方法。

可用类型：

- ``<boolean>``
- ``<integer>``
- ``<string>``
- ``<double>``\ （浮点数）
- ``<array>``
- ``<object>``

.. code-block:: xml

    <extension class="Vendor\MyExtension">
        <arguments>
            <integer>1</integer>
            <integer>2</integer>
            <integer>3</integer>
            <string>hello world</string>
            <boolean>true</boolean>
            <double>1.23</double>
            <array>
                <element index="0">
                    <string>value1</string>
                </element>
                <element index="1">
                    <string>value2</string>
                </element>
            </array>
            <object class="Vendor\MyPhpClass">
                <string>constructor arg 1</string>
                <string>constructor arg 2</string>
            </object>
        </arguments>
    </extension>


.. _appendixes.configuration.php:

``<php>`` 元素
#####################

父元素：``<phpunit>``

``<php>`` 元素及其子元素用于配置 PHP 设置、常量以及全局变量。同时也可用于向 ``include_path`` 前面添加内容。

.. _appendixes.configuration.php.includePath:

``<includePath>`` 元素
-----------------------------

父元素：``<php>``

此元素可用于向 ``include_path`` 前面添加一个路径。

.. _appendixes.configuration.php.ini:

``<ini>`` 元素
---------------------

父元素：``<php>``

此元素可用于设置 PHP 配置。

.. code-block:: xml

    <php>
      <ini name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    ini_set('foo', 'bar');

.. _appendixes.configuration.php.const:

``<const>`` 元素
-----------------------

父元素：``<php>``

此元素可用于设置全局常数。

.. code-block:: xml

    <php>
      <const name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    define('foo', 'bar');

.. _appendixes.configuration.php.var:

`<var>`` 元素
---------------------

父元素：``<php>``

此元素可用于设置全局变量。

.. code-block:: xml

    <php>
      <var name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $GLOBALS['foo'] = 'bar';

.. _appendixes.configuration.php.env:

``<env>`` 元素
---------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_ENV`` 中设置一个值。

.. code-block:: xml

    <php>
      <env name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_ENV['foo'] = 'bar';

默认情况下，如果环境变量已经存在，则不会覆盖之。要强制覆盖已存在的变量，用 ``force`` 属性：

.. code-block:: xml

    <php>
      <env name="foo" value="bar" force="true"/>
    </php>

.. _appendixes.configuration.php.get:

``<get>`` 元素
---------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_GET`` 中设置一个值。

.. code-block:: xml

    <php>
      <get name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_GET['foo'] = 'bar';

.. _appendixes.configuration.php.post:

``<post>`` 元素
----------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_POST`` 中设置一个值。

.. code-block:: xml

    <php>
      <post name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_POST['foo'] = 'bar';

.. _appendixes.configuration.php.cookie:

``<cookie>`` 元素
------------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_COOKIE`` 中设置一个值。

.. code-block:: xml

    <php>
      <cookie name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_COOKIE['foo'] = 'bar';

.. _appendixes.configuration.php.server:

``<server>`` 元素
------------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_SERVER`` 中设置一个值。

.. code-block:: xml

    <php>
      <server name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_SERVER['foo'] = 'bar';

.. _appendixes.configuration.php.files:

``<files>`` 元素
-----------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_FILES`` 中设置一个值。

.. code-block:: xml

    <php>
      <files name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_FILES['foo'] = 'bar';

.. _appendixes.configuration.php.request:

``<request>`` 元素
-------------------------

父元素：``<php>``

此元素可用于在超全局数组 ``$_REQUEST`` 中设置一个值。

.. code-block:: xml

    <php>
      <request name="foo" value="bar"/>
    </php>

以上 XML 配置对应于如下 PHP 代码：

.. code-block:: php

    $_REQUEST['foo'] = 'bar';

