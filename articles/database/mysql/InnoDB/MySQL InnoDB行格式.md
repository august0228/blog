## InnoDB行格式

------

### InnoDB行格式简介

记录在磁盘上存放方式被称为`行格式`，`InnoDB`表格 的行格式决定了其物理行结构。

`InnoDB`目前支持的4种`行格式`

- Redundant
- Compact
- Dynamic
- Compressed

### 指定表的行格式语法

InnoDB表的默认行格式由`innodb_default_row_format`变量定义，该变量的默认值为`DYNAMIC`。

当未显式定义`ROW_FORMAT`表选项或指定`ROW_FORMAT= default`时，将使用默认行格式。
可以使用`CREATE table`或`ALTER table`语句中的`ROW_FORMAT table`选项显式定义表的行格式。

例如:

```mysql
CREATE TABLE t1 (c1 INT) ROW_FORMAT=DYNAMIC;
```

显式定义的`ROW_FORMAT`设置优先级大于默认行格式。指定`ROW_FORMAT=DEFAULT`等同于隐式默认行格式。

可以通过`innodb_default_row_format`变量可以动态设置:

```mysql
mysql> SET GLOBAL innodb_default_row_format=DYNAMIC;
```

可以通过以下语句查看默认行格式

```mysql
SELECT @@innodb_default_row_format;
```

查看表的行格式

```mysql
SHOW TABLE STATUS FROM demo WHERE NAME='user_info'
```

### Compact行格式

![](https://raw.githubusercontent.com/kristensun/cloudimg/master/data/Compact行格式示意图.jpg)

### Reference

------

[InnoDB Row Formats](https://dev.mysql.com/doc/refman/8.0/en/innodb-row-format.html#innodb-row-format-compact)

[InnoDB Storage Formats](https://mariadb.com/kb/en/library/innodb-storage-formats/ )

[InnoDB记录结构](https://juejin.im/book/5bffcbc9f265da614b11b731/section/5bffda656fb9a049b13deba8)