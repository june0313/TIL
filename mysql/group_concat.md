# MySQL - group_concat

## group_concat 함수
MySQL에서 group_concat 함수를 사용하면 `GROUB BY` 로 그룹핑 된 컬럼의 값들을 하나의 문자열(VARCHAR)로 합칠 수 있다.

## Example
다음과 같이 Order 와 OrderItem 테이블이 있다. 

**Order**
| orderId(PK) | price |
| ----------- | ----- |
| 1           | 10000 |
| 2           | 20000 |
| 3           | 20000 |

**OrderItem**
| orderItemId(PK) | productId | sellerType  | orderId |
| --------------- | --------- | ----------- | ------- |
| 100             | 11        | RETAIL      | 1       |
| 101             | 22        | THRID_PARTY | 1       |
| 102             | 33        | THIRD_PARTY | 2       |
| 103             | 33        | THIRD_PARTY | 2       |
| 104             | 44        | RETAIL      | 3       |
| 105             | 55        | RETAIL      | 3       |

각 주문(Order)은 여러개의 OrderItem을 가질 수 있고, 각 OrderItem은 상품의 판매 형태에 따라 RETAIL, THIRD_PARTY 타입을 갖는다. 이 때 RETAIL 상품만 주문한 주문번호(orderId)를 뽑아내려면?

먼저 Order와 OrderItem 을 조인하고 orderId로 그룹핑 한 뒤 sellerType 컬럼에 대해 `group_concat`을 적용한다

```sql
select o.orderId, group_concat(oi.sellerType) as sellerTypes
from `order` o
join orderItem oi on o.orderId = oi.orderId
group by orderId;
```

쿼리 실행 결과

| orderId | sellerTypes             |
| ------- | ----------------------- |
| 1       | RETAIL,THIRD_PARTY      |
| 2       | THIRD_PARTY,THIRD_PARTY |
| 3       | RETAIL,RETAIL           |

`,`를 구분자로 하여 sellerType이 하나의 문자열로 합쳐진 결과를 얻을 수 있다.

`distinct` 키워드를 사용하면 중복 제거가 가능하다.
```sql
select o.orderId, group_concat(distinct oi.sellerType) as sellerTypes
from `order` o
join orderItem oi on o.orderId = oi.orderId
group by orderId;
```
쿼리 실행 결과

| orderId | sellerTypes        |
| ------- | ------------------ |
| 1       | RETAIL,THIRD_PARTY |
| 2       | THIRD_PARTY        |
| 3       | RETAIL             |

이제 RETAIL 상품만 주문한 주문을 뽑아내는건 식은 죽 먹기다.
```sql
select s.orderId 
from
    (select o.orderId as orderId, group_concat(distinct oi.sellerType) as sellerTypes
    from `order` o
    join orderItem oi on o.orderId = oi.orderId
    group by orderId) s
where s.sellerTypes = 'RETAIL';
```

`having` 절을 사용하여 서브쿼리를 없애면 더 깔끔한 쿼리를 만들 수 있다.
```sql
select o.orderId
from `order` o
join orderItem oi on o.orderId = oi.orderId
group by orderId
having group_concat(distinct oi.sellerType) = 'RETAIL';
```