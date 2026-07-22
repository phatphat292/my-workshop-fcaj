---
title : "CloudWatch + SNS — Monitoring"
date : 2026-07-12
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Data Flow

```
EC2/ALB/RDS/SQS metrics ──► CloudWatch Alarm ──► SNS Topic ──► Email
Application container logs ──► CloudWatch Logs (log group /fashion-shop/prod/backend)
```

Five alarms are configured for the system: EC2 CPU (2 alarms, 1 per instance) > 80%, ALB 5xx > 10/minute, RDS connections > 80, and SQS DLQ has messages.

#### 1. Watch the application logs

To verify real-time logging:
1. On the **CloudWatch console**, navigated to **Logs** → **Log groups** and selected `/fashion-shop/prod/backend`.
2. Clicked **Live Tail**, selected the log group, and started streaming.
3. Opening the storefront and clicking around, new log lines appeared in real time.

![Log](/images/5-Workshop/5.7-CloudWatch-SNS/live.png)

#### 2. Check the SNS Topic + subscription status

To ensure alert emails would be delivered:
1. On the **SNS console**, went to **Topics** and opened `fashion-shop-prod-alerts`.
2. In the **Subscriptions** tab, the status was successfully `Confirmed` (rather than `Pending confirmation`).

![Topic](/images/5-Workshop/5.7-CloudWatch-SNS/confirm.png)

#### 3. Check the 5 alarms

1. On the **CloudWatch console**, opened **Alarms** → **All alarms**.
2. Filtered the list by names starting with `fashion-shop-prod` — all 5 alarms were green **OK**.

![Alarm](/images/5-Workshop/5.7-CloudWatch-SNS/alarm.png)

#### 4. Real test — trigger a fake incident to receive an alert email

To verify that the alerting mechanism works, an alarm was deliberately triggered in a safe manner by dropping a message into the dead-letter queue. This caused the `order-dlq-has-messages` alarm to flip from `OK` to `In alarm` and send an email:

1. Opened the **SQS console** and navigated to the queue `fashion-shop-prod-order-dlq`.
2. Clicked **Send and receive messages**, typed a sample payload (e.g., `{"test":"trigger-alarm"}`) into the **Message body**, and clicked **Send message**.
3. Since CloudWatch evaluates metrics every 5 minutes, after waiting about 5-10 minutes the alert inbox showed an email titled `ALARM: "fashion-shop-prod-order-dlq-has-messages"`, and the alarm state in the console had turned red **In alarm**.

![Trigger alarm](/images/5-Workshop/5.7-CloudWatch-SNS/inalarm.png)

**Clean up after testing:** 
Back on the DLQ page, **Actions → Purge** emptied the queue. After the next ~5-minute cycle, the alarm returned to green **OK**.

![Recovery](/images/5-Workshop/5.7-CloudWatch-SNS/outalarm.png)
