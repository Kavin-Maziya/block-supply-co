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

## 4. Theme Update — Dawn to Horizon

1. Added Horizon to the dev store via **Online Store → Themes → Add theme → Visit Theme Store**
2. Listed all themes using the CLI to get Horizon's theme ID:

`shopify theme list --store=block-supply-co-dev.myshopify.com`

3. Pulled Horizon's files down locally using the theme ID `149023326250`:

`shopify theme pull --theme=149023326250 --store=block-supply-co-dev.myshopify.com`

4. Created `shopify.theme.toml` in the project root to set Horizon as the default theme so `shopify theme dev` picks it up automatically without needing flags every time:

```toml
   [environments.default]
   store = "block-supply-co-dev.myshopify.com"
   theme = "149023326250"
```
   > **Note:** The toml must use `[environments.default]` — any other named environment requires an explicit `--environment` flag. Also the file must be in the project root, not inside any subfolder.

5. Verified Horizon is running correctly at `http://127.0.0.1:9292` with `preview_theme_id=149023326250` confirmed in the terminal.

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

## Day 2 — Liquid Fundamentals

### Part 1 — Filter & Conditional Plan

#### 1.1 Filter Inventory

| # | Filter | File | What it changes on the page |
|---|--------|------|-----------------------------|
| 1 | `money` | `blocks/price.liquid` | Turns the raw price number into a readable currency format like `R299.00` instead of displaying `29900` |
| 2 | `image_url` | `blocks/product-card.liquid` | Generates the correct web address for the product image at a set width so the browser can load it at the right size |
| 3 | `truncate` | `blocks/product-description.liquid` | Cuts long product descriptions short on product cards so they don't overflow the layout |
| 4 | `upcase` | `blocks/product-title.liquid` | Displays the product title in all caps — e.g. `"Nike Air Force 1"` becomes `"NIKE AIR FORCE 1"` — for streetwear styling |
| 5 | `capitalize` | `blocks/_product-card.liquid` | Ensures the product vendor/brand name always starts with a capital letter — e.g. `"adidas"` becomes `"Adidas"` |

#### 1.2 Conditional Logic Plan

- **Object/property driving the condition:** `product.available`
- **File:** `blocks/product-card.liquid`
- **True branch:** When `product.available` is true, an "Add to Cart" button is displayed
- **False branch:** When `product.available` is false, a "Sold Out" badge is displayed instead of the button

---

### Part 2 — Product Page Section Edits

All filters and the conditional were implemented in `snippets/product-card.liquid`. 
Horizon is heavily componentized — blocks like `blocks/price.liquid` and 
`blocks/product-description.liquid` delegate their output to snippets via 
`{% render %}`, so the actual product data is only directly accessible in the 
snippet layer.

Filters `money` and `image_url` are already used internally by Horizon in 
`snippets/price.liquid` and `snippets/product-card.liquid` respectively — 
confirmed by reviewing those files. The 3 new filters added are `upcase`, 
`capitalize`, and `truncate`.

### Part 3 — Collection Page Section Edits

The collection page at `http://127.0.0.1:9292/collections/all` uses the same 
`snippets/product-card.liquid` snippet to render each product card in the grid. 
This means all 5 filters and the conditional are active on the collection page 
as well, bringing the combined total to 5 distinct filters across both pages.

### Verification Notes

- Collection page tested at `http://127.0.0.1:9292/collections/all`
- Product cards showing upcase titles, capitalize vendor, truncated description, 
  and formatted prices
- Both conditional branches confirmed — "Add to Cart" on available products, 
  "Sold Out" on unavailable products
- No Liquid errors in the terminal during testing

---

---

## Day 3 — Sections, Blocks & Schema

### Part 1 — Section & Block Plan

#### 1.1 Section Concepts

**Section 1: Brand Showcase** → `sections/brand-showcase.liquid`

A homepage section that highlights the key brands stocked at Block Supply Co. Each brand gets its own block displaying a logo, name, and a short tagline. It gives the store an editorial feel similar to Shelflife's brand directory, and lets merchants reorder or add brands without touching code.

**Section 2: Drop Countdown** → `sections/drop-countdown.liquid`

A homepage section that announces an upcoming sneaker or streetwear drop with a product image, drop name, release date and a countdown timer. It builds hype for new releases the way Shelflife does with their "Coming Soon" drops, and lets merchants configure each upcoming drop without touching code.

---

#### 1.2 Block Inventory

| Block type | Filename | Purpose | Reusable? |
|---|---|---|---|
| `brand-logo` | `blocks/brand-logo.liquid` | Displays a brand's logo image with a link to their collection page | Reusable elsewhere in the theme |
| `brand-card` | `blocks/brand-card.liquid` | Displays brand name, tagline, and a "Shop Now" link as a text card | Specific to brand-showcase section |
| `brand-heading` | `blocks/brand-heading.liquid` | A configurable heading block e.g. "Featured Brands" that merchants can reposition or hide | Reusable elsewhere in the theme |
| `drop-title` | `blocks/drop-title.liquid` | Displays the drop name and release date label for an upcoming drop | Specific to drop-countdown section |
| `drop-image` | `blocks/drop-image.liquid` | Displays the product teaser image for the upcoming drop | Specific to drop-countdown section |
| `drop-timer` | `blocks/drop-timer.liquid` | Renders a static countdown display with a configurable target release date | Specific to drop-countdown section |

---

#### 1.3 Settings Plan

| Setting id | Type | File | What it visibly changes |
|---|---|---|---|
| `background_color` | `color` | `sections/brand-showcase.liquid` | Background colour of the entire brand showcase section |
| `columns` | `range` | `sections/brand-showcase.liquid` | Number of brand blocks displayed per row (2–4) |
| `logo_size` | `range` | `blocks/brand-logo.liquid` | Display width of the brand logo image in pixels |
| `show_tagline` | `checkbox` | `blocks/brand-card.liquid` | Toggles the brand tagline text visible or hidden |
| `link_url` | `url` | `blocks/brand-card.liquid` | Sets the destination URL for the "Shop Now" link |
| `section_background` | `color` | `sections/drop-countdown.liquid` | Background colour of the drop countdown section |
| `release_date` | `text` | `blocks/drop-timer.liquid` | Sets the target release date displayed in the countdown |
| `drop_label` | `text` | `blocks/drop-title.liquid` | Sets the drop label e.g. "Exclusive Drop" or "Coming Soon" |

---

### Part 2 — Schema Notes

- `sections/brand-showcase.liquid` schema validated against `schemas/section.json` ✅ — appeared in theme editor Add section picker with zero errors
- `sections/drop-countdown.liquid` schema validated against `schemas/section.json` ✅ — appeared in theme editor Add section picker with zero errors
- All block schemas validated against `schemas/theme_block.json` ✅ — all blocks appeared in Add block picker with zero errors

### Part 4 — Verification Notes

- Both sections added to homepage via theme editor — no JSON files edited manually ✅
- Brand Showcase verified with Brand Heading, Brand Card and Brand Logo blocks all rendering ✅
- Drop Countdown verified with Drop Title, Drop Timer and Drop Image blocks all rendering ✅
- Background color and Brands per row settings toggled and confirmed working in theme editor ✅
- Local preview confirmed at `http://127.0.0.1:9292` with zero Liquid errors ✅

---