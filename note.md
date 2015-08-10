#RDMBS Session Note

####數據正確度
* 數據的先後次序是否關鍵
* 同一個數據，多人存取問題
* 容許系統進入暫時性錯誤的狀態嘛？
* NoSql 較適合做可超賣的系統

#### 數據生存時間
* 沒有永遠的生存時間

#### 系統可用性
* 在超高使用量時，能暫時拒絕用戶嘛?
* 災難時，能讓部份資料先行在線嘛?

#### 規模可伸縮性
* NoSql 沒有 atomicty
* RDB 不適合做擴展

#### 數據延遲是可以接受的
* Facebook,wahtapp是有延遲的
* 在流動電話，一切延遲可以推給流動網路
* 有些延遲用戶不會發現，而且用戶也不會發現 ex 討論區的熱門話題

#### 延遲的數據正確性
* 銀行也是先扣錢，後來才轉帳

#### 不是所有數據都是重要
* 部份數據是（相對上）不重要的
* 越能把正確性『放鬆』，系統便會越快
    - amazon 的 shopp cat 就適合放在NoSql 不適合放在 RDMBS

#### 有限度服務的系統
* 在超乎預計的用戶量時，系統要拒絕部份用戶
    - http 503 
    - 排隊機制
* 收下用戶請求，先回應用戶"請求收下"
    - asynchronous IO
    - 防止http timeout
    - 防止用戶 不斷 reload 增加 request

#### ACID
* atomicity
* consistency
* isolation
* durability

#### RDBMS locking
* RDMBS 在 update時 會保證資料完整性，自動上 write lock 

#### phantom read
[說明](phantom read)

#### non-repeated read

#### 兩種isolation 流派
* Mysql
 - shared-exclusive lock
* oracle
 - mvcc (multiversion concurrency control)

#### RMDBS 常犯錯誤
* 超過100行 會有報應
* 把系統邏輯放到RDBMS的trigger
    - 當出現了bug，要修改資料，trigger會妨礙你
* 在sp使用 loop
    - sql本身是 set-based 盡量用 set operate
* 在sp中使用 recursion

#### 常犯錯誤 schema
* 使用auto-increment 作為pk
 - 別害怕OR/M層的麻煩，系統發生blocking要花的時間成本比OR/M多
 - 如果有 natural key 請優先使用，natural PK 有機會能用作 index skip scan
 - 真的不能用 natural key時 請用 uuid
> instagram 案例 它門的 photo pk 為 {uid + timestamp} 因為同一使用者同一毫秒不會有兩張相片上傳 

* 為了報表而建立index
 - index會大幅影響 master 的 write 效能
 - orcale :每個額外的index 便多用3X時間
 - 報表能在slave node 慢慢產生
 - 報表很多時侯需要range scan, secondary B + tree index 幫助不大

* 在一般系統中使用bitmap index
 - bitmap index 是針對報表的
 - update bitmap index 是單線程的

* 把不同種類的row 放在同一table
 - 會有一個叫row_type的 column，application tier 需要先知道 row_type是什麼才能決定怎去處理row
 - 會有大量的rows 在特定 columns 是 null value
 - 把[狗] {主人，體重，高度} 和 [貓] {主人，體重，高度，毛色，可愛度}放在同一張table

###### 名詞
* Elasticsearch
* redis channel
* OLAP
* OLTP
