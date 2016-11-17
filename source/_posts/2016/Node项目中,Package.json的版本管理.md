---
layout: "post"
title: "Node项目中,Package.json的版本管理"
date: "2015-09-12 16:47"
categories:
- Node
tags:
- Node
- package.json
- npm
---

package.json 的版本的匹配规则

> 用version来表示一个具体的版本

#### 一般范围语法

* `version`  //必须严格匹配到 version版本
* `>version` //必须大于 version的版本
* `>=version`  //大于等于 version的版本
* `<version`  //小于version的版本
* `<=version`  //小于等于
* 带alpha的版本  // 例如 `>1.2.3-alpha.3`,允许匹配版本`1.2.3-alpha.7`，但它不匹配 `3.4.5-alpha.9`，即使`3.4.5-alpha.9`在技术上是“大于” `1.2.3-alpha.3`的。[SemVer排序规则](http://semver.org/)

#### 高级区域语法

* `version1 - version2 `  //连字符范围, `1.2.3 - 2.3.4` 等价于 `>=1.2.3 <=2.3.4`
* `~version` //一般是补丁级别的修改 例如：`~1.2.3` 等价于 `>=1.2.3 <1.(2+1).0` 等价于 `>=1.2.3 <1.3.0`
* `^version` //“兼容的版本”  [参考](https://docs.npmjs.com/misc/semver#caret-ranges-123-025-004)
* `1.2.x`  //匹配  >=1.2.0 <1.3.0
* `*` //匹配任意版本
"" // `""`（空字符串）等价于 `*` 等价于 `>=0.0.0`

* range1 || range2  //range1 或者 range2 的任一版本.


更多的使用方法请访问参考链接


#### 参考

> [The semantic versioner for npm](https://docs.npmjs.com/misc/semver)
> [Semantic Versioning 2.0.0](http://semver.org/#semantic-versioning-200)
