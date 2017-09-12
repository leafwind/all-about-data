# 關於 ETL 那些事：設計原則
#### 

---

## time

### 時區(timezone)設計

假設使用者需要以「天」為單位看到資料呈現，但使用者位於不同國家的時區

使用可以用 int 加減的 timestamp，並以小時為單位存
- 優點
    - 一張表用加減法即可得到需要的資料，非常直覺
- 缺點
    - 需要以天為單位的資料時，必須 runtime 處理，資料多的時候很致命


將資料存成 N 張表（N=時區數量），以天為單位存
- 優點
    - 有些情況下（e.g. MySQL）可能會讓整體 index 變小、效率變高（不同時區的 table 其實就是一種 index）
    - 需要以天為單位時，可以把運算在 ETL 做完，省去 runtime 處理
- 缺點
    - 世界並不是整齊劃分成 24 個整點時區，有例外要處理（e.g. UTC+5:30, UTC+5:45, UTC+14)


regular backfill
abnormal backfill


### interval

half-open interval is preferred

[Time intervals and other ranges should be half-open](http://wrschneider.github.io/2014/01/07/time-intervals-and-other-ranges-should.html)


## 上下游需求

上游更新頻率、delay，何時開始跑才不會炸
下游需求的更新頻率、需求 delay，如何通知他

-> airflow dag, jenkins trigger/polling