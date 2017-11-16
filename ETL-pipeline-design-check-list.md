# Data Pipeline Design Rules & Check List

#### 

---

## Rule \#1 做好 data pruning 的準備

沒有做過 data pipeline 的工程師，在做一個服務的時候

很自然會想要把資料先全部蒐集起來，然後再慢慢處理邏輯

但這麼做很常是不切實際的

的確，機器成本越來越低，很多技術也都已經 scalable，但技術上做得到，不代表預算做得到

所以常常會面臨到無法處理完所有資料的情形

把 fetch cost 最低的資料先拿到，不需要的支線砍掉，fetch cost 最高的擺在最後處理

通常可以只用到原本的 10% 甚至 1% 機器

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



