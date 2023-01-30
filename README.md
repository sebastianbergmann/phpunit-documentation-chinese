**The PHPUnit project is no longer involved with the translation of its documentation to other languages. This repository is now archived.**

# 翻译

文档的每一种翻译都用单独的存储库来维护：

* [英文文档](https://github.com/sebastianbergmann/phpunit-documentation-english)
* [西班牙语文档](https://github.com/sebastianbergmann/phpunit-documentation-spanish)
* [法语文档](https://github.com/sebastianbergmann/phpunit-documentation-french)
* [巴西葡萄牙语文档](https://github.com/sebastianbergmann/phpunit-documentation-brazilian-portuguese)
* [日语文档](https://github.com/sebastianbergmann/phpunit-documentation-japanese)
* [简体中文文档](https://github.com/sebastianbergmann/phpunit-documentation-chinese)
* [俄语文档](https://github.com/sebastianbergmann/phpunit-documentation-russian)

## 添加新的翻译

如果要创建新的翻译，请在英语文档的问题跟踪器（issue tracker）中开一个新的问题（issue），
说明您要翻译的语言。会创建一个新的存储库并将其添加到可用翻译中。

理想情况下，您应当已经基于英语文档的分支或副本而准备好一个翻译的版本，
这个版本稍后将导入到官方存储库中。

# 构建文档

## 需求

- Python
- [Sphinx](http://www.sphinx-doc.org/)
- [Read the Docs Sphinx Theme](https://github.com/rtfd/sphinx_rtd_theme)

## 构建 HTML 文档

要构建完整的文档，运行：

```
$ make html
```

之后你就可以在 `build/html` 中找到相应的 HTML 文件了。

## 校对自动化

### 安装

```
$ pip install docutils-ast-writer
$ npm install
```

### 使用

```
$ ./node_modules/.bin/textlint src
```

