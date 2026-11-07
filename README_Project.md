# Marketplace Revenue and Delivery-Performance Pipeline

**Team:** 6\
**Platform:** Databricks (PySpark / Delta Lake)\
**Dataset:** Brazilian E-Commerce Public Dataset by Olist\
**GitHub repository:**
<https://github.com/jayesh05bablr-code/Marketplace-Revenue-Delivery-Performance-Pipeline>

## 1. Project overview

This project is designed to build a Bronze--Silver--Gold data pipeline
using the Olist Brazilian e-commerce dataset. It supports analysis of
marketplace revenue, delivery performance, seller performance and the
relationship between delivery timeliness and customer review scores.

### Business questions

-   How does revenue vary by month, product category and customer state?
-   Which customer states experience longer delivery delays?
-   How do sellers compare on revenue and delivery performance?
-   Do late-delivered orders have different average review scores from
    on-time or early-delivered orders?

## 2. Databricks workspace notebooks

The Databricks Workspace screenshot provided for this project shows
these six notebooks in `Team6_Marketplace_Revenue_Delivery_Pipeline`:

  ----------------------------------------------------------------------------------
  \#                      Notebook                           Responsibility
  ----------------------- ---------------------------------- -----------------------
  01                      `01_Bronze_Layer_Ingestion`        Read the nine source
                                                             CSV files, preserve raw
                                                             values, add
                                                             `load_timestamp` and
                                                             `source_file_name`, and
                                                             write Bronze Delta
                                                             tables.

  02                      `02_Silver_Layer_Transformation`   Clean and standardise
                                                             data, cast data types,
                                                             parse timestamps,
                                                             handle duplicates and
                                                             missing values, and
                                                             translate product
                                                             categories.

  03                      `03_Data_Modeling`                 Build an order-item
                                                             fact table and
                                                             customer, seller,
                                                             product and date
                                                             dimensions. Aggregate
                                                             reviews at order level
                                                             before joining to avoid
                                                             row multiplication.

  04                      `04_Data_Quality_Validation`       Validate row counts,
                                                             nulls, key uniqueness,
                                                             orphan references and
                                                             date consistency;
                                                             record validation
                                                             results.

  05                      `05_Gold_Layer_Analytics`          Create business-level
                                                             revenue,
                                                             delivery-performance,
                                                             seller-scorecard and
                                                             review-analysis
                                                             aggregations.

  06                      `06_Dashboard_Insights`            Query Gold tables and
                                                             prepare visual outputs
                                                             and interpretations for
                                                             dashboard reporting.
  ----------------------------------------------------------------------------------

**Important status distinction:** The six notebook names are confirmed
by the Databricks Workspace screenshot. The GitHub repository was
checked when this README was prepared; its `main` branch displayed only
a `README.md` containing the repository heading. The notebook files and
documentation must be exported from Databricks and pushed to GitHub
before they can be considered repository contents. Notebook creation
alone does not prove successful execution.

## 3. Recommended GitHub repository structure

After exporting the notebooks and adding the documentation, organise the
repository as follows:

``` text
Marketplace-Revenue-Delivery-Performance-Pipeline/
├── README.md
├── notebooks/
│   ├── 01_Bronze_Layer_Ingestion.ipynb
│   ├── 02_Silver_Layer_Transformation.ipynb
│   ├── 03_Data_Modeling.ipynb
│   ├── 04_Data_Quality_Validation.ipynb
│   ├── 05_Gold_Layer_Analytics.ipynb
│   └── 06_Dashboard_Insights.ipynb
└── documentation/
    ├── Agile_Approach_Marketplace_Pipeline.pdf
    └── Checkpoint3_Progress_Note.pdf
```

This is the target repository structure, not a statement that these
files are already present.

## 4. Source data

The project uses nine Olist CSV datasets:

1.  Customers
2.  Geolocation
3.  Order items
4.  Order payments
5.  Order reviews
6.  Orders
7.  Products
8.  Sellers
9.  Product category name translation

The project plan identifies the dataset licence as CC BY-NC-SA 4.0. Cite
Olist as the source and verify the dataset page's current licence and
attribution requirements before redistributing raw or derived data. Do
not commit raw CSVs to a public repository unless the licence and course
rules permit it.

## 5. Databricks environment configuration

The notebooks have been designed around this Unity Catalog layout:

-   Catalog: `marketplace_catalog`
-   Schema: `team6_marketplace`
-   Volume: `olist_raw_data`
-   Suggested volume path:
    `/Volumes/marketplace_catalog/team6_marketplace/olist_raw_data`

Upload the nine CSV files to the volume. If your workspace does not
allow creation of the proposed catalog, use an available catalog and
update the catalog, schema and volume path consistently across the
notebooks.

Never commit passwords, personal access tokens, service-principal
secrets or cloud credentials.

## 6. Intended table outputs

The following are the intended names from the six-stage design. Confirm
the exact names in your notebook code and catalog before relying on
them.

### Bronze

-   `bronze_customers`
-   `bronze_geolocation`
-   `bronze_order_items`
-   `bronze_order_payments`
-   `bronze_order_reviews`
-   `bronze_orders`
-   `bronze_products`
-   `bronze_sellers`
-   `bronze_category_translation`

### Silver

-   `silver_customers`
-   `silver_geolocation`
-   `silver_order_items`
-   `silver_order_payments`
-   `silver_reviews`
-   `silver_orders`
-   `silver_products`
-   `silver_sellers`
-   `silver_category_translation`

### Data model

-   `dim_customer`
-   `dim_seller`
-   `dim_product`
-   `dim_date`
-   `fact_order_items`

### Quality

-   `data_quality_log` and validation outputs, subject to the actual
    implementation.

### Gold

-   `gold_monthly_category_revenue`
-   `gold_revenue_by_state`
-   `gold_delivery_by_state`
-   `gold_seller_scorecard`
-   `gold_late_delivery_review_analysis`

## 7. Recommended execution order

Run the notebooks in dependency order:

1.  `01_Bronze_Layer_Ingestion`
2.  `02_Silver_Layer_Transformation`
3.  `03_Data_Modeling`
4.  `04_Data_Quality_Validation`
5.  `05_Gold_Layer_Analytics`
6.  `06_Dashboard_Insights`

Validate each stage before running the next. If a notebook fails,
inspect the failing cell and its inputs, correct the issue, and rerun
the affected stage and its downstream dependencies.

## 8. Data model and metric definitions

The intended fact-table grain is one row per order item, identified by
`order_id` and `order_item_id`.

-   **Item revenue:** sum of `price`; keep `freight_value` separate
    unless a report explicitly defines a combined amount.
-   **Delivery delay (days):** actual customer delivery date minus
    estimated delivery date. Positive means late; negative means early;
    zero means on the estimated date.
-   **Review score:** aggregate review scores at order level before
    joining to order items when necessary, to avoid multiplying fact
    rows.
-   **Seller average delivery delay:** if order-level delivery
    information is repeated across order items, the resulting average is
    item-weighted; disclose this in interpretation.

Check these definitions against the actual expressions in the notebooks
before publishing results. A data-quality check does not automatically
enforce database foreign-key constraints.

## 9. Agile approach and team

The project is organised around iterative work packages aligned with the
six notebooks: ingestion, transformation, modeling, quality validation,
analytics and dashboard insights. The team should track task owners,
priorities, status, dependencies, sprint dates, review notes and
retrospective actions.

Team members listed in the approved project plan:

-   Jayesh Santosh Shinde
-   Yash Parmar
-   Kirti Jain
-   Jigyas Niraj Borah
-   Prishita Girdhar

See `documentation/Agile_Approach_Marketplace_Pipeline.pdf` after it has
been added to the repository. Update the document with actual task
assignments, sprint dates and review outcomes; do not treat suggested
roles or iterations as completed activities.

## 10. Checkpoint 3 submission scope

The DigiCampus Checkpoint 3 description requests a Git repository
containing notebook code for data transformation and a write-up on the
Agile approach to the Big Data project. Prioritise the implemented
Bronze and Silver transformation code and the Agile write-up for this
checkpoint. The remaining notebooks document the broader six-stage
pipeline.

Before submission: - Execute the required notebook cells and resolve
errors. - Verify that expected Delta tables are created. - Check schemas
and row counts. - Export the implemented notebooks as `.ipynb`. - Push
notebook exports and documentation to this repository. - Ensure this
README accurately reflects the files actually committed.

## 11. Project status

-   Databricks Workspace: six notebooks are visible in the supplied
    screenshot.
-   GitHub: repository URL is established; the repository listing at the
    time of this update showed only the initial README file.
-   Notebook execution: not independently verified by the Workspace
    listing or repository page.
-   Dashboard and workflow: include links and completion status only
    after they have been saved and tested.

**Repository:**
<https://github.com/jayesh05bablr-code/Marketplace-Revenue-Delivery-Performance-Pipeline>
