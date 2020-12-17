

.. _installation:

==================
安装 PHPUnit
==================

.. _installation.requirements:

需求
############

PHPUnit |version| 需要 PHP 7.3，强烈推荐使用最新版本的 PHP。

PHPUnit 需要使用 `dom <http://php.net/manual/en/dom.setup.php>`_ 和 `json <http://php.net/manual/en/json.installation.php>`_ 扩展，它们通常是默认启用的。

PHPUnit 还需要 `pcre <http://php.net/manual/en/pcre.installation.php>`_、`reflection <http://php.net/manual/en/reflection.installation.php>`_ 和 `spl <http://php.net/manual/en/spl.installation.php>`_ 扩展。这些标准扩展默认启用，并且除非修改 PHP 的构建系统和 C 源代码，否则无法禁用它们。

代码覆盖率分析报告功能需要 `Xdebug <http://xdebug.org/>`_\ （2.7.0 或以上）和 `tokenizer <http://php.net/manual/en/tokenizer.installation.php>`_ 扩展。生成 XML 格式的报告需要有 `xmlwriter <http://php.net/manual/en/xmlwriter.installation.php>`_ 扩展。

.. _installation.phar:

PHP 档案包（PHAR）
##################

要获取 PHPUnit，最简单的方法是下载 PHPUnit 的 `PHP 档案包（PHAR） <http://php.net/phar>`_，它将 PHPUnit 所需要的所有必要组件（以及某些可选组件）捆绑在单个文件中：

要使用 PHP 档案包（PHAR）需要有 `phar <http://php.net/manual/en/phar.installation.php>`_ 扩展。

如果启用了 `Suhosin <http://suhosin.org/>`_ 扩展，需要在 ``php.ini`` 中允许执行 PHAR：

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

可以在下载后立即使用 PHPUnit PHAR：

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

让 PHAR 可执行是种常见做法：

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ chmod +x phpunit-|version|.phar
    $ ./phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.verification:

校验 PHPUnit PHAR 发行包
===============================

由 PHPUnit 项目分发的所有官方代码发行包都由发行包管理器进行签名。在 `phar.phpunit.de <https://phar.phpunit.de/>`_ 上有 PGP 签名和 SHA256 散列值可用于校验。

下面的例子详细说明了如何对发行包进行校验。首先下载 :file:`phpunit.phar` 和与之对应的单独 PGP 签名 :file:`phpunit.phar.asc`：

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ wget https://phar.phpunit.de/phpunit-|version|.phar.asc

用单独的签名（:file:`phpunit-x.y.phar`）对 PHPUnit 的 PHP 档案包（:file:`phpunit-x.y.phar.asc`）进行校验：

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

在本地系统中没有发行包管理器的公钥（``6372C20A``）。为了能进行校验，必须从某个密钥服务器上取得发行包管理器的公钥。其中一个服务器是 :file:`pgp.uni-mainz.de`。所有密钥服务器是链接在一起的，因此连接到任一密钥服务器都可以。

.. parsed-literal::

    $ curl --silent https://sebastian-bergmann.de/gpg.asc | gpg --import
    gpg: key 4AA394086372C20A: 452 signatures not checked due to missing keys
    gpg: /root/.gnupg/trustdb.gpg: trustdb created
    gpg: key 4AA394086372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    gpg: no ultimately trusted keys found

现在已经取得了条目名称为“Sebastian Bergmann <sb@sebastian-bergmann.de>”的公钥。不过，无法检验这个密钥确实是由名叫 Sebastian Bergmann 的人创建的。但是可以先试着校验发行包的签名：为运行的测试以 HTML 或纯文本格式生成敏捷文档

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
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

用 GPG 来手工验证 PHPUnit PHAR 的真实性和完整性是很繁琐的。这就是 PHAR 安装与校验环境 PHIVE 创建的原因。你可以在其\ `网站 <https://phar.io/>`_\ 上了解 PHIVE。

.. _installation.composer:

Composer
########

如果用 `Composer <https://getcomposer.org/>`_ 来管理项目的依赖关系，只要在项目的 ``composer.json`` 文件中加上对 ``phpunit/phpunit`` 的（开发时）依赖关系即可：

.. parsed-literal::

    composer require --dev phpunit/phpunit ^\ |version|

.. _installation.global:

全局安装
###################

请注意，并不推荐全局安装 PHPUnit，比如说放在 ``/usr/bin/phpunit`` 或 ``/usr/local/bin/phpunit``。

相反，PHPUnit 应该作为项目本地依赖项进行管理。

可以将你所需的特定 PHPUnit 版本的 PHAR 放入项目的 ``tools`` 目录（这目录应当是由 PHIVE 管理的）或者，如果使用 Composer，则取决于在项目的 ``composer.json`` 中指定的所需特定 PHPUnit 版本。

Web 服务器
#############

PHPUnit 是用于编写测试的框架，也是用于运行测试的命令行工具。编写和运行测试是开发时的活动。没有理由要将 PHPUnit 安装在 Web 服务器上。

**如果将 PHPUnit 上传到 Web 服务器，则部署过程会中断。一般而言，如果** ``vendor`` **目录在 Web 服务器上可公开访问，则您的部署过程也会中断。**

请注意，如果将 PHPUnit 上传到 Web 服务器，则可能会发生“坏事”。\ `已经警告过你了。 <https://thephp.cc/news/2020/02/phpunit-a-security-risk>`_
