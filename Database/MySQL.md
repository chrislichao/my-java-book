# MySQL

##### 进阶技巧
- CASE WHEN 进行统计
    ```sql
    SELECT CASE pref_name
          WHEN '长沙' THEN '湖南' 
          WHEN '衡阳' THEN '湖南'
          WHEN '海口' THEN '海南' 
          WHEN '三亚' THEN '海南'
        ELSE '其他' END AS district,
        SUM(population) 
    FROM PopTbl
    GROUP BY district;
    ```

- CASE WHEN 进行更新
    ```sql
    UPDATE Salaries
    SET salary = CASE WHEN salary >= 10000 THEN salary * 0.9
    WHEN salary < 10000 THEN salary * 1.2
    ELSE salary END;
    ```

- 巧用 HAVING 子句
    ```sql
    SELECT '存在缺失的编号' AS gap FROM SeqTbl HAVING COUNT(*) <> MAX(seq);
    ```

- 使用自连接删除重复的行
    ```sql
    DELETE FROM Products P1 WHERE id < ( SELECT MAX(P2.id) FROM Products P2 WHERE P1.name = P2.name AND P1.price = P2.price );
    ```
- 使用自连接做排名
    ```sql
    -- 排序从 1 开始。如果已出现相同位次，则跳过之后的位次 
    SELECT P1.name,
           P1.price,
           (SELECT COUNT(P2.price)
              FROM Products P2
             WHERE P2.price > P1.price) + 1 AS rank_1
      FROM Products P1 
      ORDER BY rank_1;
    ```
    
- 巧用 COALESCE 函数
    ```sql
    SELECT COALESCE(city, 'N/A') FROM customers;
    ```

##### 数据库层面问题解决思路
- 突然业务卡顿，无法进行正常业务处理，需要立马解决
  - 1，show processlist
  - 2，explain select * from xxx where name = 'xxx'
  - 3，show index from table;
  - 4，通过执行计划判断，索引问题和语句本身问题
  - 5，查询锁状态，杀掉有问题的session
      ```sql
      show status like '%lock%';
      kill SESSION_ID;
      ```
- 业务周期性卡顿，但还能使用，过了这段时间就好了
  - 1，查看slowlog，分析slowlog，分析出慢查询的语句
  - 2，按照一定的优先级别，进行一个一个排查所有慢语句
  - 3，分析top sql，进行explain调试，查看语句执行时间
  - 4，调整索引或者语句本身

- 系统层面问题解决办法
  - 在实际的生产中，一般认为 cpu只要不超过90%都没什么问题
  - 1：cpu负载高，IO负载低
    - 内存不够
    - 磁盘性能差
    - SQL问题
    - IO问题，磁盘满了
    - TPS过高，大量的小数据IO，大量的全表扫描
  - 2：IO负载高，cpu负载低
    - 大量小的IO写操作：
      - autocommit，产生大量的小IO
      - IO/PS,磁盘的一个定值，硬件出厂的时候，厂家定义的一个每秒最大的IO次数
    - 大量大的IO写操作：
      - SQL问题几率比较大
  - 3：IO和cpu负载都很高
    - 硬件不够了或者SQL存在问题

##### SQL性能优化技巧
- 参数是子查询，使用 EXISTS 代替 IN
- 避免排序
  - 会产生排序的代表性运算：
    - GROUP BY 子句
    - ORDER BY 子句
    - 聚合函数(SUM、COUNT、AVG、MAX、MIN)
    - DISTINCT
    - 集合运算符(UNION、INTERSECT、EXCEPT)
    - 窗口函数(RANK、ROW_NUMBER 等)
  - 较少排序的技巧：
    - 使用集合运算符的 ALL 可选项，如使用UNION ALL替换UNION，前提是不在乎是否重复
    - 使用 EXISTS 代替 DISTINCT
- 在极值函数（MAX/MIN）中使用索引
- 能写在 WHERE 子句里的条件不要写在 HAVING 子句里
- 在 GROUP BY 子句和 ORDER BY 子句中使用索引
- 使用索引时，条件表达式的左侧应该是原始字段，及不应该对字段进行计算或者字段上加函数
- 尽量避免使用否定形式， <>， !=， NOT IN 将不会使用索引
- 进行默认的类型转换，如：字段是字符串，则参数传入最好也是字符串
- 避免使用中间表，最好不要使用子查询
- 使用 LIMIT 1 取得唯一行
- 组合索引使用，要符合最左匹配原则，如果无法保证查询条件里列的顺序和索引一致，可以考虑将联合索引拆分成多个索引
- 使用 LIKE 谓词时，只有前方一致的匹配才能用到索引（最左匹配原则）
- 模型字符串可以使用 _ 时， 尽可能避免使用 %
- 尽量使用自增 id 作为主键
- 在无 WHERE 条件下要计算表的行数，优先使用 count(*)，按照效率排序的话，count(字段)<count(主键 id)<count(1)≈count(*)，count(*) 会选用性能最好的索引来进行排序
- 避免使用 SELECT * ，尽量利用覆盖索引来优化性能
- 如有必要，使用 force index() 强制走某个索引
- 使用 EXPLAIN 来查看 SQL 执行计划
- 批量插入，速度更快