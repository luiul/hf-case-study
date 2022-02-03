# Hellofresh Case Study

## Data Engineering

To prepare the analysis, we perform the following steps:

- Perform exploratory analysis on raw data
- Convert TXT file to CSV
- Engineer new data frame and export file to CSV

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

Notes on the raw data: 

- The order type can be described as a boolean value
- German values are translated into English
- We omit columns without useful information, e.g. the column "unit_weight_uom" or "expected_arrival_date"
- Products are categorized into dairy products, processed products or proteins
- The first tag in the "product" column seems to be the most important

### Engineered Columns and Description

The engineered data frame contains the following columns: 

| col_name             | unique_values                        |
| -------------------- | ------------------------------------ |
| country              | ['UK', 'DE', 'AU']                   |
| distribution_center  | ['GR', 'VE', 'CB', 'SY', 'MP', 'NW'] |
| order_price_eur      | 11533                                |
| order_size           | 4315                                 |
| order_type           | ['Regular', 'Emergency']             |
| order_weight_kg      | 6999                                 |
| sku_category         | ['PTN', 'DAI', 'PRO']                |
| sku_id               | 784                                  |
| sku_name             | 768                                  |
| sku_price_eur        | 660                                  |
| sku_price_eur_per_kg | 881                                  |
| sku_tags             | 226                                  |
| sku_weight_g         | 92                                   |
| supplier_name        | 136                                  |
| week                 | 36                                   |
| sku_count            | 91                                   |
| sku_count_rank       | 784                                  |
| new_supplier         | [1, 0]                               |
| sku_main_tag         | 74                                   |

The (transposed) describe method returns: 

|                      | count | mean        | std         | min      | 25%     | 50%    | 75%        | max     |
|----------------------|-------|-------------|-------------|----------|---------|--------|------------|---------|
| order_price_eur      | 13526 | 6019.246106 | 9674.990489 | 0.1872   | 573.156 | 2000   | 7295.28585 | 133128  |
| order_size           | 13526 | 7709.846518 | 17083.60225 | 2        | 320     | 1414   | 6660       | 275400  |
| order_weight_kg      | 13526 | 1294.599583 | 2716.356409 | 0.36     | 90.9125 | 340.5  | 1344       | 50688   |
| sku_price_eur        | 13526 | 1.987639    | 1.502323    | 0.0061   | 0.6786  | 1.8018 | 2.9952     | 12.56   |
| sku_price_eur_per_kg | 13526 | 6.892391    | 5.102869    | 0.055556 | 4.4928  | 6.1    | 7.93       | 125.904 |
| sku_weight_g         | 13526 | 324.082803  | 210.536455  | 8        | 150     | 300    | 450        | 1500    |
| sku_count            | 13526 | 60.081177   | 58.896358   | 1        | 16      | 38     | 85         | 238     |
| sku_count_rank       | 13526 | 151.431096  | 164.43608   | 1        | 26      | 86     | 229        | 784     |
| new_supplier         | 13526 | 0.010055    | 0.099771    | 0        | 0       | 0      | 0          | 1       |

Note that there is a high correlation between **product weight** and **local product price**. 

## Analysis and Data Visualization

The data analysis and visualization is performed in a jupyter notebook and Tableau. 
