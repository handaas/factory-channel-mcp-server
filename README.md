# 渠道洞察服务

[该MCP服务提供渠道分布和销售渠道分析，包括经销商网络、销售渠道、渠道覆盖等信息，帮助用户了解企业的渠道布局。](https://www.handaas.com/)

## 主要功能

- 🔍 企业关键词模糊搜索
- 🏪 渠道统计分析
- 📊 渠道企业搜索
- 🌍 同行业对比分析
- 📈 渠道效率分析


## 环境要求

- Python 3.10+
- 依赖包：python-dotenv, requests, mcp

## 本地快速启动

### 1. 克隆项目
```bash
git clone https://github.com/handaas/factory-channel-mcp-server
cd factory-channel-mcp-server
```

### 2. 创建虚拟环境&安装依赖

```bash
python -m venv mcp_env && source mcp_env/bin/activate
pip install -r requirements.txt
```

### 3. 环境配置

复制环境变量模板并配置：

```bash
cp .env.example .env
```

编辑 `.env` 文件，配置以下环境变量：

```env
INTEGRATOR_ID=your_integrator_id
SECRET_ID=your_secret_id
SECRET_KEY=your_secret_key
```

### 4. streamable-http启动服务

```bash
python server/mcp_server.py streamable-http
```

服务将在 `http://localhost:8000` 启动。

#### 支持启动方式 stdio 或 sse 或 streamable-http

### 5. Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "handaas-mcp-server": {
      "type": "streamableHttp",
      "url": "http://127.0.0.1:8000/mcp"
    }
  }
}
```

## STDIO版安装部署

### 设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "factory-channel-mcp-server": {
      "command": "uv",
      "args": ["run", "mcp", "run", "{workdir}/server/mcp_server.py"],
      "env": {
        "PATH": "{workdir}/mcp_env/bin:$PATH",
        "PYTHONPATH": "{workdir}/mcp_env",
        "INTEGRATOR_ID": "your_integrator_id",
        "SECRET_ID": "your_secret_id",
        "SECRET_KEY": "your_secret_key"
      }
    }
  }
}
```

## 使用官方Remote服务

### 1. 直接设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "factory-channel-mcp-server":{
      "type": "streamableHttp",
      "url": "https://mcp.handaas.com/factory/channel_insight?token={token}"  
      }
  }
}
```

### 注意：integrator_id、secret_id、secret_key及token需要登录 https://www.handaas.com/ 进行注册开通平台获取


## 可用工具

### 1. channel_insight_fuzzy_search
**功能**: 企业关键词模糊查询

根据提供的企业名称、人名、品牌、产品、岗位等关键词模糊查询相关企业列表。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 查询各类信息包含匹配关键词的企业
- `pageIndex` (可选): 分页开始位置
- `pageSize` (可选): 分页结束位置 - 一页最多获取50条数据

**返回值**:
- `total`: 总数
- `resultList`: 结果列表，包含企业基本信息

### 2. channel_insight_channel_analysis
**功能**: 渠道统计分析

对指定企业进行渠道统计分析，输出企业在同行业中的资金实力、品牌影响力、注册资本和销售实力等多方面的数据。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型枚举 - name/nameId/regNumber/socialCreditCode

**返回值**:
- `categoryCount`: 同行总数
- `financialStat`: 资金实力分析
  - `regCapitalRank`: 注册资本排名 - 显示注册资本超过同行的数量
  - `regCapitalScope`: 所在注册资本范围
  - `data`: 同行年营业额数据
    - `name`: 数据范围
    - `ratio`: 同行比例
    - `power`: 同行数量
- `salesStat`: 销量实力分析
  - `annualTurnoverRank`: 年营业排名 - 显示营业额超过同行的数量
  - `annualTurnoverScope`: 所在年营业额范围
  - `data`: 同行年营业额数据
  - `categoryCount`: 同行总数
- `productBrandList`: 品牌占比
  - `name`: 数据范围
  - `number`: 数量
  - `ratio`: 同行比例
- `productCategoryList`: 品类占比
  - `name`: 数据范围
  - `number`: 数量
  - `ratio`: 同行比例

### 3. channel_insight_channel_search
**功能**: 渠道企业搜索

根据工厂名称或产品名称以及指定的搜索条件，提供匹配的企业信息列表，包括企业基本信息及主营产品等。

**参数**:
- `matchKeyword` (必需): 匹配关键词 - 工厂名称或产品名称（多个词用顿号隔开）
- `keywordType` (可选): 搜索类型 - 综合搜索/主营产品
- `address` (可选): 地区 - 格式：[["省份","市"],["省份","市"]]
- `channelTradeType` (可选): 销售模式 - 全部/外贸/内贸
- `pageIndex` (可选): 页码 - 从1开始
- `pageSize` (可选): 分页大小 - 一页最多获取50条数据

**返回值**:
- `total`: 总数 - 最多返回10W家
  - `nameId`: 企业id
  - `name`: 企业名称
  - `foundTime`: 成立日期
  - `mainProducts`: 主营产品
  - `tagNames`: 产品类目
  - `regCapital`: 注册资本

## 使用场景

1. **渠道优化**: 企业优化销售渠道布局和管理，制定渠道发展策略
2. **市场拓展**: 分析市场空白区域，制定市场拓展和渠道扩张策略
3. **竞争分析**: 了解竞争对手的渠道优势和市场布局，制定竞争策略
4. **合作伙伴选择**: 寻找优质的经销商和渠道合作伙伴，建立合作关系
5. **投资决策**: 评估企业的渠道实力和市场覆盖能力，支持投资判断
6. **行业研究**: 研究特定行业的渠道分布和销售模式特点
7. **供应链优化**: 优化供应链布局，提高渠道效率和覆盖范围

## 使用注意事项

1. **企业全称要求**: 在调用需要企业全称的接口时，如果没有企业全称则先调取channel_insight_fuzzy_search接口获取企业全称
2. **渠道数据**: 渠道数据可能存在滞后性，建议结合实地调研和市场验证
3. **市场变化**: 渠道布局会随市场变化而调整，需关注数据的时效性
4. **数据来源**: 不同来源的渠道数据可能存在差异，建议交叉验证
5. **地区格式**: 地区搜索需按照指定格式输入，支持多地区组合查询
6. **销售模式**: 注意区分外贸和内贸的不同销售特点和渠道模式

## 使用提问示例


### channel_insight_channel_analysis (渠道统计分析)
3. 华为在手机行业的渠道实力如何？注册资本在同行中排第几？
4. 小米的销售实力分析怎么样？年营业额在同行中的位置如何？
5. 比亚迪的品牌影响力在新能源汽车行业中如何？品类占比情况怎样？

### channel_insight_channel_search (渠道企业搜索)
6. 搜索一下主营"手机配件"的渠道企业，主要分布在哪些地区？
7. 找一下做外贸销售的企业有哪些？注册资本都是多少？
8. 广东省有哪些做内贸的渠道商？产品类目都有什么？