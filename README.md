# 在庫管理プログラム

## 処理概要
あるショップの在庫管理をするアプリケーションを作成する。以下の機能を実装する。

##### 1. 在庫一覧表示
<pre style="background-color:#222; color:#FFF; font-family:MS Gothic">
【在庫一覧】
ID　　　ITEM　　　　　　PRICE　　　QUANTITY
--------------------------------------------------/
1　　　 Notebook　　　　280　　　　5
2　　　 Pencil　　　　　120　　　　12
3　　　 Scissors　　　　350　　　　3

*** 処理番号を選択 ***
1:登録　2:更新　3:削除　4:終了
</pre>

##### 2. 在庫登録機能

<pre style="background-color:#222; color:#FFF; font-family:MS Gothic">
*** 処理番号を選択 ***
1:登録　2:更新　3:削除　4:終了
<span style="color:orange">1</span>

【在庫登録】
------ 登録情報を入力してください ------
商品名：<span style="color:orange">Stapler</span>
価格：<span style="color:orange">680</span>
数量：<span style="color:orange">4</span>

登録が完了しました。
----------------------------------------

（在庫一覧表示）
</pre>

##### 3. 在庫更新機能

<pre style="background-color:#222; color:#FFF; font-family:MS Gothic">
*** 処理番号を選択 ***
1:登録　2:更新　3:削除　4:終了
<span style="color:orange">2</span>

【在庫更新】
------ 更新するIDを入力してください -------------
ID：<span style="color:orange">3</span>

※更新しない項目は未入力でEnterしてください。
商品名：
価格：<span style="color:orange">400</span>
数量：<span style="color:orange">8</span>

更新が完了しました。
-------------------------------------------------

（在庫一覧表示）
</pre>

##### 4. 在庫削除機能

<pre style="background-color:#222; color:#FFF; font-family:MS Gothic">
*** 処理番号を選択 ***
1:登録　2:更新　3:削除　4:終了
<span style="color:orange">3</span>

【在庫削除】
------ 削除するIDを入力してください -------------
ID：<span style="color:orange">3</span>

削除が完了しました。
-------------------------------------------------

（在庫一覧表示）
</pre>

##### 5. 終了

<pre style="background-color:#222; color:#FFF; font-family:MS Gothic">
*** 処理番号を選択 ***
1:登録　2:更新　3:削除　4:終了
<span style="color:orange">4</span>

処理を終了します。
</pre>

<br>
## 実装方法


<img src="img/map.png" alt="map" width="650">

#### STOCKテーブル
sample_shopデータベースを作成し、以下のテーブルを作成する。

| 列名 | データ型 | PK | UK | FK | Not Null | 備考 |
|------|---------|:--:|:--:|:--:|:--------:|------|
| ID | INT(11) | 〇 |  |  |  | オートインクリメント |
| ITEM | VARCHAR(20) |  |  |  | 〇 |  |
| PRICE | INT(11) |  |  |  | 〇 |  |
| QUANTITY | INT(11) |  |  |  | 〇 |  |
| UPDATE_DATE | TIMESTAMP |  |  |  | 〇 | 既定値：現在日時 |

```sql
CREATE TABLE STOCK (
    ID        INT(11) AUTO_INCREMENT PRIMARY KEY,
    ITEM      VARCHAR(20) NOT NULL,
    PRICE     INT(11) NOT NULL,
    QUANTITY  INT(11) NOT NULL,
    UPDATE_DATE TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
);
```
```sql
INSERT INTO stock (item, price, quantity) VALUES ('Notebook', 280, 5);
INSERT INTO stock (item, price, quantity) VALUES ('Pencil', 100, 12);
INSERT INTO stock (item, price, quantity) VALUES ('Scissors', 350, 3);
```

<br>
#### Stock.java（パッケージ：jp.shop.dto）

フィールド
| 可視性 | フィールド名 |
|:-----:|-------------|
| private | ID |
| private | 商品名 |
| private | 価格 |
| private | 数量 |

メソッド
※フィールドに関連するsetter/getterを定義

<br>
#### StockDAO.java（パッケージ：jp.shop.dao）

| 可視性 | メソッド名 | 戻り値 | 引数 | 説明 |
|-------|------------|-------|------|---------|
| public | findAll  | List\<Stock>    | なし | 在庫データを全件取得する。 |
| public | insert   | int | Stock | 引数のStockクラスを元に在庫データを登録する。<br>戻り値として登録件数を返却する。 |
| public | update   | int | Stock | 引数のStockクラスを元に在庫データを更新する。<br>戻り値として更新件数を返却する。 |
| public | delete   | int | int | 引数のIDに紐づく在庫データを削除する。<br>戻り値として削除件数を返却する。 |

<br>
#### StockMain.java（パッケージ：jp.shop.main）
