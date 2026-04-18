# INPUT_SPEC

本文件定义 promo-analysis-skill 的输入要求。

---

## 0. 调用前必须补齐的输入信息

在正式分析前，除数据表本身外，还需要先明确：
- 数据路径/数据来源
- 输出格式（Word / HTML / 两者都要）
- 输出路径
- 必要选择项（见 `RUN_SETUP.md`）

若用户没有提供数据路径/数据来源，必须先询问。

---

## 1. 输入模式

### 模式 A：标准模式
推荐至少提供三张表：

- `order_info`
- `exposure_info`
- `activity_timeline`

### 模式 B：最简模式
至少提供两张表：

- `order_info`
- `activity_timeline`

---

## 2. 各表字段要求

## 2.1 订单表 `order_info`

### 必选字段
| 字段名 | 含义 | 示例 |
|---|---|---|
| `order_date` | 下单日期 | 2025-10-01 |
| `user_id` | 用户ID | u_001 |
| `order_id` | 订单ID | o_1001 |
| `sku_id` | SKU ID | sku_888 |
| `category_name` | 商品类目 | 饮料 |
| `gmv` | 销售金额 | 39.90 |
| `qty` | 销售件数 | 2 |
| `discount_amount` | 优惠金额 | 5.00 |

### 可选增强字段
| 字段名 | 含义 |
|---|---|
| `brand_name` | 品牌 |
| `pay_flag` | 是否支付 |
| `coupon_amount` | 券优惠 |
| `platform_subsidy` | 平台补贴 |
| `merchant_subsidy` | 商家补贴 |
| `city` | 城市 |
| `channel` | 渠道 |
| `is_new_user` | 是否新用户 |

### 检查要求
- `order_id + sku_id` 不应重复，或重复可解释
- `gmv >= 0`
- `discount_amount >= 0`
- `order_date` 可转换为日期类型

---

## 2.2 曝光表 `exposure_info`

### 必选字段
| 字段名 | 含义 | 示例 |
|---|---|---|
| `date` | 日期 | 2025-10-01 |
| `sku_id` | SKU ID | sku_888 |
| `exposure_uv` | 曝光用户数 | 312 |
| `purchase_uv` | 购买用户数 | 25 |

### 可选增强字段
| 字段名 | 含义 |
|---|---|
| `click_uv` | 点击用户数 |
| `impression` | 曝光次数 |
| `page_type` | 页面类型 |

### 检查要求
- `date + sku_id` 最好唯一
- `purchase_uv <= exposure_uv`，若不满足需解释口径
- 日期字段能与订单表对齐

---

## 2.3 活动表 `activity_timeline`

### 必选字段
| 字段名 | 含义 | 示例 |
|---|---|---|
| `activity_date` | 活动日期 | 2025-10-25 |
| `activity_name` | 活动名称 | 10月大促 |

### 可选增强字段
| 字段名 | 含义 |
|---|---|
| `activity_type` | 活动类型 |
| `budget` | 预算 |
| `coupon_type` | 优惠形式 |
| `target_group` | 目标群体 |
| `priority_level` | 优先级 |

### 检查要求
- 同一日期若存在多活动，需先聚合
- 日期字段必须唯一到日级
- 活动期定义要清晰，若为窗口型需额外给出 start/end

---

## 3. 各模块最低依赖

| 模块 | 最低依赖 |
|---|---|
| 描述性统计 | 订单表 |
| 周期识别 | 订单表 + 可解析日期 |
| 基线构建 | 订单表 + 活动表 |
| 活动增量评估 | 订单表 + 活动表 |
| 三因子拆解 | 订单表 + 曝光表 |
| 用户分层 | `user_id` |
| SKU 敏感度 | `sku_id` |
| 提前消费 / 蚕食 | 活动表 + SKU/类目粒度 |
| 预算优化 | `discount_amount` 或 `budget` |
| DID / 合成控制 | 活动表 + 足够对照期 |

---

## 4. 推荐派生底表

分析前建议整理出以下底表：

### SKU-day
适合做：
- 单品经营表现
- SKU 敏感度
- SKU 蚕食
- 折扣响应

### Category-day
适合做：
- 活动整体趋势
- 基线建模
- 类目差异
- DID / 合成控制 / 活动窗口评估

### User-day
适合做：
- 用户画像
- 用户分层
- 高敏感用户识别
- 触达建议

### Event-window
适合做：
- 活动前后动态变化
- 提前消费
- 累计增量
- 窗口稳健性

---

## 5. 建议衍生字段

### 时间维度
- `dom`：月内日期
- `dow`：周内星期
- `month`
- `is_month_end`
- `is_weekend`

### 价格与折扣
- `unit_price = gmv / qty`
- `discount_rate = discount_amount / (gmv + discount_amount)` 或按业务口径定义
- `discount_bucket`

### 动态特征
- `lag_gmv_1`
- `lag_gmv_7`
- `lead_gmv_1`
- `lead_gmv_7`

### 用户特征
- `total_spend`
- `total_orders`
- `active_days`
- `month_end_ratio`
- `activity_ratio`
- `avg_order_value`
- `category_diversity`
- `recency`

---

## 6. 最低可分析阈值建议

并非强制，但建议满足：

- 至少覆盖 6~8 周以上的数据
- 至少有一个明确活动窗口
- 类目 / SKU / 用户任一维度样本量不能过小
- 非活动日样本量要足以训练基线
- 若要做稳健性检验，建议有多个活动日或多个活动窗口

---

## 7. 数据不足时的降级分析

| 缺失情况 | 能做 | 不能高质量做 |
|---|---|---|
| 无曝光表 | 基础增量评估、分层、预算优化 | 流量—转化—客单拆解 |
| 无用户表 | 类目 / SKU 分析 | 用户分层 |
| 无SKU | 平台 / 类目分析 | SKU 敏感度与蚕食 |
| 活动太少 | 描述性评估 | 稳健因果识别 |

---

## 8. 调用时需要用户明确选择的事项

除数据表外，还建议让用户同步选择：

1. **输出格式**
   - Word
   - HTML
   - 两者都要

2. **输出路径**
   - 最终文件保存位置

3. **若输出 HTML，风格预设**
   - Executive Blue
   - Strategy Gray
   - Growth Red

其余如报告对象、分析重点、输出粒度、商业建议与 A/B 方案、字段映射等，默认不作为首次调用必问项；仅在口径不清或业务方明确要求时再补问。

### 输出格式差异说明
- **Word**：更完整，适合长文档审阅和修改。
- **HTML**：更精简，只放核心结果、核心数据、核心发现、核心图以及建议。
- **HTML 文件要求**：必须可修改，且修改后可以保存为 PDF。

## 9. 不推荐的输入情况

以下情况要明确提示边界：

1. 只有截图，没有结构化数据
2. 只有活动期，没有足够非活动期
3. 日期无法解析
4. 订单金额、优惠金额口径混乱
5. 用户、SKU、类目主键不一致且无法映射
