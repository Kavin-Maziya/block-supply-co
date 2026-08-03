# Block Supply Co. — Build Your Own Storefront

---

## 1. Store Brief

### 1.1 Niche Selection

* Premium sneaker & streetwear online store called **Block Supply Co.**

* Sneakers and streetwear are ideal for demonstrating advanced Shopify features because they have multiple variants on every product like size (numeric UK/US/EU scales), colorway (often named "Off White" or "University Blue" rather than generic colors), and gender fit (Men's / Women's / Grade-School). Beyond variants, the niche will contain brand metafields like release-date metafields for "Coming Soon" drops, and exclusivity tags, all of which will be necessary to implement advanced collection filtering and custom metafield displays later in the week.

---

### 1.2 Target Audience

* Block Supply Co. serves Genz and Millenials (18–34 year olds) in South African neighbourhoods either the suburbs and townships who follow global sneaker culture and prioritise brand authenticity over fast fashion. They are digitally native, regularly browse drop schedules, and expect detailed product information — including colourway names, release dates, and size guides — before committing to a purchase.

---

## 2. Page Scope

Standard policy pages (Privacy, Returns, Terms) are excluded from this list.

| # | Page | Purpose | Metaobject Type(s) |
|---|------|----------|--------------------|
| 1 | **Brands Page** | A browsable A–Z page listing with every brand in stock with a logo, short bio, and a link to their collection page. | `Brand` — fields: `name`, `logo_image`, `short_description`, `collection_handle` |
| 2 | **Drop Calendar Page** | A "Coming Soon" schedule showing upcoming releases with product image, release date, and a notify-me prompt. | `Drop` — fields: `product_reference`, `release_date`, `drop_label`, `teaser_image` |
| 3 | **Size Guide Page** | A detailed sizing reference covering global footwear (UK/US/EU) conversion charts per brand and apparel (XS, S, M, L, XL, 2XL, 3XL) size measurements. | `Size_Chart` — fields: `category`, `brand_reference`, `chart_image`, `notes` |

---

## 3. Dev Environment

| Detail | Value |
|--------|-------|
| **Development store name** | block-supply-co-dev |
| **Development store URL** | block-supply-co-dev.myshopify.com |
| **GitHub repository** | https://github.com/Kavin-Maziya/block-supply-co |
| **Shopify CLI version** | 4.6.0 |
| **Hot reloading verified** | ☑ Confirmed on `http://127.0.0.1:9292` |

---

## Stretch Goals

### Stretch A — GitHub Integration via Admin

* Connected the `block-supply-co` GitHub repository to the Shopify Theme Editor 
via Online Store > Themes > Add theme > Connect from GitHub. Selected the  `Kavin-Maziya/block-supply-co` repository and the `main` branch.

**Difference between local CLI changes vs Theme Editor changes when GitHub sync is active:**

* When changes are made locally using `shopify theme dev`, those changes only live on my my local code and sync to the store preview in real time — but they don't automatically synced to GitHub. I still need to `git commit` and `git push` to update the repo.

* When changes are made in the Shopify Theme Editor, those changes save directly to the store but they don't go back to GitHub either — so my local code and the GitHub repo won't reflect what was changed in the editor.

**This means the CLI is for code changes, and the Theme Editor is for content and design changes — and you must be careful not to let them get out of sync.**

### Stretch B — VS Code Configuration

* Installed the official **Shopify Liquid** extension by Shopify in VS Code. 
Configured `.vscode/settings.json` to automatically format Liquid files on save 
using the following JSON setting:

```json
{
  "[liquid]": {
    "editor.defaultFormatter": "Shopify.theme-check-vscode",
    "editor.formatOnSave": true
  }
}
```

---
