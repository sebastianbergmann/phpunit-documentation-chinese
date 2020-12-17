

.. _test-doubles:

============
测试替身
============

Gerard Meszaros 在 :ref:`Meszaros2007 <appendixes.bibliography>` 中介绍了测试替身的概念：

    *Gerard Meszaros*：

    有时候对被测系统（SUT）进行测试是很困难的，因为它依赖于其他无法在测试环境中使用的组件。这有可能是因为这些组件不可用，它们不会返回测试所需要的结果，或者执行它们会有不良副作用。在其他情况下，我们的测试策略要求对被测系统的内部行为有更多控制或更多可见性。

    如果在编写测试时无法使用（或选择不使用）实际的依赖组件（DOC），可以用测试替身来代替。测试替身不需要和真正的依赖组件有完全一样的的行为方式；它只需要提供和真正的组件同样的 API 即可，这样被测系统就会以为它是真正的组件！

PHPUnit 提供的 ``createStub($type)``、``createMock($type)`` 和 ``getMockBuilder($type)`` 方法可以在测试中用来自动生成对象，此对象可以充当任意指定原版类型（接口或类名）的测试替身。在任何预期或要求使用原版类的实例对象的上下文中都可以使用这个测试替身对象来代替。

``createStub($type)`` 和 ``createMock($type)`` 方法直接返回指定类型（接口或类）的测试替身对象实例。此测试替身的创建使用了最佳实践默认方案。原始类的 ``__construct()`` 和 ``__clone()`` 方法不会执行，且不对传递给测试替身的方法的参数进行克隆。如果这些默认值非你所需，可以用 ``getMockBuilder($type)`` 方法并使用流畅式接口来定制测试替身的生成过程。

在默认情况下，原版类的所有方法都会被替换为返回 ``null`` 的伪实现（其中不会调用原版方法）。使用诸如 ``will($this->returnValue())`` 之类的方法可以对这些伪实现在被调用时应当返回什么值做出配置。

.. admonition:: 局限性：final、private、与 static 方法

   请注意，``final``、``private`` 和 ``static`` 方法无法制作相应桩件（Stub）或仿件（Mock）。PHPUnit 的测试替身功能将会忽略它们，并维持它们的原始行为，``static`` 方法例外，它会被替换为一个会抛出 ``\PHPUnit\Framework\MockObject\BadMethodCallException`` 异常的方法。


.. _test-doubles.stubs:

Stubs（桩件）
#############

将对象替换为（可选地）返回配置好的返回值的测试替身的实践方法称为\ *打桩（stubbing）*\ 。可以用\ *桩件（Stub）*\ 来“替换掉被测系统所依赖的实际组件，这样测试就有了对被测系统的间接输入的控制点。这使得测试能强制安排被测系统的执行路径，否则被测系统可能无法执行”。

:numref:`test-doubles.stubs.examples.StubTest.php` 展示了如何对方法的调用进行上桩以及如何设定返回值。首先用 ``PHPUnit\Framework\TestCase`` 类提供的 ``createStub()`` 方法来建立一个桩件对象，它表面看起来像是 ``SomeClass`` 类（:numref:`test-doubles.stubs.examples.SomeClass.php`\ ）的实例。随后用 PHPUnit 提供的\ `流畅式接口 <http://martinfowler.com/bliki/FluentInterface.html>`_\ 来指定桩件的行为。本质上，这意味着不需要建立多个临时对象然后再把它们捆到一起。取而代之的是范例中所示的链式方法调用。这使得代码更加易读并更加“流畅”。

.. code-block:: php
    :caption: 想要上桩的类
    :name: test-doubles.stubs.examples.SomeClass.php

    <?php declare(strict_types=1);
    class SomeClass
    {
        public function doSomething()
        {
            // 随便做点什么。
        }
    }

.. code-block:: php
    :caption: 对某个方法的调用进行上桩，返回固定值
    :name: test-doubles.stubs.examples.StubTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // 配置桩件。
            $stub->method('doSomething')
                 ->willReturn('foo');

            // 现在调用 $stub->doSomething() 会返回 'foo'。
            $this->assertSame('foo', $stub->doSomething());
        }
    

.. admonition:: 局限性：名字为“method”的方法

   仅当原始类中不包含名字为“method”的方法时，以上范例才能正常运行。

   如果原始类包含名为“method”的方法，就必须用 ``$stub->expects($this->any())->method('doSomething')->willReturn('foo');``。

“在幕后”，当使用了 ``createStub()`` 方法时， PHPUnit 自动生成了一个新的 PHP 类来实现想要的行为。

请注意：``createStub()`` 会自动递归地基于方法的返回类型对返回值进行上桩。考虑以下示例：

.. code-block:: php
    :caption: 带有返回类型声明的方法
    :name: test-doubles.stubs.examples.returnTypeDeclaration.php

    <?php declare(strict_types=1);
    class C
    {
        public function m(): D
        {
            // 随便做点什么。
        }
    }

在上述示例中，``C::m()`` 方法具有返回类型声明，指示此方法返回类型为 ``D`` 的对象。那么，举个例子说，创建 ``C`` 的测试替身而又未用 ``willReturn()`` 给 ``m()`` 配置返回值时（见上文），则当 PHPUnit 调用 ``m()`` 时会自动创建一个 ``D`` 的测试替身作为返回值。

类似地，如果 ``m`` 的返回类型声明是标量类型，则会生成诸如 ``0``\ （对于 ``int``）、``0.0``\ （对于 ``float``）、或 ``[]``\ （对于 ``array``）这样的返回值。

:numref:`test-doubles.stubs.examples.StubTest2.php` 展示了如何用仿件生成器的流畅式接口来配置测试替身的生成。这个测试替身的默认配置用的是和 ``createStub()`` 相同的最佳实践。

.. code-block:: php
    :caption: 使用可用于配置生成的测试替身类的仿件生成器 API
    :name: test-doubles.stubs.examples.StubTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->getMockBuilder(SomeClass::class)
                         ->disableOriginalConstructor()
                         ->disableOriginalClone()
                         ->disableArgumentCloning()
                         ->disallowMockingUnknownTypes()
                         ->getMock();

            // 配置桩件。
            $stub->method('doSomething')
                 ->willReturn('foo');

            // 现在调用 $stub->doSomething() 会返回 'foo'。
            $this->assertSame('foo', $stub->doSomething());
        }
    

在之前的例子中，用 ``willReturn($value)`` 返回简单值。这个简短的语法相当于 ``will($this->returnValue($value))``。而在这个长点的语法中，可以使用变量，从而实现更复杂的上桩行为。

有时想要将（未改变的）方法调用时所使用的参数之一作为桩件的方法的调用结果来返回。:numref:`test-doubles.stubs.examples.StubTest3.php` 展示了如何用 ``returnArgument()`` 代替 ``returnValue()`` 来做到这点。

.. code-block:: php
    :caption: 对某个方法的调用进行上桩，返回参数之一
    :name: test-doubles.stubs.examples.StubTest3.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnArgumentStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // 配置桩件。
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') 返回 'foo'
            $this->assertSame('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') 返回 'bar'
            $this->assertSame('bar', $stub->doSomething('bar'));
        }
    }

在用流畅式接口进行测试时，让某个已上桩的方法返回对桩件对象的引用有时会很有用。:numref:`test-doubles.stubs.examples.StubTest4.php` 展示了如何用 ``returnSelf()`` 来做到这点。

.. code-block:: php
    :caption: 对方法的调用进行上桩，返回对桩件对象的引用
    :name: test-doubles.stubs.examples.StubTest4.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnSelf(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // 配置桩件。
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() 返回 $stub
            $this->assertSame($stub, $stub->doSomething());
        }
    }

有时候，上桩的方法需要根据预定义的参数清单来返回不同的值。可以用 ``returnValueMap()`` 方法将参数和相应的返回值关联起来建立映射。示例参见\ :numref:`test-doubles.stubs.examples.StubTest5.php`。

.. code-block:: php
    :caption: 对方法的调用进行上桩，按照映射确定返回值
    :name: test-doubles.stubs.examples.StubTest5.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnValueMapStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // Create a map of arguments to return values.
            $map = [
                ['a', 'b', 'c', 'd'],
                ['e', 'f', 'g', 'h']
            ];

            // 配置桩件。
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() 根据提供的参数返回不同的值。
            $this->assertSame('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertSame('h', $stub->doSomething('e', 'f', 'g'));
        }
    }

如果上桩的方法需要返回计算得到的值而不是固定值（参见 ``returnValue()``）或某个（未改变的）参数（参见 ``returnArgument()``），可以用 ``returnCallback()`` 来让上桩的方法返回回调函数或方法的结果。示例参见\ :numref:`test-doubles.stubs.examples.StubTest6.php`。

.. code-block:: php
    :caption: 对方法的调用进行上桩，由回调生成返回值
    :name: test-doubles.stubs.examples.StubTest6.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnCallbackStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // 配置桩件。
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // $stub->doSomething($argument) 返回 str_rot13($argument)
            $this->assertSame('fbzrguvat', $stub->doSomething('something'));
        }
    }

相比于建立回调方法，有一个更简单的选择是直接给出期望返回值的列表。可以用 ``onConsecutiveCalls()`` 方法来做到这个。示例参见\ :numref:`test-doubles.stubs.examples.StubTest7.php`。

.. code-block:: php
    :caption: 对方法的调用上桩，按照指定顺序返回列表中的值
    :name: test-doubles.stubs.examples.StubTest7.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testOnConsecutiveCallsStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // 配置桩件。
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // $stub->doSomething() 每次都会返回不同的值
            $this->assertSame(2, $stub->doSomething());
            $this->assertSame(3, $stub->doSomething());
            $this->assertSame(5, $stub->doSomething());
        }
    }

除了返回一个值之外，上桩的方法还能抛出一个异常。:numref:`test-doubles.stubs.examples.StubTest8.php` 展示了如何用 ``throwException()`` 做到这点。

.. code-block:: php
    :caption: 对方法的调用进行上桩，抛出异常
    :name: test-doubles.stubs.examples.StubTest8.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testThrowExceptionStub(): void
        {
            // 为 SomeClass 类创建桩件。
            $stub = $this->createStub(SomeClass::class);

            // 配置桩件。
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // $stub->doSomething() 抛出异常
            $stub->doSomething();
        }
    }

另外，也可以自行编写桩件，并在此过程中改善设计。在系统中被广泛使用的资源是通过单个外观（facade）来访问的，因此就能用桩件替换掉资源。例如，将散落在代码各处的对数据库的直接调用替换为单个 ``Database`` 对象，这个对象实现了 ``IDatabase`` 接口。接下来，就可以创建实现了 ``IDatabase`` 的桩件并在测试中使用之。甚至可以创建一个选项来控制是用桩件还是用真实数据库来运行测试，这样测试就既能在开发过程中用作本地测试，又能在实际数据库环境中进行集成测试。

需要上桩的功能往往集中在同一个对象中，这就改善了内聚度。将功能通过单一且一致的接口呈现出来，就降低了这部分与系统其他部分之间的耦合度。

.. _test-doubles.mock-objects:

仿件对象（Mock Object）
#######################

将对象替换为能验证预期行为（例如断言某个方法必会被调用）的测试替身的实践方法称为\ *模仿（mocking）*\ 。

可以用\ *仿件对象（mock object）*\ “作为观察点来核实被测试系统在测试中的间接输出。通常，仿件对象还需要包括桩件的功能，因为如果测试尚未失败则仿件对象需要向被测系统返回一些值，但是其重点还是在对间接输出的核实上。因此，仿件对象远不止是桩件加断言，它是以一种从根本上完全不同的方式来使用的”（Gerard Meszaros）。

.. admonition:: 局限性：对预期的自动校验

   PHPUnit 只会对在某个测试的作用域内生成的仿件对象进行自动校验。诸如在数据供给器内生成或用 ``@depends`` 标注注入测试的仿件对象，PHPUnit 并不会自动对其进行校验。

这有个例子：假设需要测试的当前方法，在例子中是 ``update()``，确实在一个观察着另外一个对象的对象中上被调用了。:numref:`test-doubles.mock-objects.examples.SUT.php` 展示了被测系统（SUT）中 ``Subject`` 和 ``Observer`` 两个类的代码。

.. code-block:: php
    :caption:  被测系统（SUT）中 Subject 与 Observer 类的代码
    :name: test-doubles.mock-objects.examples.SUT.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    class Subject
    {
        protected $observers = [];
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // 随便做点什么。
            // ...

            // 通知观察者我们做了点什么。
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Something bad happened', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // 其他方法。
    }

    class Observer
    {
        public function update($argument)
        {
            // 随便做点什么。
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // 随便做点什么
        }

        // 其他方法。
    }

:numref:`test-doubles.mock-objects.examples.SubjectTest.php` 展示了如何用仿件对象来测试 ``Subject`` 和 ``Observer`` 对象之间的互动。

首先用 ``PHPUnit\Framework\TestCase`` 类提供的 ``createMock()`` 方法来为 ``Observer`` 建立仿件对象。

由于关注的是检验某个方法是否被调用，以及调用时具体所使用的参数，因此引入 ``expects()`` 与 ``with()`` 方法来指明此交互应该是什么样的。

.. code-block:: php
    :caption: 测试某个方法会以特定参数被调用一次
    :name: test-doubles.mock-objects.examples.SubjectTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated(): void
        {
            // 为 Observer 类建立仿件
            // 只模仿 update() 方法。
            $observer = $this->createMock(Observer::class);

            // 为 update() 方法建立预期：
            // 只会以字符串 'something' 为参数调用一次。
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // 建立 Subject 对象并且将模仿的 Observer 对象附加其上。
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // 在 $subject 上调用 doSomething() 方法，
            // 我们预期会以字符串 'something' 调用模仿的 Observer
            // 对象的 update() 方法。
            $subject->doSomething();
        }
    }

``with()`` 方法可以携带任何数量的参数，对应于被模仿的方法的参数数量。可以对方法的参数指定更加高等的约束而不仅是简单的匹配。

.. code-block:: php
    :caption: 测试某个方法将会以特定数量的参数进行调用，并且对各个参数以多种方式进行约束
    :name: test-doubles.mock-objects.examples.SubjectTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SubjectTest extends TestCase
    {
        public function testErrorReported(): void
        {
            // 为 Observer 类建立仿件，模仿 reportError() 方法
            $observer = $this->createMock(Observer::class);

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // doSomethingBad() 方法应当会通过 
            // reportError() 方法向 observer 报告错误。
            $subject->doSomethingBad();
        }
    }

``withConsecutive()`` 方法可以接受任意多个数组作为参数，具体数量取决于欲测试的调用。每个数组都都是对被仿方法的相应参数的一组约束，就像 ``with()`` 中那样。

.. code-block:: php
    :caption: 测试某个方法将会以特定参数被调用两次。
    :name: test-doubles.mock-objects.examples.with-consecutive.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FooTest extends TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments(): void
        {
            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['set'])
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     [$this->equalTo('foo'), $this->greaterThan(0)],
                     [$this->equalTo('bar'), $this->greaterThan(0)]
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }

``callback()`` 约束用来进行更加复杂的参数校验。此约束的唯一参数是一个 PHP 回调项（callback）。此 PHP 回调项接受需要校验的参数作为其唯一参数，并应当在参数通过校验时返回 ``true``，否则返回 ``false``。

.. code-block:: php
    :caption: 更复杂的参数校验
    :name: test-doubles.mock-objects.examples.SubjectTest3.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SubjectTest extends TestCase
    {
        public function testErrorReported(): void
        {
            // 为 Observer 类建立仿件，模仿 reportError() 方法
            $observer = $this->createMock(Observer::class);

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                         $this->greaterThan(0),
                         $this->stringContains('Something'),
                         $this->callback(function($subject)
                         {
                             return is_callable([$subject, 'getName']) &&
                                    $subject->getName() == 'My subject';
                         }
                     ));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // doSomethingBad() 方法应当会通过 
            // reportError() 方法向 observer 报告错误。
            $subject->doSomethingBad();
        }
    }

.. code-block:: php
    :caption: 测试某个方法将会被调用一次，并且以某个特定对象作为参数。
    :name: test-doubles.mock-objects.examples.clone-object-parameters-usecase.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed(): void
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['foo'])
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }

.. code-block:: php
    :caption: 创建仿件对象时启用参数克隆
    :name: test-doubles.mock-objects.examples.enable-clone-object-parameters.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed(): void
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->enableArgumentCloning()
                         ->getMock();

            // 现在仿件会克隆参数，因此 identicalTo 约束会失败。
        }
    }

:ref:`appendixes.assertions.assertThat.tables.constraints`\ 中列出了可以应用于方法参数的各种约束，:numref:`test-doubles.mock-objects.tables.matchers` 中列出了可以用于指定调用次数的各种匹配器。

.. rst-class:: table
.. list-table:: 匹配器
    :name: test-doubles.mock-objects.tables.matchers
    :header-rows: 1

    * - 匹配器
      - 含义
    * - ``PHPUnit\Framework\MockObject\Matcher\AnyInvokedCount any()``
      - 返回一个匹配器，当被评定的方法执行0次或更多次（即任意次数）时匹配成功。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount never()``
      - 返回一个匹配器，当被评定的方法从未执行时匹配成功。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtLeastOnce atLeastOnce()``
      - 返回一个匹配器，当被评定的方法执行至少一次时匹配成功。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount once()``
      - 返回一个匹配器，当被评定的方法执行恰好一次时匹配成功。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount exactly(int $count)``
      -  返回一个匹配器，当被评定的方法执行恰好 ``$count`` 次时匹配成功。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtIndex at(int $index)``
      - 返回一个匹配器，当被评定的方法是第 ``$index`` 个执行的方法时匹配成功。

.. admonition:: 注

   ``at()`` 匹配器的 ``$index`` 参数指的是对给定仿件对象的\ *所有方法的调用*\ 的索引，从零开始。使用这个匹配器要谨慎，因为它可能导致测试由于与具体的实现细节过分紧密绑定而变得脆弱。

如一开始提到的，如果 ``createStub()`` 和 ``createMock()`` 方法在生成测试替身时所使用的默认值不符合你的要求，则可以通过 ``getMockBuilder($type)`` 方法来用流畅式接口定制测试替身的生成过程。以下是仿件生成器所提供的方法列表：

-

  ``setMethods(array $methods)`` 可以在仿件生成器对象上调用，来指定哪些方法将被替换为可配置的测试替身。其他方法的行为不会有所改变。如果调用 ``setMethods(null)``，那么没有方法会被替换。

-

  可以在仿件生成器对象上调用 ``setMethodsExcept(array $methods)`` 来指定哪些方法不被替换为可配置的测试替身，与此同时所有其他 public 方法都会被替换。``setMethods()`` 的作用则相反。

-

  ``setConstructorArgs(array $args)`` 可用于向原版类的构造函数（默认情况下不会被替换为伪实现）提供参数数组。

-

  ``setMockClassName($name)`` 可用于指定生成的测试替身类的类名。

-

  ``disableOriginalConstructor()`` 参数可用于禁用对原版类的构造方法的调用。

-

  ``disableOriginalClone()`` 可用于禁用对原版类的克隆方法的调用。

-

  ``disableAutoload()`` 可用于在测试替身类的生成期间禁用 ``__autoload()``。

.. _test-doubles.mocking-traits-and-abstract-classes:

对特质（Trait）与抽象类进行模仿
###################################

``getMockForTrait()`` 方法返回一个使用了特定特质（trait）的仿件对象。给定特质的所有抽象方法将都被模仿。这样就能对特质的具体方法进行测试。

.. code-block:: php
    :caption: 测试特质的具体方法
    :name: test-doubles.mock-objects.examples.TraitClassTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    final class TraitClassTest extends TestCase
    {
        public function testConcreteMethod(): void
        {
            $mock = $this->getMockForTrait(AbstractTrait::class);

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($mock->concreteMethod());
        }
    }

``getMockForAbstractClass()`` 方法返回一个抽象类的仿件对象。给定抽象类的所有抽象方法将都被模仿。这样就能对抽象类的具体方法进行测试。

.. code-block:: php
    :caption: 测试抽象类的具体方法
    :name: test-doubles.mock-objects.examples.AbstractClassTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    final class AbstractClassTest extends TestCase
    {
        public function testConcreteMethod(): void
        {
            $stub = $this->getMockForAbstractClass(AbstractClass::class);

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($stub->concreteMethod());
        }
    }

.. _test-doubles.stubbing-and-mocking-web-services:

对 Web 服务（Web Services）进行上桩或模仿
###########################################

当应用程序需要和 web 服务进行交互时，会想要在不与 web 服务进行实际交互的情况下对其进行测试。为了给 web 服务创建桩件或仿件，可以像使用 ``getMock()``\ （见上文）那样使用 ``getMockFromWsdl()``\ 。唯一的区别是 ``getMockFromWsdl()`` 所返回的桩件或者仿件是基于以 WSDL 描述的 web 服务，而 ``getMock()`` 返回的桩件或者仿件是基于 PHP 类或接口的。

:numref:`test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php` 展示了如何用 ``getMockFromWsdl()`` 来对（例如）\ :file:`GoogleSearch.wsdl` 中描述的 web 服务上桩。

.. code-block:: php
    :caption: 给 web 服务上桩
    :name: test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class GoogleTest extends TestCase
    {
        public function testSearch(): void
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = true;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = false;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = false;
            $result->resultElements = [$element];
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = [];
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() 现在会返回桩结果，
             * 并不会调用 web 服务的 doGoogleSearch() 方法。
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                false,
                '',
                false,
                '',
                '',
                ''
              )
            );
        }
    }
