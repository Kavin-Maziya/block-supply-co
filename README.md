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
---

## Day 4 — Metafields & Metaobjects

### Part 1 — Metafield & Metaobject Plan

#### 1.1 Metafield Plan

- **Resource type:** Product
- **Namespace.key:** `custom.colorway_name`
- **Type:** Single line text
- **Display:** Shown as a spec line under the drop title in `blocks/drop-title.liquid` — e.g. "Colorway: University Blue / White / Black". If the metafield is blank, the spec line doesn't render at all — no empty wrapper, no orphaned label.

---

#### 1.2 Metaobject Plan

- **Type name:** `drop_details`
- **Fields:**
  - `story` — rich text (the narrative behind the drop, e.g. the collab history or design inspiration)
  - `retail_price` — single line text (e.g. "R2,799.90")
- **Real-world content:** A single drop story entry that can be reused across multiple drop announcements — merchants write the story once and reference it from any product without duplicating content.
- **Access method:** Products reach it through a metafield of type Metaobject reference (`custom.drop_details`), following the same pattern as `blocks/disclosures.liquid` uses for `shopify.disclosure`.

---

#### 1.3 Integration Plan

- **Target file:** `blocks/drop-title.liquid`
- **Metafield blank state:** The colorway spec line doesn't render — no empty div, no label with nothing next to it
- **Metaobject blank state:** The story and retail price section doesn't render at all — the block looks exactly as it did before

---

### Part 2 — Admin Definitions

- Product metafield `custom.colorway_name` (Single line text) created in Admin ✅
- Product metafield `custom.drop_details` (Metaobject reference → Drop Details) created in Admin ✅
- Metaobject type `drop_details` created with `story` (Rich text) and `retail_price` (Single line text) fields ✅
- Two entries created with real content — one for Air Jordan 4 Bred, one for Nike Air Force 1 Triple White ✅
- Air Jordan 4 Retro "Bred" product created with both metafields populated ✅

---

### Part 3 — Integration Notes

- Target file modified: `blocks/drop-title.liquid`
- Added `block.settings.product` picker so merchants can select which product's metafields to surface
- `custom.colorway_name` renders as a spec line under the drop title using `metafield_text`
- `custom.drop_details` metaobject renders the retail price and story using `metafield_tag`
- Both are wrapped in `{% if %} {% endif %}` guards — if blank, nothing renders

---

### Part 4 — Verification Notes

- Populated state verified at `http://127.0.0.1:9292` with Air Jordan 4 Bred selected ✅
- Colorway: Black / Cement Grey / Fire Red / Varsity Royal rendering correctly ✅
- Retail price R2,799.90 and drop story rendering correctly ✅
- Blank state verified — cleared product picker, colorway and story sections disappeared completely with no empty wrappers ✅
- Zero Liquid errors confirmed ✅

---

## Day 5 — Cart, AJAX & Interactivity

### Part 1 — Written Decisions

#### 1.1 Threshold Plan

- **Setting id:** `free_shipping_threshold`
- **Type:** `number`
- **Scope:** Global — added to `config/settings_schema.json` inside the existing `"name": "t:names.cart"` group, not section-scoped. A free shipping threshold is a store-wide business rule — a merchant would never want one threshold on one cart drawer instance and a different one on another. Section-scoped settings suit layout or copy choices that vary per placement; a shipping policy threshold does not.
- **Default value:** `75` (whole currency units — 75 = R750.00)
- **Enable/disable setting id:** `enable_free_shipping_indicator` (checkbox)

---

#### 1.2 Messaging Plan

- **"Still short" state:** `You're {{ remaining_amount | money }} away from free shipping!`
- **"Threshold met" state:** `You've unlocked free shipping!`
- **Disabled / zero-threshold fallback:** The entire indicator block does not render — no wrapper div, no empty space, no placeholder. The outermost element is wrapped in a Liquid `{% if %}` guard so nothing is output to the DOM at all.

---

#### 1.3 Integration Plan

- **Target file:** `snippets/cart-drawer.liquid`
- `snippets/cart-drawer.liquid` is rendered via `{% render 'cart-drawer' %}` inside `sections/cart-drawer-section.liquid`, making it part of that section's HTML output.
- In `assets/component-cart-items.js`, quantity changes call `updateQuantity()`, which fetches `cart_change_url` and passes `sections: [this.sectionId]` in the request body. The response includes updated section HTML under `parsedResponseText.sections[this.sectionId]`. That HTML is passed directly to `morphSection(this.sectionId, ...)` from `@theme/section-renderer`, which replaces the section in-place — including all markup rendered by `cart-drawer.liquid`. The indicator's values (`cart.total_price`, threshold setting) are re-evaluated server-side on every morph.
- External cart updates (e.g. add-to-cart) dispatch a `CartLinesUpdateEvent` caught by `#handleCartUpdate`, which also calls `morphSection` with the same section HTML from the event's resolved promise — or falls back to `sectionRenderer.renderSection` if no HTML was provided.
- **No new JavaScript is required.** Both paths (`updateQuantity` and `#handleCartUpdate`) already replace the full section HTML, which includes the indicator markup. Adding a new fetch or event listener would double-render and is incorrect.


### Part 3 — Verification

#### 3.1 First Load (Progressive Enhancement Baseline)
- Opened cart drawer with items already in cart before any JavaScript-driven update
- Indicator rendered correctly on the initial server render — "You've unlocked free shipping!" with a full bar ✅

#### 3.2 Live Update via Section Rendering
- With the drawer open, added another item from the product page
- Indicator message and bar updated with no full page reload ✅
- Confirmed in DevTools → Network tab: the `add.js` request to `/cart/add` returned a JSON response containing `"sections":{"cart-drawer-section":"<div id=\"shopify-section-cart-drawer-section\"..."}` — the full section HTML is bundled in the cart response and passed directly to `morphSection()`. No separate `?section_id=` request is made — Horizon's `updateQuantity` and `#handleCartUpdate` flows handle the re-render entirely ✅

#### 3.3 Both Message States and Off State
- **Threshold met:** "You've unlocked free shipping!" with a full bar — confirmed with cart total above R750 ✅
- **Still short:** "You're R 1,000.20 away from free shipping!" with a partial bar — confirmed by raising the threshold to R5000 in the theme editor ✅
- **Disabled state:** Toggled `enable_free_shipping_indicator` off in the theme editor — indicator disappeared completely with no empty box or leftover space ✅

#### 3.4 Existing Behavior Unaffected
- Cart drawer opens correctly, quantity selectors work, empty cart state works ✅
- No Liquid errors in the local preview ✅

---

### Part 4 — Validation & Version Control

#### 4.1 Theme Check
- Ran `shopify theme check` — 0 errors on all files touched in Day 5
- 1 pre-existing warning in `blocks/brand-card.liquid` (unclosed `<a>` tag from Day 3) — fixed as part of this commit ✅

---

## Day 6 — Collections, Filtering & Merchandising

### Part 1 — Written Decisions

#### 1.1 Collection & Filter Plan

- **Collection:** All products (handle: `all`) — contains Adidas Campus 00s, Nike Air Force 1, Adidas Samba OG, Nike Dunk Low, Nike Air Max 95
- **Filter dimension 1:** `Shoe size` — list filter on the product option, already exists and varies across all products ✅
- **Filter dimension 2:** `Price` — price_range filter, products already vary in price (R1,999.90 – R2,799.90) ✅
- **Filter dimension 3:** `Color` — list filter on the Color option, all products already have Color values assigned ✅
- **Data gap:** None — all three filter dimensions have real varied data across products with no setup required
- **Settings changed from defaults:**
  - `enable_filtering` → turned ON (default is `false` — nothing renders without this)
  - `filter_style` → set to `vertical` (default is `horizontal` — vertical keeps filters always visible on desktop alongside the product grid, which suits a sneaker store browse experience)
  - `enable_sorting` → left ON (already the default)

---

#### 1.2 Swatch Plan

- **Products:** Nike Air Force 1 Low '07 'Triple White', Adidas Campus 00s 'Core Black', Adidas Samba OG 'White & Black'
- **Option:** `Color`
- **Swatch values to assign in Admin:**
  - `White` — white swatch
  - `Core Black` — black swatch
  - `White & Black` — white swatch with black
- **Where swatches appear:** The collection grid card via `blocks/swatches.liquid` — this block reads `closest.product.options_with_values` and checks for assigned swatch data via `product_option.values | map: 'swatch' | compact`. No new block needs to be created; the swatches block already exists in the product card and will render automatically once swatch colors are assigned in Admin.

---

#### 1.3 Customization Plan

- **File:** `blocks/filters.liquid`
- **Edit:** Inside the existing `{% stylesheet %}` block, add a CSS rule that gives the active filter count bubble a distinct accent color using the theme's existing `--color-foreground-rgb` token at full opacity, making it visually stand out more clearly from the default muted style
- **Selector:** `.filter-count-bubble__background`
- **What it visibly changes:** The active filter count bubble background becomes more prominent — clearly signaling to the user that filters are active


### Part 2 — Build

#### 2.1 Filter Configuration (Search & Discovery App)
- Opened Search & Discovery app → Filters
- Added `Color` (product option) and `Shoe size` (product option) to the existing Availability and Price filters
- Left filter logic as OR — customers see all products matching any selected size or color, not only products matching every filter simultaneously ✅

#### 2.2 Swatch Setup (Admin → Metafields and metaobjects → Color)
- Two swatch entries already existed: `Black` (#000000) and `White` (#FFFFFF)
- All products already had a `Color` option with values linked to these swatch entries
- No new swatch entries required — Horizon reads swatches via `product_option.values | map: 'swatch'` and renders them automatically on the collection card ✅

#### 2.3 Theme Editor Settings (Filtering and sorting block)
- Filters → ON
- Direction → Vertical
- Text label case → Uppercase
- Sorting → left ON (default) ✅

#### 2.4 Code Edit — `blocks/filters.liquid`
- Added two CSS rules inside the existing `{% stylesheet %}` block targeting `.filter-count-bubble__background` and `.filter-count-bubble__text`
- `.filter-count-bubble__background` uses `rgb(var(--color-foreground-rgb))` at full opacity — making the active filter count bubble solid black instead of the default muted style
- `.filter-count-bubble__text` uses `rgb(var(--color-background-rgb))` to keep the number legible against the dark background ✅

---

### Part 3 — Verification

#### 3.1 All Four Filter Dimensions Rendering
- AVAILABILITY, PRICE, COLOR (color chip swatches), and SHOE SIZE (button swatches) all visible in the vertical filter panel on desktop and in the mobile filter drawer ✅

#### 3.2 Filters Update Without Full Page Reload
- Applied Availability and Color filters simultaneously
- Network tab confirmed requests to `all?filter.v.availability=1&fil...` as `fetch` type — not a full page navigation
- URL updated with filter parameters, grid updated live, "See X items" count updated in the mobile drawer in real time ✅

#### 3.3 Filter Count Bubble
- Active filter count bubble rendered with solid black background and white text from the CSS edit in `blocks/filters.liquid` ✅

#### 3.4 Theme Check
- `shopify theme check` — 0 errors, 0 warnings on all files touched in Day 6 ✅

---

---

## In-Class Challenge: Merchandising Spotlight

### Part 1 — Plan

**Section name and file path:** `sections/sneaker-spotlight.liquid`

**3 Block types:**

| Block file | Purpose |
|---|---|
| `blocks/spotlight-story.liquid` | Shows the product title, `custom.release_year` metafield, and a `description` |
| `blocks/spotlight-faq.liquid` | Shows a single FAQ entry from a `sneaker_faq` metaobject |
| `blocks/spotlight-picker.liquid` | Swatch variant picker tied to the product's `Shoe size` and the add-to-cart button with no reload |

**Spotlight product:** Nike Air Force 1 Low '07 'Triple White'

**New metafield:**
- Namespace.key: `custom.release_year`
- Type: Single line text
- Defined on: Product
- Purpose: Displays the silhouette's original release year inside `blocks/spotlight-story.liquid`

**New metaobject type:**
- Name: `sneaker_faq`
- Fields: `question` (Single line text) + `answer` (Multi-line text)
- One real entry: Do Air Force 1s run true to size? / "Air Force 1s run large — we recommend sizing down half a size from your usual Nike size. If you have wide feet, your normal size should work fine."
- Rendered inside: `blocks/spotlight-faq.liquid`

**Swatch/variant picker:** `blocks/spotlight-picker.liquid` reads from the `Shoe size` option on the Nike Air Force 1 Low '07.

---
### Part 2 — Filter & Conditional Log

| Filter | File | Effect |
|---|---|---|
| `upcase` | `blocks/spotlight-story.liquid` | Renders the section eyebrow label in uppercase |
| `escape` | `blocks/spotlight-story.liquid` | Sanitises the product title before output |
| `money` | `blocks/spotlight-story.liquid` | Formats `product.price` and `product.compare_at_price` as currency |
| `strip_html` | `blocks/spotlight-story.liquid` | Removes any HTML tags from the product description before truncating |
| `truncate` | `blocks/spotlight-story.liquid` | Caps the product description at 220 characters |

**Conditional:** `product.compare_at_price > product.price` in `blocks/spotlight-story.liquid` — checks whether the product is on sale. If true, renders both the compare-at price (struck through) and current price with a sale badge. If false, renders the current price only.

---

### Part 3 — Structured Content Notes

**Metafield:**
- Definition: `custom.release_year` (Single line text) on the Product resource
- Value on spotlight product: `1982`
- Rendered in: `blocks/spotlight-story.liquid` as "Silhouette est. 1982"
- Blank-state: wrapped in `{%- if release_year != blank -%}` — the line does not render if the metafield is unset

**Metaobject:**
- Definition: `sneaker_faq` with fields `question` (Single line text) and `answer` (Multi-line text)
- Entry: "Do Air Force 1s run true to size?" / "Air Force 1s run large — we recommend sizing down half a size from your usual Nike size. If you have wide feet, your normal size should work fine."
- Rendered in: `blocks/spotlight-faq.liquid`
- Blank-state: wrapped in `{%- if faq_entry != blank -%}` — renders "No FAQ entry selected." if no metaobject entry is assigned in the theme editor

---

### Part 4 — Interactivity Notes

**Variant picker:** `blocks/spotlight-picker.liquid` renders the `Shoe size` option values (6.5, 7, 8, 9, 10, 11) as radio-input size tiles. Each tile is labelled with the size value and marked sold-out if `variant.available == false`.

**Add to cart:** The form uses Horizon's `<product-form-component>` custom element wrapping a standard `{%- form 'product' -%}` tag. The custom element intercepts the submit event and handles the cart update via the Section Rendering API without a full page reload. No new JavaScript was written — the existing `product-form-component` registration in `assets/product-form.js` handles all cart interaction.

**Network tab confirmation:** On Add to Cart, the request goes to `/cart/add` and the cart count updates without a full page reload. The `product-form-component` element manages the AJAX submission using Horizon's existing cart infrastructure.

---

### Part 5 — Verification Notes

- `shopify theme check` returned zero errors on all modified files
- Full end-to-end walkthrough completed on `127.0.0.1:9292/pages/sneaker-sportlight`:
  - Section renders with product image, title, price, metafield, FAQ, size tiles, and Add to Cart button
  - `custom.release_year` metafield displays "Silhouette est. 1982"
  - `sneaker_faq` metaobject entry renders question and answer correctly
  - Size tiles render all six variants (6.5–11)
  - Add to Cart successfully adds the selected variant to the cart
- Section added to the `sneaker-sportlight` page via the theme editor — no JSON template hand-editing

---

## Day 7–8 Assignment: Performance, Accessibility, Quality & Deployment

### Part 1 — Audit & Deployment Plan

**Step 1.1 — Baseline Scores**

Homepage:
- Performance: 44
- Accessibility: 89
- Best Practices: 73
- SEO: 85

Spotlight page (in-class-challenge):
- Performance: 37
- Accessibility: 96
- Best Practices: 73
- SEO: 92

`shopify theme check`: 0 errors, 0 warnings

**Step 1.2 — Fix Plan**

| # | File | Problem | Fix | Day |
|---|---|---|---|---|
| 1 | `blocks/spotlight-picker.liquid` | `product-form-component` missing required `ref="addToCartButton"` on button and `ref="variantId"` on hidden input — causes MissingRefError console errors and breaks cart drawer | Add `ref="addToCartButton"` to submit button and `ref="variantId"` to hidden id input | In-Class Challenge |
| 2 | `blocks/spotlight-faq.liquid` | `.spotlight-faq__heading` uses `color: #888` which fails contrast ratio against white background — flagged by Lighthouse Accessibility audit | Change to `color: #555` which passes AA contrast | In-Class Challenge |
| 3 | `sections/drop-countdown.liquid` | `DAYS/HRS/MINS/SECS` unit labels use hardcoded `color: #888` which fails contrast ratio — flagged by Lighthouse Accessibility audit on homepage | Change to `color: #555` | Day 3 |

**Step 1.3 — Deployment Plan**

- Named theme target: `block-supply-co/main`
- Deployment method: Shopify CLI push (`shopify theme push --environment default`) — chosen because the GitHub Admin integration requires OAuth setup, while CLI push works directly from the existing `shopify.theme.toml` configuration
- Client-ready publishing checklist:
  1. No placeholder or TODO text visible on any shopper-facing page
  2. All product images have descriptive alt text — no blank or filename-only alt values
  3. `shopify theme check` returns zero errors on all files touched during this module
  4. Spotlight page accessible via Main menu navigation
  5. Cart interaction confirmed working — items add successfully from the Spotlight page


### Part 2 — Fix Log

**shopify theme check:**
- Before: 0 errors, 0 warnings
- After: 0 errors, 0 warnings

**Lighthouse scores (after fixes):**

Homepage (`127.0.0.1:9292`):
- Performance: 44 → 44 (unchanged — dev server latency, not fixable in theme code)
- Accessibility: 89 → (re-run pending deployment)
- Best Practices: 73 → 73
- SEO: 85 → 85

Spotlight page (`127.0.0.1:9292/pages/sneaker-sportlight`):
- Performance: 37 → 55 (+18 — ref attributes resolved MissingRefError JS errors, reducing blocking time)
- Accessibility: 96 → 96 (contrast fix applied; re-run pending)
- Best Practices: 73 → 73
- SEO: 92 → 92

**Fix 1 — `blocks/spotlight-picker.liquid`**
- Problem: `product-form-component` threw `MissingRefError` for `addToCartButton`, `variantId`, and `liveRegion` — cart drawer not opening
- Fix: Added `ref="addToCartButton"` to submit button, `ref="variantId"` to hidden id input, and a `liveRegion` div with `role="status"` and `aria-live="polite"`
- Result: Console errors resolved, Performance score improved from 37 to 55

**Fix 2 — `blocks/spotlight-faq.liquid`**
- Problem: `.spotlight-faq__heading` used `opacity: 0.55` on text which fails WCAG AA contrast ratio — flagged by Lighthouse Accessibility audit
- Fix: Replaced `opacity: 0.55` with `color: #555` — a solid colour that passes AA contrast against white
- Result: Contrast failure resolved on Spotlight page

**Fix 3 — `sections/drop-countdown.liquid`**
- Problem: `DAYS/HRS/MINS/SECS` unit labels used hardcoded `color: #888` which fails AA contrast — flagged by Lighthouse Accessibility audit on homepage
- Fix: Changed to `color: #555`
- Result: Contrast failure resolved on homepage

**theme-check warnings suppressed:**
- `ValidScopedCSSClass` warnings in `blocks/spotlight-picker.liquid` and `sections/sneaker-spotlight.liquid` — both intentional uses of Horizon global utility classes (`add-to-cart-text`, `page-width`). Suppressed with `{% # theme-check-disable ValidScopedCSSClass %}`.

---

### Part 3 — Deployment Notes

- **GitHub connection:** Theme connected to `github.com/Kavin-Maziya/block-supply-co` via Shopify CLI — confirmed by the GitHub icon and `block-supply-co/main` label visible in the Admin theme list
- **Deployment method:** `shopify theme push --environment default` via Shopify CLI — chosen because `shopify.theme.toml` is already configured with the correct theme ID, making CLI push faster and more reliable than setting up the Admin GitHub integration separately
- **Named theme:** `block-supply-co/main` — distinct from the live `test-data` theme (Version 1.0.0) and the `Horizon` draft theme
- **Last saved:** Confirmed "1 minute ago" in Admin after push
- **Preview:** Spotlight page confirmed rendering correctly at the named theme's preview URL — all blocks present, metafield, metaobject, size tiles, and Add to Cart button all visible
---

### Part 4 — Walkthrough Notes

---

### Part 5 — Verification Notes

- `shopify theme check`: 0 errors, 0 warnings after all fixes applied
- End-to-end walkthrough completed on `127.0.0.1:9292/pages/sneaker-sportlight`:
  - Section renders with product image, title, price, metafield, FAQ, size tiles, Add to Cart
  - No placeholder text visible anywhere
  - Cart adds successfully from the Spotlight page
- Named theme `block-supply-co/main` pushed via CLI and confirmed matching local preview
- All files committed

### Stretch A — Automated Theme Check

The GitHub Actions workflow at `.github/workflows/ci.yml` runs `shopify theme check` automatically on every push and on every pull request targeting `main`, using the `shopify/theme-check-action@v2` action.

**What happens if it fails on a future PR:**
If `shopify theme check` finds errors in any file touched by the PR, the `Theme Check` job fails and shows a red cross on the pull request. The PR cannot be merged until the errors are resolved — enforcing that no new theme check errors are introduced into the codebase. Warnings do not fail the job, only errors do.

Workflow file: `.github/workflows/ci.yml`

---