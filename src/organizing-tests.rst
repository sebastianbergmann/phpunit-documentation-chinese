

.. _organizing-tests:

====
组织测试
====

PHPUnit 的目标之一是测试应当可组合：我们希望能将任意数量的测试以任意组合方式运行，例如，整个项目的所有测试，或者项目中的某个组件内的所有类的测试，又或者仅仅某单个类的测试。

PHPUnit 支持好几种不同的方式来组织测试以及将它们编排组合成测试套件。本章介绍了最常用的方法。

.. _organizing-tests.filesystem:

用文件系统来编排测试套件
############

编排测试套件的各种方式中，最简单的大概就是把所有测试用例源文件放在一个测试目录中。通过对测试目录进行递归遍历，PHPUnit 能自动发现并运行测试。

现在来看看 `sebastianbergmann/money <http://github.com/sebastianbergmann/money/>`_ 这个库的测试套件。在这个项目的目录结构中，可以看到 :file:`tests` 目录下的测试用例类镜像了 :file:`src` 目录下被测系统(SUT, System Under Test)的包(package)与类(class)的结构：

.. code-block:: bash

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

要运行这个库的全部测试，只要将 PHPUnit 命令行测试执行器指向测试目录即可：

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests
    PHPUnit 7.0.0 by Sebastian Bergmann.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

.. admonition:: Note

   当 PHPUnit 命令行测试执行器指向一个目录时，它会在目录下查找 :file:`*Test.php` 文件。

如果只想运行在 ``CurrencyTest`` 文件中的 :file:`tests/CurrencyTest.php` 测试用例类中声明的测试，可以使用如下命令：

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests/CurrencyTest
    PHPUnit 7.0.0 by Sebastian Bergmann.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

如果想要对运行哪些测试有更细粒度的控制，可以使用 ``--filter`` 选项：

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit 7.0.0 by Sebastian Bergmann.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

.. admonition:: Note

   这种方法的缺点是无法控制测试的运行顺序。这可能导致测试的依赖关系方面的问题，参见 :ref:`writing-tests-for-phpunit.test-dependencies`。在下一节中，可以看到如何用 XML 配置文件来明确指定测试的执行顺序。

.. _organizing-tests.xml-configuration:

用 XML 配置来编排测试套件
###############

PHPUnit的 XML 配置文件（:ref:`appendixes.configuration`）也可以用于编排测试套件。:numref:`organizing-tests.xml-configuration.examples.phpunit.xml`展示了一个最小化的 :file:`phpunit.xml` 例子，它将在递归遍历 :file:`tests` 时添加所有在 :file:`*Test.php` 文件中找到的 ``*Test`` 类。

.. code-block:: php
    :caption: 用 XML 配置来编排测试套件
    :name: organizing-tests.xml-configuration.examples.phpunit.xml

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

如果 :file:`phpunit.xml` 或 :file:`phpunit.xml.dist` （按此顺序）存在于当前工作目录并且*未*使用 ``--configuration``，将自动从此文件中读取配置。

可以明确指定测试的执行顺序：

.. code-block:: php
    :caption: 用 XML 配置来编排测试套件
    :name: organizing-tests.xml-configuration.examples.phpunit.xml2

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>


