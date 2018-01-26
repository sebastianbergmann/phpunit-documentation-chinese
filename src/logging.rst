

.. _logging:

==============
Logging （日志记录）
==============

PHPUnit 可以生成几种类型的日志文件。

.. _logging.xml:

测试结果 (XML)
##########

PHPUnit 所生成的测试结果 XML 日志文件是基于 `JUnit task for Apache Ant <http://ant.apache.org/manual/Tasks/junit.html>`_ 所使用的 XML 日志的。下面的例子展示了 ``ArrayTest`` 中的测试所生成的 XML 日志文件：

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="ArrayTest"
                 file="/home/sb/ArrayTest.php"
                 tests="2"
                 assertions="2"
                 failures="0"
                 errors="0"
                 time="0.016030">
        <testcase name="testNewArrayIsEmpty"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="6"
                  assertions="1"
                  time="0.008044"/>
        <testcase name="testArrayContainsAnElement"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="15"
                  assertions="1"
                  time="0.007986"/>
      </testsuite>
    </testsuites>

以下 XML 日志文件是由名为 ``FailureErrorTest`` 的测试用例类中的两个测试 ``testFailure`` 和 ``testError`` 所生成的，展示了失败和错误是如何表示的：

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="FailureErrorTest"
                 file="/home/sb/FailureErrorTest.php"
                 tests="2"
                 assertions="1"
                 failures="1"
                 errors="1"
                 time="0.019744">
        <testcase name="testFailure"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="6"
                  assertions="1"
                  time="0.011456">
          <failure type="PHPUnit_Framework_ExpectationFailedException">
    testFailure(FailureErrorTest)
    Failed asserting that &lt;integer:2&gt; matches expected value &lt;integer:1&gt;.

    /home/sb/FailureErrorTest.php:8
    </failure>
        </testcase>
        <testcase name="testError"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="11"
                  assertions="0"
                  time="0.008288">
          <error type="Exception">testError(FailureErrorTest)
    Exception:

    /home/sb/FailureErrorTest.php:13
    </error>
        </testcase>
      </testsuite>
    </testsuites>

.. _logging.codecoverage.xml:

代码覆盖率 (XML)
###########

PHPUnit 所生成的 XML 格式代码覆盖率信息日志记录不严格地基于 `Clover <http://www.atlassian.com/software/clover/>`_.  所使用的 XML 日志的。下面的例子展示了 ``BankAccountTest`` 中的测试所生成的 XML 日志文件：

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <coverage generated="1184835473" phpunit="3.6.0">
      <project name="BankAccountTest" timestamp="1184835473">
        <file name="/home/sb/BankAccount.php">
          <class name="BankAccountException">
            <metrics methods="0" coveredmethods="0" statements="0"
                     coveredstatements="0" elements="0" coveredelements="0"/>
          </class>
          <class name="BankAccount">
            <metrics methods="4" coveredmethods="4" statements="13"
                     coveredstatements="5" elements="17" coveredelements="9"/>
          </class>
          <line num="77" type="method" count="3"/>
          <line num="79" type="stmt" count="3"/>
          <line num="89" type="method" count="2"/>
          <line num="91" type="stmt" count="2"/>
          <line num="92" type="stmt" count="0"/>
          <line num="93" type="stmt" count="0"/>
          <line num="94" type="stmt" count="2"/>
          <line num="96" type="stmt" count="0"/>
          <line num="105" type="method" count="1"/>
          <line num="107" type="stmt" count="1"/>
          <line num="109" type="stmt" count="0"/>
          <line num="119" type="method" count="1"/>
          <line num="121" type="stmt" count="1"/>
          <line num="123" type="stmt" count="0"/>
          <metrics loc="126" ncloc="37" classes="2" methods="4" coveredmethods="4"
                   statements="13" coveredstatements="5" elements="17"
                   coveredelements="9"/>
        </file>
        <metrics files="1" loc="126" ncloc="37" classes="2" methods="4"
                 coveredmethods="4" statements="13" coveredstatements="5"
                 elements="17" coveredelements="9"/>
      </project>
    </coverage>

.. _logging.codecoverage.text:

代码覆盖率 (TEXT)
############

以易于常人了解(human-readable)的格式生成代码覆盖率，输出到命令行或保存成文本文件。这个输出格式旨在为工作于少量类时提供快捷的覆盖情况概览。对于更大的项目，这个输出有助于对项目的覆盖情况有一个快速的概览，或者配合 ``--filter`` 功能使用也会很有用。若从命令行调用并且写入到 ``php://stdout``，``--colors`` 设置会非常好用。从命令行调用时，写入到标准输出是默认选项。默认情况下，只会显示至少有一行被覆盖的文件。这只能通过 XML 配置选项 ``showUncoveredFiles`` 来改变。参见 :ref:`appendixes.configuration.logging`。默认情况下，在详细报告中会显示所有文件以及它们的覆盖情况。这可以通过 XML 配置选项 ``showOnlySummary`` 来改变。.


