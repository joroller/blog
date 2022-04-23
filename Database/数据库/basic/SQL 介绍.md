# SQL 介绍

## 1. SQL 语言组成部分

SQL 语言有以下几个部分：

* 数据定义语言（Data-Definition Language，DDL）：DDL 提供定义关系模式、删除关系以及修改关系模式的命令。
* 数据操纵语言（Data-Manipulation Language，DML）：DML 提供从数据库中查询信息，以及在数据中插入元组、删除元组、修改元组的能力。
* 完整性（integrity）：DDL 包括定义完整性约束的命令，保存在数据库中的数据必须满足所定义的完整性约束。破坏完整性约束的更新是不允许的。
* 视图定义（view definition）：DDL 包括定义视图的命令。
* 事务控制（transaction control）:SQL 包括定义事务的开始和结束的命令。
* 嵌入式 SQL 和动态 SQL（embedded SQL and dynamic SQL）：嵌入式和动态 SQL 定义 SQL 语句如何嵌入到通用编程语言，如 C、C++ 和 Java 中。
* 授权（authorization）：DDL 包括定义对关系和视图的访问权限和命令。