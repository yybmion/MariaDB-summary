## SQL 고급 7장 -3
___

#### INNER JOIN
> 쉽게 말해 교집합
```sql
SELECT <열 목록>
FROM <첫 번째 테이블>
        INNER JOIN <두 번째 테이블>
        ON <조인될 조건>
[WHERE 검색조건]
```
> 출처(https://doh-an.tistory.com/30)

- 첫 번째 테이블과 두 번째 테이블의 교집합이 합께 출력됨
  
```sql
SELECT Sales.*, Countries.Country
FROM Sales
JOIN Countries
ON Sales.CountryID = Countries.ID
```

![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc7kLBI%2FbtrbquYTQ0x%2FM4kso0nOk1n1kplQmyjHXk%2Fimg.png)

#### OUTER JOIN ~ UNION

> 정리본(https://www.notion.so/Outer-Cross-Self-join-91b894df7c144715a71a4feed2299ec8)
> 유용한 출처 (https://m.blog.naver.com/regenesis90/222190313921)

