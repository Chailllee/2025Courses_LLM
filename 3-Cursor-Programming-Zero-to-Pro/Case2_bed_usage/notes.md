## 后端 vs 前端

| 类型 | 文件 | 职责 |
|------|------|------|
| **后端** (Python) | 3_app.py | Flask 服务器，提供数据接口和渲染页面 |
| **后端** (预处理) | `1_precompute_data.py` | 从 Excel 读取、计算数据、存入缓存 |
| **后端** (辅助) | `2_view_excel_data.py` | 数据分析和可视化探索 |
| **数据源** | `hospital_bed_usage_data.xlsx` | Excel 原始数据 |
| **数据缓存** | data_cache | 预计算好的缓存文件（加快速度） |
| **前端** (HTML/CSS/JS) | `templates/index.html` | 网页界面、图表、交互 |

## 项目运作思路（3 个阶段）

```
第 1 步：数据准备（离线）
  hospital_bed_usage_data.xlsx
         ↓
  1_precompute_data.py（读取 → 计算 → 保存到 data_cache/）
         ↓
  data_cache/data_cache.pkl（预计算结果）


第 2 步：启动服务器（在线）
  python 3_app.py
         ↓
  Flask 应用监听 http://127.0.0.1:5000
         ↓
  浏览器打开这个地址


第 3 步：前后端交互（实时）
  用户打开网页
         ↓
  index.html（前端）发送请求到后端
         ↓
  /api/hospital_usage、/api/department_usage 等接口
         ↓
  3_app.py 返回 JSON 数据
         ↓
  index.html 用 ECharts 绘制图表
```

## 文件之间如何合作？

**后端流程：**
1. `1_precompute_data.py` → 读取 Excel，计算医院/科室/热力图数据 → 存入 `data_cache/data_cache.pkl`
2. 3_app.py 启动时 → 加载缓存文件到内存 → 准备好接收请求
3. 用户访问 http://127.0.0.1:5000 → 3_app.py 的 `/` 路由 → 返回 `index.html`

**前后端交互：**
```javascript
// index.html 中的 JavaScript 代码
$.getJSON('/api/hospital_usage', function(data) {
    // 后端返回 JSON 数据
    // 前端用 ECharts 画图
    updateHospitalChart(data);
});
```

**流程图：**
```
浏览器打开 http://127.0.0.1:5000
    ↓
3_app.py 的 index() 函数返回 index.html
    ↓
浏览器加载 index.html（HTML + CSS + JavaScript）
    ↓
JavaScript 自动发起 4 个请求：
    - /api/hospital_usage        → 获取医院数据
    - /api/department_usage      → 获取科室数据
    - /api/hospital_dept_heatmap → 获取热力图数据
    - /api/free_beds_summary     → 获取汇总数据
    ↓
3_app.py 的各个 @app.route() 函数处理请求，返回 JSON
    ↓
前端 JavaScript 用 ECharts 把 JSON 数据绘制成 4 个图表
    ↓
用户看到最终的网页（蓝色深色主题 + 4 个图表）
```

## 简单总结

- **后端 (.py)**：数据的主人 → 读、存、算、供应数据
- **前端 (index.html)**：展示的艺术家 → 接收数据、化妆、呈现给用户
- **合作方式**：后端通过 HTTP API（JSON 接口）给前端供应数据，前端通过 JavaScript 请求数据并画图

#
#
#


## 后端（3_app.py）的工作流程

```python
@app.route('/api/compute_status')  # ← 定义一个 HTTP 路由
def compute_status():
    """获取数据计算状态"""
    return jsonify({  # ← 把 Python 字典转换成 JSON，发送给前端
        'precomputing': _cache['precomputing'],
        'ready': _cache['ready'],
        'hospital_ready': _cache['hospital_usage'] is not None,
        ...
    })
```

**关键点：**
1. `@app.route('/api/compute_status')` = 这个函数监听 `http://127.0.0.1:5000/api/compute_status` 这个网址
2. 当前端访问这个网址时，Flask 就执行这个函数
3. `jsonify()` = 把 Python 字典转换成 JSON 格式发送回前端

---

## 前端（index.html）的请求流程

```javascript
// 前端 JavaScript 代码
function checkDataStatus() {
    $.getJSON('/api/compute_status', function(data) {
        // data 就是后端返回的 JSON 数据
        console.log(data.ready);        // 获取 ready 状态
        console.log(data.precomputing); // 获取 precomputing 状态
        
        // 根据数据更新前端显示
        if (data.ready) {
            // 数据准备好了，显示内容
        } else if (data.precomputing) {
            // 正在计算，显示加载中
        }
    });
}
```

---

## 完整的前后端通信流程图

```
前端（index.html）                后端（3_app.py）
       ↓                               ↓
发送 HTTP GET 请求                 接收请求
/api/compute_status          @app.route('/api/compute_status')
       ↓                               ↓
       └──────────────────────────────→
                                  执行 compute_status() 函数
                                       ↓
                                  读取 _cache 内存数据
                                       ↓
                                  return jsonify({...})
       ↓                               ↓
接收 JSON 格式的数据 ←─────────────────┘
       ↓
JavaScript 的 callback 函数处理
data.ready、data.precomputing 等
       ↓
更新页面、显示加载状态、绘制图表
```

---

## 具体例子：4 个 API 接口

|  后端函数 | 前端调用 | 返回的数据 |
|---------|---------|---------|
| `@app.route('/api/hospital_usage')` | `$.getJSON('/api/hospital_usage', function(data) {...})` | 医院使用率 JSON |
| `@app.route('/api/department_usage')` | `$.getJSON('/api/department_usage', ...)` | 科室使用率 JSON |
| `@app.route('/api/hospital_dept_heatmap')` | `$.getJSON('/api/hospital_dept_heatmap', ...)` | 热力图 JSON |
| `@app.route('/api/free_beds_summary')` | `$.getJSON('/api/free_beds_summary', ...)` | 汇总数据 JSON |

---

## 数据流动的完整链条

```
用户打开浏览器
     ↓
访问 http://127.0.0.1:5000
     ↓
后端 index() 函数返回 index.html
     ↓
前端加载 HTML + CSS + JavaScript
     ↓
JavaScript 自动执行（页面加载完成）
     ↓
发送 4 个 HTTP 请求：
  - /api/hospital_usage
  - /api/department_usage
  - /api/hospital_dept_heatmap
  - /api/free_beds_summary
     ↓
后端各个函数处理请求，返回 JSON
     ↓
前端 JavaScript 接收 JSON
     ↓
用 ECharts 绘制 4 个图表
     ↓
用户看到最终的网页
```

---

## 简单总结

- **后端** = "数据服务员"，监听前端的请求，返回数据
- **前端** = "请求者"，主动发送请求，接收数据，展示给用户
- **通信方式** = HTTP 请求 + JSON 响应
- **联系纽带** = `@app.route()` 定义的 URL 路由

现在你明白了吗？有什么想修改的（比如添加新的 API 接口、修改返回的数据格式）？