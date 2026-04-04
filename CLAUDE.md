# CLAUDE.md — AI Assistant Guide for daily-rpg

## Project Overview

**daily-rpg** is a gamified daily habit and task tracker. Users complete daily tasks to earn EXP and coins, level up their character, and spend coins in a reward shop. The UI is in Chinese.

**Architecture:** Single-page vanilla JavaScript application — no framework, no build step, no backend, no npm packages. The entire application lives in `index.html`.

---

## Repository Structure

```
daily-rpg/
├── index.html    # Entire application (~821 lines)
└── README.md     # Minimal placeholder
```

All HTML, CSS, and JavaScript are in `index.html`. There is no build process.

---

## Running the Application

Open `index.html` directly in a browser. No server, no install, no build needed.

**Default password:** `linlin000`

---

## Data Storage

All persistence is via browser `localStorage`. No backend, no database.

| localStorage Key         | Value            |
|--------------------------|------------------|
| `xlin-final-char`        | Character object |
| `xlin-final-tasks`       | Tasks array      |
| `xlin-final-rewards`     | Rewards array    |
| `xlin-final-checkin`     | Check-in streak  |
| `xlin-final-skin`        | Active theme     |
| `xlin-final-date`        | Last save date   |
| `xlin-final-auth`        | Auth flag        |

**Critical:** Never change the storage key prefix `SK = 'xlin-final'` — doing so breaks all saved user data.

---

## Data Models

### Character
```javascript
{
  level: number,       // 1–10
  exp: number,         // XP within current level
  totalExp: number,    // Cumulative all-time XP
  coins: number,       // Spendable currency
  name: string,        // Display name
  customColor: string  // Hex color for custom theme
}
```

### Task
```javascript
{
  id: number,
  name: string,
  exp: number,         // EXP reward on completion
  coins: number,       // Coin reward on completion
  category: string,    // e.g. '作息', '饮食', '学业', '健康', '生活'
  custom: boolean,     // User-created vs default
  freq: string,        // 'daily' | 'once' | 'weekly'
  completed: boolean,
  weekDays?: number[]  // [0–6] Mon–Sun (weekly tasks only)
}
```

### Reward
```javascript
{
  id: number,
  name: string,
  icon: string,   // Emoji
  cost: number    // Coin cost to redeem
}
```

### Check-in
```javascript
{
  lastDate: string,      // ISO date string of last check-in
  streak: number,        // Consecutive day count
  weekDays: boolean[]    // 7-element array for current week
}
```

---

## Application Structure (index.html)

### CSS (lines ~12–295)
- CSS custom properties (`--c1`, `--c2`, etc.) drive the theme system
- Six built-in themes: `t-dark`, `t-pink`, `t-blue`, `t-green`, `t-gold`, `t-custom`
- Applied as classes on `<body>`: `body.t-dark { --c1: #7c3aed; ... }`
- Mobile-first, max-width 480px, Flexbox layout

### HTML (lines ~296–369)
Five modals plus four pages inside `#app`:
- `#p-home` — Task list ("副本")
- `#p-checkin` — Daily check-in ("签到")
- `#p-shop` — Reward shop ("商店")
- `#p-settings` — Settings ("设置")

### JavaScript (lines ~370–815)

#### Constants
```javascript
const PW = 'linlin000';              // App password — change with care
const SK = 'xlin-final';            // localStorage prefix — DO NOT CHANGE
const TITLES = [...];               // 10 level title strings (Chinese)
const EXP_TABLE = [100,250,450,...] // EXP required per level (10 entries)
const DEFAULT_TASKS = [...];        // 8 default tasks on first load
const DEFAULT_REWARDS = [...];      // 5 default rewards on first load
```

#### State Variables
```javascript
let char = {...};     // Current character state
let tasks = [];       // All tasks
let rewards = [];     // All rewards
let checkin = {...};  // Check-in state
let skin = 'dark';    // Active theme name
```

#### Key Functions
| Function | Purpose |
|----------|---------|
| `save()` | Serialize all state to localStorage |
| `load()` | Deserialize state from localStorage |
| `updateUI()` | Re-render all visible UI elements |
| `checkPw()` | Validate password from input |
| `showPage(id)` | Switch active page |
| `addTask()` | Create and save a new task |
| `completeTask(id)` | Mark task done, award EXP/coins, check level-up |
| `buyReward(id)` | Deduct coins, show toast |
| `doCheckin()` | Process daily check-in with streak bonuses |
| `exportSave()` | Download state as `.json` |
| `importSave()` | Load state from uploaded `.json` |
| `resetData()` | Wipe localStorage and reload |
| `toast(msg)` | Show transient notification |

---

## Game Mechanics

### Character Progression
- 10 levels; non-linear EXP curve: `[100, 250, 450, 700, 1050, 1500, 2100, 2900, 4000, 6000]`
- Level titles (1–10): 浮梦初醒, 孤城暗涌, 星海漂泊, 椿词爵士, 浪漫偏航, 深渊之上, 梦魇独裁, 执掌八方, 一步之遥, 此间唯我

### Check-in Bonuses
- Base: 30 EXP + 20 coins
- 3-day streak bonus: +50 EXP + 30 coins
- 7-day streak bonus: +120 EXP + 80 coins

### Task Frequency
- `daily` — resets at midnight each day
- `once` — completed permanently (hidden after done)
- `weekly` — shown only on selected days of the week (`weekDays` array)

---

## UI Conventions

### CSS Class Naming
| Class | Role |
|-------|------|
| `.page` | Full-screen page container |
| `.panel` | Card/section container |
| `.task-row` | Single task list item |
| `.chk` | Circular checkbox button |
| `.pbtn` | Primary gradient button |
| `.gbtn` | Ghost/secondary button |
| `.dbtn` | Danger/delete button |
| `.sinput` | Styled text input |

### Page Navigation
Pages are toggled with `display: block/none`. Only one page is visible at a time. The bottom nav updates the active indicator class.

---

## Development Guidelines

### Modifying the Single File
Because everything is in `index.html`, changes to CSS, HTML, or JS all happen in the same file. Keep logical sections clearly separated:
- CSS block first
- HTML structure second
- JavaScript logic last

### Adding a New Task Field
1. Add the field to the task object in `addTask()` and `DEFAULT_TASKS`
2. Update `save()` / `load()` if the field needs defaults on migration
3. Update the task rendering in `updateUI()`

### Adding a New Theme
1. Add a `body.t-<name>` CSS block defining `--c1`, `--c2`, and related variables
2. Add a theme selector button in `#p-settings`
3. Add the theme name to the `skin` handling in `updateUI()` and the settings toggle logic

### Changing the Password
Update `const PW = 'linlin000'` in the JavaScript constants section. The auth flag in localStorage (`xlin-final-auth`) will remain valid for existing sessions until cleared.

### localStorage Key Prefix
`const SK = 'xlin-final'` — **do not change this** in production. Changing it silently abandons all existing user saves.

### Save/Load Compatibility
When adding new state fields, always provide defaults in `load()` so older saves don't break:
```javascript
char.newField = char.newField ?? defaultValue;
```

---

## What This Project Does Not Have
- No tests (no testing framework)
- No build system (no webpack, Vite, etc.)
- No backend or API
- No npm / package.json
- No TypeScript
- No CI/CD pipeline
- No linter configuration

Changes can be made directly to `index.html` and verified by opening it in a browser.
