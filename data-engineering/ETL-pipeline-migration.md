# Data Pipeline Migration Rules and Case Study

#### 

---

Rule \#1 盡可能縮短新舊 pipeline 並存的時間

想要淘汰舊的 pipeline 無非是

1. 要加新的功能但舊的架構無法滿足
2. 舊的架構因為資料變多等原因而不堪使用

migrate 期間，需求也會一直增加

如果加在舊的，到時候會變成「亞種」跟新的衝突

如果加在新的，最新的功能可能會跟新的架構衝突



pull request 也會隨著時間慢慢地跟 master branch（假設以 master 為主）越離越遠

如果沒有定時 rebase，到時候解 conflict 的時間可能比重寫還久

---

某個 metric 原本應該要根據 key 分開出

但當時實作卻把這些 metric 取 max\(\) 吐出去

原本要不要取 max\(\) 或是用 sum\(\) 等等決策，應該是由收到資料的 business component 決定

但因為時間有限，商業邏輯跟資料邏輯都混在一起出

於是實作新的 pipeline 時，可能就會忽略當時 business workaround

所有 metric 都分開出

雖然這是邏輯上正確的，但就會跟原本的有落差

首先會遇到測試困難的問題

既然原本的資料要求跟現在不一樣，除非回去把原本的 code 加上測試，否則新的資料結果一定也不一樣

但原本的 code 要在中間產物加測試又談何容易

再來，API 對接也會需要對應的修改

使用到這些舊資料的 component 會預期這些資料已經包含商業邏輯

新的資料要嘛也包商業邏輯，要嘛就要請所有用到的人都改

