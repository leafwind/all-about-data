# Data Schema 設計原則

#### 

---

## 基本原則


---

* What: 資料所代表的物理意義是什麼？
* How: 生產者是如何產生、使用者會如何使用（同時也有助於了解上一點）
* Who: 誰產生這些資料、誰可能會用到這些資料
* Define what should (not) appear


無形的 gap
應用之間、資料庫之間、組織之間、公司之間
這些都會增加 data pipeline 的溝通困難，因而導致資料「變質」


## time

### 時區\(timezone\)設計

假設使用者需要以「天」為單位看到資料呈現，但使用者位於不同國家的時區

使用可以用 int 加減的 timestamp，並以小時為單位存

* 優點
  * 一張表用加減法即可得到需要的資料，非常直覺
* 缺點
  * 需要以天為單位的資料時，必須 runtime 處理，資料多的時候很致命

將資料存成 N 張表（N=時區數量），以天為單位存

* 優點
  * 有些情況下（e.g. MySQL）可能會讓整體 index 變小、效率變高（不同時區的 table 其實就是一種 index）
  * 需要以天為單位時，可以把運算在 ETL 做完，省去 runtime 處理
* 缺點
  * 世界並不是整齊劃分成 24 個整點時區，有例外要處理（e.g. UTC+5:30, UTC+5:45, UTC+14\)

regular backfill  
abnormal backfill

關於時區，打算用另一篇文章來解釋我遇過的問題

### interval

half-open interval is preferred

[Time intervals and other ranges should be half-open](http://wrschneider.github.io/2014/01/07/time-intervals-and-other-ranges-should.html)

## 不該犯的錯誤

### 不要增加無意義的欄位

明確地說，如果已經有 A，而且可以從 A 輕易得到 B，那就不要增加 B 欄位

比如已經有了 int 存的 timestamp，就不要再新增一個對齊小時的 `hr_timestamp`

他其實可以用 `timestamp - timestamp % 3600` 得到

而這樣的操作不管是在 Spark SQL, MySQL 甚至 python 內部都能輕易做到，不需要額外的資料，也不會耗費太多計算資源

實在是沒有必要為它增加一個欄位

### 多個類似欄位存在時，命名要非常清楚避免混淆

延續上面例子，如果一張表裡面已經有不只一個 timestamp

此時又產生一個叫做 `hr_timestamp` 的欄位

除非去看原始碼，否則看到的人絕對不知道是從哪個 timestamp 生出來的時間


