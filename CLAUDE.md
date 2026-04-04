# 此间唯我 · 项目上下文

## 项目简介

**名称：** 此间唯我（daily-rpg）  
**用途：** 林林的个人自律 RPG app，完成日常任务赚取经验值和皇帝币，升级解锁称号，兑换奖励  
**使用者：** 林林（个人使用，密码 linlin000）  
**风格定位：** 漂亮、轻量、打开就能用，不要加太多需要每天填写/设置的功能

---

## 技术栈

- 纯 HTML + CSS + JavaScript，零依赖，单文件
- 数据存储：`localStorage`，存储 key 前缀为 `xlin-final`（**永远不要改这个 key**）
- 移动端优先，max-width: 480px
- 支持 6 套主题皮肤（dark/pink/blue/green/gold/custom）

---

## 关键常量（不要随意改）

```javascript
const PW = 'linlin000';       // 密码，永不改
const SK = 'xlin-final';      // localStorage key 前缀，永不改
const TITLES = ['浮梦初醒','孤城暗涌','星海漂泊','椿词爵士','浪漫偏航','深渊之上','梦魇独裁','执掌八方','一步之遥','此间唯我'];
const EXP_TABLE = [100,250,450,700,1050,1500,2100,2900,4000,6000]; // 每级所需EXP（非累计）
```

---

## 等级系统说明

EXP_TABLE[i] 是从第 i+1 级升到第 i+2 级所需的 EXP（非累计）：
- Lv1→2：100 EXP
- Lv2→3：250 EXP（从0开始算，不是从100继续）
- char.exp 是当前等级内的进度，升级后重置
- char.totalExp 是历史累计总量（仅展示用）

---

## 数据结构

```javascript
let char = {level, exp, totalExp, coins, name, customColor}
let tasks = [{id, name, exp, coins, category, custom, completed, frequency, weekDays, permanentDone}]
let rewards = [{id, name, icon, cost}]
let checkin = {lastDate, streak, weekDays}
let redeemLog = [{date, name, icon, cost}]   // 兑换历史
let levelLog = {2:'日期', 3:'日期', ...}      // 升级记录，key 是到达的等级
let skin = 'dark'
```

---

## 已实现功能

- [x] 任务管理（每日/一次性/每周任务，自定义分类）
- [x] 角色升级系统（10个等级，各有称号）
- [x] 皇帝币 + 奖励兑换商店
- [x] 每日签到 + 连续签到奖励
- [x] 6套主题皮肤 + 自定义颜色
- [x] 导出/导入存档（JSON）
- [x] 连续签到3/7天弹出鼓励语（短句风格，如「不否认自己。」）
- [x] 编辑按钮 SVG 图标美化 + 任务行 hover 效果
- [x] 商店兑换历史记录（redeemLog）
- [x] 等级进度表 + 升级日期记录（levelLog）

---

## 用户偏好（重要）

- **UI 要漂亮**，但不要过度复杂
- **鼓励语风格**：短、冷静、有力，不滥情。例：「已经做得很好了，虽然你不信。」「继续。就这样。」
- **不要加**：心情/能量值、日记功能、需要每天手动填写的复杂模块
- **功能添加原则**：打开就能用，可选但不强制填写
- **不要用"老公"等特定关系词**（app 可能会分享给他人）

---

## 开发规范

- 所有改动在分支 `claude/enhance-ui-and-features-KDRD8` 上进行
- 修改前先用 grep/sed 读取目标行，再用 Edit 工具精准修改
- 文件体积大（~400KB）因为有 base64 内嵌图标，用 offset/limit 分段读取
- 每次功能完成后 commit + push

---

## 未来计划

- **diary-app**：独立的私人日记 app（新项目，独立文件夹）
  - 本地存储（IndexedDB），自动保存，支持导出备份 ZIP
  - 全文搜索、标签分类、图片上传
  - 漂亮 UI，移动端优先
