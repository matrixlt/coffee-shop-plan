# 讨论记录

## 数据库设计

### 实体（entity）

- 用户（user）
- 送货地址（address）
- 产品分类（category）
- 产品（product）
- 配料分类（ingredient_category）
- 配料(ingredient)
- 门店（store）
- 订单（order）
- 外送员（delivery）
- 当季新品（season_special）

### 关系

- 用户与送货地址之间是 1:n 的关系，update: CASCADE；delete: CASCADE
- 产品分类与产品之间是 1:n 的关系，update: CASCADE；delete: SET NULL
- 当季新品与产品之间是 1:n 的关系，update：CASCADE；delete：CASCADE
- 配料分类与配料之间是 1:n 的关系，update：CASCADE；delete：CASCADE
- 产品与配料之间是 n:n 的关系
- 送货地址与订单之间是 1:n 的关系
- 产品与订单之间是 n:n 的关系
- 门店与订单之间是 1:n 的关系
- 外送员与订单之间是 1:n 的关系

## 使用逻辑

用户打开网页
-> 看到主界面（登陆、menu）
-> 选择商品
-> 选择自定义项（杯型、甜度、热度、价格）
-> 继续购物/查看购物车
-> 下单（选择地址/新建地址），自动分配门店
-> 支付方式：可选只有到付
-> 支付成功（等待派送，确认收货选项）
-> 门店查看未完成订单（是否完成选项）
-> 增加上新功能，包括配料，奶茶，季节新品（单独表）（数据库LOB存图片）

外键删除的设置
