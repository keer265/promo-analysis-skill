# pseudocode_pipeline

> 这里不是完整可执行代码，而是为了说明这套 skill 背后的典型分析管线。

---

## 1. 读取数据

```text
read(order_info)
read(exposure_info)
read(activity_timeline)
```

---

## 2. 标准化字段

```text
rename columns
parse dates
check keys
unify metric definitions
```

---

## 3. 数据质量检查

```text
check negative gmv
check negative discount
check duplicate order_id + sku_id
check duplicated activity dates
check missing key fields
```

---

## 4. 构建底表

```text
build sku_day
build category_day
build user_day
build event_window
```

---

## 5. 构建时间与折扣衍生变量

```text
dom = day_of_month(date)
dow = day_of_week(date)
month = extract_month(date)
is_month_end = dom >= threshold
unit_price = gmv / qty
discount_rate = discount / gross_amount
discount_bucket = bucket(discount_rate)
```

---

## 6. 周期识别

```text
plot daily trends
compute acf
compare activity vs non_activity
summarize month-end effects
summarize intra-month cycle and weekday effects
```

---

## 7. 反事实基线

```text
baseline_sample = non_activity_days excluding buffer
fit additive baseline model:
    log(1 + gmv) ~ category FE + dom FE + dow FE + month FE
predict counterfactual baseline
```

---

## 8. 活动增量评估

```text
incremental_gmv = actual_gmv - baseline_gmv
incremental_rate = incremental_gmv / baseline_gmv
iroi = incremental_gmv / discount_amount
```

---

## 9. 事件研究

```text
for each activity date:
    build [-k, +k] window
align relative days
average by relative day
```

---

## 10. 销售变化来源拆解（有曝光表时优先做三因子拆解）

```text
GMV ≈ View_UV × CVR × UValue

compare:
- activity period
- baseline period
decompose incremental change into:
- traffic effect
- conversion effect
- user value effect
```

---

## 11. 提前消费与蚕食

```text
post_activity_gap = actual - baseline
share_below_baseline in post window

sku_lift = avg_gmv_activity - avg_gmv_non_activity
identify share of SKUs with non-positive lift
```

---

## 12. 用户分层

```text
aggregate to user level
create features:
- total_spend
- total_orders
- active_days
- month_end_ratio
- activity_ratio
- avg_discount_rate
- avg_aov
- recency
rule-based segmentation
optional clustering validation
```

---

## 13. 商品敏感度与折扣响应

```text
by sku:
  baseline demand
  activity lift
  avg discount rate

bucket discount rates
observe gmv / proxy cvr response
```

---

## 14. 预算优化

```text
unit = activity × category
for each unit:
  compute incremental_gmv
  compute discount_cost
  compute iroi

simulate:
- current allocation
- 80% budget ranked by iroi
- high-iroi only
- low-discount strategy
```

---

## 15. 稳健性检验

```text
placebo fake activity dates
window robustness: [-3,+3], [-5,+5], [-7,+7]
optional DID / synthetic control
```

---

## 16. 输出报告

```text
render Word report
optional render HTML report
attach checklist results
```
