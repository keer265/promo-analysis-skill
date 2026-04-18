# Promo Analysis Skill

一个面向**促销 / 活动效果评估与优化分析**的可复用 skill 项目。

本项目强调的是**分析目标导向、方法链清晰、输出可复用**，而不是依赖大段脚本。
把调用流程拆成两步：
1. **先做调用配置**：补齐背景信息、数据路径/来源、输出格式、输出路径和必要选项；
2. **再做阶段闸门**：判断当前数据到底能执行到哪一层，再由用户决定是否继续。

---

## 适用场景

适用于以下问题：

- 电商平台促销活动复盘
- 月度 / 节日 / 大促营销活动增量评估
- 优惠券、满减、折扣、补贴策略分析
- 类目、SKU、用户分层的活动敏感度分析
- 活动预算优化与投放优先级重排
- 面向比赛、业务汇报、管理层复盘的分析报告生成

---

## 设计目标

1. **复用优先**：不是只为单一题目服务，而是能迁移到其他平台、活动、品类。
2. **流程固定**：调用配置 → 闸门检查 → 数据检查 → 周期识别 → 反事实基线 → 增量评估 → 因果验证 → 分层 → 优化 → 稳健性 → 商业建议。
3. **交付明确**：支持 Word、HTML、双格式交付。
4. **少代码依赖**：主 skill 以规范、模板、口径、清单为主；`scripts/` 仅放少量伪代码和公式说明。
5. **便于二次改造**：你可以替换字段名、删减模块、扩展图表、切换行业。

---

## 项目结构

```text
promo-analysis-skill-v3/
├── README.md
├── SKILL.md
├── RUN_SETUP.md
├── READINESS_GATE.md
├── INPUT_SPEC.md
├── WORKFLOW.md
├── CHECKLIST.md
├── CHART_GUIDE.md
├── OUTPUT_SCHEMA.md
├── REPORT_TEMPLATE.md
├── HTML_STYLE_PRESETS.md
├── DELIVERY_OPTIONS.md
├── templates/
│   └── html-report-template.html
├── examples/
│   ├── example_prompt.md
│   └── example_output_outline.md
└── scripts/
    ├── pseudocode_pipeline.md
    ├── reference_metric_formulas.md
    └── export-pdf.sh
```

---

## 你至少需要什么数据

### 标准模式（推荐）
至少 3 张表：

1. `order_info`
2. `exposure_info`
3. `activity_timeline`

### 最简模式
至少 2 张表：

1. `order_info`
2. `activity_timeline`

> 没有曝光表时，仍可做基础版活动增量评估，但不能完整拆解 `View_UV × CVR × UValue`，也不适合做高质量的流量—转化—客单分解。

---

## skill 默认怎么工作

### 第一步：先做调用配置
先确认：
- 背景信息
- 数据路径/来源
- 输出格式
- 输出路径
- HTML 风格（仅 HTML）

### 第二步：再做阶段闸门
不是直接开始报告，而是先告诉用户：
- 你目前的数据能做到哪里
- 哪些章节现在能跑
- 哪些会被跳过或降级

### 第三步：经用户确认后再进入分析
可输出：
- 数据质量检查
- 描述性统计
- 自然消费周期识别
- 反事实基线构建
- 活动窗口增量评估
- 事件研究分析
- 增量来源拆解
- 提前消费 / 蚕食效应
- 用户分层与优惠敏感度
- 商品敏感度
- 折扣响应
- 预算优化模拟
- 稳健性检验
- 商业建议 / A/B 方案 / 落地计划

### 第四步：按用户选择交付
- 完整版 **Word 报告**
- 精简版 **HTML 报告**
- 或 **两者都给**

### 最后：对话框补充检查
仅在对话框输出：
- 幻觉/事实一致性检查
- 证据薄弱处
- 建议人工复核点

---

## 推荐使用方式

### 方式 1：先做调用配置 + 闸门检查
```text
请用 promo-analysis-skill-v3。
如果我没有给数据路径，先问我数据路径；
然后让我选择输出格式（Word / HTML / 两者都要）、输出路径，
再做 readiness gate，列出能做、不能做和降级做，然后问我是否继续。
```

### 方式 2：闸门后再继续完整分析
```text
请用 promo-analysis-skill-v3。
数据路径是 D:/promo_data/project_a/
输出格式选 C（Word + HTML）
输出路径是 D:/promo_output/project_a/
如果能达到 Level 2 及以上，就继续生成完整报告。
```

### 方式 3：只生成数据缺口清单
```text
请用 promo-analysis-skill-v3，只做调用配置和数据可执行性检查，
不要正式分析，只给我缺哪些表、哪些字段、补齐后能做到哪一步。
```

---
