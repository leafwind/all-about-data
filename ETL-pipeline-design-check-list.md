# Data Pipeline Design Check List

#### 

---

## Data Generation

* Delay

  * 上游拿到資料時已經延遲多久？
  * 下游可以接受資料延遲多久才拿到？

* Frequency

  * 上游多久更新一次？
  * 下游可以接受多久更新一次？

* Timing

  * 什麼時間 cluster 比較忙？
  * 什麼時間上游還沒生出來不用白跑？

* Trigger

  * 需要有人通知他開跑嗎？
  * 上游有辦法通知他嗎？
  * 網路能通嗎？

* Backfill

  * 會需要以前的資料嗎？
  * 如果要，從多久以前開始？

## Data Storage

#### SQL

* Index

  * explain query first
  * Index 有順序的概念，Query 是否有按照順序（i.e. 1 or 1+2 or 1+2+3）？
  
* Partition

  * 單張 table 太大時要切 partition 嗎？
  * 自動 event 定時增加 partition 還是手動？AWS RDS 怎麼設定？
  * partition size?
  * partition key?
  * 如果原本沒有 partition 要怎麼 migrate table?