# TextNow Bot

[![pypi](https://img.shields.io/pypi/v/textnow-bot)](https://pypi.org/project/textnow-bot)
![pyversions](https://img.shields.io/pypi/pyversions/textnow-bot)
[![ci](https://github.com/george-lim/textnow-bot-python/workflows/CI/badge.svg)](https://github.com/george-lim/textnow-bot-python/actions)
[![license](https://img.shields.io/github/license/george-lim/textnow-bot-python)](https://github.com/george-lim/textnow-bot-python/blob/main/LICENSE)

## [Usage](#usage) | [Features](#features) | [Examples](#examples) | [CI/CD](#cicd)

TextNow Bot is a Python library to send text messages on TextNow with the [Playwright API](https://microsoft.github.io/playwright-python).

## Usage

```bash
python3 -m pip install textnow-bot
python3 -m playwright install
```

This installs TextNow Bot and its dependencies. Once installed, add `import textnow_bot` to a Python script to begin using TextNow Bot.

## Features

TextNow Bot accepts either account credentials or existing login cookies to authenticate the user. It allows users to send text messages programmatically to various recipients.

## Examples

### Send text message (synchronously)

This snippet logs into TextNow, sends a text message to a recipient, and persists all login cookies to a file. It will prioritize login cookies over account credentials for authentication.

```python
import json
from pathlib import Path

from playwright.sync_api import sync_playwright
from textnow_bot import TextNowBot


def run(playwright):
    username = "test@example.com"
    password = "********"
    recipient = "123-456-7890"
    message = "Hello world!"

    cookies_path = Path("cookies.json")

    browser = None

    try:
        browser = playwright.firefox.launch()
        page = browser.new_page()

        bot = TextNowBot(page)

        if cookies_path.exists():
            cookies = json.loads(cookies_path.read_text())
            bot.log_in(cookies)
        else:
            bot.log_in(None, username, password)

        bot.send_text_message(recipient, message)

        cookies_path.write_text(json.dumps(bot.cookies))

        browser.close()
    except Exception:
        if browser:
            browser.close()

        raise


with sync_playwright() as playwright:
    run(playwright)
```

### Send text message (asynchronously)

This snippet logs into TextNow, sends a text message to a recipient, and persists all login cookies to a file. It will prioritize login cookies over account credentials for authentication.

```python
import asyncio
import json
from pathlib import Path

from playwright.async_api import async_playwright
from textnow_bot import AsyncTextNowBot


async def run(playwright):
    username = "test@example.com"
    password = "********"
    recipient = "123-456-7890"
    message = "Hello world!"

    cookies_path = Path("cookies.json")

    browser = None

    try:
        browser = await playwright.firefox.launch()
        page = await browser.new_page()

        bot = AsyncTextNowBot(page)

        if cookies_path.exists():
            cookies = json.loads(cookies_path.read_text())
            await bot.log_in(cookies)
        else:
            await bot.log_in(None, username, password)

        await bot.send_text_message(recipient, message)

        cookies_path.write_text(json.dumps(await bot.cookies))

        await browser.close()
    except Exception:
        if browser:
            await browser.close()

        raise


async def main():
    async with async_playwright() as playwright:
        await run(playwright)


asyncio.run(main())
```

## CI/CD

### Secrets

```yaml
PYPI_USERNAME: __token__
PYPI_PASSWORD: "********"

TESTPYPI_USERNAME: __token__
TESTPYPI_PASSWORD: "********"
```

These secrets must exist in the repository for `CD` workflows to publish the PyPI package.

```yaml
SCHEDULE_TEXTNOW_USERNAME: test@example.com
SCHEDULE_TEXTNOW_PASSWORD: "********"
SCHEDULE_TEXTNOW_RECIPIENT: 123-456-7890
```

These secrets must exist in the repository for `Schedule` workflow to periodically send weekend text messages.
