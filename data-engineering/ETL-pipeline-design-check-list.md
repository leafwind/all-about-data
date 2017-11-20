# Data Pipeline Design Rules & Check List

#### 

---

## Rule \#1 做好 data pruning 的準備 / 培養 data fetch cost 的概念

沒有做過 data pipeline 的工程師，在做一個服務的時候

很自然會想要把資料先全部蒐集起來，然後再慢慢處理邏輯

但這麼做很常是不切實際的

的確，機器成本越來越低，很多技術也都已經 scalable，但技術上做得到，不代表預算做得到

所以常會面臨到無法處理完所有資料的情形


把 fetch cost 最低的資料先拿到，不需要的支線砍掉，fetch cost 最高的擺在最後處理

通常可以只用到原本的 10% 甚至 1% 機器



舉例來說，假設手上有一年的資料，但遠超出可以負荷的時間與預算

那麼如果時效性重要的，可以考慮只留下最近一個月的資料即可

或者想要分析 CTR，那麼只要留下使用者看過的 log 即可

或是一一調整客戶的案子，那麼只要拿目前還有在跑的案子即可

這些看似簡單，但若不知道資料的意義，卻很容易被忽略

## Rule #2 培養找尋備案（fallback plan）的能力

如果是在一間組織分工明確、需求被清楚定義的公司

那可能可以假設需要的資料一定隨時都能拿到，所有服務也都很難會有 downtime

但大部分公司都不是如此（至少台灣不是）

有時候是系統不穩定、自己寫的程式不穩定

有時候是外部來的資料不穩定

再有時候是成長快速、需求變動頻繁，即使系統沒有出狀況，也很容易不敷使用

因此

需要 A 資料，但原本抓取一次回應時間只要 1s，現在變成 10s 怎麼辦

需要 B 資料，但有 10% 的機會拿不到要 retry，甚至無法同步拿到，只能改用非同步

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



