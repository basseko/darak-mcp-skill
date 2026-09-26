---
name: darak-saudi-real-estate
description: Researches Saudi Arabian rental and sale property with Darak's live listing data (MCP server at mcp.darak.app) — searching apartments, villas, land, offices and other property, checking whether a price is fair, comparing neighborhoods and cities, rent and price levels, rental yield, supply and stale inventory, price trends, and off-plan or ready development projects. Use when the user asks about property prices, rents, listings, neighborhoods, investment or developers anywhere in Saudi Arabia (Riyadh, Jeddah, Eastern Province, Makkah, Madinah and other covered cities), even without mentioning Darak.
---

# Saudi real estate with Darak

Darak aggregates active Saudi property listings from 13+ sources, deduplicated, with market statistics and development projects. The `darak` MCP server's tool descriptions explain each tool; this skill covers what they can't: what the data means and how to answer honestly.

## What the data is (and isn't)

- **Asking prices of active listings.** Not sold prices, signed leases or registered deals. Never present an asking price as what something sold or rented for. If the user asks for sold prices or transactions, say Darak doesn't have them, then still fetch and give the closest asking-price figures, labelled as asking prices.
- **Rent is SAR per year**, even for ads posted monthly (`price.as_posted` has the original). Say "per year", or divide by 12 and say "per month".
- **No crime, safety, school-quality or demographic data.** Say so; don't fill the gap from general knowledge.
- **No forecasts.** Trends show past asking prices; a changing mix of listings moves them too. Describe the direction and period, never predict next year.

## Rules

1. **Budgets:** the price filters are yearly. A monthly budget of X means `price_max` = 12 × X.
2. **Compare like with like:** set `property_type` and `beds` whenever the user states them, and use the same segment for every side of a comparison (cities, neighborhoods).
3. **Numbers come from tools.** Don't quote typical prices from memory; call `darak:get_market_stats`.
4. **Link every listing and project you mention** with the `url` the tool returned. Never write a placeholder or invented link; if you have no `url`, don't link.
5. **Neighborhoods:** pass names (English or Arabic) in `neighborhood`. For "north/east/… Riyadh", get the areas from `darak:get_reference_data` (`type: city_directions`) first.
6. **Reply in the user's language.** Arabic questions get Arabic answers; keep numbers in Latin digits with SAR.

## Which tool

| Question                                                                 | Tool                                                                                                      |
| ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| Typical rent/price, range, share under a budget, supply, stale inventory | `darak:get_market_stats` (`view`: summary, price_distribution, area_distribution, supply, days_on_market) |
| Find specific properties                                                 | `darak:search_listings` (then `darak:get_listings` for detail)                                            |
| How many                                                                 | `darak:get_listings_count`                                                                                |
| Is this price fair?                                                      | `darak:check_listing_price`, then `darak:get_comparable_listings` and `darak:get_price_history`           |
| Cheapest / most expensive neighborhoods                                  | `darak:get_neighborhood_rent_map`                                                                         |
| Neighborhood A vs B                                                      | `darak:compare_neighborhoods`                                                                             |
| Rising or falling?                                                       | `darak:get_neighborhood_trends`                                                                           |
| Buy-to-let returns                                                       | `darak:get_rental_yield`                                                                                  |
| Underpriced listings                                                     | `darak:get_best_value_listings`                                                                           |
| New developments, units, developers                                      | `darak:search_projects`, `darak:get_project_units`, `darak:search_project_units`, `darak:list_developers` |

## Answer shape

- Lead with the answer (a median, a verdict, a shortlist), then the evidence: sample size, range, the segment it covers. Flag any figure based on few listings (under about 20) as less reliable.
- For "is this a good deal?" give a clear verdict from the percentile or median difference, name what it was compared against, and add one line of caveat (asking prices; comparables vary).
- For negotiation, base leverage on evidence: price vs comparables, time on the market, earlier price cuts. Don't promise a discount.
- For investment, give gross yield by neighborhood and say it excludes vacancy, fees and costs. Not financial advice.
- When results are thin, say so and suggest one concrete relaxation (budget, bedrooms, nearby neighborhood).
