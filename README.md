# Predict Future Sales
本專案為Kaggle上[**Predict Future Sales**](https://www.kaggle.com/c/competitive-data-science-predict-future-sales/overview)以預測未來銷售量為目標的一項比賽。具體目標為使用過去各間商店各個商品的每日銷售量預測各商店中各種商品的未來一個月銷售量。本專案使用XGBoost建構預測模型，並使用過去34個月的銷售數據進行訓練，並預測未來一個月的銷售量。

# Development Environment
|Package|Version|
|:---:|:---:|
|Pandas|1.2.4
|Numpy|1.19.5
|Scikit-learn|0.24.2
|Joblib|1.0.1

# Dataset
本專案所使用的資料皆由[**Predict Future Sales**](https://www.kaggle.com/c/competitive-data-science-predict-future-sales/overview)提供，詳情請參閱[**Predict Future Sales/Data**](https://www.kaggle.com/c/competitive-data-science-predict-future-sales/data)。
|Data(.csv)|Index|
|:---:|:---:|
|sales_train|date日期、date_block_num某年某月的代號、shop_id商店唯一識別代號、item_id商品唯一識別代號、item_price商品單價、item_cnt_day每日售出商品數量
|items|item_name商品名稱、item_id商品唯一識別代號、item_category_id商品類別唯一識別代號
|items_category|item_category_name商品類別名稱、item_category_id商品類別唯一識別代號
|shop|shop_name商店名稱、shop_id商店唯一識別代號
|test|ID表示測試集中(shop,item)組的唯一識別代號、shop_id商店唯一識別代號、item_id商品唯一識別代號

# Data Processing
## 訓練資料
1. 匯入sales_train.csv
2. 將sales_train.csv中的時間格式由日-月-年改成年-月，並將日期由遠到近排列
3. 將sales_train.csv中日期的部分拆解成年和月，刪除原本date和日的資訊
4. 將item_cnt_day和item_price的離群資料(item_cnt_day>1001 and item_price>300000)刪除
5. 將某筆item_price<0的資料修改成該商店該商品所有價錢的中間值
6. 將data_block_num=0 ~ data_block_num=32的資料當成訓練集，而data_block_num=33的資料當成驗證集

## 測試資料
1. 匯入test.csv
2. 將test.csv多新增'year'欄位，且所有值設為2015
3. 將test.csv多新增'month'欄位，且所有值設為11
4. 將test.csv多新增'data_block_num'欄位，且所有值設為34
5. 將test.csv的所有欄位順序調整成和sales_train.csv一樣

# Model Architecture
本專案之訓練模型使用Scikit-learn中的XGBoost建構，其參數設定如下表所示。
