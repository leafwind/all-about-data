# Data Pipeline Migration Case Study

#### 

---

某個 metric 原本應該要根據 key 分開出

但當時實作卻把這些 metric 取 max() 吐出去

原本要不要取 max() 或是用 sum() 等等決策，應該是由收到資料的 business component 決定

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