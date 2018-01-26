

.. _other-uses-for-tests:

=======
测试的其他用途
=======

一旦习惯了编写自动测试，就可能会发现测试的更多用途。这有一些例子。

.. _other-uses-for-tests.agile-documentation:

敏捷文档
####

通常，在使用了诸如极限编程之类的敏捷流程的项目中，文档往往无法跟上项目设计与代码的频繁变更。极限编程要求*群体代码所有权(collective code ownership)*，因此所有开发者都需要知道整个系统是如何工作的。如果你足够训练有素，为测试使用了“能说明问题的名称(speaking names)”来描述各个类应当干什么，那么就可以用 PHPUnit 的 TestDox 功能来基于项目的测试生成项目的自动文档。这个文档能够就项目中的各个类应当起什么作用给开发者一份概述。

PHPUnit 的 TestDox 功能着眼于测试类及其所有测试方法的名称，将它们驼峰式大小写（camel case）拼写的 PHP 名称转换为句子：``testBalanceIsInitiallyZero()`` 转化为 "Balance is initially zero（初始结余为零）"。如果有多个测试方法的名字互相之间的差异只是一个或多个数字的后缀，例如 ``testBalanceCannotBecomeNegative()`` 和 ``testBalanceCannotBecomeNegative2()``，假如所有这些测试都成功，句子"Balance cannot become negative（结余不能变为负数）"只会出现一次。

来看一下从 ``BankAccount`` 类生成的敏捷文档：

.. code-block:: bash

    $ phpunit --testdox BankAccountTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    BankAccount
     [x] Balance is initially zero
     [x] Balance cannot become negative

另外，敏捷文档也可以以 HTML 或纯文本格式生成，并写入文件中，用 ``--testdox-html`` 和 ``--testdox-text`` 参数即可。

敏捷文档可以用于将对项目所使用的外部包所做出的假设文档化。使用外部包，你就暴露于这个包的行为与你所预期的不同的风险中，并且包的未来版本可能在你所不知道的情况下有微妙的改变并破坏你的代码。每次做出假设时就编写一个对应的测试可以处理这些风险。如果测试成功，那么假设就有效。如果所有的假设都通过测试来文档化，外部包在未来发布新版本就不会引起忧虑：如果测试成功，那么系统就应当能继续正常运作。

.. _other-uses-for-tests.cross-team-tests:

跨团队测试
#####

一旦用测试将假设文档化，你就拥有了测试。包的提供者——你做假设的对象——对你的测试一无所知。如果打算与包的提供者有更亲密的关系，可以用测试来沟通与协调你的活动。

当你愿意和包的提供着协调你的活动时，你们可以共同编写测试。通过这样的方式，测试能够展现出尽可能多的假设。隐藏的假设是在给合作判死刑。利用测试，你精确的将对所提供的包的预期文档化。提供者在所有测试顺利运行时就知道包已经完整了。

通过使用桩件（参见本书前面关于“仿件对象”的那一章），你可以更好的与供应商解耦：供应商的工作就是让测试能够运行于包的实际实现上；你的工作则是让测试能够运行于你自己的代码上。在你拿到包的实际实现前，使用桩件对象。通过这种方式，两个团队可以互相独立的进行开发。

