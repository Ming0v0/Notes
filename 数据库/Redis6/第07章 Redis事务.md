# Redis-事务-锁机制-秒杀
## Redis的事务定义
![[attachment/Pasted image 20220614234908.png]]
Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的主要作用就是**串联多个命令**防止别的命令插队。

## Multi、Exec、discard
从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行。

组队的过程中可以通过discard来放弃组队。
![[attachment/Pasted image 20220614235003.png]]

**案例：**
![[attachment/Pasted image 20220614235022.png]]
组队成功，提交成功

![[attachment/Pasted image 20220614235031.png]]
组队阶段报错，提交失败

![[attachment/Pasted image 20220614235041.png]]
组队成功，提交有成功有失败情况

## 事务的错误处理
组队中某个命令出现了报告错误，执行时整个的所有队列都会被取消。
![[attachment/Pasted image 20220614235112.png]]
如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。
![[attachment/Pasted image 20220614235126.png]]

## 为什么要做成事务
想想一个场景：有很多人有你的账户,同时去参加双十一抢购

### 事务冲突的问题
**例子**
- 一个请求想给金额减8000
- 一个请求想给金额减5000
- 一个请求想给金额减1
![[attachment/Pasted image 20220614235244.png]]

### 悲观锁和乐观锁
![[attachment/Pasted image 20220614235343.png]]
**悲观锁(Pessimistic Lock)**, 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。**传统的关系型数据库里边就用到了很多这种锁机制**，比如**行锁**，**表锁**等，**读锁**，**写锁**等，都是在做操作之前先上锁。

![[attachment/Pasted image 20220614235347.png]]
**乐观锁(Optimistic Lock),** 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。**乐观锁适用于多读的应用类型，这样可以提高吞吐量**。Redis就是利用这种check-and-set机制实现事务的。

###  WATCH key [key]
在执行multi之前，先执行watch key1 \[key2],可以监视一个(或多个) key ，如果在事务**执行之前这个(或这些) key被其他命令所改动，那么事务将被打断。**
![[attachment/Pasted image 20220918214930.png]]
### unwatch
取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后，EXEC 命令或DISCARD 命令先被执行了的话，那么就不需要再执行UNWATCH 了
[http://doc.redisfans.com/transaction/exec.html](http://doc.redisfans.com/transaction/exec.html)
### Redis 事务三特性
- 单独的隔离操作
	事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 没有隔离级别的概念
	 队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行
- 不保证原子性
	事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚