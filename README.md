# User-Behavior-Analysis
电信运营商在竞争日益激烈的今天，如何挽留更多用户成为一项关键的业务指标。这就需要了解流失用户的特征，
分析流失的原因，预测流失用户，确定挽留目标用户并制定有效方案。

## 一、分析目标
1. 流失用户都有什么共同的特征？
2. 找出用户流失的原因。
3. 预测流失的用户。
4. 对可能流失的用户制定有效的方案来挽留用户。

## 二、理解数据

数据源来自于[Kaggle](https://www.kaggle.com/blastchar/telco-customer-churn/home)

本数据集描述了电信用户是否流失以及其相关信息，共包含7044条数据，共21个字段，分别介绍如下：

***用户属性***

customerID ： 用户ID。

gender：性别。（Female & Male）

SeniorCitizen ：是否是老年用户（1表示是，0表示不是）

Partner ：是否有伴侣 （Yes or No）

Dependents ：是否有亲属 （Yes or No）

tenure ： 在网时长（0-72，共73个职位）

***电话服务***

PhoneService ： 是否开通电话服务业务 （Yes or No）

MultipleLines： 是否开通了多线服务（Yes 、No or No phoneservice 三种）

***上网服务***

InternetService：是否开通互联网服务 （No, DSL数字网络，fiber optic光纤网络 三种）

OnlineSecurity：是否开通网络安全服务（Yes，No，No internetserive 三种）

OnlineBackup：是否开通在线备份业务（Yes，No，No internetserive 三种）

DeviceProtection：是否开通了设备保护业务（Yes，No，No internetserive 三种）

TechSupport：是否开通了技术支持服务（Yes，No，No internetserive 三种）

StreamingTV：是否开通在线电视（Yes，No，No internetserive 三种）

StreamingMovies：是否开通在线电影（Yes，No，No internetserive 三种）

***合同属性***

Contract：签订合同方式 （按月，一年，两年）

PaperlessBilling：是否开通电子账单（Yes or No）

PaymentMethod：付款方式（bank transfer，credit card，electronic check，mailed check）

MonthlyCharges：月费用

TotalCharges：总费用

***判定属性***

Churn：该用户是否流失（Yes or No）

## 三、导入数据并进行清洗

#### 1. 在MySQL中创建数据库

```
CREATE DATABASE 电信流失用户;
```

#### 2. 使用navicat将数据集导入到MySQL中映射成表

#### 3. 将列名翻译成中文

```
BEGIN;
ALTER TABLE 流失表 CHANGE customerID 顾客ID VARCHAR(20);
ALTER TABLE 流失表 CHANGE gender 性别 VARCHAR(20);
ALTER TABLE 流失表 CHANGE SeniorCitizen 老年用户 VARCHAR(20);
ALTER TABLE 流失表 CHANGE Partner 伴侣用户 VARCHAR(20);
ALTER TABLE 流失表 CHANGE Dependents 亲属用户 VARCHAR(20);
ALTER TABLE 流失表 CHANGE tenure 在网时长 VARCHAR(20);
ALTER TABLE 流失表 CHANGE PhoneService 电话服务 VARCHAR(20);
ALTER TABLE 流失表 CHANGE MultipleLines 多线服务 VARCHAR(20);
ALTER TABLE 流失表 CHANGE InternetService 上网服务 VARCHAR(20);
ALTER TABLE 流失表 CHANGE OnlineSecurity 网络安全 VARCHAR(20);
ALTER TABLE 流失表 CHANGE OnlineBackup 网络备份 VARCHAR(20);
ALTER TABLE 流失表 CHANGE DeviceProtection 设备保护 VARCHAR(20);
ALTER TABLE 流失表 CHANGE TechSupport 技术支持 VARCHAR(20);
ALTER TABLE 流失表 CHANGE StreamingTV 在线电视 VARCHAR(20);
ALTER TABLE 流失表 CHANGE StreamingMovies 在线电影 VARCHAR(20);
ALTER TABLE 流失表 CHANGE Contract 合同期 VARCHAR(20);
ALTER TABLE 流失表 CHANGE PaperlessBilling 电子账单 VARCHAR(20);
ALTER TABLE 流失表 CHANGE PaymentMethod 付款方式 VARCHAR(30);
ALTER TABLE 流失表 CHANGE MonthlyCharges 月租费 VARCHAR(20);
ALTER TABLE 流失表 CHANGE TotalCharges 累计付费 VARCHAR(20);
ALTER TABLE 流失表 CHANGE Churn 是否流失 VARCHAR(20);
COMMIT;
```

#### 4. 数据一致化

除了ID和累计付费列，将表中英文数据替换为中文，将在网时长和月租费两列进行分档。


```
BEGIN;
UPDATE 流失表 SET 性别=REPLACE(性别,'Female','女'),性别=REPLACE(性别,'Male','男') ;
UPDATE 流失表 SET 老年用户=REPLACE(老年用户,0,'否'),老年用户=REPLACE(老年用户,1,'是');
UPDATE 流失表 SET 伴侣用户=REPLACE(伴侣用户,'Yes','是'),伴侣用户=REPLACE(伴侣用户,'No','否');
UPDATE 流失表 SET 亲属用户=REPLACE(亲属用户,'Yes','是'),亲属用户=REPLACE(亲属用户,'No','否');
UPDATE 流失表 SET 电话服务=REPLACE(电话服务,'Yes','是'),电话服务=REPLACE(电话服务,'No','否');
UPDATE 流失表 SET 多线服务=REPLACE(多线服务,'No phone service','无'),多线服务=REPLACE(多线服务,'Yes','是'),多线服务=REPLACE(多线服务,'No','否');
UPDATE 流失表 SET 上网服务=REPLACE(上网服务,'DSL','拨号'),上网服务=REPLACE(上网服务,'Fiber optic','光纤'),上网服务=REPLACE(上网服务,'No','无');
UPDATE 流失表 SET 网络安全=REPLACE(网络安全,'No internet service','无'),网络安全=REPLACE(网络安全,'Yes','是'),网络安全=REPLACE(网络安全,'No','否');
UPDATE 流失表 SET 网络备份=REPLACE(网络备份,'No internet service','无'),网络备份=REPLACE(网络备份,'Yes','是'),网络备份=REPLACE(网络备份,'No','否');
UPDATE 流失表 SET 设备保护=REPLACE(设备保护,'No internet service','无'),设备保护=REPLACE(设备保护,'Yes','是'),设备保护=REPLACE(设备保护,'No','否');
UPDATE 流失表 SET 技术支持=REPLACE(技术支持,'No internet service','无'),技术支持=REPLACE(技术支持,'Yes','是'),技术支持=REPLACE(技术支持,'No','否');
UPDATE 流失表 SET 在线电视=REPLACE(在线电视,'No internet service','无'),在线电视=REPLACE(在线电视,'Yes','是'),在线电视=REPLACE(在线电视,'No','否');
UPDATE 流失表 SET 在线电影=REPLACE(在线电影,'No internet service','无'),在线电影=REPLACE(在线电影,'Yes','是'),在线电影=REPLACE(在线电影,'No','否');
UPDATE 流失表 SET 合同期=REPLACE(合同期,'Month-to-month','1月'),合同期=REPLACE(合同期,'One year','1年'),合同期=REPLACE(合同期,'Two year','2年');
UPDATE 流失表 SET 电子账单=REPLACE(电子账单,'Yes','是'),电子账单=REPLACE(电子账单,'No','否');
UPDATE 流失表 SET 付款方式=REPLACE(付款方式,'Electronic check','电子支票'),付款方式=REPLACE(付款方式,'Mailed check','邮寄支票'),
    付款方式=REPLACE(付款方式,'Bank transfer (automatic)','银行自动转账'),付款方式=REPLACE(付款方式,'Credit card (automatic)','信用卡自动转账');
UPDATE 流失表 SET 是否流失=REPLACE(是否流失,'Yes','是'),是否流失=REPLACE(是否流失,'No','否');
UPDATE 流失表
 SET 在网时长 = (CASE WHEN 在网时长 BETWEEN 1 AND 6  THEN '0.5年' 
    		WHEN 在网时长 BETWEEN 7 AND 12  THEN '1年' 
    	  WHEN 在网时长 BETWEEN 13 AND 24  THEN '2年' 
    	  WHEN 在网时长 BETWEEN 25 AND 36  THEN '3年' 
    		WHEN 在网时长 BETWEEN 37 AND 48  THEN '4年' 
    		WHEN 在网时长 BETWEEN 49 AND 60  THEN '5年'
    		WHEN 在网时长 BETWEEN 61 AND 72  THEN '6年'  
    		ELSE '新用户'  
    	END);-- 在网时长分档
UPDATE 流失表
  SET 月租费 = (CASE 
WHEN 月租费 BETWEEN 0 AND 30.00  THEN '1档' 
WHEN 月租费 BETWEEN 30.01 AND 60.00  THEN '2档' 
WHEN 月租费 BETWEEN 60.01 AND 80.00  THEN '3档' 
WHEN 月租费 BETWEEN 80.01 AND 100.00  THEN '4档' 
WHEN 月租费 BETWEEN 100.01 AND 120.00  THEN '5档'
	END);-- 月付费分档
COMMIT;
```
#### 5. 将在网时长短的新用户从数据集中剔除。
```
DELETE FROM 流失表 WHERE 在网时长='新用户';
```

#### 6. 最后将清洗后的流失表导出为csv文件

## 四、用Python对数据进行分析

#### 4.1. 读取数据
```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

%matplotlib inline
df = pd.read_csv('流失表.csv')
df.head(10)
```

读取后的数据如下所示。

![1.png](https://upload-images.jianshu.io/upload_images/15578485-c2ff6a98fa6162b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4.2. 查看数据集大小

![2](https://upload-images.jianshu.io/upload_images/15578485-661750f56b136817.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4.3. 数据归一化处理

将是否流失这一列中的“是”转化为1，将“否”转化为0。

![3](https://upload-images.jianshu.io/upload_images/15578485-2cd80413be365b3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4.4. 数据可视化呈现

###### 4.4.1 导入绘图包

![4](https://upload-images.jianshu.io/upload_images/15578485-8a1f4744c36e4fcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 4.4.2 查看客户流失占比

![客户流失占比](https://upload-images.jianshu.io/upload_images/15578485-18983da8784726ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图可以看出总的用户流失率为26.6%.

###### 4.4.3 从用户属性角度来看用户属性对流失率的影响

![用户属性对流失率的影响](https://upload-images.jianshu.io/upload_images/15578485-4e7f8f4370f60a46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![用户属性对流失率的影响](https://upload-images.jianshu.io/upload_images/15578485-cb302f27e0b9786f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过观察可以得出性别对用户的流失情况几乎没有影响，而老年用户中流失占比明显要比非老年用户的流失占比要高，单身用户中的流失占比也要比非单身用户的流失占比要高，非亲属用户的的流失占比要比亲属用户的流失占比要高。

###### 4.4.4 对特征进行相关性分析 

提取特征并对特征进行编码

![提取特征并对特征进行编码](https://upload-images.jianshu.io/upload_images/15578485-57300bc2a4833c3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![提取特征并对特征进行编码](https://upload-images.jianshu.io/upload_images/15578485-64e388663bc28d7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建相关性矩阵

![构建相关性矩阵](https://upload-images.jianshu.io/upload_images/15578485-5bdfb349d542744f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用热力图显示相关系数

![使用热力图显示相关系数](https://upload-images.jianshu.io/upload_images/15578485-3ef461c318235c1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![变量间的相关性](https://upload-images.jianshu.io/upload_images/15578485-2d3ac8b699b4a0c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

观察上图得出电话服务与多线服务之间较强的相关性，上网服务、网络安全、
网络备份、设备保护、技术支持、在线电视、在线电影之间也存在较强的相关性，而且都是正相关。

###### 4.4.5 用户流失与各变量之间的相关性

![用户流失与各变量之间的相关性](https://upload-images.jianshu.io/upload_images/15578485-6c0b9a7f6ccbb564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上图可以看出性别与电话服务对用户是否流失几乎没有影响。

###### 4.4.6从上网服务角度来看上网服务对用户流失的影响

![上网服务对用户流失的影响](https://upload-images.jianshu.io/upload_images/15578485-92462c70a051f495.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过观察可知没有开通网络安全、网络备份、设备保护、技术支持的用户的流失占比要开通的用户要高。

###### 4.4.7从合同属性的角度来看不同的签订方式对流失率的影响

![不同的签订方式对流失率的影响](https://upload-images.jianshu.io/upload_images/15578485-b34e70a18282e269.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

合同期越长流失率就月低

###### 4.4.8 付款方式对流失率的影响

![付款方式对流失率的影响](https://upload-images.jianshu.io/upload_images/15578485-f96d5ea9bacc999a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过电子支票付款的用户的流失率要远远高于其他付款的方式。

## 总结与结论

总结：

1. 老年用户、单身用户以及非亲属用户是易流失人群。

2. 电话服务对用户的流失没有直接的影响。

3. 对于开通了上网服务的用户，开通网络安全、网路备份、设备保护、技术支持这些服务可以有效的降低流失率。

4. 签订的合同期越长用户的流失率就越低。

5. 付款方式为电子支票的用户的流失率要高于其他用户。

建议：

1. 可以针对老年用户、单身用户以及非亲属用户展开优惠促销活动，激励用户签订较长时间的合同，并开通上网服务及相关网络服务（可以增加用户粘性）。

2. 优化电子支票付款的使用体验，或者诱导用户选择其它的付款方式。
