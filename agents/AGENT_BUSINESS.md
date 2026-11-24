# AGENT_BUSINESS.md — Business 项目级补充（默认关闭）

> 以 `agent_matrix/roles/03-business.yaml` 为准：products/categories/skus/orders/coupons/ali_api/logistics/reviews/wishlist/currency_converter，edition=official。

## 网站项目

**默认不启用。** tarka.cn 公网站点不读商品/订单/会员/库存/优惠/物流。

如未来启用：只读、脱敏、带来源/时效；不写、不跨 site_key、不把业务数据注入 site builder。必须经业务与合规审批。
