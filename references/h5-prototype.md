# H5 可交互原型规范

用于 PRD 之后验证交互与信息架构。产物是**演示用原型**，不是生产代码：不接后端、不写构建链、不做工程化。

## 技术约束

- 纯 HTML + CSS + JavaScript，不用 Vue / React / Angular，不引构建工具。
- Tailwind 走 CDN：`<script src="https://cdn.tailwindcss.com"></script>`（跨平台零配置，不内联整包 CSS）。
- 图标用内联 SVG（w-5 / w-6），字体用系统默认字体。
- 单文件交付：所有页面在同一 HTML 内，用 `showPage()` 切换 section 显示/隐藏，不依赖服务器。

## 页面结构

- 顶部 Tab 导航（sticky）+ 页面容器 + 可选底部固定操作栏。
- 每个页面为独立 `<section id="page-xxx" class="page">`，`.page { display:none }`、`.page.active { display:block }`。
- 视觉：卡片 `bg-white rounded-xl shadow-sm p-4`，背景 `bg-gray-50`，主按钮 `bg-blue-500 text-white rounded-xl`，触摸反馈 `active:bg-gray-100`。

## 核心脚本（必须包含）

```html
<script>
  function showPage(name) {
    document.querySelectorAll('.page').forEach(p => {
      p.classList.remove('active');
      p.classList.add('hidden');
    });
    const target = document.getElementById('page-' + name);
    if (target) { target.classList.remove('hidden'); target.classList.add('active'); }
    document.querySelectorAll('.tab-btn').forEach(btn => {
      const on = (btn.getAttribute('onclick') || '').includes("'" + name + "'");
      btn.classList.toggle('border-blue-500', on);
      btn.classList.toggle('text-blue-500', on);
      btn.classList.toggle('border-transparent', !on);
      btn.classList.toggle('text-gray-500', !on);
    });
    window.scrollTo(0, 0);
  }
</script>
```

## 页面骨架模板

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>产品名称-原型</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    .page { display: none; }
    .page.active { display: block; }
    .hidden { display: none !important; }
  </style>
</head>
<body class="bg-gray-100">
  <div class="flex border-b bg-white sticky top-0 z-10">
    <button onclick="showPage('home')" class="tab-btn flex-1 py-3 text-center border-b-2 border-blue-500 text-blue-500 font-medium">首页</button>
    <button onclick="showPage('detail')" class="tab-btn flex-1 py-3 text-center border-b-2 border-transparent text-gray-500">详情</button>
    <button onclick="showPage('profile')" class="tab-btn flex-1 py-3 text-center border-b-2 border-transparent text-gray-500">我的</button>
  </div>
  <div class="pb-16">
    <section id="page-home" class="page active">
      <div class="p-4 space-y-3">
        <div onclick="showPage('detail')" class="bg-white rounded-xl p-4 shadow-sm cursor-pointer active:bg-gray-50">
          <h3 class="font-medium text-gray-800">列表示例</h3>
          <p class="text-gray-400 text-xs mt-1">点击进入详情页</p>
        </div>
      </div>
    </section>
    <section id="page-detail" class="page hidden">
      <div class="p-4 space-y-4">
        <div class="bg-white rounded-xl p-4 shadow-sm">
          <h1 class="text-lg font-bold text-gray-800">详情标题</h1>
          <p class="text-gray-400 text-sm mt-2">详情描述内容。</p>
        </div>
        <button onclick="showPage('home')" class="w-full py-3 bg-gray-100 text-gray-600 rounded-xl">返回列表</button>
      </div>
    </section>
    <section id="page-profile" class="page hidden">
      <div class="p-4">
        <div class="bg-white rounded-xl p-4 shadow-sm">
          <p class="text-gray-500">个人中心内容</p>
        </div>
      </div>
    </section>
  </div>
</body>
</html>
```

## 交互模式

- 列表 → 详情：`onclick="showPage('detail')"`，返回按钮切回列表页。
- 表单提交：JS 校验（如手机号 `/^1[3-9]\d{9}$/`），成功后 `showPage()` 跳转目标页。
- 简单状态（登录态、用户信息）用 `localStorage` 存取，进入页面时读取渲染。
- 空状态必须有占位提示，不留空白列表。

## 交付前检查

- [ ] 主路径能点通：入口 → 核心动作 → 结果页 → 返回
- [ ] 关键失败态有页面（校验失败、空数据、无权限）
- [ ] 空列表、加载中、错误三种状态各有一处示例
- [ ] 点击区域 ≥ 40px，主按钮在首屏可见
- [ ] 导航高亮与当前页一致，切页后滚动回顶部
- [ ] 单文件可双击打开，不依赖本地服务；除 Tailwind CDN 外无外部资源
- [ ] 与 PRD 的功能范围一致，不悄悄多做一个功能

## 与其他 skill 的边界

- 只要可视化原型、不需要深度产品咨询：用 `product-prototype-generation`（零提问直出、含移动/PC 模板工程）。
- 需要视觉重塑、去模板化、Awwwards 级设计：Web 用 `frontend-design`，App 用 `app-design`。
- 需要无障碍专项（读屏、键盘、对比度）：用 `accessibility`。
