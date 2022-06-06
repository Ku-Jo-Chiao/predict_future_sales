# Predict Future Sales
本專案為Kaggle上[**Predict Future Sales**](https://www.kaggle.com/c/competitive-data-science-predict-future-sales/overview)以預測未來銷售量為目標的一項比賽。具體目標為使用過去各間商店各個商品的每日銷售量預測各商店中各種商品的未來一個月銷售量。本專案使用XGBoost建構預測模型，並使用過去34個月的銷售數據進行訓練，並預測未來一個月的銷售量。本專案測試了許多不同格式的訓練資料集與模型，最終採用[**Predict_future_sales.ipynb**](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/Predict_future_sales.ipynb) 版本作為最終實作成果。

# Development Environment
本專案是在colab上做訓練與測試。
|Package|Version|
|:---:|:---:|
|Pandas|1.2.4
|Numpy|1.19.5
|Scikit-learn|0.24.2

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

## 資料觀察
1. 查看商品類別與商品月銷售量之關係。
![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/category_%E6%9C%88%E9%8A%B7%E5%94%AE.png)
2. 查看店家與商品月銷售量之關係。
![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/shop%E6%9C%88%E9%8A%B7%E5%94%AE.png)
3. 將數據的時間線向後平移（1月label是2月銷售量）
4. 針對每個商品價格波動作分析（商品最高價格、商品最低價格、價格增加量、價格減少量）
5. 對商品、店家、年、月建構均值特徵

## 訓練資料
1. 匯入sales_train.csv
2. 將sales_train.csv中的時間格式由日-月-年改成年-月，並將日期由遠到近排列
3. 將sales_train.csv中日期的部分拆解成年和月，刪除原本date和日的資訊
4. 將item_cnt_day和item_price的離群資料(item_cnt_day>1001 and item_price>300000)刪除
5. 將某筆item_price<0的資料修改成該商店該商品所有價錢的中間值
6. 將data_block_num=0 ~ data_block_num=26的資料當成訓練集，而data_block_num=27~33的資料當成驗證集

## 測試資料
1. 匯入test.csv
2. 將test.csv的所有欄位順序調整成和sales_train.csv一樣

# Model Architecture
本專案之訓練模型使用Scikit-learn中的XGBoost建構，其參數設定如下表所示。
![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/xgb_arch.PNG "XGBoost 參數設定") 

## 訓練模型
使用[**Predict_future_sales.ipynb**](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/Predict_future_sales.ipynb)進行訓練，本程式中包含了訓練資料的處理以及訓練、驗證模型兩部分。

|Name|Input|Default
|:---:|---|---
|--training|訓練資料|./data/sales_train.csv
|--testing|測試目標商店與商品的列表|./data/test.csv
|--output|輸出訓練模型|xgb_model.h5

#### 訓練資料集所有的特徵如下圖所示。
![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/all_features.png)

#### 訓練與驗證資料集格式如下圖所示。
![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/data_format.png)

## 測試模型
使用[**Predict_future_sales.ipynb**](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/Predict_future_sales.ipynb)進行測試，本程式中包含了測試資料的處理以及使用模型預測結果輸出兩部分。

|Name|Input|Default
|:---:|---|---
|--model|訓練完成的模型|xgb_model.h5
|--training|訓練模型時使用的資料|./data/sales_train.csv
|--testing|測試目標商店與商品的列表|./data/test.csv
|--output|輸出預測結果|xgb_sub_v4.csv

#### 測試資料集格式如下圖所示。
![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/data_format.png)

# 成果
## 模型
本專案之模型訓練成果如下圖所示。

![GITHUB](https://github.com/Ku-Jo-Chiao/predict_future_sales/blob/main/figure/xgb_mse.PNG "XGBoost mse result")

## 預測
預測結果之格式如下表所示

|ID|item_cnt_month|
|:---|:---|
|0|0.4320
|1|0.08875
|2|0.55715
|3|0.088752
|4|0.088752
|214196|0.08897
|214197|0.2167
|214198|0.0889
|214199|0.19906

# 其他
1.由於我們訓練的時候是在colab上做訓練與測試，若要下載本專案之程式碼來跑的話檔案路徑需更改('/content/gdrive/MyDrive/Colab Notebooks/sales_train.csv'->'sales_train.csv')
2.由於sales_train.csv檔案太大無法上傳至github，所以跑程式碼前須先至kaggle下載該檔案
