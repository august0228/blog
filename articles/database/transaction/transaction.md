
### Transaction

事物是包含一个或多个SQL语句的原子工作单位。当一个事物多次对数据库做出更改时，只有两种可能，要么是`committed `全部提交：所有更改被应用到数据库，
要么是`rolled back`全部回滚：全部更改被数据库回滚。
数据库事物具有原子性(atomicity)、一致性(consistency)、隔离性(isolation)、持久性(durability)，简称 `ACID`。

#### ACID
`ACID` 模型是一组数据库设计原则，保证了数据库的可靠性。具有`ACID`特性的数据库不需要自己再做额外的一致性检查和崩溃回滚操作，没有可能会因为断电等异常宕机情况导致数据库数据混乱。
所有支持事物的数据库都应该遵守这四个特性：

- A: 原子性(atomicity)
- C: 一致性(consistency)
- I: 隔离性(isolation)
- D: 持久性(durability)

##### 原子性(atomicity)

数据库修改必须遵循`全部成功或全部失败`规则。每个事务都被称为`原子的`。如果事物一部分失败了，那么整个事物也就失败了。不管DBMS、操作系统或硬件出现什么故障，数据库管理系统都必须保持事务的原子性。
例如一个事物要修改100行数据，但是系统在20次之后失败了，数据库会回滚已经更新的20条数据。

##### 一致性(consistency)
一致性表示只有有效的数据将被写入数据库。如果由于某种原因执行的事务违反了数据库的一致性规则，那么整个事务将回滚，一致性确保事务只能将数据库从一种有效状态带到另一种有效状态，维护数据库不变性:
写入数据库的任何数据必须根据所有定义的规则(包括约束、级联、触发器以及它们的任何组合)有效。 这可以防止非法事务破坏数据库，但是不能保证事务是正确的。

例如引用完整性保证了主键-外键关系。

##### 隔离性(isolation)

事物通常情况下是并发执行的(例如多个事物同时操作一张表)，隔离性要求同时发生的多个事物互相不影响彼此的运行。例如隔离级别RC下，A看不到B未提交的修改。

##### 持久性(durability)

持久性确保提交给数据库的任何事务都不会丢失。数据库通过其恢复机制确保事务中的更改不会丢失。

#### ACID是怎样工作的
数据库通常用以下几种方式来实行`ACID`:
用于提升`原子性`和`持久性`的一种方法是`预写式日志(write ahead WAL)`，先把事物细节写到日志中，包括重做日志和撤销日志。这确保了再数据库出乐任何故障的时候，可以通过日志进行比较数据状态。
另一种方法是通过`影子分页`，在修改的时候先新建一个`影子分页`，更新操作的是`影子分页`，而不是真的数据，当提交的时候才把变更修改到数据库。


### References

<hr/>
[Database_transaction]: https://en.wikipedia.org/wiki/Database_transaction
[InnoDB and the ACID Model]: https://dev.mysql.com/doc/refman/8.0/en/mysql-acid.html
[Transactions]: https://docs.oracle.com/en/database/oracle/oracle-database/18/cncpt/transactions.html#GUID-B97790CB-DF82-442D-B9D5-50CCE6BF9FBD
[The ACID Database Model]: https://www.lifewire.com/the-acid-model-1019731