---
title: 如何维护更新日志
date: 2020-09-22
tags: [Git, MarkDown]
categories: 
- 其他
---

### 更新日志是什么

更新日志不是Git提交记录的累加。

更新日志（Change Log）是一个由人工编辑，以时间为倒序的列表， 以记录一个项目中所有版本的显著变动。

### 为什么要提供更新日志

为了让用户和开发人员更简单明确的知晓项目在不同版本之间有哪些显著变动。

### 哪些人需要更新日志

人人需要更新日志。无论是消费者还是开发者，软件的最终用户都关心软件所包含什么。 当软件有所变动时，大家希望知道改动是为何、以及如何进行的

### 怎样制作高质量的更新日志

#### 指导原则

 - 记住日志是写给人的，而非机器。

 - 每个版本都应该有独立的入口。

 - 同类改动应该分组放置。

 - 版本与章节应该相互对应。

 - 新版本在前，旧版本在后。

 - 应包括每个版本的发布日期。

 - 注明是否遵守语义化版本格式。

#### 变动类型

 - `Added` 新添加的功能。

 - `Changed` 对现有功能的变更。

 - `Deprecated` 已经不建议使用，准备很快移除的功能。

 - `Removed` 已经移除的功能。

 - `Fixed` 对bug的修复

 - `Security` 对安全的改进

### 如何减少维护更新日志的精力

在文档最上方提供 `Unreleased` 区块以记录即将发布的更新内容。

这样有两大意义：

 - 大家可以知道在未来版本中可能会有哪些变更

 - 在发布新版本时，可以直接将`Unreleased`区块中的内容移动至新发 布版本的描述区块就可以了

### 有很糟糕的更新日志吗

- 使用git日志：git日志充满各种无意义的信息， 如合并提交、语焉不详的提交标题、文档更新等。提交的目的是记录源码的演化。 一些项目会清理提交记录，一些则不会；更新日志的目的则是记录重要的变更以供最终受众阅读，而记录范围通常涵盖多次提交。

- 无视即将弃用功能：当从一个版本升级至另一个时，人们应清楚（尽管痛苦）的知道哪些部分将出现问题。 应该允许先升级至一个列出哪些功能将会被弃用的版本，待去掉那些不再支持的部分后， 再升级至把那些弃用功能真正移除的版本。即使其他什么都不做，也要在更新日志中列出derecations，removals以及其他重大变动。

- 易混淆的日期格式：2012-06-02不与其他日期格式相混淆，而且还 符合ISO标准（yyyy-MM-dd）。

### 例子

```markdown
## [0.0.5] - 2015-02-16
### Added
- Link, and make it obvious that date format is ISO 8601.

### Changed
- Clarified the section on "Is there a standard change log format?".

### Fixed
- Fix Markdown links to tag comparison URL with footnote-style links.

## [0.0.4] - 2014-08-09
### Added
- Better explanation of the difference between the file ("CHANGELOG")
and its function "the change log".

### Changed
- Refer to a "change log" instead of a "CHANGELOG" throughout the site
to differentiate between the file and the purpose of the file — the
logging of changes.

### Removed
- Remove empty sections from CHANGELOG, they occupy too much space and
create too much noise in the file. People will have to assume that the
missing sections were intentionally left out because they contained no
notable changes.

## [0.0.3] - 2014-08-09
### Added
- "Why should I care?" section mentioning The Changelog podcast.

## [0.0.2] - 2014-07-10
### Added
- Explanation of the recommended reverse chronological release ordering.

## [0.0.1] - 2014-05-31
### Added
- This CHANGELOG file to hopefully serve as an evolving example of a
  standardized open source project CHANGELOG.
- CNAME file to enable GitHub Pages custom domain
- README now contains answers to common questions about CHANGELOGs
- Good examples and basic guidelines, including proper date formatting.
- Counter-examples: "What makes unicorns cry?"
```

参考资料：[https://keepachangelog.com/](https://keepachangelog.com/)