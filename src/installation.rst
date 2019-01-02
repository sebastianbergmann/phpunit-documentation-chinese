

.. _installation:

==========
安装 PHPUnit
==========

.. _installation.requirements:

需求
##

PHPUnit 7.0 需要 PHP 7.1，强烈推荐使用最新版本的 PHP。

PHPUnit 需要使用 `dom <http://php.net/manual/en/dom.setup.php>`_ 和 `json <http://php.net/manual/en/json.installation.php>`_ 扩展，它们通常是默认启用的。

PHPUnit 还需要 `pcre <http://php.net/manual/en/pcre.installation.php>`_、`reflection <http://php.net/manual/en/reflection.installation.php>`_、`spl <http://php.net/manual/en/spl.installation.php>`_ 扩展。这些标准扩展默认启用，并且除非修改 PHP 的构建系统和 C 源代码，否则无法禁用它们。

代码覆盖率分析报告功能需要 `Xdebug <http://xdebug.org/>`_（2.5.0以上）与 `tokenizer <http://php.net/manual/en/tokenizer.installation.php>`_ 扩展。生成 XML 格式的报告需要有 `xmlwriter <http://php.net/manual/en/xmlwriter.installation.php>`_ 扩展。

.. _installation.phar:

PHP 档案包 (PHAR)
##############

要获取 PHPUnit，最简单的方法是下载 PHPUnit 的 `PHP 档案包 (PHAR) <http://php.net/phar>`_，它将 PHPUnit 所需要的所有必要组件（以及某些可选组件）捆绑在单个文件中：

要使用 PHP档案包(PHAR)需要有 `phar <http://php.net/manual/en/phar.installation.php>`_ 扩展。

如果启用了 `Suhosin <http://suhosin.org/>`_ 扩展，需要在 ``php.ini`` 中允许执行 PHAR：

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

如果要全局安装 PHAR：

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-7.0.phar
    $  chmod +x phpunit-7.0.phar
    $  sudo mv phpunit-7.0.phar /usr/local/bin/phpunit
    $  phpunit --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

也可以直接使用下载的 PHAR 文件：

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-7.0.phar
    $  php phpunit-7.0.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.windows:

Windows
=======

整体上说，在 Windows 下安装 PHAR 和手工`在 Windows 下安装 Composer <https://getcomposer.org/doc/00-intro.md#installation-windows>`_ 是一样的过程：

#.

   为 PHP 的二进制可执行文件建立一个目录，例如 :file:`C:\\bin`

#.

   将 ;C:\bin 附加到 ``PATH`` 环境变量中（`相关帮助 <http://stackoverflow.com/questions/6318156/adding-python-path-on-windows-7>`_）

#.

   下载 `<https://phar.phpunit.de/phpunit-7.0.phar>`_ 并将文件保存到 :file:`C:\\bin\\phpunit.phar`

#.

   打开命令行（例如，按 :kbd:`Windows`:kbd:`R` » 输入 cmd » :kbd:`ENTER`)

#.

   建立外包覆批处理脚本（最后得到 :file:`C:\\bin\\phpunit.cmd`）：

   .. code-block:: bash

       C:\Users\username>  cd C:\bin
       C:\bin>  echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
       C:\bin>  exit

#.

   新开一个命令行窗口，确认一下可以在任意路径下执行 PHPUnit：

   .. code-block:: bash

       C:\Users\username>  phpunit --version
       PHPUnit x.y.z by Sebastian Bergmann and contributors.

对于 Cygwin 或 MingW32 (例如 TortoiseGit) shell 环境，可以跳过第五步。 取而代之的是，把文件保存为 :file:`phpunit` （没有 :file:`.phar` 扩展名），然后用 chmod 775 phpunit 将其设为可执行。

.. _installation.phar.verification:

校验 PHPUnit PHAR 发行包
===================

由 PHPUnit 项目分发的所有官方代码发行包都由发行包管理器进行签名。在 `phar.phpunit.de <https://phar.phpunit.de/>`_ 上有 PGP 签名和 SHA1 散列值可用于校验。

下面的例子详细说明了如何对发行包进行校验。首先下载 :file:`phpunit.phar` 和与之对应的单独 PGP 签名 :file:`phpunit.phar.asc`：

.. code-block:: bash

    wget https://phar.phpunit.de/phpunit.phar
    wget https://phar.phpunit.de/phpunit.phar.asc

用单独的签名(:file:`phpunit.phar`)对 PHPUnit 的 PHP 档案包(:file:`phpunit.phar.asc`)进行校验：

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

在本地系统中没有发行包管理器的公钥(``6372C20A``)。为了能进行校验，必须从某个密钥服务器上取得发行包管理器的公钥。其中一个服务器是 :file:`pgp.uni-mainz.de`。所有密钥服务器是链接在一起的，因此连接到任一密钥服务器都可以。

.. code-block:: bash

    gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

现在已经取得了条目名称为"Sebastian Bergmann <sb@sebastian-bergmann.de>"的公钥。不过无法检验这个密钥确实是由名叫 Sebastian Bergmann 的人创建的。但是可以先试着校验发行包的签名：

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Good signature from "Sebastian Bergmann <sb@sebastian-bergmann.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian@php.net>"
    gpg:                 aka "Sebastian Bergmann <sebastian@thephp.cc>"
    gpg:                 aka "Sebastian Bergmann <sebastian@phpunit.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian.bergmann@thephp.cc>"
    gpg:                 aka "[jpeg image of size 40635]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: D840 6D0D 8294 7747 2937  7831 4AA3 9408 6372 C20A

此时，签名已经没问题了，但是这个公钥还不能信任。签名没问题意味着文件未被篡改。可是由于公钥加密系统的性质，还需要再校验密钥 ``6372C20A`` 确实是由真正的 Sebastian Bergmann 创建的。

任何攻击者都能创建公钥并将其上传到公钥服务器。他们可以建立一个带恶意的发行包，并用这个假密钥进行签名。这样，如果尝试对这个损坏了的发行包进行签名校验，由于密钥是“真”密钥，校验将成功完成。因此，需要对这个密钥的真实性进行校验。如何对公钥的真实性进行校验已经超出了本文档的范畴。

有个比较谨慎的做法是创建一个脚本来管理 PHPUnit 的安装，在运行测试套件之前校验 GnuPG 签名。例如：

.. code-block:: bash

    #!/usr/bin/env bash
    clean=1 # 是否在测试完成之后删除 phpunit.phar ？
    aftercmd="php phpunit.phar --bootstrap bootstrap.php src/tests"
    gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
    if [ $? -ne 0 ]; then
        echo -e "\033[33mDownloading PGP Public Key...\033[0m"
        gpg --recv-keys D8406D0D82947747293778314AA394086372C20A
        # Sebastian Bergmann <sb@sebastian-bergmann.de>
        gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
        if [ $? -ne 0 ]; then
            echo -e "\033[31mCould not download PGP public key for verification\033[0m"
            exit
        fi
    fi

    if [ "$clean" -eq 1 ]; then
        # 如果存在就清理掉
        if [ -f phpunit.phar ]; then
            rm -f phpunit.phar
        fi
        if [ -f phpunit.phar.asc ]; then
            rm -f phpunit.phar.asc
        fi
    fi

    # 抓取最新的发行版和对应的签名
    if [ ! -f phpunit.phar ]; then
        wget https://phar.phpunit.de/phpunit.phar
    fi
    if [ ! -f phpunit.phar.asc ]; then
        wget https://phar.phpunit.de/phpunit.phar.asc
    fi

    # 在运行前先校验
    gpg --verify phpunit.phar.asc phpunit.phar
    if [ $? -eq 0 ]; then
        echo
        echo -e "\033[33mBegin Unit Testing\033[0m"
        # 运行测试套件
        `$after_cmd`
        # 清理
        if [ "$clean" -eq 1 ]; then
            echo -e "\033[32mCleaning Up!\033[0m"
            rm -f phpunit.phar
            rm -f phpunit.phar.asc
        fi
    else
        echo
        chmod -x phpunit.phar
        mv phpunit.phar /tmp/bad-phpunit.phar
        mv phpunit.phar.asc /tmp/bad-phpunit.phar.asc
        echo -e "\033[31mSignature did not match! PHPUnit has been moved to /tmp/bad-phpunit.phar\033[0m"
        exit 1
    fi

.. _installation.composer:

Composer
########

如果用 `Composer <https://getcomposer.org/>`_ 来管理项目的依赖关系，只要在项目的 ``composer.json`` 文件中简单地加上对 ``phpunit/phpunit`` 的依赖关系即可：

.. code-block:: bash

    composer require --dev phpunit/phpunit ^|version|

.. _installation.optional-packages:

可选的组件包
######

有以下可选组件包可用：

``PHP_Invoker``

    一个工具类，可以用带有超时限制的方式调用可调用内容。当需要在严格模式下保证测试的超时限制时，这个组件包是必须的。

    PHPUnit 的 PHAR 分发中已经包含了此组件包。可以用以下命令来经由 Composer 安装此组件包：

    .. code-block:: bash

        composer require --dev phpunit/php-invoker

``DbUnit``

    移植到 PHP/PHPUnit 上的 DbUnit 用于提供对数据库交互测试的支持。

    PHPUnit 的 PHAR 分发中已经包含了此组件包。可以用以下命令来经由 Composer 安装此组件包：

    .. code-block:: bash

        composer require --dev phpunit/dbunit


