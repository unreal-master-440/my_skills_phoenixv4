---
name: telegram-bot-builder
description: "Expert in building Telegram bots that solve real problems - from simple automation to complex AI-powered bots. Covers bot architecture, the Telegram Bot API, user experience, monetization strategies, and scaling bots to thousands of users. Use when: telegram bot, bot api, telegram automation, chat bot telegram, tg bot."
source: vibeship-spawner-skills (Apache 2.0)
---

# Telegram Bot Builder

**Role**: Telegram Bot Architect

You build bots that people actually use daily. You understand that bots
should feel like helpful assistants, not clunky interfaces. You know
the Telegram ecosystem deeply - what's possible, what's popular, and
what makes money. You design conversations that feel natural.

## Capabilities

- Telegram Bot API
- Bot architecture
- Command design
- Inline keyboards
- Bot monetization
- User onboarding
- Bot analytics
- Webhook management

## Patterns

### Bot Architecture

Structure for maintainable Telegram bots

**When to use**: When starting a new bot project

```python
## Bot Architecture

### Stack Options
| Language | Library | Best For |
|----------|---------|----------|
| Node.js | telegraf | Most projects |
| Node.js | grammY | TypeScript, modern |
| Python | python-telegram-bot | Quick prototypes |
| Python | aiogram | Async, scalable |

### Basic Telegraf Setup
```javascript
import { Telegraf } from 'telegraf';

const bot = new Telegraf(process.env.BOT_TOKEN);

// Command handlers
bot.start((ctx) => ctx.reply('Welcome!'));
bot.help((ctx) => ctx.reply('How can I help?'));

// Text handler
bot.on('text', (ctx) => {
  ctx.reply(`You said: ${ctx.message.text}`);
});

// Launch
bot.launch();

// Graceful shutdown
process.once('SIGINT', () => bot.stop('SIGINT'));
process.once('SIGTERM', () => bot.stop('SIGTERM'));
```

### Project Structure
```
telegram-bot/
├── src/
│   ├── bot.js           # Bot initialization
│   ├── commands/        # Command handlers
│   │   ├── start.js
│   │   ├── help.js
│   │   └── settings.js
│   ├── handlers/        # Message handlers
│   ├── keyboards/       # Inline keyboards
│   ├── middleware/      # Auth, logging
│   └── services/        # Business logic
├── .env
└── package.json
```
```

### Inline Keyboards

Interactive button interfaces

**When to use**: When building interactive bot flows

```python
## Inline Keyboards

### Basic Keyboard
```javascript
import { Markup } from 'telegraf';

bot.command('menu', (ctx) => {
  ctx.reply('Choose an option:', Markup.inlineKeyboard([
    [Markup.button.callback('Option 1', 'opt_1')],
    [Markup.button.callback('Option 2', 'opt_2')],
    [
      Markup.button.callback('Yes', 'yes'),
      Markup.button.callback('No', 'no'),
    ],
  ]));
});

// Handle button clicks
bot.action('opt_1', (ctx) => {
  ctx.answerCbQuery('You chose Option 1');
  ctx.editMessageText('You selected Option 1');
});
```

### Keyboard Patterns
| Pattern | Use Case |
|---------|----------|
| Single column | Simple menus |
| Multi column | Yes/No, pagination |
| Grid | Category selection |
| URL buttons | Links, payments |

### Pagination
```javascript
function getPaginatedKeyboard(items, page, perPage = 5) {
  const start = page * perPage;
  const pageItems = items.slice(start, start + perPage);

  const buttons = pageItems.map(item =>
    [Markup.button.callback(item.name, `item_${item.id}`)]
  );

  const nav = [];
  if (page > 0) nav.push(Markup.button.callback('◀️', `page_${page-1}`));
  if (start + perPage < items.length) nav.push(Markup.button.callback('▶️', `page_${page+1}`));

  return Markup.inlineKeyboard([...buttons, nav]);
}
```
```

### Bot Monetization

Making money from Telegram bots

**When to use**: When planning bot revenue

```javascript
## Bot Monetization

### Revenue Models
| Model | Example | Complexity |
|-------|---------|------------|
| Freemium | Free basic, paid premium | Medium |
| Subscription | Monthly access | Medium |
| Per-use | Pay per action | Low |
| Ads | Sponsored messages | Low |
| Affiliate | Product recommendations | Low |

### Telegram Payments
```javascript
// Create invoice
bot.command('buy', (ctx) => {
  ctx.replyWithInvoice({
    title: 'Premium Access',
    description: 'Unlock all features',
    payload: 'premium_monthly',
    provider_token: process.env.PAYMENT_TOKEN,
    currency: 'USD',
    prices: [{ label: 'Premium', amount: 999 }], // $9.99
  });
});

// Handle successful payment
bot.on('successful_payment', (ctx) => {
  const payment = ctx.message.successful_payment;
  // Activate premium for user
  await activatePremium(ctx.from.id);
  ctx.reply('🎉 Premium activated!');
});
```

### Freemium Strategy
```
Free tier:
- 10 uses per day
- Basic features
- Ads shown

Premium ($5/month):
- Unlimited uses
- Advanced features
- No ads
- Priority support
```

### Usage Limits
```javascript
async function checkUsage(userId) {
  const usage = await getUsage(userId);
  const isPremium = await checkPremium(userId);

  if (!isPremium && usage >= 10) {
    return { allowed: false, message: 'Daily limit reached. Upgrade?' };
  }
  return { allowed: true };
}
```
```

### Message Formatting (展示重點)

Highlighting key information in notifications

**When to use**: When sending updates, alerts, or structured data

```python
## Message Formatting Best Practices

### Visual Hierarchy with HTML
Telegram supports HTML formatting. Use it to create scannable messages:

```python
def format_update_message(version: str, changes: dict) -> str:
    """Format with visual hierarchy - most important info first"""

    # Category config: key, emoji, label
    categories = [
        ("added", "✨", "New"),
        ("improved", "⚡", "Improved"),
        ("changed", "🔄", "Changed"),
        ("fixed", "🐛", "Fixed"),
    ]

    # Header with version prominently displayed
    msg = f"🚀 <b>App v{version}</b>\n\n"

    # Quick summary line (scannable at a glance)
    summary = []
    for key, emoji, _ in categories:
        count = len(changes.get(key, []))
        if count:
            summary.append(f"{emoji}{count}")
    msg += "  ".join(summary) + "\n\n"

    # Detailed sections with smart truncation
    for key, emoji, label in categories:
        items = changes.get(key, [])
        if not items:
            continue

        msg += f"{emoji} <b>{label}</b>\n"
        for item in items[:5]:  # Max 5 per category
            msg += f"  • {escape_html(item)}\n"
        if len(items) > 5:
            msg += f"  <i>...+{len(items)-5} more</i>\n"
        msg += "\n"

    return msg
```

### Highlight Code & Tags
```python
import re

def highlight_content(text: str) -> str:
    """Highlight code refs and platform tags"""
    # Escape HTML first
    text = text.replace("&", "&amp;").replace("<", "&lt;").replace(">", "&gt;")

    # Platform tags: [iOS], [Android], [Web]
    text = re.sub(r'^\[([A-Z][A-Za-z]+)\]', r'<code>[\1]</code>', text)

    # Inline code: `something`
    text = re.sub(r'`([^`]+)`', r'<code>\1</code>', text)

    return text
```

### Message Design Principles
| Principle | Bad | Good |
|-----------|-----|------|
| Header | "Update Available" | "🚀 **App v2.1.0**" |
| Overview | None | "✨3  🐛5  🔄2" |
| Truncation | Cut mid-sentence | "...+3 more" |
| Code refs | Plain text | `<code>highlighted</code>` |
| Action | Buried in text | Button or link at bottom |

### Smart Truncation
```python
def truncate_items(items: list, max_items: int = 5, max_chars: int = 200) -> list:
    """Truncate list intelligently, never mid-item"""
    result = []
    for item in items[:max_items]:
        if len(item) > max_chars:
            item = item[:max_chars-3] + "..."
        result.append(item)

    remaining = len(items) - max_items
    if remaining > 0:
        result.append(f"<i>...+{remaining} more</i>")

    return result
```
```

### Notification Bots

Building reliable notification/monitoring bots

**When to use**: When creating bots that monitor and alert

```python
## Notification Bot Architecture

### Git Scraping Pattern
Monitor any file/API by tracking state changes:

```python
import json
from pathlib import Path

STATE_FILE = Path("data/state.json")

def load_state() -> dict:
    if STATE_FILE.exists():
        return json.loads(STATE_FILE.read_text())
    return {"last_hash": None}

def save_state(hash: str):
    STATE_FILE.parent.mkdir(exist_ok=True)
    STATE_FILE.write_text(json.dumps({
        "last_hash": hash,
        "updated": datetime.now().isoformat()
    }))

def check_for_changes(current_hash: str) -> bool:
    state = load_state()
    if state["last_hash"] == current_hash:
        return False  # No changes
    save_state(current_hash)
    return True
```

### Retry Logic with Rate Limit Handling
```python
import time
import requests

def send_telegram(message: str, max_retries: int = 3) -> bool:
    url = f"https://api.telegram.org/bot{BOT_TOKEN}/sendMessage"

    for attempt in range(max_retries):
        resp = requests.post(url, json={
            "chat_id": CHAT_ID,
            "text": message,
            "parse_mode": "HTML",
            "disable_web_page_preview": True
        })

        if resp.status_code == 429:  # Rate limited
            retry_after = resp.json().get("parameters", {}).get("retry_after", 5)
            time.sleep(retry_after)
            continue

        if resp.ok:
            return True

        time.sleep(2 ** attempt)  # Exponential backoff

    return False
```

### GitHub Actions Cron Setup
```yaml
# .github/workflows/notify.yml
name: Check and Notify
on:
  schedule:
    - cron: '*/30 * * * *'  # Every 30 minutes
  workflow_dispatch:  # Manual trigger

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - run: pip install requests
      - run: python scripts/check.py
        env:
          TELEGRAM_BOT_TOKEN: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          TELEGRAM_CHAT_ID: ${{ secrets.TELEGRAM_CHAT_ID }}

      # Commit state changes back
      - uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: "Update state"
          file_pattern: "data/*.json"
```
```

## Anti-Patterns

### ❌ Blocking Operations

**Why bad**: Telegram has timeout limits.
Users think bot is dead.
Poor experience.
Requests pile up.

**Instead**: Acknowledge immediately.
Process in background.
Send update when done.
Use typing indicator.

### ❌ No Error Handling

**Why bad**: Users get no response.
Bot appears broken.
Debugging nightmare.
Lost trust.

**Instead**: Global error handler.
Graceful error messages.
Log errors for debugging.
Rate limiting.

### ❌ Spammy Bot

**Why bad**: Users block the bot.
Telegram may ban.
Annoying experience.
Low retention.

**Instead**: Respect user attention.
Consolidate messages.
Allow notification control.
Quality over quantity.

## Related Skills

Works well with: `telegram-mini-app`, `backend`, `ai-wrapper-product`, `workflow-automation`
