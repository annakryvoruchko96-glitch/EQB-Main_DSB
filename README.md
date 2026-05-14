# Boosters Database - Query Reference Guide
# Last Updated: May 2026

==============================================================
## 1. DATABASE STRUCTURE OVERVIEW
==============================================================

There are 3 main databases:
- boosters        → Main database (148 GiB) — all TikTok, Amazon, Facebook, Naver ad data + TTS data
- boosters_erp    → ERP/inventory data [not using]
- boosters_mart   → Mart/reporting layer [not using]

==============================================================
## 2. KEY TABLES & SCHEMAS
==============================================================

--------------------------------------------------------------
### [TIKTOK ADS]
--------------------------------------------------------------

TABLE: tiktok_campaign_infos (128 KiB)
  - id                    INT (PK, AUTO_INCREMENT)
  - created_at            DATETIME
  - updated_at            DATETIME
  - shop_id               INT           ← links to shop (e.g. 이콜베리 = shop_id 38)
  - advertiser_id         INT
  - advertiser_api_id     VARCHAR(45)
  - campaign_api_id       VARCHAR(45)   ← TikTok campaign ID (use this for joins!)
  - campaign_created_at   DATETIME
  - campaign_updated_at   VARCHAR(45)
  - campaign_name         VARCHAR(200)
  - campaign_type         VARCHAR(45)
  - objective             VARCHAR(45)
  - objective_type        VARCHAR(45)
  - operation_status      VARCHAR(45)   ← e.g. ENABLE, CAMPAIGN_STATUS_DISABLE
  - secondary_status      VARCHAR(45)   ← e.g. CAMPAIGN_STATUS_ENABLE, CAMPAIGN_STATUS_PRODUCT_USED_BY_PRODUCT_G...
  - budget_mode           VARCHAR(45)
  - budget                FLOAT
  - rending_channel       VARCHAR(50)
  - json_data             TEXT
  - gmv_store_id          VARCHAR(50)
  - gmv_schedule_start_time  DATETIME
  - gmv_schedule_end_time    DATETIME
  - gmv_schedule_type        VARCHAR(100)
  - gmv_shopping_ads_type    VARCHAR(100)
  - gmv_json_data            TEXT

NOTE: operation_status values:
  ENABLE = active
  CAMPAIGN_STATUS_DISABLE = disabled/paused

--------------------------------------------------------------

TABLE: tiktok_adgroup_infos (1.5 MiB)
  - id                    INT
  - created_at / updated_at DATETIME
  - shop_id               INT
  - advertiser_id         INT
  - campaign_id           INT
  - advertiser_api_id     VARCHAR
  - campaign_api_id       VARCHAR   ← join key to campaigns
  - adgroup_api_id        VARCHAR   ← join key to ads
  - adgroup_created_at    DATETIME
  - adgroup_updated_at    DATETIME
  - campaign_name         VARCHAR
  - adgroup_name          VARCHAR
  - schedule_start_time   DATETIME
  - schedule_end_time     DATETIME
  - schedule_type         VARCHAR
  - scheduled_budget      FLOAT
  - operation_status      VARCHAR
  - secondary_status      VARCHAR
  - placement_type        VARCHAR
  - optimization_goal     VARCHAR
  - bid_type              VARCHAR
  - bid_display_mode      VARCHAR
  - bid_price             FLOAT
  - conversion_bid_price  FLOAT
  - deep_cpa_bid          FLOAT
  - promotion_type        VARCHAR
  - budget                FLOAT
  - budget_mode           VARCHAR
  - pacing                VARCHAR
  - billing_event         VARCHAR
  - json_data             TEXT

--------------------------------------------------------------

TABLE: tiktok_ad_infos (1.6 MiB)
  - id                    INT
  - created_at / updated_at DATETIME
  - shop_id               INT
  - advertiser_id         INT
  - campaign_id           INT
  - adgroup_id            INT
  - advertiser_api_id     VARCHAR
  - campaign_api_id       VARCHAR
  - adgroup_api_id        VARCHAR   ← join key to adgroups
  - ad_api_id             VARCHAR   ← TikTok ad ID
  - ad_created_at         DATETIME
  - ad_updated_at         DATETIME
  - campaign_name         VARCHAR
  - adgroup_name          VARCHAR
  - ad_name               VARCHAR
  - identity_id           VARCHAR   ← creator identity
  - identity_type         VARCHAR
  - landing_page_url      TEXT
  - tiktok_item_id        VARCHAR   ← VIDEO ID used in the ad (join to tts_video_stats)
  - app_name              VARCHAR
  - display_name          VARCHAR
  - operation_status      VARCHAR
  - secondary_status      VARCHAR
  - ad_text               TEXT
  - ad_format             VARCHAR
  - json_data             TEXT

⚠️ WARNING: tiktok_ad_infos may not be synced for all campaigns.
   Always check COUNT(*) for your campaign before relying on this table.

--------------------------------------------------------------

TABLE: tiktok_campaign_reports (4.9 MiB)
  - id                    INT
  - created_at / updated_at DATETIME
  - shop_id               INT
  - advertiser_api_id     VARCHAR(25)
  - search_date           DATE         ← date of the report data
  - campaign_id           INT
  - campaign_api_id       VARCHAR(45)  ← join key
  - spend                 FLOAT
  - impressions           FLOAT
  - billed_cost           FLOAT
  - cash_spend            FLOAT
  - voucher_spend         FLOAT
  - cpc                   FLOAT
  - cpm                   FLOAT
  - clicks                FLOAT
  - ctr                   FLOAT
  - reach                 FLOAT
  - cost_per_1000_reached FLOAT
  - frequency             FLOAT
  - conversion            FLOAT
  - cost_per_conversion   FLOAT
  - conversion_rate       FLOAT
  - conversion_rate_v2    FLOAT
  - result                FLOAT
  - cost_per_result       FLOAT
  - result_rate           FLOAT

--------------------------------------------------------------

TABLE: tiktok_adgroup_reports (3.6 MiB)
  - Similar structure to campaign_reports but at adgroup level
  - campaign_api_id, adgroup_api_id as join keys

--------------------------------------------------------------

TABLE: tiktok_ad_reports (7.0 MiB)
  - Similar structure but at ad level
  - campaign_api_id, adgroup_api_id, ad_api_id as join keys

--------------------------------------------------------------

TABLE: tt_prd_gmv_campaign_lv (288 KiB) ← "TikTok GMV Max 캠페인 레벨 성과"
  - id                    BIGINT
  - created_at / updated_at DATETIME
  - advertiser_id         VARCHAR
  - campaign_id           VARCHAR      ← campaign identifier
  - stat_time_day         DATETIME     ← date dimension
  - campaign_name         VARCHAR
  - operation_status      VARCHAR
  - bid_type              VARCHAR
  - schedule_type         VARCHAR
  - schedule_start_time   DATETIME
  - schedule_end_time     DATETIME
  - target_roi_budget     DECIMAL
  - max_delivery_budget   DECIMAL
  - roas_bid              DECIMAL
  - cost                  DECIMAL
  - net_cost              DECIMAL
  - gross_revenue         DECIMAL
  - orders                INT
  - cost_per_order        DECIMAL
  - roi                   DECIMAL

--------------------------------------------------------------

TABLE: tt_prd_gmv_creative_lv (1.9 MiB)
  - Creative level GMV Max performance data

--------------------------------------------------------------

TABLE: tt_prd_gmv_product_lv (32 KiB)
  - Product level GMV Max performance data

--------------------------------------------------------------

TABLE: tt_all_gmv_advertiser_lv (32 KiB)
  - Advertiser level GMV aggregated data

--------------------------------------------------------------
### [TIKTOK SHOP (TTS)]
--------------------------------------------------------------

TABLE: tts_video_stats (1.6 MiB)
  - id                    BIGINT
  - created_at / updated_at DATETIME
  - video_id              VARCHAR      ← TikTok video ID (18-19 digit number)
  - title                 TEXT
  - username              VARCHAR      ← creator's TikTok handle
  - video_post_time       DATETIME
  - duration              INT
  - hash_tags             JSON
  - gmv_amount            DECIMAL      ← GMV from TikTok Shop for this video
  - gmv_currency          CHAR
  - gpm_amount            DECIMAL
  - gpm_currency          CHAR
  - avg_customers         INT
  - sku_orders            INT
  - items_sold            INT
  - views                 INT
  - click_through_rate    VARCHAR
  - products              JSON         ← [{id: "product_id", name: "product_name"}, ...]
  - latest_available_date DATE

⚠️ NOTE: No shop_id column — table appears to be scoped to one brand per deployment.
   Use products JSON to filter by product:
   JSON_UNQUOTE(JSON_EXTRACT(products, '$[0].id')) = 'YOUR_PRODUCT_ID'

--------------------------------------------------------------

TABLE: tts_video_daily_stats (17.0 MiB)
  - id                    BIGINT
  - created_at / updated_at DATETIME
  - video_id              VARCHAR
  - start_date            VARCHAR
  - end_date              VARCHAR
  - gmv_amount            DECIMAL
  - gmv_currency          VARCHAR
  - gpm_amount            DECIMAL
  - gpm_currency          VARCHAR
  - customers             INT
  - items_sold            INT
  - ctr                   DECIMAL
  - product_impressions   BIGINT
  - product_clicks        BIGINT
  - views                 BIGINT
  - new_followers         INT
  - shares                INT
  - comments              INT
  - likes                 INT
  - sales_breakdowns      JSON
  - viewer_profile        JSON
  - latest_available_date VARCHAR

--------------------------------------------------------------

TABLE: tts_products (80 KiB)
  - id                    BIGINT
  - created_at / updated_at DATETIME
  - product_id            VARCHAR(32)   ← TTS product ID
  - sku_id                VARCHAR(32)
  - title                 VARCHAR(500)
  - product_status        VARCHAR(32)
  - has_draft             TINYINT
  - is_not_for_sale       TINYINT
  - listing_quality_tier  VARCHAR(16)
  - sales_regions         JSON
  - recommended_categories JSON
  - audit_status          VARCHAR(32)
  - audit_pre_approved_reasons JSON
  - product_create_time   DATETIME
  - product_update_time   DATETIME
  - seller_sku            VARCHAR(255)
  - sku_status            VARCHAR(32)
  - price_currency        CHAR(3)
  - price_tax_exclusive   DECIMAL(18,4)
  - list_price_amount     DECIMAL(18,4)
  - list_price_currency   CHAR(3)
  - external_list_prices  JSON
  - inventory             JSON

--------------------------------------------------------------

TABLE: tts_shop_stats (32 KiB)
  - Shop-level TTS statistics

--------------------------------------------------------------

TABLE: tts_inventory (32 KiB)
  - Inventory data for TikTok Shop products

--------------------------------------------------------------
### [TIKTOK SHOP ORDERS]
--------------------------------------------------------------

TABLE: tiktok_shop_order_info_details (54.1 MiB)
  - id                    INT
  - created_at / updated_at DATETIME
  - order_info_id         INT
  - order_api_id          VARCHAR(25)
  - currency              VARCHAR(5)
  - display_status        VARCHAR(25)
  - order_line_item_id    VARCHAR(25)  ← FK (red key)
  - is_dangerous_good     TINYINT
  - is_gift               TINYINT
  - item_tax              FLOAT
  - original_price        FLOAT
  - platform_discount     FLOAT
  - product_id            VARCHAR(25)  ← product identifier
  - product_name          VARCHAR(300)
  - sale_price            FLOAT
  - seller_discount       FLOAT
  - seller_sku            VARCHAR(25)
  - sku_id                VARCHAR(25)
  - sku_name              VARCHAR(25)
  - sku_image             VARCHAR(150)
  - sku_type              VARCHAR(25)
  - tracking_number       VARCHAR(25)
  - json_data             TEXT

--------------------------------------------------------------

TABLE: tiktok_shop_order_infos (135 MiB) ← largest order table
  - Main order header table (join with order_info_details via order_info_id)

--------------------------------------------------------------

TABLE: tiktok_shop_order_statement_transactions (20.3 MiB)
  - Financial settlement transactions

--------------------------------------------------------------

TABLE: tiktok_shop_order_statement_transaction_details (1.7 MiB)
  - Detailed breakdown of settlement transactions

--------------------------------------------------------------
### [OTHER AD PLATFORMS]
--------------------------------------------------------------

TABLE: amazon_ad_report_campaign_infos
TABLE: amazon_ad_report_campaign_placement_infos
TABLE: facebook_data_campaigns
TABLE: facebook_id_campaign_dailys
TABLE: facebook_id_campaigns
TABLE: naver_id_campaigns
TABLE: ohouse_campaign_info_dailys
TABLE: ohouse_campaign_infos
TABLE: pyler_moment_campaign_reports
TABLE: retail_b2b_coupang_campaigns
TABLE: ad_tiktok_infos
TABLE: ad_seeding_global_infos  ← has: sheet_id, post_id, date, seeding_num, shop_id, brand_name, channel_name, channel, country, cost, currency, json_data
TABLE: tiktok_advertiser_infos (16 MiB)
TABLE: tiktok_cpc_free_apply_daily_costs (224 KiB)
TABLE: tiktok_shop_acrossbe_rate (16 KiB)

==============================================================
## 3. KEY RELATIONSHIPS & JOIN PATTERNS
==============================================================

### Campaign → Adgroup → Ad (hierarchy):
  tiktok_campaign_infos
    └── campaign_api_id
          └── tiktok_adgroup_infos (campaign_api_id)
                └── adgroup_api_id
                      └── tiktok_ad_infos (adgroup_api_id)
                            └── tiktok_item_id → tts_video_stats (video_id)

### Campaign Performance:
  tiktok_campaign_infos → campaign_api_id → tiktok_campaign_reports
  tiktok_campaign_infos → campaign_api_id → tt_prd_gmv_campaign_lv (campaign_id)

### Video → Creator → GMV:
  tts_video_stats: video_id + username + gmv_amount + products JSON

### Product filtering in tts_video_stats:
  WHERE JSON_UNQUOTE(JSON_EXTRACT(products, '$[0].id')) = 'PRODUCT_ID'

### Orders:
  tiktok_shop_order_infos → order_info_id → tiktok_shop_order_info_details

==============================================================
## 4. KNOWN GOTCHAS & WARNINGS
==============================================================

1. tiktok_ad_infos is often NOT synced for all campaigns.
   Always run: SELECT COUNT(*) FROM tiktok_ad_infos WHERE campaign_api_id = 'X'
   before building queries that depend on it.

2. campaign_api_id vs campaign_id:
   - campaign_api_id = TikTok's external API string ID (VARCHAR) → use this for joins
   - campaign_id = internal integer ID → less reliable for cross-table joins

3. tts_video_stats has NO shop_id column.
   Filter by product ID using JSON_EXTRACT on the products column.

4. operation_status in tiktok_campaign_infos:
   - Active campaigns: operation_status = 'ENABLE'
   - Disabled: operation_status = 'CAMPAIGN_STATUS_DISABLE'
   - Use: WHERE operation_status != 'CAMPAIGN_STATUS_DISABLE' for active only

5. raw_videos table does NOT exist in boosters database.
   Video/creator data lives in tts_video_stats (username column).

6. Product IDs look like campaign IDs (18-19 digit numbers).
   e.g. 1732315297481462699 is a PRODUCT ID, not a campaign ID.
   Always verify which table a long numeric ID belongs to.

7. tts_video_stats.products is a JSON array — always use $[0] for first product.
   If a video has multiple products, use JSON_TABLE or multiple extracts.

8. Shop IDs:
   - 이콜베리 (EQQUALBERRY) = shop_id 38

9. Database is MySQL — use MySQL syntax:
   - JSON_EXTRACT(), JSON_UNQUOTE()
   - CONCAT() for building URLs
   - No ISNULL() — use IS NULL or IFNULL()

==============================================================
## 5. USEFUL QUERY TEMPLATES
==============================================================

### Find active campaigns for a shop:
SELECT campaign_api_id, campaign_name, operation_status, secondary_status,
       gmv_schedule_start_time, gmv_schedule_end_time
FROM tiktok_campaign_infos
WHERE shop_id = 38
  AND operation_status != 'CAMPAIGN_STATUS_DISABLE'
ORDER BY gmv_schedule_start_time DESC;

--------------------------------------------------------------

### Get all videos + creator + GMV for a product:
SELECT
    tvs.username AS creator_name,
    tvs.video_id,
    CONCAT('https://www.tiktok.com/@', tvs.username, '/video/', tvs.video_id) AS video_link,
    tvs.title AS video_title,
    JSON_UNQUOTE(JSON_EXTRACT(tvs.products, '$[0].id')) AS product_id,
    JSON_UNQUOTE(JSON_EXTRACT(tvs.products, '$[0].name')) AS product_name,
    tvs.gmv_amount AS tts_gmv,
    tvs.gmv_currency,
    tvs.items_sold,
    tvs.views,
    tvs.video_post_time,
    tvs.latest_available_date
FROM tts_video_stats tvs
WHERE JSON_UNQUOTE(JSON_EXTRACT(tvs.products, '$[0].id')) = 'PRODUCT_ID_HERE'
ORDER BY tvs.gmv_amount DESC;

--------------------------------------------------------------

### Get videos + GMV + campaign status:
SELECT
    tvs.username AS creator_name,
    tvs.video_id,
    CONCAT('https://www.tiktok.com/@', tvs.username, '/video/', tvs.video_id) AS video_link,
    tvs.title AS video_title,
    JSON_UNQUOTE(JSON_EXTRACT(tvs.products, '$[0].id')) AS product_id,
    JSON_UNQUOTE(JSON_EXTRACT(tvs.products, '$[0].name')) AS product_name,
    tvs.gmv_amount AS tts_gmv,
    tvs.gmv_currency,
    tvs.items_sold,
    tvs.views,
    tci.operation_status AS campaign_status,
    tci.secondary_status AS campaign_secondary_status
FROM tts_video_stats tvs
LEFT JOIN tiktok_campaign_infos tci
    ON tci.campaign_api_id = 'CAMPAIGN_API_ID_HERE'
WHERE JSON_UNQUOTE(JSON_EXTRACT(tvs.products, '$[0].id')) = 'PRODUCT_ID_HERE'
ORDER BY tvs.gmv_amount DESC;

--------------------------------------------------------------

### Check if campaign data exists across tables:
SELECT 'tiktok_campaign_infos' AS source, campaign_api_id
FROM tiktok_campaign_infos WHERE campaign_api_id = 'YOUR_ID'
UNION ALL
SELECT 'tiktok_adgroup_infos', campaign_api_id
FROM tiktok_adgroup_infos WHERE campaign_api_id = 'YOUR_ID'
UNION ALL
SELECT 'tiktok_ad_infos', campaign_api_id
FROM tiktok_ad_infos WHERE campaign_api_id = 'YOUR_ID'
UNION ALL
SELECT 'tiktok_campaign_reports', campaign_api_id
FROM tiktok_campaign_reports WHERE campaign_api_id = 'YOUR_ID';

--------------------------------------------------------------

### Find what tables exist with a keyword:
SELECT table_schema, table_name
FROM information_schema.tables
WHERE table_name LIKE '%video%'
   OR table_name LIKE '%creator%'
   OR table_name LIKE '%ad%';

--------------------------------------------------------------

### Check columns of any table:
SELECT column_name, data_type
FROM information_schema.columns
WHERE table_schema = 'boosters'
  AND table_name = 'YOUR_TABLE_NAME'
ORDER BY ordinal_position;

==============================================================
## 6. KNOWN PRODUCTS (이콜베리 / EQQUALBERRY)
==============================================================

Product: EQQUALBERRY Vitamin Illuminating Duo
Product ID: 1732315297481462699
Shop ID: 38 (이콜베리 / EQQUALBERRY USA)

Active Campaign (as of May 2026):
  Campaign API ID: 1862620765999105
  Name: [이콜베리][TikTokShop(USA)] (GMV Max) V-SRM + V-CR
  Status: ENABLE

Disabled Campaign:
  Campaign API ID: 1861974625435650
  Name: [이콜베리][TikTokShop(USA)] (GMV Max) V-SRM + V-CR
  Status: DISABLE

==============================================================
## 7. GOOGLE SHEETS FORMULAS
==============================================================

### VLOOKUP from another sheet (creator name by video ID):
  Video ID in col B of current sheet, Sheet1 has col A=creator_name, col B=video_id:
  =IFERROR(INDEX(Sheet1!$A:$A, MATCH(B3, Sheet1!$B:$B, 0)), "Not found")

### Import data from another Google Sheet:
  =IMPORTRANGE("SPREADSHEET_URL", "SheetName!A1:H10000")
  ⚠️ Must click "Allow access" on first use.

### VLOOKUP with IMPORTRANGE (get Tier from affiliate sheet):
  =IFERROR(VLOOKUP(A3,
    IMPORTRANGE("https://docs.google.com/spreadsheets/d/17jx9ca25gIzTYW05Nd824SkUmoHoPRsEFLlJ0vtM7pQ","Sheet1!C:D"),
    2, FALSE), "Not in list")

==============================================================
