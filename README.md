# Chrome Font Blocker

A minimal Chrome extension to block problematic webfonts on selected sites, forcing Chrome to use system fonts.  
**Fixes broken fonts on Linux after Chrome 127+ (Fontations backend issue).**

---

## Problem

After Chrome 127+, Google enabled the Fontations font backend on **Linux**.  
Some webfonts (like YouthBase on [mobile.de](https://mobile.de)) fail to render correctly.  
**Symptoms:** Pages show strange or corrupted fonts, while Firefox displays them fine.

Left wrong, right with blocked fonts (working extension):  
![Screenshot: Left wrong, right with blocked fonts](screenshots/Screenshot%20from%202025-09-21%2013-10-24.png)

---

## Solution

Block the problematic webfonts at the browser level.  
With Manifest V3, classic uBlock Origin filters can't be used for this.  
This extension blocks selected font requests, forcing Chrome to fall back to your system fonts (Arial, Noto Sans, Liberation Sans, etc.).

---

## Installation

1. **Create a new folder** (e.g. `chrome-font-blocker`).
2. **Save** [`manifest.json`](manifest.json) and [`rules_1.json`](rules_1.json) inside it.
3. Open Chrome and go to `chrome://extensions`.
4. Enable **Developer mode** (top right).
5. Click **Load unpacked** and select your folder.
6. Reload the target website (e.g. [mobile.de](https://mobile.de)). Fonts matching your rules will be blocked.

---

## Editing Rules

To add more sites or fonts, open [`rules_1.json`](rules_1.json).

- Duplicate one of the rule objects.
- Increment the `"id"`.
- Adjust:
  - `"urlFilter"` → substring of the font URL you want to block (wildcards `*` allowed).
  - `"initiatorDomains"` → domain where the block should apply.

**Example:**
```json
{
  "id": 3,
  "priority": 1,
  "action": { "type": "block" },
  "condition": {
    "urlFilter": "*customfont*",
    "resourceTypes": ["font"],
    "initiatorDomains": ["othersite.com"]
  }
}
```
After editing, go to `chrome://extensions` and press **Reload** on your extension.

---

## Notes

- Use specific `urlFilter` patterns to avoid breaking all fonts.
- You can use `*woff*` to block all WOFF/WOFF2 fonts on a domain.
- If Chrome later fixes Fontations, you can disable or remove the extension.

---

## Extending the Font Filter

The `urlFilter` in `rules_1.json` only accepts a single string (with optional `*` wildcards).  
To block multiple fonts, you have two options:

### 1. Multiple Rules

Add one rule for each font, each with its own `"id"`:

```json
[
  {
    "id": 1,
    "priority": 1,
    "action": { "type": "block" },
    "condition": {
      "urlFilter": "*youth-base*",
      "resourceTypes": ["font"],
      "initiatorDomains": ["mobile.de"]
    }
  },
  {
    "id": 2,
    "priority": 1,
    "action": { "type": "block" },
    "condition": {
      "urlFilter": "*youth-hero*",
      "resourceTypes": ["font"],
      "initiatorDomains": ["mobile.de"]
    }
  }
]
```

### 2. Wildcard Match

If the font files share a common part of the name, a single rule is enough:

```json
{
  "id": 1,
  "priority": 1,
  "action": { "type": "block" },
  "condition": {
    "urlFilter": "*youth*",
    "resourceTypes": ["font"],
    "initiatorDomains": ["mobile.de"]
  }
}
```

---

## Tips

- Use wildcards (`*`) to keep rules short.
- Make sure each rule has a unique `"id"`.
- After editing, reload the extension in `chrome://extensions`.
