---
title : "DynamoDB — Activity Log"
date : 2026-07-12
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Data Flow

```
User views a product / searches ──► EC2 (@Async) ──► DynamoDB PutItem
Lambda order-processor           ──► DynamoDB PutItem (ORDER_CREATED)

Admin ──► GET /api/v1/admin/activity?userId=... ──► DynamoDB Query (userId + timestamp)
```

The table is designed with `hash_key = userId`, `range_key = timestamp`, and enables TTL on the `expireAt` attribute (records are automatically deleted after 90 days, eliminating the need for any manual cleanup jobs).

#### 1. Inspect the table schema

To verify the table configuration:
1. On the **DynamoDB console**, navigated to **Tables** and opened `fashion-shop-prod-activity-log`.
2. In the **Overview** tab, the Partition key was `userId` (String), the Sort key was `timestamp` (String), and the Billing mode was set to `On-demand`.
3. In the **Additional settings** tab, under the **Time to Live (TTL)** section, the attribute `expireAt` had a status of `Enabled`.

![Overview](/images/5-Workshop/5.6-DynamoDB-Activity-Log/overview.png)

#### 2. Generate test data

To generate actual activity logs, the following actions were performed on the storefront:
1. Signed in to the site, viewed 2-3 products, typed a keyword in the search bar, and pressed Enter.
2. Placed an order to trigger the Lambda function to write an `ORDER_CREATED` record.

#### 3. Inspect the records directly in the DynamoDB Console

To verify the records were written correctly:
1. On the table page, clicked **Explore table items**.
2. Located the rows with `action` = `VIEW_PRODUCT`, `SEARCH`, and `ORDER_CREATED`, all matching the `userId` column for the email used in testing.

![Explore](/images/5-Workshop/5.6-DynamoDB-Activity-Log/log.png)

#### 4. View it through the Admin page (no AWS Console needed)

The activity logs are also exposed through the application's admin interface:
1. On the site, signed in as an admin and went to **Admin → Activity log**.
2. Selected the exact email used during testing from the dropdown and clicked **View**.
3. The result table fetched and displayed the Time / Action / Detail of the test actions, sorting the newest events first.

![Admin](/images/5-Workshop/5.6-DynamoDB-Activity-Log/admin_log.png)

{{% notice note %}}
Logging is implemented as **best-effort and asynchronous** (`@Async`). If DynamoDB is temporarily unavailable, the customer's core requests (like viewing a product or searching) still succeed normally; the log write is simply skipped without bubbling an exception back to the caller.
{{% /notice %}}
