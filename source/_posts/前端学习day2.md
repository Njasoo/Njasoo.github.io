---
title: 前端学习day2
date: 2025-11-12 14:39:52
tags: ["前端", "typescript"]
---

昨天发现 ts-node 运行 ts 文件一直没有输出结果，搞了大半天还是没搞过来，用 tsc 编译成 js 文件，再运行又太麻烦，这里发现可以安装 tsx 来替代 ts-node 也是非常的方便，那么现在问题就是如何改 code runner 的设置里，因为这里 ts 默认都是 ts-node 执行的

在 settings 搜索框输入 code runner executor map，然后 edit in settings.json，自己看一下就会了

mysql 本地登录

```bash
mysql -u root -p
```

然后输入密码，这里悄悄提醒一下，我的密码很短

```bash
EXPLAIN ANALYZE SELECT * FROM users WHERE id = 878787;

                                                     QUERY PLAN
---------------------------------------------------------------------------------------------------------------------
 Gather  (cost=1000.00..15857.43 rows=1 width=46) (actual time=43.447..51.137 rows=1 loops=1)
   Workers Planned: 2
   Workers Launched: 2
   ->  Parallel Seq Scan on users  (cost=0.00..14857.33 rows=1 width=46) (actual time=38.862..40.451 rows=0 loops=3)
         Filter: (id = 878787)
         Rows Removed by Filter: 333333
 Planning Time: 0.121 ms
 Execution Time: 51.158 ms
(8 rows)
```

explain analyze 可以显示语句执行的策略和执行时间

sql 的 index 原理就是 B+树，可以选定单个或者几个列建立索引，然后就会根据这些属性列创建 B+树，以便后续快速查询

js 的 Map 的 key 存对象和数组的时候，存的是引用，不是值，所以可以存 JSON 字符串

Array(n).fill(Array(m).fill(0))这个玩意会导致每一行都用同一个引用，然后修改其中一个元素就会影响其他几行
