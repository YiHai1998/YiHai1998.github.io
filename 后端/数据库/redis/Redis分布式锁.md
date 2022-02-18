占分布式锁

```
set lock haha NX
```

- NX表示Not Exist不存在
- EX表示过期时间

使用FinalSehll开启全部会话，多台虚拟机都尝试插入lock haha

