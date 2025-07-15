# dbt-rutorial

# usage
## モデルの生成
model配下にsqlを配置する。以下記述サンプル
```
with customers as (

    select
        id as customer_id,
        first_name,
        last_name

    from `dbt-tutorial`.jaffle_shop.customers

),

orders as (

    select
        id as order_id,
        user_id as customer_id,
        order_date,
        status

    from `dbt-tutorial`.jaffle_shop.orders

),

customer_orders as (

    select
        customer_id,

        min(order_date) as first_order_date,
        max(order_date) as most_recent_order_date,
        count(order_id) as number_of_orders

    from orders

    group by 1

),

final as (

    select
        customers.customer_id,
        customers.first_name,
        customers.last_name,
        customer_orders.first_order_date,
        customer_orders.most_recent_order_date,
        coalesce(customer_orders.number_of_orders, 0) as number_of_orders

    from customers

    left join customer_orders using (customer_id)

)

select * from final
```

```
dbt run
```
ビューをテーブルにしたり、テーブルをビューにしたい場合
```
dbt run --full-refresh
```




## モデルのテスト
`models/schema.yml`に記述することでテストできる
```
version: 2

models:
  - name: customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null

  - name: stg_customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null
```
### テストを実行する
```
dbt test
```

## モデルのドキュメント
`models/schema.yml`に記述することでドキュメント作成できる
```
version: 2

models:
  - name: customers
    description: One record per customer
    columns:
      - name: customer_id
        description: Primary key
        tests:
          - unique
          - not_null
      - name: first_order_date
        description: NULL when a customer has not yet placed an order.

  - name: stg_customers
    description: This model cleans up customer data
    columns:
      - name: customer_id
        description: Primary key
        tests:
          - unique
          - not_null
```

### ドキュメントを生成する
```
dbt docs generate
```


### ドキュメントを閲覧する
```
dbt docs serve
```


# links
https://github.com/dbt-labs/jaffle-shop-classic/tree/main?tab=readme-ov-file

