

.. _appendixes.assertions:

==========
断言
==========

本附录列举可用的各种断言方法。

.. _appendixes.assertions.static-vs-non-static-usage-of-assertion-methods:

断言方法的用法：静态 vs. 非静态
################################################

PHPUnit 的各个断言是在 ``PHPUnit\Framework\Assert`` 中实现的。``PHPUnit\Framework\TestCase`` 则继承于 ``PHPUnit\Framework\Assert``。

各个断言方法均声明为 ``static``，可以从任何上下文以类似于 ``PHPUnit\Framework\Assert::assertTrue()`` 的方式调用，或者也可以用类似于 ``$this->assertTrue()`` 或 ``self::assertTrue()`` 的方式在扩展自 ``PHPUnit\Framework\TestCase`` 的类内调用。甚至可以用全局函数封装，例如 ``assertTrue()``。


有个常见的疑问——对于那些 PHPUnit 的新手尤甚——是究竟应该用诸如 ``$this->assertTrue()`` 还是诸如 ``self::assertTrue()`` 这样的形式来调用断言才是“正确的方式”？简而言之：没有正确方式。同时，也没有错误方式。这基本上是个人喜好问题。

对于大多数人而言，由于测试方法是在测试对象上调用，因此用 ``$this->assertTrue()`` 会“觉的更正确”。然而请记住断言方法是声明为 ``static`` 的，这使其可以（重）用于测试对象的作用域之外。最后，全局函数封装让开发者能再少打一些字（用 ``assertTrue()`` 代替 ``$this->assertTrue()`` 或者 ``self::assertTrue()``）。

.. _appendixes.assertions.assertArrayHasKey:

assertArrayHasKey()
###################

``assertArrayHasKey(mixed $key, array $array[, string $message = ''])``

当 ``$array`` 不包含 ``$key`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertArrayNotHasKey()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertArrayHasKey() 的用法
    :name: appendixes.assertions.assertArrayHasKey.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ArrayHasKeyTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }

.. parsed-literal::

    $ phpunit ArrayHasKeyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasAttribute:

assertClassHasAttribute()
#########################

``assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])``

当 ``$className::attributeName`` 不存在时报告错误，错误讯息由 ``$message`` 指定。

``assertClassNotHasAttribute()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertClassHasAttribute() 的用法
    :name: appendixes.assertions.assertClassHasAttribute.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ClassHasAttributeTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }

.. parsed-literal::

    $ phpunit ClassHasAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasStaticAttribute:

assertClassHasStaticAttribute()
###############################

``assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])``

当 ``$className::attributeName`` 不存在时报告错误，错误讯息由 ``$message`` 指定。

``assertClassNotHasStaticAttribute()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertClassHasStaticAttribute() 的用法
    :name: appendixes.assertions.assertClassHasStaticAttribute.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }

.. parsed-literal::

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContains:

assertContains()
################

``assertContains(mixed $needle, iterable $haystack[, string $message = ''])``

当 ``$needle`` 不是 ``$haystack`` 的元素时报告错误，错误讯息由 ``$message`` 指定。

``assertNotContains()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertContains() 的用法
    :name: appendixes.assertions.assertContains.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ContainsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }

.. parsed-literal::

    $ phpunit ContainsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringContainsString()
############################

``assertStringContainsString(string $needle, string $haystack[, string $message = ''])``

当 ``$needle`` 不是 ``$haystack`` 的子字符串时报告错误，错误讯息由 ``$message`` 指定。

``assertStringNotContainsString()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertStringContainsString() 的用法
    :name: appendixes.assertions.assertStringContainsString.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringContainsStringTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringContainsString('foo', 'bar');
        }
    }

.. parsed-literal::

    $ phpunit StringContainsStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 37 ms, Memory: 6.00 MB

    There was 1 failure:

    1) StringContainsStringTest::testFailure
    Failed asserting that 'bar' contains "foo".

    /home/sb/StringContainsStringTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringContainsStringIgnoringCase()
########################################

``assertStringContainsStringIgnoringCase(string $needle, string $haystack[, string $message = ''])``

当 ``$needle`` 不是 ``$haystack`` 的子字符串时报告错误，错误讯息由 ``$message`` 指定。

在 ``$haystack`` 中搜索 ``$needle`` 时，忽略大小写差异。

``assertStringNotContainsStringIgnoringCase()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertStringContainsStringIgnoringCase() 的用法
    :name: appendixes.assertions.assertStringContainsStringIgnoringCase.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringContainsStringIgnoringCaseTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringContainsStringIgnoringCase('foo', 'bar');
        }
    }

.. parsed-literal::

    $ phpunit StringContainsStringIgnoringCaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 40 ms, Memory: 6.00 MB

    There was 1 failure:

    1) StringContainsStringTest::testFailure
    Failed asserting that 'bar' contains "foo".

    /home/sb/StringContainsStringIgnoringCaseTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, iterable $haystack[, boolean $isNativeType = null, string $message = ''])``

当 ``$haystack`` 并非仅包含类型为 ``$type`` 的变量时报告错误，错误讯息由 ``$message`` 指定。

``$isNativeType`` 是一个标志，用来表明 ``$type`` 是否是原生 PHP 类型。

``assertNotContainsOnly()`` 是与之相反的断言，并接受相同的参数。

.. code-block:: php
    :caption: assertContainsOnly() 的用法
    :name: appendixes.assertions.assertContainsOnly.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ContainsOnlyTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }

.. parsed-literal::

    $ phpunit ContainsOnlyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnlyInstancesOf:

assertContainsOnlyInstancesOf()
###############################

``assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])``

当 ``$haystack`` 并非仅包含类 ``$classname`` 的实例时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertContainsOnlyInstancesOf() 的用法
    :name: appendixes.assertions.assertContainsOnlyInstancesOf.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }

.. parsed-literal::

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertCount:

assertCount()
#############

``assertCount($expectedCount, $haystack[, string $message = ''])``

当 ``$haystack`` 中的元素数量不是 ``$expectedCount`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertNotCount()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertCount() 的用法
    :name: appendixes.assertions.assertCount.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class CountTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertCount(0, ['foo']);
        }
    }

.. parsed-literal::

    $ phpunit CountTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryExists:

assertDirectoryExists()
#######################

``assertDirectoryExists(string $directory[, string $message = ''])``

当 ``$directory`` 所指定的目录不存在时报告错误，错误讯息由 ``$message`` 指定。

``assertDirectoryDoesNotExist()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertDirectoryExists() 的用法
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DirectoryExistsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }

.. parsed-literal::

    $ phpunit DirectoryExistsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryExistsTest::testFailure
    Failed asserting that directory "/path/to/directory" exists.

    /home/sb/DirectoryExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsReadable:

assertDirectoryIsReadable()
###########################

``assertDirectoryIsReadable(string $directory[, string $message = ''])``

当 ``$directory`` 所指定的目录不是个目录或不可读时报告错误，错误讯息由 ``$message`` 指定。

``assertDirectoryIsNotReadable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertDirectoryIsReadable() 的用法
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }

.. parsed-literal::

    $ phpunit DirectoryIsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsReadableTest::testFailure
    Failed asserting that "/path/to/directory" is readable.

    /home/sb/DirectoryIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsWritable:

assertDirectoryIsWritable()
###########################

``assertDirectoryIsWritable(string $directory[, string $message = ''])``

当 ``$directory`` 所指定的目录不是个目录或不可写时报告错误，错误讯息由 ``$message`` 指定。

``assertDirectoryIsNotWritable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertDirectoryIsWritable() 的用法
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }

.. parsed-literal::

    $ phpunit DirectoryIsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsWritableTest::testFailure
    Failed asserting that "/path/to/directory" is writable.

    /home/sb/DirectoryIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEmpty:

assertEmpty()
#############

``assertEmpty(mixed $actual[, string $message = ''])``

当 ``$actual`` 非空时报告错误，错误讯息由 ``$message`` 指定。

``assertNotEmpty()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertEmpty() 的用法
    :name: appendixes.assertions.assertEmpty.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EmptyTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertEmpty(['foo']);
        }
    }

.. parsed-literal::

    $ phpunit EmptyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

当两个变量 ``$expected`` 和 ``$actual`` 不相等时报告错误，错误讯息由 ``$message`` 指定。

``assertNotEquals()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertEquals() 的用法
    :name: appendixes.assertions.assertEquals.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2(): void
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3(): void
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

如果 ``$expected`` 和 ``$actual`` 是某些特定的类型，将使用更加专门的比较方式，参阅下文。

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

当 ``$expected`` 和 ``$actual`` 这两个 DOMDocument 对象所表示的 XML 文档对应的无注释规范形式不相同时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: 对 DOMDocument 对象使用 assertEquals() 时的用法
    :name: appendixes.assertions.assertEquals.example3

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(object $expected, object $actual[, string $message = ''])``

当 ``$expected`` 和 ``$actual`` 这两个对象的属性值不相等时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: 对对象使用 assertEquals() 时的用法
    :name: appendixes.assertions.assertEquals.example4

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(array $expected, array $actual[, string $message = ''])``

当 ``$expected`` 和 ``$actual`` 这两个数组不相等时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: 对数组使用 assertEquals() 时的用法
    :name: appendixes.assertions.assertEquals.example5

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEqualsCanonicalizing()
############################

``assertEqualsCanonicalizing(mixed $expected, mixed $actual[, string $message = ''])``

当两个变量 ``$expected`` 和 ``$actual`` 不相等时报告错误，错误讯息由 ``$message`` 指定。

先对 ``$expected`` 和 ``$actual`` 进行规范化，然后再进行比较。例如，如果两个变量 ``$expected`` 和 ``$actual`` 是数组，则会先对这些数组进行排序然后再进行比较。当 ``$expected`` 和 ``$actual`` 是对象时，则每个对象都会被转换为一个包含所有私有、保护、和公开属性的数组。

``assertNotEqualsCanonicalizing()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertEqualsCanonicalizing() 的用法
    :name: appendixes.assertions.assertEqualsCanonicalizing.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsCanonicalizingTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEqualsCanonicalizing([3, 2, 1], [2, 3, 0, 1]);
        }
    }

.. parsed-literal::

    $ phpunit EqualsCanonicalizingTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 42 ms, Memory: 6.00 MB

    There was 1 failure:

    1) EqualsCanonicalizingTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    -    1 => 2
    -    2 => 3
    +    0 => 0
    +    1 => 1
    +    2 => 2
    +    3 => 3
     )

    /home/sb/EqualsCanonicalizingTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEqualsIgnoringCase()
##########################

``assertEqualsIgnoringCase(mixed $expected, mixed $actual[, string $message = ''])``

当两个变量 ``$expected`` 和 ``$actual`` 不相等时报告错误，错误讯息由 ``$message`` 指定。

比较 ``$expected`` 和 ``$actual`` 时，忽略大小写差异。

``assertNotEqualsIgnoringCase()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertEqualsIgnoringCase() 的用法
    :name: appendixes.assertions.assertEqualsIgnoringCase.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsIgnoringCaseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEqualsIgnoringCase('foo', 'BAR');
        }
    }

.. parsed-literal::

    $ phpunit EqualsIgnoringCaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 51 ms, Memory: 6.00 MB

    There was 1 failure:

    1) EqualsIgnoringCaseTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'BAR'

    /home/sb/EqualsIgnoringCaseTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEqualsWithDelta()
#######################

``assertEqualsWithDelta(mixed $expected, mixed $actual, float $delta[, string $message = ''])``

当 ``$expected`` 和 ``$actual`` 之间的差的绝对值大于 ``$delta`` 时报告错误，错误讯息由 ``$message`` 指定。

请阅读《`What Every Computer Scientist Should Know About Floating-Point Arithmetic <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_》来了解为什么需要有 ``$delta``。

``assertNotEqualsWithDelta()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertEqualsWithDelta() 的用法
    :name: appendixes.assertions.assertEqualsWithDelta.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsWithDeltaTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEqualsWithDelta(1.0, 1.5, 0.1);
        }
    }

.. parsed-literal::

    $ phpunit EqualsWithDeltaTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 41 ms, Memory: 6.00 MB

    There was 1 failure:

    1) EqualsWithDeltaTest::testFailure
    Failed asserting that 1.5 matches expected 1.0.

    /home/sb/EqualsWithDeltaTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectEquals:

assertObjectEquals()
####################

``assertObjectEquals(object $expected, object $actual, string $method = 'equals', string $message = ''])``

当按照 ``$actual->$method($expected)`` 判断出 ``$actual`` 不等于 ``$expected`` 时报告错误，错误讯息由 ``$message`` 指定。

在对象上使用 ``assertEquals()``\ （以及其否断言形式 ``assertNotEquals()``）而不注册自定义比较器来定制对象的比较方式是一种不良做法。但是，很不幸地，为每个要在测试中进行断言的对象都实现自定义比较器无论如何至少也是不方便的。

自定义比较器最常见的用例是值对象。这类对象一般都有一个 ``equals(self $other): bool`` 方法（或者名称不同的类似方法）用于比较这个同为值对象类型的两个实例。对于这类用例，``assertObjectEquals()`` 让自定义对象比较变得很方便：

.. code-block:: php
    :caption: assertObjectEquals() 的用法
    :name: appendixes.assertions.assertObjectEquals.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SomethingThatUsesEmailTest extends TestCase
    {
        public function testSomething(): void
        {
            $a = new Email('user@example.org');
            $b = new Email('user@example.org');
            $c = new Email('user@example.com');

            // This passes
            $this->assertObjectEquals($a, $b);

            // This fails
            $this->assertObjectEquals($a, $c);
        }
    }

.. code-block:: php
    :caption: 带有 equals() 方法的 Email 值对象
    :name: appendixes.assertions.Email.example

    <?php declare(strict_types=1);
    final class Email
    {
        private string $email;

        public function __construct(string $email)
        {
            $this->ensureIsValidEmail($email);

            $this->email = $email;
        }

        public function asString(): string
        {
            return $this->email;
        }

        public function equals(self $other): bool
        {
            return $this->asString() === $other->asString();
        }

        private function ensureIsValidEmail(string $email): void
        {
            // ...
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 00:00.017, Memory: 4.00 MB

    There was 1 failure:

    1) SomethingThatUsesEmailTest::testSomething
    Failed asserting that two objects are equal.
    The objects are not equal according to Email::equals().

    /home/sb/SomethingThatUsesEmailTest.php:16

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

请注意：

* ``$actual`` 对象必须存在名叫 ``$method`` 的方法
* 此方法必须只接受一个参数
* 相应的参数必须有声明类型
* ``$expected`` 对象必须与这个声明的类型兼容
* 此方法必须有声明为 ``bool`` 的返回类型

如果以上假设中的任何一条不满足，或 ``$actual->$method($expected)`` 返回 ``false``，则断言失败。

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

当 ``$condition`` 为 ``true`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertNotFalse()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertFalse() 的用法
    :name: appendixes.assertions.assertFalse.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FalseTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFalse(true);
        }
    }

.. parsed-literal::

    $ phpunit FalseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileEquals:

assertFileEquals()
##################

``assertFileEquals(string $expected, string $actual[, string $message = ''])``

当 ``$expected`` 所指定的文件与 ``$actual`` 所指定的文件内容不同时报告错误，错误讯息由 ``$message`` 指定。

``assertFileNotEquals()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertFileEquals() 的用法
    :name: appendixes.assertions.assertFileEquals.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileEqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }

.. parsed-literal::

    $ phpunit FileEqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertFileExists:

assertFileExists()
##################

``assertFileExists(string $filename[, string $message = ''])``

当 ``$filename`` 所指定的文件不存在时报告错误，错误讯息由 ``$message`` 指定。

``assertFileDoesNotExist()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertFileExists() 的用法
    :name: appendixes.assertions.assertFileExists.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileExistsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileExists('/path/to/file');
        }
    }

.. parsed-literal::

    $ phpunit FileExistsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsReadable:

assertFileIsReadable()
######################

``assertFileIsReadable(string $filename[, string $message = ''])``

当 ``$filename`` 所指定的文件不是个文件或不可读时报告错误，错误讯息由 ``$message`` 指定。

``assertFileIsNotReadable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertFileIsReadable() 的用法
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileIsReadableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }

.. parsed-literal::

    $ phpunit FileIsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsReadableTest::testFailure
    Failed asserting that "/path/to/file" is readable.

    /home/sb/FileIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsWritable:

assertFileIsWritable()
######################

``assertFileIsWritable(string $filename[, string $message = ''])``

当 ``$filename`` 所指定的文件不是个文件或不可写时报告错误，错误讯息由 ``$message`` 指定。

``assertFileIsNotWritable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertFileIsWritable() 的用法
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileIsWritableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }

.. parsed-literal::

    $ phpunit FileIsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsWritableTest::testFailure
    Failed asserting that "/path/to/file" is writable.

    /home/sb/FileIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThan:

assertGreaterThan()
###################

``assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])``

当 ``$actual`` 的值不大于 ``$expected`` 的值时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertGreaterThan() 的用法
    :name: appendixes.assertions.assertGreaterThan.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class GreaterThanTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertGreaterThan(2, 1);
        }
    }

.. parsed-literal::

    $ phpunit GreaterThanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThanOrEqual:

assertGreaterThanOrEqual()
##########################

``assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

当 ``$actual`` 的值不大于且不等于 ``$expected`` 的值时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertGreaterThanOrEqual() 的用法
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }

.. parsed-literal::

    $ phpunit GreaterThanOrEqualTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertInfinite:

assertInfinite()
################

``assertInfinite(mixed $variable[, string $message = ''])``

当 ``$actual`` 不是 ``INF`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertFinite()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertInfinite() 的用法
    :name: appendixes.assertions.assertInfinite.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class InfiniteTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertInfinite(1);
        }
    }

.. parsed-literal::

    $ phpunit InfiniteTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInstanceOf:

assertInstanceOf()
##################

``assertInstanceOf($expected, $actual[, $message = ''])``

当 ``$actual`` 不是 ``$expected`` 的实例时报告错误，错误讯息由 ``$message`` 指定。

``assertNotInstanceOf()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertInstanceOf() 的用法
    :name: appendixes.assertions.assertInstanceOf.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class InstanceOfTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }

.. parsed-literal::

    $ phpunit InstanceOfTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsArray()
###############

``assertIsArray($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``array`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotArray()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsArray() 的用法
    :name: appendixes.assertions.assertIsArray.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsArray(null);
        }
    }

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayTest::testFailure
    Failed asserting that null is of type "array".

    /home/sb/ArrayTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsBool()
##############

``assertIsBool($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``bool`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotBool()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsBool() 的用法
    :name: appendixes.assertions.assertIsBool.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class BoolTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertIsBool(null);
        }
    }

.. code-block:: bash

    $ phpunit BoolTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) BoolTest::testFailure
    Failed asserting that null is of type "bool".

    /home/sb/BoolTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsCallable()
##################

``assertIsCallable($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``callable`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotCallable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsCallable() 的用法
    :name: appendixes.assertions.assertIsCallable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CallableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsCallable(null);
        }
    }

.. code-block:: bash

    $ phpunit CallableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) CallableTest::testFailure
    Failed asserting that null is of type "callable".

    /home/sb/CallableTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsFloat()
###############

``assertIsFloat($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``float`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotFloat()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsFloat() 的用法
    :name: appendixes.assertions.assertIsFloat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FloatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsFloat(null);
        }
    }

.. code-block:: bash

    $ phpunit FloatTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FloatTest::testFailure
    Failed asserting that null is of type "float".

    /home/sb/FloatTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsInt()
#############

``assertIsInt($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``int`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotInt()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsInt() 的用法
    :name: appendixes.assertions.assertIsInt.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IntTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsInt(null);
        }
    }

.. code-block:: bash

    $ phpunit IntTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) IntTest::testFailure
    Failed asserting that null is of type "int".

    /home/sb/IntTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsIterable()
##################

``assertIsIterable($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``iterable`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotIterable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsIterable() 的用法
    :name: appendixes.assertions.assertIsIterable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IterableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsIterable(null);
        }
    }

.. code-block:: bash

    $ phpunit IterableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) IterableTest::testFailure
    Failed asserting that null is of type "iterable".

    /home/sb/IterableTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsNumeric()
#################

``assertIsNumeric($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``numeric`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotNumeric()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsNumeric() 的用法
    :name: appendixes.assertions.assertIsNumeric.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NumericTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsNumeric(null);
        }
    }

.. code-block:: bash

    $ phpunit NumericTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NumericTest::testFailure
    Failed asserting that null is of type "numeric".

    /home/sb/NumericTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsObject()
################

``assertIsObject($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``object`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotObject()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsObject() 的用法
    :name: appendixes.assertions.assertIsObject.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsObject(null);
        }
    }

.. code-block:: bash

    $ phpunit ObjectTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ObjectTest::testFailure
    Failed asserting that null is of type "object".

    /home/sb/ObjectTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsResource()
##################

``assertIsResource($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``resource`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotResource()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsResource() 的用法
    :name: appendixes.assertions.assertIsResource.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ResourceTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsResource(null);
        }
    }

.. code-block:: bash

    $ phpunit ResourceTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ResourceTest::testFailure
    Failed asserting that null is of type "resource".

    /home/sb/ResourceTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsScalar()
################

``assertIsScalar($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``scalar`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotScalar()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsScalar() 的用法
    :name: appendixes.assertions.assertIsScalar.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ScalarTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsScalar(null);
        }
    }

.. code-block:: bash

    $ phpunit ScalarTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ScalarTest::testFailure
    Failed asserting that null is of type "scalar".

    /home/sb/ScalarTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsString()
################

``assertIsString($actual[, $message = ''])``

当 ``$actual`` 的类型不是 ``string`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotString()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsString() 的用法
    :name: appendixes.assertions.assertIsString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsString(null);
        }
    }

.. parsed-literal::

    $ phpunit StringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringTest::testFailure
    Failed asserting that null is of type "string".

    /home/sb/StringTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

当 ``$filename`` 所指定的文件或目录不可读时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotReadable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsReadable() 的用法
    :name: appendixes.assertions.assertIsReadable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class IsReadableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }

.. parsed-literal::

    $ phpunit IsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsReadableTest::testFailure
    Failed asserting that "/path/to/unreadable" is readable.

    /home/sb/IsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsWritable:

assertIsWritable()
##################

``assertIsWritable(string $filename[, string $message = ''])``

当 ``$filename`` 所指定的文件或目录不可写时报告错误，错误讯息由 ``$message`` 指定。

``assertIsNotWritable()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertIsWritable() 的用法
    :name: appendixes.assertions.assertIsWritable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class IsWritableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }

.. parsed-literal::

    $ phpunit IsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsWritableTest::testFailure
    Failed asserting that "/path/to/unwritable" is writable.

    /home/sb/IsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertJsonFileEqualsJsonFile:

assertJsonFileEqualsJsonFile()
##############################

``assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])``

当 ``$actualFile`` 对应的值与 ``$expectedFile`` 对应的值不匹配时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertJsonFileEqualsJsonFile() 的用法
    :name: appendixes.assertions.assertJsonFileEqualsJsonFile.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }

.. parsed-literal::

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonFile:

assertJsonStringEqualsJsonFile()
################################

``assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])``

当 ``$actualJson`` 对应的值与 ``$expectedFile`` 对应的值不匹配时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertJsonStringEqualsJsonFile() 的用法
    :name: appendixes.assertions.assertJsonStringEqualsJsonFile.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. parsed-literal::

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonString:

assertJsonStringEqualsJsonString()
##################################

``assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])``

当 ``$actualJson`` 对应的值与 ``$expectedJson`` 对应的值不匹配时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertJsonStringEqualsJsonString() 的用法
    :name: appendixes.assertions.assertJsonStringEqualsJsonString.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. parsed-literal::

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascot' => 'Tux'
     +    'Mascot' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertLessThan:

assertLessThan()
################

``assertLessThan(mixed $expected, mixed $actual[, string $message = ''])``

当 ``$actual`` 的值不小于 ``$expected`` 的值时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertLessThan() 的用法
    :name: appendixes.assertions.assertLessThan.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class LessThanTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertLessThan(1, 2);
        }
    }

.. parsed-literal::

    $ phpunit LessThanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertLessThanOrEqual:

assertLessThanOrEqual()
#######################

``assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

当 ``$actual`` 的值不小于且不等于 ``$expected`` 的值时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertLessThanOrEqual() 的用法
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class LessThanOrEqualTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }

.. parsed-literal::

    $ phpunit LessThanOrEqualTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertNan:

assertNan()
###########

``assertNan(mixed $variable[, string $message = ''])``

当 ``$variable`` 不是 ``NAN`` 时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption:  assertNan() 的用法
    :name: appendixes.assertions.assertNan.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class NanTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertNan(1);
        }
    }

.. parsed-literal::

    $ phpunit NanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertNull:

assertNull()
############

``assertNull(mixed $variable[, string $message = ''])``

当 ``$actual`` 不是  ``null`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertNotNull()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertNull() 的用法
    :name: appendixes.assertions.assertNull.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class NullTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertNull('foo');
        }
    }

.. parsed-literal::

    $ phpunit NotNullTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectHasAttribute:

assertObjectHasAttribute()
##########################

``assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])``

当 ``$object->attributeName`` 不存在时报告错误，错误讯息由 ``$message`` 指定。

``assertObjectNotHasAttribute()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertObjectHasAttribute() 的用法
    :name: appendixes.assertions.assertObjectHasAttribute.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }

.. parsed-literal::

    $ phpunit ObjectHasAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertMatchesRegularExpression:

assertMatchesRegularExpression()
################################

``assertMatchesRegularExpression(string $pattern, string $string[, string $message = ''])``

当 ``$string`` 不匹配于正则表达式 ``$pattern`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertDoesNotMatchRegularExpression()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertMatchesRegularExpression() 的用法
    :name: appendixes.assertions.assertMatchesRegularExpression.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class RegExpTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertMatchesRegularExpression('/foo/', 'bar');
        }
    }

.. parsed-literal::

    $ phpunit RegExpTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringMatchesFormat:

assertStringMatchesFormat()
###########################

``assertStringMatchesFormat(string $format, string $string[, string $message = ''])``

当 ``$string`` 不匹配于 ``$format`` 定义的格式时报告错误，错误讯息由 ``$message`` 指定。

``assertStringNotMatchesFormat()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertStringMatchesFormat() 的用法
    :name: appendixes.assertions.assertStringMatchesFormat.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringMatchesFormatTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringMatchesFormatTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

格式定义字符串中可以使用如下占位符：

-

  ``%e``：表示目录分隔符，例如在 Linux 系统中是 ``/``。

-

  ``%s``：一个或多个除了换行符以外的任意字符（非空白字符或者空白字符）。

-

  ``%S``：零个或多个除了换行符以外的任意字符（非空白字符或者空白字符）。

-

  ``%a``：一个或多个包括换行符在内的任意字符（非空白字符或者空白字符）。

-

  ``%A``：零个或多个包括换行符在内的任意字符（非空白字符或者空白字符）。

-

  ``%w``：零个或多个空白字符。

-

  ``%i``：带符号整数值，例如 ``+3142``、``-3142``。

-

  ``%d``：无符号整数值，例如 ``123456``。

-

  ``%x``：一个或多个十六进制字符。所谓十六进制字符，指的是在以下范围内的字符：``0-9``、``a-f``、``A-F``。

-

  ``%f``：浮点数，例如 ``3.142``、``-3.142``、``3.142E-10``、``3.142e+10``。

-

  ``%c``：单个任意字符。

-

  ``%%``：原本的百分比字符：``%``。

.. _appendixes.assertions.assertStringMatchesFormatFile:

assertStringMatchesFormatFile()
###############################

``assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])``

当 ``$string`` 不匹配于 ``$formatFile`` 的内容所定义的格式时报告错误，错误讯息由 ``$message`` 指定。

``assertStringNotMatchesFormatFile()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertStringMatchesFormatFile() 的用法
    :name: appendixes.assertions.assertStringMatchesFormatFile.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringMatchesFormatFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertSame:

assertSame()
############

``assertSame(mixed $expected, mixed $actual[, string $message = ''])``

当两个变量 ``$expected`` 和 ``$actual`` 的值与类型不完全相同时报告错误，错误讯息由 ``$message`` 指定。

``assertNotSame()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertSame() 的用法
    :name: appendixes.assertions.assertSame.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SameTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertSame('2204', 2204);
        }
    }

.. parsed-literal::

    $ phpunit SameTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertSame(object $expected, object $actual[, string $message = ''])``

当两个变量 ``$expected`` 和 ``$actual`` 不是指向同一个对象的引用时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: 对对象使用 assertSame() 时的用法
    :name: appendixes.assertions.assertSame.example2

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SameTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }

.. parsed-literal::

    $ phpunit SameTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEndsWith:

assertStringEndsWith()
######################

``assertStringEndsWith(string $suffix, string $string[, string $message = ''])``

当 ``$string`` 不以 ``$suffix`` 结尾时报告错误，错误讯息由 ``$message`` 指定。

``assertStringEndsNotWith()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertStringEndsWith() 的用法
    :name: appendixes.assertions.assertStringEndsWith.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringEndsWithTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringEndsWithTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEqualsFile:

assertStringEqualsFile()
########################

``assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])``

当 ``$expectedFile`` 所指定的文件其内容不是 ``$actualString`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertStringNotEqualsFile()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertStringEqualsFile() 的用法
    :name: appendixes.assertions.assertStringEqualsFile.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringEqualsFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }

.. parsed-literal::

    $ phpunit StringEqualsFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertStringStartsWith:

assertStringStartsWith()
########################

``assertStringStartsWith(string $prefix, string $string[, string $message = ''])``

当 ``$string`` 不以 ``$prefix`` 开头时报告错误，错误讯息由 ``$message`` 指定。

``assertStringStartsNotWith()`` 是与之相反的断言，并接受相同的参数。

.. code-block:: php
    :caption: assertStringStartsWith() 的用法
    :name: appendixes.assertions.assertStringStartsWith.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringStartsWithTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringStartsWithTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertThat:

assertThat()
############

可以用 ``PHPUnit\Framework\Constraint`` 类来订立更加复杂的断言。随后可以用 ``assertThat()`` 方法来评定这些断言。:numref:`appendixes.assertions.assertThat.example` 展示了如何用 ``logicalNot()`` 和 ``equalTo()`` 约束条件来表达与 ``assertNotEquals()`` 等价的断言。

``assertThat(mixed $value, PHPUnit\Framework\Constraint $constraint[, $message = ''])``

当 ``$value`` 不符合约束条件 ``$constraint`` 时报告错误，错误讯息由 ``$message`` 指定。

.. code-block:: php
    :caption: assertThat() 的用法
    :name: appendixes.assertions.assertThat.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class BiscuitTest extends TestCase
    {
        public function testEquals(): void
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }

:numref:`appendixes.assertions.assertThat.tables.constraints` 列举了所有可用的 ``PHPUnit\Framework\Constraint`` 类。

.. rst-class:: table
.. list-table:: 约束条件
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - 约束条件
      - 含义
    * - ``PHPUnit\Framework\Constraint\IsAnything anything()``
      - 此约束接受任意输入值。
    * - ``PHPUnit\Framework\Constraint\ArrayHasKey arrayHasKey(mixed $key)``
      - 此约束断言数组拥有指定键名。
    * - ``PHPUnit\Framework\Constraint\TraversableContains contains(mixed $value)``
      - 此约束断言 ``array`` 或实现了 ``Iterator`` 接口的对象包含有给定值。
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnly(string $type)``
      - 此约束断言 ``array`` 或实现了 ``Iterator`` 接口的对象仅包含给定类型的值。
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnlyInstancesOf(string $classname)``
      - 此约束断言 ``array`` 或实现了 ``Iterator`` 接口的对象仅包含给定类名的类的实例。
    * - ``PHPUnit\Framework\Constraint\IsEqual equalTo($value, $delta = 0, $maxDepth = 10)``
      - 此约束检验一个值是否等于另外一个。
    * - ``PHPUnit\Framework\Constraint\DirectoryExists directoryExists()``
      - 此约束检查目录是否存在。
    * - ``PHPUnit\Framework\Constraint\FileExists fileExists()``
      - 此约束检查文件（名）是否存在。
    * - ``PHPUnit\Framework\Constraint\IsReadable isReadable()``
      - 此约束检查文件（名）是否可读。
    * - ``PHPUnit\Framework\Constraint\IsWritable isWritable()``
      - 此约束检查文件（名）是否可写。
    * - ``PHPUnit\Framework\Constraint\GreaterThan greaterThan(mixed $value)``
      - 此约束断言值大于给定值。
    * - ``PHPUnit\Framework\Constraint\LogicalOr greaterThanOrEqual(mixed $value)``
      - 此约束断言值大于或等于给定值。
    * - ``PHPUnit\Framework\Constraint\ClassHasAttribute classHasAttribute(string $attributeName)``
      - 此约束断言类具有给定属性。
    * - ``PHPUnit\Framework\Constraint\ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)``
      - 此约束断言类具有给定静态属性。
    * - ``PHPUnit\Framework\Constraint\ObjectHasAttribute objectHasAttribute(string $attributeName)``
      - 此约束断言对象具有给定属性。
    * - ``PHPUnit\Framework\Constraint\IsIdentical identicalTo(mixed $value)``
      - 此约束断言值与另外一个值全等。
    * - ``PHPUnit\Framework\Constraint\IsFalse isFalse()``
      - 此约束断言值是 ``false``。
    * - ``PHPUnit\Framework\Constraint\IsInstanceOf isInstanceOf(string $className)``
      - 此约束断言对象是给定类的实例。
    * - ``PHPUnit\Framework\Constraint\IsNull isNull()``
      - 此约束断言值是 ``null``。
    * - ``PHPUnit\Framework\Constraint\IsTrue isTrue()``
      - 此约束断言值是 ``true``。
    * - ``PHPUnit\Framework\Constraint\IsType isType(string $type)``
      - 此约束断言值是指定的类型。
    * - ``PHPUnit\Framework\Constraint\LessThan lessThan(mixed $value)``
      - 此约束断言值小于给定值。
    * - ``PHPUnit\Framework\Constraint\LogicalOr lessThanOrEqual(mixed $value)``
      - 此约束断言值小于或等于给定值。
    * - ``logicalAnd()``
      - 逻辑与(AND)。
    * - ``logicalNot(PHPUnit\Framework\Constraint $constraint)``
      - 逻辑非(NOT)。
    * - ``logicalOr()``
      - 逻辑或(OR)。
    * - ``logicalXor()``
      - 逻辑异或(XOR)。
    * - ``PHPUnit\Framework\Constraint\PCREMatch matchesRegularExpression(string $pattern)``
      - 此约束断言字符串匹配于正则表达式。
    * - ``PHPUnit\Framework\Constraint\StringContains stringContains(string $string, bool $case)``
      - 此约束断言字符串包含指定字符串。
    * - ``PHPUnit\Framework\Constraint\StringEndsWith stringEndsWith(string $suffix)``
      - 此约束断言字符串以给定后缀结尾。
    * - ``PHPUnit\Framework\Constraint\StringStartsWith stringStartsWith(string $prefix)``
      - 此约束断言字符串以给定前缀开头。

.. _appendixes.assertions.assertTrue:

assertTrue()
############

``assertTrue(bool $condition[, string $message = ''])``

当 ``$condition`` 为 ``false`` 时报告错误，错误讯息由 ``$message`` 指定。

``assertNotTrue()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertTrue() 的用法
    :name: appendixes.assertions.assertTrue.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class TrueTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertTrue(false);
        }
    }

.. parsed-literal::

    $ phpunit TrueTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertXmlFileEqualsXmlFile:

assertXmlFileEqualsXmlFile()
############################

``assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])``

当 ``$actualFile`` 对应的 XML 文档与 ``$expectedFile`` 对应的 XML 文档不相同时报告错误，错误讯息由 ``$message`` 指定。

``assertXmlFileNotEqualsXmlFile()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertXmlFileEqualsXmlFile() 的用法
    :name: appendixes.assertions.assertXmlFileEqualsXmlFile.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }

.. parsed-literal::

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlFile:

assertXmlStringEqualsXmlFile()
##############################

``assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])``

当 ``$actualXml`` 对应的 XML 文档与 ``$expectedFile`` 对应的 XML 文档不相同时报告错误，错误讯息由 ``$message`` 指定。

``assertXmlStringNotEqualsXmlFile()`` 是与之相反的断言，并接受相同的参数。

.. code-block:: php
    :caption: assertXmlStringEqualsXmlFile() 的用法
    :name: appendixes.assertions.assertXmlStringEqualsXmlFile.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }

.. parsed-literal::

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlString:

assertXmlStringEqualsXmlString()
################################

``assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])``

当 ``$actualXml`` 对应的 XML 文档与 ``$expectedXml`` 对应的 XML 文档不相同时报告错误，错误讯息由 ``$message`` 指定。

``assertXmlStringNotEqualsXmlString()`` 是与之相反的断言，接受相同的参数。

.. code-block:: php
    :caption: assertXmlStringEqualsXmlString() 的用法
    :name: appendixes.assertions.assertXmlStringEqualsXmlString.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }

.. parsed-literal::

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.


