---
title : "ElastiCache Redis"
date : 2026-07-12
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Data Flow

```
Application ──► Redis HIT  ──► return immediately, no RDS hit
            └──► Redis MISS ──► RDS ──► write back to Redis (TTL)
```

The system caches three kinds of data: `productDetail` (TTL 90 seconds — it contains stock quantity, so a short TTL), `categories` and `brands` (TTL 10 minutes). Any admin operation to create, update, or delete products, categories, or brands evicts the matching cache immediately, without waiting for the TTL to expire.

#### 1. Inspect the Redis cluster

To ensure the cache layer was running properly:
1. On the **ElastiCache console**, navigated to **Redis caches**.
2. Selected `fashion-shop-prod-redis` and confirmed the **Status** was `available` and the **Node type** was `cache.t3.micro`.
3. Noted the **Primary endpoint**, used for the test below.

![Endpoint](/images/5-Workshop/5.4-ElastiCache-Redis/redis_info.png)

#### 2. Inspect the Security Group

To check internal security configurations:
1. From the **Security groups** tab on the cluster page, opened the SG `fashion-shop-prod-redis-sg`.
2. In the **Inbound rules** tab, confirmed only 1 rule for port `6379`, with the source set strictly to the application's Security Group (not open to `0.0.0.0/0`).

![Redis SG](/images/5-Workshop/5.4-ElastiCache-Redis/sg.png)

#### 3. Test the cache — inspect real keys

Since Redis lives in a private subnet and can't be reached from the internet, access to run `redis-cli` from inside the network went through **Session Manager** on one of the application instances (a browser-based shell requiring no SSH keys or open ports).

1. First, browsed the storefront and opened a few products so the application would write data to the cache.
2. Opened **Systems Manager** → **Session Manager** → **Start session**, and picked one of the `fashion-shop-prod-app` instances.
3. In the browser shell, ran a transient docker container to use `redis-cli` against the primary endpoint:

```bash
docker run --rm redis:7-alpine redis-cli \
  -h fashion-shop-prod-redis.voe1xs.ng.0001.apse1.cache.amazonaws.com \
  --scan --pattern 'fashion-shop:*'
```

The shell output successfully listed the expected keys, such as `fashion-shop:productDetail::...` and `fashion-shop:categories::...`.

![redis_key](/images/5-Workshop/5.4-ElastiCache-Redis/redis_key.png)

#### 4. Check the cache-hit metrics in CloudWatch

Finally, to verify cache efficiency:
1. From the **ElastiCache console**, selected the cluster and opened the **Metrics** tab.
2. Monitored the **CacheHits** and **CacheMisses** graphs. Refreshing the storefront a few times showed the CacheHits metric climbing accordingly.

![hit](/images/5-Workshop/5.4-ElastiCache-Redis/hit.png)
