# SQL Function

| Write  | contect            | Date       |
| ------ | ------------------ | ---------- |
| 김현호 | lhyun058@gmail.com | 2022.08.13 |

> **📝정리**
>
> 좌표 간 거리를 계산해 가까운 거리 순으로 데이터를 가져오기 위해 MySQL에 ST_Distance_Sphere()함수를 사용할 것이며. 현재 진행하는 프로젝트에서는 Querydsl을 사용 하기때문에 Querydsl에서 SQL Function사용을 통해 쿼리를 작성해야 해서 사용법을 정리하려고 합니다.

## 1. ST_Distance_Sphere()

`ST_Distance_Sphere()`는 두 점 사이의 거리르 `meter`단위로 변환하는 함수입니다.

```sql
SET @location = POINT(127.036377, 37.500643);
SELECT ST_Distance_Sphere(@location,
                          POINT(trade_longitude, trade_latitude)) AS distance,
       deal.trade_longitude,
       deal.trade_latitude
FROM deal
order by distance;
```

![image](https://user-images.githubusercontent.com/58923731/184480171-f4d4e5ad-a3f1-4ef0-a09f-3e65c5363bb5.png) 

## 2.querydsl에서 SQL Function 호출하기

`SQL Function`은 `Expressions.stringTemplate()`를 통해 사용할 수 있습니다.
Expressions.stringTemplate()을 사용해 문자열로 직접 함수를 작성 하며,  `StringExpression`을 반환하여  `select`문에서 사용할 수 있게 해줍니다.

사용방법 - `Expressions.stringTemplate("ST_Distance_Sphere({0}, {1})",파라미터1, 파라미터2)` 

```sql
List<Deal> content = queryFactory
        .selectFrom(deal)
        .where(
                categoryEq(dealFilterRequest.getCategory()),
                categoryDetailEq(dealFilterRequest.getCategoryDetail()),
                deal.deletedAt.isNull()
        )
        .join(deal.user, user).fetchJoin()
        .join(deal.location, location).fetchJoin()
        .limit(pageable.getPageSize())
        .offset(pageable.getOffset())
        .orderBy(Expressions.stringTemplate("ST_Distance_Sphere({0}, {1})",
                        Expressions.stringTemplate("POINT({0}, {1})",
                                dealFilterRequest.getCurrentLongitude(),
                                dealFilterRequest.getCurrentLatitude()
                        ),
                        Expressions.stringTemplate("POINT({0}, {1})",
                                deal.tradeLongitude,
                                deal.tradeLatitude
                        )
                ).asc(),
                deal.createdAt.desc())
        .fetch();
```

위에 코드를 실행하면 아래처럼 쿼리가 나가는걸 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/58923731/184498920-bcb1a144-3558-478c-a7b5-62e5bc05d8bf.png) 

---

### 참고

💡 [MySQL_ST_Distance_Sphere](https://tzara.tistory.com/45)