---
name: darak-saudi-real-estate
description: Use when researching Saudi Arabian rental or sale property markets, searching for apartments, villas, land, buildings, offices, or any property type, comparing neighborhoods, evaluating listing prices, or analyzing real estate trends in Riyadh, Jeddah, Eastern Province, Makkah, or Madinah. Also use when asked about Saudi housing costs, apartment hunting, best deals, rental yield, investment returns, ROI, affordability, rent burden, market supply trends, vacancy rates, stale listings, neighborhood safety, market conditions, or commercial real estate. Trigger on any question about Saudi property prices, rents, or real estate data, even if the user doesn't mention "Darak" by name.
---

# Darak Saudi Real Estate Research

20 read-only MCP tools for 65,000+ Saudi property listings. All prices in SAR (Saudi Riyal). All tools are read-only and require no authentication.

## Critical Rules

1. **Neighborhood names must be exact English matches.** Always call `list_neighborhoods` first, then use the returned English name verbatim. Never guess or transliterate from Arabic.
2. **Default city is Riyadh.** Always confirm which city the user means. The 5 supported cities: `riyadh`, `jeddah`, `eastern_province`, `makkah`, `madinah`.
3. **Default listing type is rent.** Confirm whether the user wants rental or sale listings.
4. **`get_map_pois` returns empty below zoom 10.** Use `get_neighborhood_pois` instead when you have a neighborhood name.

## Workflow Patterns

### Apartment/Villa Hunting

```
list_neighborhoods → search_listings (with filters) → get_listing (for details on promising ones) → get_listing_market_stats (is the price fair?) → get_comparable_listings (what else is nearby?)
```

### "Is this a good price?"

```
get_listing → get_listing_market_stats (percentile rank) → get_comparable_listings (nearby alternatives) → get_price_history (has it dropped?)
```

Price percentile tells you where the listing sits: below P25 is a strong deal, above P75 is expensive for the area.

### Best Deals in an Area

```
list_neighborhoods → get_best_value_listings (sorted by discount %)
```

Returns listings priced below their neighborhood median. Lead with the discount percentage and neighborhood median for context.

### Neighborhood Comparison

```
list_neighborhoods → compare_neighborhoods (2-5 at once) → get_neighborhood_pois (amenities for each) → get_neighborhood_trends (price direction)
```

Present as a comparison table: median price, price range (P25-P75), area, amenities, and price trend (rising/falling/stable).

### Market Overview

```
get_market_summary → get_price_distribution (shape of the market)
```

Start broad with city-level summary, then drill into price distribution to show where most listings cluster.

### Investment Yield Analysis

```
get_rental_yield (city or neighborhood) → compare_neighborhoods (with yield data for rent)
```

Returns gross yield = median annual rent / median sale price. Use to fact-check yield claims (e.g. "8% return on a building"). When listing_type=rent, compare_neighborhoods also returns `gross_yield_pct` and `rent_to_income_10k` per neighborhood.

### Affordability Analysis

```
get_price_distribution → use cumulative percentiles
```

The response includes a `cumulative` array: "72% of apartments in Riyadh rent for under 50K." Use for rent burden or affordability discussions. `rent_to_income_10k` in compare_neighborhoods shows median rent as a % of a 10K SAR salary.

### Supply/Demand Analysis

```
get_supply_stats (week/month trends) → get_vacancy_indicator (stale listing counts)
```

Supply stats show new listing volume changes. Vacancy indicator shows how many listings have sat for 30/60/90+ days (oversupply signal). Combine for "supply up 15% this month but 33% of listings are stale."

### Stale/Overpriced Listings

```
search_listings (min_days_on_market: 60, sort: days_on_market_desc) → get_vacancy_indicator
```

Find listings sitting unsold/unrented. Use to argue oversupply or overpricing in a neighborhood.

### Price Trend Analysis

```
list_neighborhoods → get_neighborhood_trends (1-5 neighborhoods, up to 24 months)
```

The response includes `price_change_pct` between earliest and latest month. `get_market_summary` also returns `yoy_change` with median price from 12 months ago and percentage change. Present as: "Prices in [neighborhood] have [risen/fallen] X% over the past N months."

### City-Wide Rent Map

```
get_neighborhood_rent_map → identify cheapest/most expensive neighborhoods
```

Returns median rent for every neighborhood in the city. Use for broad "where is rent cheapest in Riyadh?" questions.

## Domain Context

**Typical Riyadh apartment rents (annual):**

- Studio/1BR: 15,000-30,000 SAR
- 2BR: 20,000-45,000 SAR
- 3BR: 25,000-60,000 SAR

Jeddah and Eastern Province are generally comparable. Makkah and Madinah vary by proximity to the Haram.

**Property types:** `apartment`, `villa`, `land`, `building`, `office`, `shop`, `warehouse`, `floor`, `duplex`. Use `all` when the user hasn't specified. Commercial types (building, office, shop, warehouse) are supported across all tools.

**Sort options worth knowing:**

- `relevance` — default, quality-weighted
- `best_value` — biggest discount vs neighborhood median
- `price_per_sqm_asc` — cheapest per square meter
- `newest` — most recently listed
- `recently_updated` — most recently refreshed (good for active listings)
- `days_on_market_desc` — longest on market first (stale/overpriced signal)

## Presentation Tips

- Always show prices in SAR. For international users, note that 1 USD ≈ 3.75 SAR.
- When comparing listings, use a table with: price, bedrooms, area (sqm), neighborhood, and price/sqm.
- For market stats, lead with the median (not the mean) as Saudi listings have extreme outliers.
- When showing "best value" listings, include both the listing price and the neighborhood median so the user sees the actual discount.
- Link to listings using the `url` field returned by each tool (format: `https://darak.app/listing/{id}`).

## Tool Quick Reference

| Task                          | Tool                        | Key params                                                                                 |
| ----------------------------- | --------------------------- | ------------------------------------------------------------------------------------------ |
| Search with filters           | `search_listings`           | city, listing_type, property_type, beds, price range, neighborhood, min/max_days_on_market |
| Single listing details        | `get_listing`               | id                                                                                         |
| Similar nearby listings       | `get_comparable_listings`   | id, limit                                                                                  |
| Price change over time        | `get_price_history`         | id                                                                                         |
| Best deals                    | `get_best_value_listings`   | city, listing_type, property_type, neighborhood, beds                                      |
| Price histogram + percentiles | `get_price_distribution`    | city, listing_type, property_type, neighborhood                                            |
| Area histogram                | `get_area_distribution`     | city, listing_type, property_type, neighborhood                                            |
| Listing vs market             | `get_listing_market_stats`  | id                                                                                         |
| Compare neighborhoods         | `compare_neighborhoods`     | neighborhoods (includes yield + affordability for rent)                                    |
| City overview + YoY           | `get_market_summary`        | city, listing_type                                                                         |
| Monthly trends                | `get_neighborhood_trends`   | neighborhoods, months, property_type                                                       |
| Rental yield                  | `get_rental_yield`          | city, neighborhood, property_type                                                          |
| Supply trends                 | `get_supply_stats`          | city, listing_type, neighborhood, property_type                                            |
| Stale listings                | `get_vacancy_indicator`     | city, listing_type, neighborhood, property_type                                            |
| City rent map                 | `get_neighborhood_rent_map` | city, listing_type, bedrooms                                                               |
| Neighborhood list             | `list_neighborhoods`        | city                                                                                       |
| City districts                | `list_city_directions`      | city                                                                                       |
| Nearby POIs                   | `get_neighborhood_pois`     | city, neighborhood                                                                         |
| Map bounds search             | `get_map_listings`          | bounds, zoom, property_type                                                                |
| Map POIs                      | `get_map_pois`              | bounds, zoom (min 10)                                                                      |
