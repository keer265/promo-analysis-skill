# reference_metric_formulas

本文件列出常用指标公式与解释口径。

---

## 1. 基础指标

### GMV
```text
GMV = 销售金额总和
```

### Order Count
```text
订单数 = 去重订单ID数量
```

### Pay UV / Purchase UV
```text
支付用户数 / 购买用户数 = 去重用户ID数量
```

### Exposure UV
```text
曝光用户数 = 在曝光表中看到某SKU/类目的用户数
```

---

## 2. 派生指标

### Unit Price
```text
unit_price = gmv / qty
```

### Discount Rate
可按业务口径选一种，但必须全文一致。

常见写法：
```text
discount_rate = discount_amount / (gmv + discount_amount)
```

或：
```text
discount_rate = discount_amount / original_price_amount
```

---

## 3. 反事实增量指标

### Incremental GMV
```text
incremental_gmv = actual_gmv - baseline_gmv
```

### Incremental Rate
```text
incremental_rate = incremental_gmv / baseline_gmv
```

### iROI
```text
iROI = incremental_gmv / discount_amount
```

> 注意：这里是“每 1 元优惠投入带来多少增量 GMV”，不是利润 ROI。

---

## 4. 三因子近似拆解

### 基本关系
```text
GMV ≈ View_UV × CVR × UValue
```

其中：

- `View_UV`：曝光用户数或进入购买漏斗的用户数
- `CVR`：转化代理
- `UValue`：单个购买用户价值

### 常见定义
```text
CVR = purchase_uv / exposure_uv
UValue = gmv / purchase_uv
```

---

## 5. 用户画像指标

### Total Spend
```text
total_spend = Σ gmv
```

### Total Orders
```text
total_orders = 去重订单数
```

### Active Days
```text
active_days = 有购买行为的日期数
```

### Month-end Ratio
```text
month_end_ratio = 月底窗口消费金额 / 总消费金额
```

### Activity Ratio
```text
activity_ratio = 活动期消费金额 / 总消费金额
```

### Average Order Value
```text
avg_order_value = 总消费金额 / 总订单数
```

### Category Diversity
```text
category_diversity = 去重类目数
```

### Recency
```text
recency = 样本结束日 - 最近一次购买日
```

---

## 6. 商品敏感度指标

### SKU Lift
```text
sku_lift = avg_gmv_activity - avg_gmv_non_activity
```

或相对提升：
```text
sku_lift_rate = (avg_gmv_activity - avg_gmv_non_activity) / avg_gmv_non_activity
```

---

## 7. 提前消费与蚕食

### 活动后回吐
```text
post_gap = actual_gmv_post - baseline_gmv_post
```

### 活动后低于基线占比
```text
share_below_baseline = post_period_samples_with_gap_lt_0 / total_post_period_samples
```

### 蚕食识别
```text
share_non_positive_sku_lift = count(sku_lift <= 0) / total_common_skus
```

---

## 8. 预算优化单元

推荐粒度：
```text
optimization_unit = activity × category
```

每个单元计算：
```text
unit_incremental_gmv
unit_discount_cost
unit_iroi = unit_incremental_gmv / unit_discount_cost
```

---

## 9. 关键提醒

1. 所有公式都允许按业务口径微调，但必须全文一致  
2. 一旦改了公式，表格、正文、图表解释都要同步  
3. 在没有曝光数据时，不要强行做三因子拆解  
4. iROI 与利润率不是一个概念
