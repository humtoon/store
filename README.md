# Humtoon

A static product website for **Humtoon** — custom toonified acrylic standees and fridge magnets made from customer photos.

> **Concept:** A client sends a photo (solo, couple, baby, pet, or group). We turn it into an animated/"toonified" version, printed as an acrylic standee or fridge magnet.

---

## Status

| Part | State |
|------|-------|
| Product images (renamed, organized) | ✅ Done — [images/](images/) |
| Product/sample metadata | ✅ Done — [products.json](products.json) |
| Website (HTML/CSS/JS) | ✅ Done — [index.html](index.html) (single self-contained file) |
| GitHub Pages deploy | ⏳ Not set up yet |

The site is a **plain static site** (no backend, no build step) hosted free on **GitHub Pages**. The entire storefront — a `home / shop / product / guide / faq` screen state machine, an interactive before/after drag slider, WhatsApp ordering, photo-upload preview, and the full conversion/persuasion layer — lives in one `index.html` (embedded CSS + vanilla JS), so it works on GitHub Pages with zero build and no `fetch`.

> **Note on product data:** the live storefront's product copy/images are defined in the `PRODUCTS` array inside [index.html](index.html), shaped one-row-per-product (`before`/`after`/`gallery`) for the slider and cards. [products.json](products.json) remains the canonical archive of every sample + original WhatsApp filenames. Keep prices in sync between the two when the `₹XXX` placeholders are replaced with real amounts.

---

## Project structure

```
humtoon/
├── README.md          # this file
├── products.json      # the single source of truth for all product content
└── images/            # all photos, named by product-sample-type
    ├── logo.png
    ├── solo-businessman-original.jpeg
    ├── solo-businessman-humtoon.jpeg
    └── ...
```

When the site is built, expect to add `index.html`, a stylesheet, and a small script that fetches `products.json` and renders it.

---

## Data model — [products.json](products.json)

This is the contract the site code reads. Shape:

```jsonc
{
  "brand":   { "name", "concept", "logo" },
  "_legend": { ... },          // human-readable docs for the enums below
  "products": [
    {
      "id":          "solo",            // stable slug, safe for URLs/anchors
      "name":        "The Solo Humtoon",
      "tagline":     "Capture your vibe.",
      "description": "...",
      "people":      "1",               // free-text count ("2", "4+", "1 pet")
      "price":       "P1",              // see "Pricing" below
      "samples": [
        {
          "id":      "solo-businessman",
          "subject": "Man in a blue blazer and white shirt",
          "images": [
            {
              "file":        "images/solo-businessman-original.jpeg",
              "type":        "original",       // see enum below
              "form_factor": null,             // see enum below
              "from":        "WhatsApp Image 2026-06-13 at 15.54.34 (2).jpeg"
            }
          ]
        }
      ]
    }
  ]
}
```

**Hierarchy:** `products → samples → images`. A *product* is a menu item (a thing customers buy). A *sample* is one real example of that product (one customer's order), used to show a before/after. An *image* is a single photo within that sample.

### `image.type`
| Value | Meaning |
|-------|---------|
| `original` | The client's raw photo — the **before**. |
| `humtoon` | The finished product on its own — the **after**. |
| `combo` | Finished product photographed next to the printed original (before + after in one frame). |
| `customer` | Finished product held by / shown with the customer. |

> Not every sample has all types. Some only have a `combo` or `customer` shot — handle missing `original`/`humtoon` gracefully when rendering before/after.

### `image.form_factor`
`"acrylic_standee"`, `"fridge_magnet"`, or `null` (for raw `original` photos).

### `from`
The original WhatsApp filename each image was renamed from. Kept so renames are fully reversible; not needed for display.

---

## Image naming convention

```
{product-sample}-{type}[-{variant}].{ext}
```

Examples:
- `solo-graduation-original.jpeg` — before photo for the "graduation" solo sample
- `duo-attached-teal-humtoon.jpeg` — finished attached-duo standee
- `lil-sweetworld-humtoon-magnet.jpeg` — finished baby toon, magnet variant

To **add a sample**: drop the new photos in [images/](images/) following this pattern, then add a `sample` entry under the right product in [products.json](products.json).

---

## Products

| `id` | Name | What the client gets | Price |
|------|------|----------------------|-------|
| `solo` | The Solo Humtoon | One humtoon of one person | `P1` |
| `duo_bundle` | The Duo Bundle | **Two separate** humtoons | `P2` |
| `family_squad` | The Family / Squad Pack | One combined humtoon of the group | `P3` base (4 people) + 100 per extra person |
| `paw` | The Paw Toon | One humtoon of a pet | `P3` |
| `duo_attached` | The Attached Duo | **One joined** humtoon of two people | `P4` |
| `lil` | The Lil Toon | One humtoon of a baby (standee or named magnet) | `TBD` |

> **Duo Bundle vs Attached Duo:** the bundle is two standees you can place apart; the attached duo is a single piece with both people together.

### Pricing
`P1`–`P4` are **placeholders** carried over from the original brief — replace them with real amounts in [products.json](products.json). `lil` has no price yet (`"TBD"`).

---

## Develop locally

It's static files, so any static server works. From the project root:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

`products.json` is loaded over HTTP (via `fetch`), so you **must** use a server — opening `index.html` directly from the filesystem (`file://`) will fail on the fetch due to browser security rules.

---

## Deploy (GitHub Pages)

1. Create a GitHub repo and push this folder.
2. Repo **Settings → Pages → Build and deployment → Source: Deploy from a branch**, pick `main` / `root`.
3. Site goes live at `https://<username>.github.io/<repo>/` in a minute or two.

Free for public repos, includes HTTPS, and supports a custom domain later. Keep image files compressed (~100–300 KB each) so the gallery loads fast.

---

## Roadmap

- [ ] Build `index.html` + styles + a render script that reads `products.json`.
- [ ] Replace `P1`–`P4` placeholders with real prices; set the `lil` price.
- [ ] Decide a contact / order flow (e.g. WhatsApp link, form, or payment links — no backend needed).
- [ ] Set up GitHub Pages.
