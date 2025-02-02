---
title: 不同库名或表名的数据校验
aliases: ['/docs-cn/dev/sync-diff-inspector/route-diff/','/docs-cn/dev/reference/tools/sync-diff-inspector/route-diff/']
---

# 不同库名或表名的数据校验

用户在使用 DM 等同步工具时，可以设置 `route-rules` 将数据同步到下游指定表中。sync-diff-inspector 通过设置`rules`提供了校验不同库名、表名的表的功能。

下面是一个简单的例子：

```toml
######################### Databases config #########################
[data-sources.mysql1]
    host = "127.0.0.1"
    port = 3306
    user = "root"
    password = ""

    route-rules = ["rule1"]
    # remove comment if use tidb's snapshot data
    # snapshot = "2016-10-08 16:45:26"
    
[data-sources.tidb]
    host = "127.0.0.1"
    port = 4000
    user = "root"
    password = ""
    # remove comment if use tidb's snapshot data
    # snapshot = "2016-10-08 16:45:26"

[routes.rule1]
schema-pattern = "test_1"      # 匹配数据源的库名，支持通配符 "*" 和 "?"
table-pattern = "t_1"          # 匹配数据源的表名，支持通配符 "*" 和 "?"
target-schema = "test_2"         # 目标库名
target-table = "t_2" # 目标表名
```

使用该配置会对下游的 `test_2.t_2` 与实例 `mysql1` 中的 `test_1.t_1` 进行校验。

如果需要校验大量的不同库名或者表名的表，也可以通过 `rules` 设置映射关系来简化配置。可以只配置 schema 或者 table 的映射关系，也可以都配置。例如上游库 `test_1` 中的所有表都同步到了下游的 `test_2` 库中，可以使用如下配置进行校验：

```toml
######################### Tables config #########################
[data-sources.mysql1]
    host = "127.0.0.1"
    port = 3306
    user = "root"
    password = ""

    route-rules = ["rule1"]
    # remove comment if use tidb's snapshot data
    # snapshot = "2016-10-08 16:45:26"
    
[data-sources.tidb]
    host = "127.0.0.1"
    port = 4000
    user = "root"
    password = ""
    # remove comment if use tidb's snapshot data
    # snapshot = "2016-10-08 16:45:26"

[routes.rule1]
schema-pattern = "test_1"      # 匹配数据源的库名，支持通配符 "*" 和 "?"
table-pattern = "*"          # 匹配数据源的表名，支持通配符 "*" 和 "?"
target-schema = "test_2"         # 目标库名
target-table = "t_2" # 目标表名
```

## 注意事项

1. 如果上游数据库有 `table-0` 也会被下游数据库匹配到。

