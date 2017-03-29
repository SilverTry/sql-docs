---
title: "sys.dm_db_xtp_gc_cycle_stats (Transact-SQL) | Microsoft Docs"
ms.custom: ""
ms.date: "08/29/2016"
ms.prod: "sql-non-specified"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "database-engine"
ms.tgt_pltfrm: ""
ms.topic: "language-reference"
f1_keywords: 
  - "dm_db_xtp_gc_cycle_stats_TSQL"
  - "dm_db_xtp_gc_cycle_stats"
  - "sys.dm_db_xtp_gc_cycle_stats_TSQL"
  - "sys.dm_db_xtp_gc_cycle_stats"
dev_langs: 
  - "TSQL"
helpviewer_keywords: 
  - "sys.dm_db_xtp_gc_cycle_stats dynamic management view"
ms.assetid: bbc9704e-158e-4d32-b693-f00dce31cd2f
caps.latest.revision: 12
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
---
# sys.dm_db_xtp_gc_cycle_stats (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2014-asdb-xxxx-xxx_md](../../includes/tsql-appliesto-ss2014-asdb-xxxx-xxx-md.md)]

  Outputs the current state of committed transactions that have deleted one or more rows. The idle garbage collection thread wakes every minute or when the number of committed DML transactions exceeds an internal threshold since the last garbage collection cycle. As part of the garbage collection cycle, it moves the transactions that have committed into one or more  queues associated with generations. The transactions that have generated stale versions are grouped in a unit of 16 transactions across 16 generations as follows:  
  
-   Generation-0: This stores all transactions that committed earlier than the oldest active transaction. Row versions generated by these transactions are immediately available for garbage collection.  
  
-   Generations 1-14: Stores transactions with timestamp greater than the oldest active transaction. The row versions cannot be garbage collected. Each generation can hold up to 16 transactions. A total of 224 (14 * 16) transactions can exist in these generations.  
  
-   Generation 15: The remaining transactions with timestamp greater than the oldest active transaction go to generation 15. Similar to generation-0, there is no limit of number of transactions in generation-15.  
  
 When there is memory pressure, the garbage collection thread updates the oldest active transaction hint aggressively, which forces garbage collection.  
  
 For more information, see [In-Memory OLTP &#40;In-Memory Optimization&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md).  
  
  
|Column name|Type|Description|  
|-----------------|----------|-----------------|  
|cycle_id|**bigint**|A unique identifier for the garbage collection cycle.|  
|ticks_at_cycle_start|**bigint**|Ticks at the time the cycle started.|  
|ticks_at_cycle_end|**bigint**|Ticks at the time the cycle ended.|  
|base_generation|**bigint**|The current base generation value in the database. This represents the timestamp of the oldest active transaction used to identify transactions for garbage collection. The oldest active transaction id is updated in the increment of 16. For example, if you have transaction ids as 124, 125, 126 … 139, the value will be 124. When you add another transaction, for example 140, the value will be 140.|  
|xacts_copied_to_local|**bigint**|The number of transactions copied from the transaction pipeline into the database's generation array.|  
|xacts_in_gen_0- xacts_in_gen_15|**bigint**|Number of transactions in each generation.|  
  
## Permissions  
 Requires VIEW DATABASE STATE permission on the server.  
  
## Usage Scenario  
 Here is a sample output with a subset of columns, showing 27 generations:  
  
```  
cycle_id   ticks_at_cycle_start ticks_at_cycle_end   base_generation  xacts_in_gen_0    xacts_in_gen_1  
  
1          123160509            123160509            1                    0                    0  
2          123176822            123176822            1                    0                    1  
3          123236826            123236826            1                    0                    1  
4          123296829            123296829            1                    0                    1  
5          123356832            123356941            129                  0                    0  
6          123357473            123357473            129                  0                    0  
7          123417486            123417486            129                  0                    0  
8          123477489            123477489            129                  0                    0  
9          123537492            123537492            129                  0                    0  
10         123597500            123597500            129                  0                    0  
11         123657504            123657504            129                  0                    0  
12         123717507            123717507            129                  0                    0  
13         123777510            123777510            129                  0                    0  
14         123837513            123837513            129                  0                    0  
15         123897516            123897516            129                  0                    0  
16         123957516            123957516            129                  0                    0  
17         124017516            124017516            129                  0                    0  
18         124077517            124077517            129                  0                    0  
19         124137517            124137517            129                  0                    0  
20         124197518            124197518            129                  0                    0  
21         124257518            124257518            129                  0                    0  
22         124317523            124317523            129                  0                    0  
23         124377526            124377526            129                  0                    0  
24         124437529            124437529            129                  0                    0  
25         124497533            124497533            129                  0                    0  
26         124557536            124557536            129                  0                    0  
27         124617539            124617539            129                  0                    0  
  
```  
  
## See Also  
 [Memory-Optimized Table Dynamic Management Views &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/memory-optimized-table-dynamic-management-views-transact-sql.md)  
  
  