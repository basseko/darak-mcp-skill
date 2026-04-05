---
name: darak-saudi-real-estate
description: Use when researching Saudi Arabian rental or sale property markets, searching for apartments, villas, land, buildings, offices, or any property type, comparing neighborhoods, evaluating listing prices, or analyzing real estate trends in Riyadh, Jeddah, Eastern Province, Makkah, or Madinah. Also use when asked about Saudi housing costs, apartment hunting, best deals, rental yield, investment returns, ROI, affordability, rent burden, market supply trends, vacancy rates, stale listings, neighborhood safety, market conditions, commercial real estate, off-plan projects, new developments, developers, or project units. Trigger on any question about Saudi property prices, rents, real estate data, or development projects, even if the user doesn't mention "Darak" by name.
---

# Darak Saudi Real Estate Research

26 read-only MCP tools for 65,000+ Saudi property listings and development projects. All prices in SAR (Saudi Riyal). All tools are read-only and require no authentication.

## Critical Rules

1. **Neighborhood names must be exact English matches.** Always call `list_neighborhoods` first, then use the returned English name verbatim. Never guess or transliterate from Arabic.
2. **Default city is Riyadh.** Always confirm which city the user means. The 5 supported cities: `riyadh`, `jeddah`, `eastern_province`, `makkah`, `madinah`.
3. **Default listing type is rent.** Confirm whether the user wants rental or sale listings.
4. **Commercial properties need listing_category='commercial'.** When searching for offices, shops, or warehouses, you MUST set `listing_category` to `'commercial'`. The default is `'residential'` and will return zero results for commercial property types.
5. **`get_map_pois` returns empty below zoom 10.** Use `get_neighborhood_pois` instead when you have a neighborhood name.

## Workflow Patterns

### Apartment/Villa Hunting

```
list_neighborhoods -> search_listings (with filters) -> get_listing (for details on promising ones) -> get_listing_market_stats (is the price fair?) -> get_comparable_listings (what else is nearby?)
```

### "Is this a good price?"

```
get_listing -> get_listing_market_stats (percentile rank + neighborhood median diff) -> get_comparable_listings (nearby alternatives) -> get_price_history (has it dropped?)
```

`get_listing_market_stats` is the primary tool here. It returns the listing's price percentile (e.g. "cheaper than 72% of similar listings"), P5-P95 range, area percentile, price-per-sqm vs median, and neighborhood comparison with diff%. Price percentile below P25 is a strong deal, above P75 is expensive for the area.

### Best Deals in an Area

```
list_neighborhoods -> get_best_value_listings (sorted by discount %)
```

Returns listings priced below their neighborhood median. Each listing shows its price, neighborhood_median, and discount_pct. Lead with the discount percentage and neighborhood median for context. Use `min_discount_pct` to raise the threshold (default 10%).

### Neighborhood Comparison

```
list_neighborhoods -> compare_neighborhoods (2-5 at once) -> get_neighborhood_pois (amenities for each) -> get_neighborhood_trends (price direction)
```

Present as a comparison table: median price, price range (P25-P75), area, amenities, and price trend (rising/falling/stable).

### Market Overview

```
get_market_summary (optionally with property_type) -> get_price_distribution (shape of the market)
```

Start broad with city-level summary, then drill into price distribution to show where most listings cluster. Use `property_type` on `get_market_summary` to focus on a specific type (e.g. villas only).

### Investment Yield Analysis

```
get_rental_yield (city or neighborhood) -> compare_neighborhoods (with yield data for rent)
```

Returns gross yield = median annual rent / median sale price. When no neighborhood is specified, returns the top 10 neighborhoods by yield. Use to fact-check yield claims (e.g. "8% return on a building"). When listing_type=rent, compare_neighborhoods also returns `gross_yield_pct` and `rent_to_income_10k` per neighborhood.

### Affordability Analysis

```
get_price_distribution -> use cumulative percentiles
```

The response includes a `cumulative` array: "72% of apartments in Riyadh rent for under 50K." Use for rent burden or affordability discussions. `rent_to_income_10k` in compare_neighborhoods shows median rent as a % of a 10K SAR salary.

### Supply/Demand Analysis

```
get_supply_stats (week/month trends) -> get_vacancy_indicator (stale listing counts)
```

Supply stats show new listing volume changes. Vacancy indicator shows how many listings have sat for 30/60/90+ days (oversupply signal). Combine for "supply up 15% this month but 33% of listings are stale."

### Stale/Overpriced Listings

```
search_listings (min_days_on_market: 60, sort: days_on_market_desc) -> get_vacancy_indicator
```

Find listings sitting unsold/unrented. Use to argue oversupply or overpricing in a neighborhood.

### Price Drop Hunting

```
search_listings (sort: price_drop)
```

Find listings that recently reduced their price, sorted by biggest percentage drop first. Combine with neighborhood filters to find motivated sellers/landlords in a specific area.

### Price per Sqm Comparison

```
list_neighborhoods -> compare_neighborhoods (returns median_price_per_sqm and median_area_sqm)
```

Use when comparing raw vs developed land prices, evaluating auction pricing, or answering "is this price fair per sqm?" `compare_neighborhoods` is the only tool that returns per-neighborhood area and price/sqm data. `get_neighborhood_trends` returns price only, not area.

### Price Trend Analysis

```
list_neighborhoods -> get_neighborhood_trends (1-5 neighborhoods, up to 24 months)
```

The response includes `price_change_pct` between earliest and latest month. `get_market_summary` also returns `yoy_change` with median price from 12 months ago and percentage change. Present as: "Prices in [neighborhood] have [risen/fallen] X% over the past N months."

### City-Wide Price Map

```
get_neighborhood_rent_map -> identify cheapest/most expensive neighborhoods
```

Returns median price for every neighborhood in the city, sorted by price. Works for both rent and sale listings. Use for broad "where is rent cheapest in Riyadh?" questions.

### Quick Counts

```
get_listings_count (with filters)
```

Returns just the total count without fetching listing data. Use when the user asks "how many 3BR apartments are for rent in Al Malqa?" or you need a count for context.

### Off-Plan / Development Project Search

```
search_projects (city, type: 'off_plan') -> get_project (for full details + units)
```

Use for questions about new developments, off-plan projects, or specific developers. `search_projects` supports filtering by developer, neighborhood, features (pool, gym, etc.), supported banks, and price range. Use `list_developers` first to get valid developer names.

### Project Unit Search

```
list_developers -> search_project_units (city, unit_beds, unit_price_max, developer)
```

Search individual units across projects when the user wants specific unit types (e.g. "3BR units under 1.5M in off-plan projects"). Combine project-level filters (developer, type, features) with unit-level filters (beds, baths, area, price).

### Commercial Property Search

```
search_listings (listing_category: 'commercial', property_type: 'office') -> get_listing_market_stats
```

Always set `listing_category='commercial'` when searching for office, shop, or warehouse. The default 'residential' will return zero results for these types.

### Description Search

```
search_listings (q: '"سكن طالبات" | "سكن موظفات"')
```

Use the `q` param to search listing descriptions and titles. Supports words (AND by default), quoted "phrases" for exact substring, and `|` for OR between groups. Useful for finding specific features or housing types not captured by structured filters (e.g. women-only housing, specific amenities mentioned in text, proximity descriptions).

## Domain Context

**Typical Riyadh apartment rents (annual):**

- Studio/1BR: 15,000-30,000 SAR
- 2BR: 20,000-45,000 SAR
- 3BR: 25,000-60,000 SAR

Jeddah and Eastern Province are generally comparable. Makkah and Madinah vary by proximity to the Haram.

**Property types:** `apartment`, `villa`, `land`, `building`, `office`, `shop`, `warehouse`, `floor`, `duplex`. Use `all` when the user hasn't specified. Commercial types (office, shop, warehouse) require `listing_category='commercial'`.

**Sort options worth knowing:**

- `relevance` -- default, quality-weighted
- `best_value` -- biggest discount vs neighborhood median
- `price_per_sqm_asc` -- cheapest per square meter
- `newest` -- most recently listed
- `recently_updated` -- most recently refreshed (good for active listings)
- `price_drop` -- biggest recent price reduction first
- `days_on_market_desc` -- longest on market first (stale/overpriced signal)

## Presentation Tips

- Always show prices in SAR. For international users, note that 1 USD ~ 3.75 SAR.
- When comparing listings, use a table with: price, bedrooms, area (sqm), neighborhood, and price/sqm.
- For market stats, lead with the median (not the mean) as Saudi listings have extreme outliers.
- When showing "best value" listings, include both the listing price and the neighborhood median so the user sees the actual discount.
- Link to listings using the `url` field returned by each tool (format: `https://darak.app/listing/{id}`).

## Tool Quick Reference

| Task                          | Tool                        | Key params                                                                                    |
| ----------------------------- | --------------------------- | --------------------------------------------------------------------------------------------- |
| Search with filters           | `search_listings`           | city, listing_type, listing_category, property_type, beds, price range, neighborhood, q, sort |
| Count matching listings       | `get_listings_count`        | city, listing_type, listing_category, property_type, neighborhood, beds, price range          |
| Batch lookup by IDs           | `get_listings_by_ids`       | ids (comma-separated)                                                                         |
| Single listing details        | `get_listing`               | id                                                                                            |
| Similar nearby listings       | `get_comparable_listings`   | id, limit                                                                                     |
| Price change over time        | `get_price_history`         | id                                                                                            |
| Best deals                    | `get_best_value_listings`   | city, listing_type, listing_category, property_type, neighborhood, beds, min_discount_pct     |
| Price histogram + percentiles | `get_price_distribution`    | city, listing_type, listing_category, property_type, neighborhood, beds (1-5)                 |
| Area histogram                | `get_area_distribution`     | city, listing_type, listing_category, property_type, neighborhood                             |
| Listing vs market             | `get_listing_market_stats`  | id (use FIRST for "is this a good deal?")                                                     |
| Compare neighborhoods         | `compare_neighborhoods`     | neighborhoods (returns median_area_sqm, median_price_per_sqm, yield, affordability)           |
| City overview + YoY           | `get_market_summary`        | city, listing_type, property_type                                                             |
| Monthly trends                | `get_neighborhood_trends`   | neighborhoods, months, property_type                                                          |
| Rental yield                  | `get_rental_yield`          | city, neighborhood, property_type                                                             |
| Supply trends                 | `get_supply_stats`          | city, listing_type, neighborhood, property_type                                               |
| Stale listings                | `get_vacancy_indicator`     | city, listing_type, neighborhood, property_type                                               |
| City price map                | `get_neighborhood_rent_map` | city, listing_type, bedrooms                                                                  |
| Neighborhood list             | `list_neighborhoods`        | city (returns price_tier 1-4)                                                                 |
| City districts                | `list_city_directions`      | city (use for "north Riyadh", "eastern Jeddah")                                               |
| Nearby POIs                   | `get_neighborhood_pois`     | city, neighborhood                                                                            |
| Search projects               | `search_projects`           | city, type (off_plan/ready), category, developer, neighborhood, features, banks, price, q     |
| Project details + units       | `get_project`               | id                                                                                            |
| Developer directory           | `list_developers`           | city (get valid names before filtering search_projects)                                       |
| Search units in projects      | `search_project_units`      | city, unit_beds/baths/price/area, developer, type, features, banks, q                         |
| Map bounds search             | `get_map_listings`          | bounds, zoom, property_type                                                                   |
| Map POIs                      | `get_map_pois`              | bounds, zoom (min 10)                                                                         |
