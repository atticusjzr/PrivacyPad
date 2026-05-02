# 🔒 PrivacyPad

**A privacy-first AI document editor — sensitive data stays local, AI receives placeholder codes only.**

No server. No backend. No data leaks. Just open the HTML file.

---

## The Problem

You want AI to help analyze a document that contains names, ID numbers, addresses, or other sensitive personal data. But sending that data to an external AI API means it leaves your device.

## The Solution

PrivacyPad uses a **placeholder architecture**:

```
What you see in the editor:          What AI actually receives:
┌─────────────────────────────┐      ┌─────────────────────────────┐
│ John Smith transferred      │      │ {{PersonA-Name}} transferred │
│ $50,000 to Jane Doe on      │  →   │ $50,000 to {{PersonB-Name}} │
│ March 15th from account     │      │ on March 15th from account  │
│ #123-456-789                │      │ {{PersonA-AccountNo}}       │
└─────────────────────────────┘      └─────────────────────────────┘
        Real values in RAM                  Placeholders only
```

Personal data is stored as **chips** in the editor — they display the real value on screen, but carry a fixed `data-placeholder` attribute that never changes. When sending to AI, the editor walks the DOM and reads `data-placeholder` instead of the actual content.

**Real values never leave the browser. Ever.**

---

## Features

- **Zero-backend** — single HTML file, works offline
- **Custom entities** — define any people, companies, or objects with custom fields
- **Live placeholder preview** — see exactly what AI receives before sending
- **Resizable panels** — drag the dividers to adjust layout
- **Custom system prompt** — configure AI behavior per use case
- **No installation** — download and open in browser

---

## Quick Start

1. Download [`PrivacyPad.html`](./PrivacyPad.html)
2. Open it in any modern browser
3. Click **⚙ Settings** and enter your [Anthropic API key](https://console.anthropic.com/)
4. Add entities and fields on the left panel
5. Write your document and insert field chips via **＋ Insert field**
6. Click **Analyze Document** — AI receives only placeholder codes

---

## How It Works

The core privacy guarantee lives in one function:

```javascript
function getEditorTextForAI() {
  const editor = document.getElementById('editor');
  let text = '';
  editor.childNodes.forEach(node => {
    if (node.nodeType === Node.TEXT_NODE) {
      text += node.textContent;           // regular text: sent as-is
    } else if (node.dataset?.chip) {
      text += node.dataset.placeholder;  // chip: send {{placeholder}}, NOT real value
    }
  });
  return text;
}
```

Each chip in the editor has two parts:
- **Visual layer** — displays the real value (e.g. "John Smith")
- **Data layer** — `data-placeholder="{{PersonA-Name}}"` — this is what AI sees

The `data-placeholder` attribute is set once when the chip is created and **never updated**, regardless of what the user types into the chip.

---

## Use Cases

| Field | Example |
|---|---|
| Legal | Contract review with client names redacted |
| Healthcare | Medical note analysis without patient identifiers |
| HR | Performance review with employee names as placeholders |
| Journalism | Source-protected interview analysis |
| Law enforcement | Case document AI assistance (original use case) |
| Finance | Transaction analysis with account holders anonymized |

---

## Privacy Guarantee

| Data type | Where it lives | Leaves the browser? |
|---|---|---|
| Entity field values (names, IDs…) | Browser RAM only | ✗ Never |
| Document text with placeholders | Sent to Anthropic API | ✓ (no personal data) |
| API key | `localStorage` | Only to `api.anthropic.com` |

Even if Anthropic's servers were fully compromised, the attacker would only see placeholder codes — there is no way to reverse `{{PersonA-Name}}` back to a real identity without the local data.

---

## Requirements

- Any modern browser (Chrome, Firefox, Safari, Edge)
- An [Anthropic API key](https://console.anthropic.com/) (Claude Haiku is fast and cheap)

---

## License

MIT — use it, fork it, build on it.
