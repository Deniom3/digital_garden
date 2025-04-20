---
{"dg-publish":true,"dg-path":"Docker compose/Xray Checker.md","permalink":"/docker-compose/xray-checker/","tags":[""],"updated":"2025-04-20T22:18:03+03:00"}
---

```yaml
services:
  xray-checker:
    image: kutovoys/xray-checker
    environment:
      - SUBSCRIPTION_URL=<ссылка на подписку>
      - PROXY_CHECK_METHOD=status
      - PROXY_STATUS_CHECK_URL=http://cp.cloudflare.com/generate_204
    ports:
      - 2112:2112
networks: {}
```
