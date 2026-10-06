<div align="center">

<img src="assets/sigil.svg" width="84" alt="Nova suite sigil">

# Nova Notes

**A note editor that writes from the centre of the universe outwards.**

Google Docs–style writing in the Novacane cosmic look: centred by default, six colour themes, import from Word, HTML, Markdown and text, and send it all to Google Docs.

![Static web app](https://img.shields.io/badge/web%20app-no%20build%20step-B01D68?style=flat-square)
![Vanilla JS](https://img.shields.io/badge/vanilla-JavaScript-7A1F86?style=flat-square)
![Google Docs](https://img.shields.io/badge/exports%20to-Google%20Docs-25194D?style=flat-square)
![Part of the Nova suite](https://img.shields.io/badge/part%20of-nova--suite-FF5FA8?style=flat-square)

<img src="docs/media/demo.gif" width="760" alt="Writing a note in Nova Notes: a title, a heading, a checklist and a quote, then switching colour themes and the paper page">

<sub>Short demo · <a href="docs/media/demo.mp4">watch it in full quality (MP4)</a></sub>

</div>

---

<p align="center"><img src="docs/media/hero.jpg" width="900" alt="Nova Notes with the welcome note open: notes list on the left, the glowing page in the middle, the outline on the right"></p>

## What it does

**Writes like Google Docs**
- **Menus and a toolbar** you already know: File, Edit, View, Insert, Format, Tools and Help, with undo, redo, print, zoom, styles, fonts, sizes, colours and highlights.
- **Paragraph styles:** Title, Subtitle, Heading 1–3, Normal text, Quote and Code.
- **Lists:** bulleted, numbered and **checklists** you tick with a click; indent and outdent.
- **Tables** (pick a size, then add or remove rows and columns), **pictures** (upload, paste or drop, then resize and align), **links**, horizontal lines and **page breaks**.
- **Find and replace** (match case, whole words), **word count**, a live **outline** of your headings, and **undo that remembers everything**, including the tools the browser forgets.
- **Shortcuts as you type:** `#` heading, `-` list, `1.` numbered list, `[]` checklist, `>` quote and `---` for a line. Every Google Docs keyboard shortcut you'd expect is there too (press <kbd>Ctrl</kbd>+<kbd>/</kbd>).

**Centred by default.** Everything you write (headings, paragraphs, lists and tables) sits in the middle of the page until you choose otherwise. Exports keep it that way.

**In and out**
- **Import** Word (`.docx`), web pages (`.html`), Markdown (`.md`) and text (`.txt`): use *File → Import* or drop files onto the window. Text pasted from Google Docs or the web is cleaned up as it lands.
- **Export** to Google Docs, Word (`.doc`), a web page, Markdown, plain text or PDF (print), or a `.json` backup of one note or all of them.
- **Google Docs**, two ways:
  1. **Copy & paste (no set-up):** copies the note with its styling and opens a new Google Doc; press <kbd>Ctrl</kbd>+<kbd>V</kbd>.
  2. **Save to Google Drive (one click once set up):** signs in with Google, creates a real Google Doc, and updates that same Doc each time you save the note again.

**Looks the part**
- **Six themes** from the Nova suite: Novacane (magenta), Solar Flare (red-orange), Pulsar (cyan), Aurora (green and violet), Eclipse (gold) and Quasar (ultraviolet).
- **Two page styles:** *Cosmic* (a glowing glass page among twinkling stars) and *Paper* (white, exactly how the note exports and prints).
- **Focus mode,** a pageless view, zoom, and a layout that works on phones and tablets.

**Private by default.** Notes are saved in your browser (IndexedDB) as you type. Nothing leaves it unless you export it or send it to Google Docs.

## Screenshots

| | |
|---|---|
| <img src="docs/media/lists-and-tables.jpg" alt="Centred bullet points, a checklist, a quote and a table"> | <img src="docs/media/paper.jpg" alt="The Paper page style, matching the Google Docs export"> |
| **Lists, checklists, quotes and tables**, all centred | **Paper page:** what Google Docs, Word and print will show |
| <img src="docs/media/menus.jpg" alt="The Format menu open on Paragraph styles"> | <img src="docs/media/google-docs.jpg" alt="The Send to Google Docs dialog with two options"> |
| **Google Docs–style menus** with shortcuts | **Send to Google Docs:** copy and paste, or save straight to Drive |

<details>
<summary><b>Every colour theme</b></summary>

| | |
|---|---|
| <img src="docs/media/theme-solar.jpg" alt="Solar Flare theme"> | <img src="docs/media/theme-pulsar.jpg" alt="Pulsar theme"> |
| Solar Flare | Pulsar |
| <img src="docs/media/theme-aurora.jpg" alt="Aurora theme"> | <img src="docs/media/theme-eclipse.jpg" alt="Eclipse theme"> |
| Aurora | Eclipse |
| <img src="docs/media/theme-quasar.jpg" alt="Quasar theme"> | <img src="docs/media/phone.jpg" width="260" alt="Nova Notes on a phone"> |
| Quasar | On a phone |

</details>

## Run it

It's a static web app: no install and no build step.

```sh
cd nn
python3 -m http.server 4610
# open http://localhost:4610
```

Opening `index.html` straight from disk works too, except for *Save to Google Drive*: Google sign-in needs a web address (`http://` or `https://`). Fonts come from Google Fonts, and Word import loads [mammoth](https://github.com/mwilliamson/mammoth.js) from cdnjs the first time it's used.

### Setting up "Save to Google Drive" (optional, one time)

1. In [Google Cloud → Credentials](https://console.cloud.google.com/apis/credentials), create an **OAuth client ID** of type **Web application**.
2. Add the address you open Nova Notes from (e.g. `http://localhost:4610`) under **Authorised JavaScript origins**.
3. Enable the [Google Drive API](https://console.cloud.google.com/apis/library/drive.googleapis.com) for the project.
4. In Nova Notes, open **Google Docs → Set up** and paste the client ID. It's stored in that browser only.

Nova Notes asks only for the `drive.file` permission, which covers files the app itself creates and nothing else in your Drive.

## How it works

```mermaid
flowchart LR
  subgraph Browser
    E[editor.js<br/>contenteditable page,<br/>own undo history] --> A[app.js<br/>menus, toolbar,<br/>library, dialogs]
    A --> S[(store.js<br/>IndexedDB)]
    A --> C[convert.js<br/>clean · Markdown ·<br/>styled export]
  end
  F[.docx · .html · .md · .txt] -- import --> C
  C -- copy + docs.new --> G[Google Docs]
  C -- google.js<br/>Drive API, HTML → Doc --> G
  C -- download --> D[.doc · .html · .md · .txt · PDF]
```

- **`editor.js`**: the writing surface. It handles commands and paragraph styles, lists and checklists, tables, pictures, find and replace, and Markdown-style shortcuts. Undo works from snapshots of the page and selection, so every change can be undone.
- **`convert.js`**: cleans pasted and imported HTML down to what the editor understands, converts Markdown both ways, and builds the **styled export**: every block is inline-styled in the current theme's colours, made deep enough to read on white, so Google Docs and Word keep the look and the centring.
- **`google.js`**: the clipboard route (rich HTML plus plain text) and the Drive route (Google Identity Services token → multipart upload that Drive converts to a Google Doc, then `PATCH` to update it).
- **`store.js`**: notes in IndexedDB (falling back to localStorage), settings, backup and restore.
- **`themes.js`** and **`backdrop.js`**: the Nova suite's colour themes and the star field, shared with [Nova Task](https://github.com/tuniveza/nova-task).

## Keyboard shortcuts

| | |
|---|---|
| Bold / italic / underline | <kbd>Ctrl</kbd> <kbd>B</kbd> / <kbd>I</kbd> / <kbd>U</kbd> |
| Strikethrough | <kbd>Alt</kbd> <kbd>Shift</kbd> <kbd>5</kbd> |
| Normal text / Heading 1–3 | <kbd>Ctrl</kbd> <kbd>Alt</kbd> <kbd>0</kbd>–<kbd>3</kbd> |
| Left / centre / right / justify | <kbd>Ctrl</kbd> <kbd>Shift</kbd> <kbd>L</kbd> / <kbd>E</kbd> / <kbd>R</kbd> / <kbd>J</kbd> |
| Numbered / bulleted / checklist | <kbd>Ctrl</kbd> <kbd>Shift</kbd> <kbd>7</kbd> / <kbd>8</kbd> / <kbd>9</kbd> |
| Link | <kbd>Ctrl</kbd> <kbd>K</kbd> |
| Find and replace | <kbd>Ctrl</kbd> <kbd>H</kbd> |
| Word count | <kbd>Ctrl</kbd> <kbd>Shift</kbd> <kbd>C</kbd> |
| Page break | <kbd>Ctrl</kbd> <kbd>Enter</kbd> |
| New note · Import · Print | <kbd>Ctrl</kbd> <kbd>Alt</kbd> <kbd>N</kbd> · <kbd>Ctrl</kbd> <kbd>O</kbd> · <kbd>Ctrl</kbd> <kbd>P</kbd> |
| Focus mode | <kbd>Ctrl</kbd> <kbd>Shift</kbd> <kbd>F</kbd> |
| Every shortcut | <kbd>Ctrl</kbd> <kbd>/</kbd> |

## Project layout

```
nn/
├── index.html          the app: top bar, menus, toolbar, page, panels, dialogs, icon sprite
├── css/nova-notes.css  all the styling: cosmic and paper pages, themes, print, phones
├── js/
│   ├── themes.js       colour themes (shared with Nova Task)
│   ├── backdrop.js     the twinkling star field
│   ├── store.js        IndexedDB notes, settings, backup/restore
│   ├── convert.js      cleaning, Markdown, styled export for Google Docs / Word / HTML
│   ├── google.js       copy-and-paste and Drive routes to Google Docs
│   ├── editor.js       the rich text editor
│   └── app.js          everything around the page
├── assets/sigil.svg    the Nova sigil
└── docs/media/         README screenshots and demo
```

## Part of the Nova suite

| | |
|---|---|
| [**nova-suite**](https://github.com/tuniveza/nova-suite) | The whole suite in one place |
| [**nova-bot**](https://github.com/tuniveza/nova-bot) | The studio's chat assistant, bookings and staff app back end |
| [**nova-agent**](https://github.com/tuniveza/nova-agent) | The browser helper that works Acuity's admin pages |
| [**nova-club**](https://github.com/tuniveza/nova-club) | The members' Android app |
| [**nova-task**](https://github.com/tuniveza/nova-task) | A cosmic calendar of note cards and day cards |
| [**nova-notes**](https://github.com/tuniveza/nova-notes) | You are here |
| [**nova-observatory**](https://github.com/tuniveza/nova-observatory) | A dashboard of every project |

---

<p align="center"><sub>Made for <b>Novacane Studios</b> · All rights reserved</sub></p>
