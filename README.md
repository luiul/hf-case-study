# Hellofresh Case Study

## Assumptions

- We ignore rounding errors (unit price and conversion rate are floats)

## Data Engineering

To prepare the analysis, we perform the following steps:

- Describe raw data
- Convert txt file to csv
- Engieneer new data frame and export file to csv

### Raw Columns and Description

The raw data frame contains the following columns: 

| col_name              | unique_values                        | description (preliminary)             | UOM (preliminary) |
| --------------------- | ------------------------------------ | ------------------------------------- | ----------------- |
| expected_arrival_date | 1528                                 | order ETA                             | datetime          |
| distribution_center   | ['SY', 'GR', 'VE', 'CB', 'NW', 'MP'] | procurement HUBs                      | 1                 |
| week                  | 36                                   | week number                           | 1                 |
| supplier              | 136                                  | order supplier                        | 1                 |
| sku_id                | 784                                  | product sku (not unique to suppliers) | 1                 |
| sku_name              | 772                                  | product name                          | 1                 |
| product               | 226                                  | product description                   | 1                 |
| unit_price_loc        | 417                                  | unit price in local currency          | [currency]/unit   |
| item_quantity         | 4315                                 | order quantity                        | unit/order        |
| order_type            | ['Regular', 'Emergency']             | order type                            | 1                 |
| unit_weight_value     | 92                                   | unit weight                           | [UOM]/unit        |
| unit_weight_uom       | ['g', 'gram', 'grams']               | unit weight UOM                       | 1                 |
| country               | ['AU', 'UK', 'DE']                   | country of origin                     | 1                 |
| currency              | ['AUD', 'GBP', 'EUR']                | currency symbol                       | [currency]        |
| to_eur                | [0.61, 1.17, 1.0]                    | conversion rate                       | EUR/[currency]    |
| sku_category          | ['PTN', 'PRO', 'DAI']                | product category                      | 1                 |

Note that order type is a boolean value. 

We do the following modifications to the raw data: 

- Drop `unit_weight_uom` column (it contains no new information)
- Translate rows from German suppliers to English
- Convert string date into datetime object (see [strftime cheatsheet](https://strftime.org/))

### Engineered Columns and Description

The engineered data frame contains the following columns: 

| col_name              | unique_values                        | description                              | UOM                             |
| --------------------- | ------------------------------------ | ---------------------------------------- | ------------------------------- |
| fc                    | ['SY', 'GR', 'VE', 'CB', 'NW', 'MP'] | procurement HUBs                         | -                               |
| week                  | 36                                   | week number                              | week                            |
| supplier_name         | 136                                  | name of order supplier                   | -                               |
| prod_sku              | 784                                  | SKU for unit product                     | -                               |
| prod_name             | 768                                  | name of unit product                     | -                               |
| prod_tags             | 226                                  | tags for unit product                    | -                               |
| prod_price_local      | 417                                  | price in local currency for unit product | [local_currency] / product_unit |
| order_size            | 4315                                 | size of order                            | product_units / order           |
| order_type            | ['Regular', 'Emergency']             | type of order                            | (Boolean)                       |
| prod_weight           | 92                                   | weight of unit product                   | g / product_unit                |
| supplier_country      | ['AU', 'UK', 'DE']                   | country of order supplier                | -                               |
| local_curr            | ['AUD', 'GBP', 'EUR']                | supplier's local currency                | -                               |
| local_curr_to_eur     | [0.61, 1.17, 1.0]                    | conversion rate to EUR                   | EUR / [local_curency]           |
| prod_category         | ['PTN', 'PRO', 'DAI']                | category of unit product                 | -                               |
| prod_price_eur        | 660                                  | price in EUR for unit product            | EUR / product_unit              |
| order_weight          | 6999                                 | total weight of the order                | kg / order                      |
| order_cost_eur        | 11533                                | total cost of the order                  | EUR / order                     |
| order_eur_cost_per_kg | 1633                                 | total cost per kg order                  | EUR / kg                        |

The 

The (transposed ) describe method returns: 

|                       |    mean |      std |  min |    25% |     50% |     75% |       max |
| --------------------- | ------: | -------: | ---: | -----: | ------: | ------: | --------: |
| prod_price_local      |    2.07 |     1.75 | 0.01 |   0.76 |    1.73 |    2.81 |     18.52 |
| order_size            | 7709.85 | 17083.60 | 2.00 | 320.00 | 1414.00 | 6660.00 | 275400.00 |
| prod_weight           |  324.08 |   210.54 | 8.00 | 150.00 |  300.00 |  450.00 |    1500.0 |
| local_curr_to_eur     |    1.01 |     0.23 | 0.61 |   1.00 |    1.17 |    1.17 |      1.17 |
| prod_price_eur        |    1.99 |     1.50 | 0.01 |   0.68 |    1.80 |    3.00 |     12.56 |
| order_weight          | 1294.60 |  2716.36 | 0.36 |  90.91 |  340.50 | 1344.00 |  50688.00 |
| order_cost_eur        | 6019.25 |  9674.99 | 0.19 | 573.16 | 2000.00 | 7295.29 | 133128.00 |
| order_eur_cost_per_kg |    6.89 |     5.10 | 0.06 |   4.49 |    6.10 |    7.93 |    125.90 |


Note that there is a high correlation between **product weight** and **local product price**. 

## Open Questions

