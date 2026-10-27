# Roll the Dice: The World of Board Games

FIT3179 Data Visualisation 2, Monash University Malaysia.
Author: Megat Zain E'sham Zaphrin. October 2026.

Live page: https://aisbear16.github.io/Data-Visualization-2/

A single scrolling web page that tells the story of board games: where the world's board gamers live, what kinds of games exist, whether harder games are rated higher, and what makes a hit. It has 13 charts in 4 parts, built with Vega-Lite (and Vega for two charts).

---

## Folder structure

```
Data-Visualization-2/
├── index.html          The web page: text, layout and the code that loads every chart
├── README.md           This file
├── css/
│   └── styles.css      Colours, fonts and layout of the page
├── data/               Small CSV files that the charts read
│   ├── bgg_users_by_country.csv
│   ├── game_imports_2023.csv
│   ├── games.csv
│   ├── games_by_domain.csv
│   ├── games_per_year_domain.csv
│   ├── treemap_domains.csv
│   └── waffle_continent.csv
└── js/                 One JSON specification per chart, plus the world map shapes
    ├── map_users_symbol.vg.json
    ├── map_users_choropleth.vg.json
    ├── waffle_continent.vg.json
    ├── cartogram_imports.vg.json
    ├── treemap_domains.vg.json
    ├── stream_year_domain.vg.json
    ├── bump_domain_rank.vg.json
    ├── hist_complexity.vg.json
    ├── heatmap_complexity_rating.vg.json
    ├── box_rating_domain.vg.json
    ├── scatter_small_multiples.vg.json
    ├── parallel_top_games.vg.json
    ├── isotype_owners.vg.json
    └── ne_110m_admin_0_countries.topojson
```

The folder layout matches the course's Week 8 studio: HTML in the root, chart specifications in `js/`, data in `data/` and styles in `css/`. The page only works if every file keeps exactly this name and folder.

---

## What each file does

### `index.html`

| Lines | What is there |
|---|---|
| 7–8 | Loads the Google Fonts Fredoka (headings) and Source Sans 3 (body text) |
| 9–11 | Loads the libraries from a CDN: Vega 5.22.1, Vega-Lite 5.5.0, Vega-Embed 6.21.0 (the same versions as the course studios) |
| 17–26 | Top bar: links to the four parts and the reading progress bar |
| 29–41 | Header: title, introduction and three key numbers |
| 46–83 | Part 1, "Where the world's board gamers are" (4 charts) |
| 86–110 | Part 2, "What kinds of games are out there?" (3 charts) |
| 113–148 | Part 3, "Is harder better?" (4 charts and the key takeaway card) |
| 151–171 | Part 4, "What makes a hit?" (2 charts) |
| 175–192 | Footer: author, date, data sources, AI acknowledgement |
| 194–225 | The script that draws every chart, moves the progress bar and highlights the current part in the top bar |

Each chart sits in a `<figure class="fig">` card with a small label (for example "Fig 1.1 · Proportional symbol map"), an empty placeholder such as `<div id="map_users_symbol">`, and a `<figcaption>` with a key number and the caption.

The script at the bottom fills each placeholder:

```js
const charts = ["map_users_symbol", "map_users_choropleth", ...];
charts.forEach(function (id) {
  vegaEmbed("#" + id, "js/" + id + ".vg.json", {actions: false}).catch(console.error);
});
```

- `charts` lists the placeholder ids.
- `vegaEmbed(target, spec, options)` is the Vega-Embed function from the course. It reads the JSON file `js/<id>.vg.json` and draws the chart inside `<div id="<id>">`. Because each div id matches its JSON filename, one loop draws all 13 charts.
- `{actions: false}` hides the "..." export menu.
- `.catch(console.error)` prints any loading error to the browser console (F12).
- The `scroll` listener widens the orange progress bar as you scroll down.
- The `IntersectionObserver` watches the four sections and highlights the part you are reading in the top bar.

**To add a chart:** add a `<div id="new_chart">` where you want it, save the spec as `js/new_chart.vg.json`, and add `"new_chart"` to the `charts` list.

### `css/styles.css`

- `:root` (top of the file): colour variables (`--ink`, `--paper`, `--accent`, `--felt` for the green table colour, and so on) used across the page.
- `.topbar`, `.pip`, `.progress`: the sticky top bar, its numbered dice links and the progress bar.
- `.hero`, `.tokens`, `.track`: the green header, the three key-number tokens and the striped "board track" under it.
- `.part-head`, `.die`: each part's heading row, with a dice face (1 to 4 pips) and the intro text beside it.
- `.fig`, `.fig-tag`, `figcaption`, `.key`, `.note`: the white chart cards, their "Fig" labels, and the caption row with a big key number and the caption text (limited to about 60 characters per line).
- `.grid-2`: two charts side by side (histogram and heat map). `.side`: waffle chart with the big "89%" next to it.
- `.rule-card`: the "Key takeaway" box styled like a game rule card.
- `.scroll`: lets the wide cartogram and small multiples scroll sideways on small screens.
- `@media (max-width: 900px)` and `(max-width: 520px)`: stack everything into one column and shrink the top bar on small screens.

### `js/*.vg.json` (chart specifications)

Each file is one chart, written in Vega-Lite JSON (or Vega JSON for the treemap and cartogram). It says which data file to load, how to transform it, and which marks and channels (x, y, colour, size) to use. The table below lists them all.

### `js/ne_110m_admin_0_countries.topojson`

The Natural Earth 1:110m world country shapes from the Week 8 studio. The three maps use it for country outlines, and for each country's name (`NAME`), population estimate (`POP_EST`) and continent (`CONTINENT`).

### `data/*.csv`

| File | Rows | Columns | Used by |
|---|---|---|---|
| `bgg_users_by_country.csv` | 164 countries | country, iso_a3, bgg_users, population, per100k, continent, lon, lat | Symbol map, choropleth |
| `waffle_continent.csv` | 100 squares | cell, col, row, continent, share, users | Waffle chart |
| `game_imports_2023.csv` | 140 countries | country, iso_a3, import_musd, continent, lon, lat | Cartogram |
| `treemap_domains.csv` | 9 (root + 8 categories) | id, parent, games, avg_rating, avg_complexity | Treemap |
| `games_per_year_domain.csv` | 315 | year, domain, games | Dumbbell plot, bump chart |
| `games.csv` | 19,451 games | id, title, year, rank_2025, avg_rating_2025, voters_2025, complexity_2021, domain, min_players, max_players, play_time, min_age, owned_2021 | Histogram, heat map, parallel coordinates, ISOTYPE |
| `games_by_domain.csv` | 11,247 | id, title, year, avg_rating_2025, complexity_2021, voters_2025, domain | Box plot, small multiples |

In `games.csv`, `domain` is each game's main category. In `games_by_domain.csv`, a game listed in two categories appears once per category.

---

## Where each chart is

| # | Part | Chart (title on page) | Idiom | Placeholder in `index.html` | Spec file | Data file | Library | Main transforms and features |
|---|---|---|---|---|---|---|---|---|
| 1 | 1 | Where BoardGameGeek members live | Proportional symbol map | `#map_users_symbol`, line 57 | `js/map_users_symbol.vg.json` | `bgg_users_by_country.csv` + topojson | Vega-Lite | `equalEarth` projection; `circle` sized by `bgg_users`; legend click highlights a continent (`params` with `bind: "legend"`) |
| 2 | 1 | Board gamers per 100,000 people | Choropleth map | `#map_users_choropleth`, line 63 | `js/map_users_choropleth.vg.json` | `bgg_users_by_country.csv` + topojson | Vega-Lite | `lookup` joins the CSV to the map shapes by country name; `calculate` sorts each rate into a band (including "No members listed"), shown with an `ordinal` colour scale; dashed lines and labels mark the equator and prime meridian |
| 3 | 1 | Nine in ten members live in North America or Europe | Waffle chart | `#waffle_continent`, line 70 | `js/waffle_continent.vg.json` | `waffle_continent.csv` | Vega-Lite | 10×10 `square` marks; each square = 1% of members |
| 4 | 1 | Who spends the most importing games? | Dorling cartogram | `#cartogram_imports`, line 80 | `js/cartogram_imports.vg.json` | `game_imports_2023.csv` + topojson | **Vega** | `scale('projection', [lon, lat])` places each country; `force` transform (collide + x + y) pushes circles apart; `sqrt` size scale |
| 5 | 2 | Wargames and strategy games make up the biggest families | Treemap | `#treemap_domains`, line 97 | `js/treemap_domains.vg.json` | `treemap_domains.csv` | **Vega** | `stratify` builds the hierarchy from `id`/`parent`; `treemap` with `squarify` lays out the rectangles |
| 6 | 2 | The board game boom after 2010 | Dumbbell plot | `#stream_year_domain`, line 102 | `js/stream_year_domain.vg.json` | `games_per_year_domain.csv` | Vega-Lite | `filter` keeps 2000 and 2015; one layer per category and one `aggregate` layer for "All categories"; `line` joins each pair of `circle` marks; `joinaggregate` finds the larger value so its label goes on the right |
| 7 | 2 | Which category is released most often? | Bump chart | `#bump_domain_rank`, line 108 | `js/bump_domain_rank.vg.json` | `games_per_year_domain.csv` | Vega-Lite | `calculate` groups years in pairs; `aggregate` sums games; `window` with `rank` ranks categories per period; `line` + `circle` layers |
| 8 | 3 | Most games are easy to learn | Histogram | `#hist_complexity`, line 125 | `js/hist_complexity.vg.json` | `games.csv` | Vega-Lite | `bin` (step 0.25) on complexity; `count` |
| 9 | 3 | Heavier games tend to score higher | Heat map | `#heatmap_complexity_rating`, line 130 | `js/heatmap_complexity_rating.vg.json` | `games.csv` | Vega-Lite | `bin` on both axes, `count` as colour; dropdown filter (`params` with `bind: {input: "select"}`) |
| 10 | 3 | Strategy and war games please players most; children's games least | Box plot | `#box_rating_domain`, line 141 | `js/box_rating_domain.vg.json` | `games_by_domain.csv` | Vega-Lite | `boxplot` mark; categories sorted by median rating |
| 11 | 3 | Complexity vs rating in each category | Small multiples (scatterplots) | `#scatter_small_multiples`, line 146 | `js/scatter_small_multiples.vg.json` | `games_by_domain.csv` | Vega-Lite | `facet` by category in 4 columns; `filter` keeps games with 100+ ratings |
| 12 | 4 | What do the top 25 games have in common? | Parallel coordinates | `#parallel_top_games`, line 162 | `js/parallel_top_games.vg.json` | `games.csv` | Vega-Lite | `filter` top 25; `fold` turns 5 measures into rows; `joinaggregate` finds min/max; `calculate` normalises each axis to 0–1; hover highlights a game |
| 13 | 4 | The most-owned games | ISOTYPE pictogram | `#isotype_owners`, line 168 | `js/isotype_owners.vg.json` | `games.csv` | Vega-Lite | `window` with `row_number` picks the top 10; `calculate` with `sequence()` + `flatten` makes one 🎲 per 10,000 owners; `text` mark |

Every chart also has tooltips (`tooltip` encoding) showing exact values on hover.

**Shared settings used in most specs:**
- Category colours are fixed in each spec's `scale` (`domain` = the 8 categories, `range` = the same 8 colours) so a category has the same colour in every chart.
- Continent colours are fixed the same way across the symbol map, waffle chart and cartogram.
- `config` at the end of each spec sets fonts (Fredoka titles, Source Sans 3 text) and title and subtitle styling.
- CSV files are loaded with `"format": {"parse": {...}}` so blank cells are read as empty values rather than text.

---

## Where the data comes from

| Source | What it provides | Used in |
|---|---|---|
| bwandowando, *BoardGameGeek Games + Reviews as of Feb 2025*, Kaggle | Ratings, rankings and number of voters for each game (2025); BoardGameGeek members' countries | `games.csv`, `games_by_domain.csv`, `bgg_users_by_country.csv`, `waffle_continent.csv` |
| D. Samarasinghe, *BoardGameGeek Dataset on Board Games*, IEEE DataPort, 2021 | Complexity, categories, play time, minimum age, owners | `games.csv`, `games_by_domain.csv`, `games_per_year_domain.csv`, `treemap_domains.csv` |
| World Bank WITS (UN Comtrade), HS 950490, 2023 | Imports of board and table games by country | `game_imports_2023.csv` |
| Natural Earth 1:110m countries (FIT3179 Week 8 studio) | Country shapes, population, continent | All maps; population for the per-100,000 rate |

**How the CSVs were prepared** (done once in Python, before building the page):
1. The two BoardGameGeek datasets were joined on the game ID. Games with a complexity of 0 (no votes) were set to blank.
2. Member countries were counted per country and matched to Natural Earth country names (for example, England, Scotland and Wales counted as United Kingdom). Members per 100,000 people = members ÷ `POP_EST` × 100,000.
3. WITS country names were matched to Natural Earth names, and groups like "European Union" were removed.
4. Each country's position (`lon`, `lat`) is the centre of its largest land area, calculated from the map file.
5. Small summary files (yearly counts, treemap totals, the 100 waffle squares) were pre-calculated so the page stays fast. All data together is about 2.5 MB.

**Limitations to mention:** complexity and categories are from 2021, so the category trend charts end at 2020. Only about half the games have a category. HS 950490 also includes casino and arcade table games, not only board games.

---

## How to run it locally

Opening `index.html` by double-clicking shows empty charts, because browsers block loading data files from your own computer. Use **Live Server** in VS Code instead (right-click `index.html` → "Open with Live Server"), or view the live GitHub Pages link.

## How to update the live page

1. Edit the files in VS Code and save.
2. Source Control tab → stage (+) → write a message → Commit → Sync Changes.
3. Wait for the green tick in the repo's Actions tab, then press Ctrl+Shift+R on the site.

Keep file names and folders exactly as listed above. Do not rename downloaded files by hand.

---

AI tools (Claude) were used to help prepare the data, write the chart specifications and draft text.
