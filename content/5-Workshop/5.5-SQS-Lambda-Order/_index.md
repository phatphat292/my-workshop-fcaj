---
title : "SQS + Lambda — order processing"
date : 2026-07-12
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Data Flow

```
Customer places order ──► EC2 saves order to RDS ──► publishes message to SQS ──► responds to customer right away
                                                              │
                                                              ▼ (auto-triggered)
                                                       Lambda order-processor
                                                              │
                                                              ├──► SES: sends confirmation email
                                                              └──► DynamoDB: writes ORDER_CREATED log

3 consecutive failures ──► message lands in the Dead Letter Queue (DLQ)
```

#### 1. Inspect the main SQS queue

To ensure asynchronous order processing was robust, the queue configurations were checked:
1. On the **SQS console**, opened the queue `fashion-shop-prod-order-created`.
2. In the **Dead-letter queue** tab, confirmed it was configured to point to `fashion-shop-prod-order-dlq` with `Maximum receives = 3`.

![Queue](/images/5-Workshop/5.5-SQS-Lambda-Order/queue.png)

#### 2. Inspect the Lambda function

1. On the **Lambda console**, opened the function `fashion-shop-prod-order-processor`.
2. In the **Configuration → Triggers** tab, the trigger was the `order-created` SQS queue, with a batch size of 10.
3. In the **Configuration → Environment variables** tab, `SES_SENDER`, `SHOP_NAME`, and `ACTIVITY_LOG_TABLE` were properly set.

![Trigger](/images/5-Workshop/5.5-SQS-Lambda-Order/trigger.png)
![Environment](/images/5-Workshop/5.5-SQS-Lambda-Order/envi.png)

#### 3. Confirm the sender email is verified in SES

1. On the **SES console** (region `ap-southeast-1`), navigated to **Identities**.
2. The email address configured in `ses_sender_email` had the status **Verified**.

![SES](/images/5-Workshop/5.5-SQS-Lambda-Order/ses.png)

#### 4. Real test — place an order

To validate the end-to-end flow:
1. Signed in with an account whose email matched `ses_sender_email` (since SES is still in sandbox mode, **both sender and recipient must be verified**).
2. Added a product to the cart, proceeded to **Checkout**, and placed the order.
3. Within 10-30 seconds, the inbox showed an "Order confirmation ODxxxxx" email.

![Success](/images/5-Workshop/5.5-SQS-Lambda-Order/success.png)

#### 5. Cross-check the Lambda logs

1. On the **Lambda console** → selected the function → opened the **Monitor** tab → clicked **View CloudWatch logs**.
2. The latest log stream showed the lines `Processing order ODxxxxx` and `Email sent for order ODxxxxx`, confirming that the Lambda executed correctly.

![Log](/images/5-Workshop/5.5-SQS-Lambda-Order/log.png)

{{% notice tip %}}
To test the failure/DLQ path without placing a real order, a fake message was pushed into the DLQ as documented in the [CloudWatch + SNS](../5.7-CloudWatch-SNS/) chapter — the same trick used to test the `order-dlq-has-messages` alarm.
{{% /notice %}}
