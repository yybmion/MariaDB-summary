## SQL 고급 7장 - 2

___
#### 순위함수 
> 출처 (https://doorbw.tistory.com/221)

순위함수는 **(RANK, DENSE_RANK, ROW_NUMBER, NTILE)** - PARTITION BY와 함께

1. **RANK**
> RANK 함수는 중복 값들에 대해서 동일 순위로 표시하고, 중복 순위 다음 값에 대해서는 중복 개수만큼 떨어진 순위로 출력하도록 하는 함수 입니다.
![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99C0FA4E5CDBB3BE15)
```sql
SELECT empNo, empName, salary,

RANK() OVER (ORDER BY salary DESC) RANK등수

FROM employee;
```
![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99BAB9495CDBC97008)

2. **DENSE_RANK**
> DENSE_RANK 함수는 중복 값들에 대해서 동일 순위로 표시하고, 중복 순위 다음 값에 대해서는 중복 값 개수와 상관없이 순차적인 순위 값을 출력하도록 하는 함수 입니다.

```sql
SELECT empNo, empName, salary,

DENSE_RANK() OVER (ORDER BY salary DESC) DENSE_RANK등수

FROM employee;
```
![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F997A62475CDBC9C81B)

3. **ROW_NUMBER**
> ROW_NUMBER 함수는 중복 값들에 대해서도 순차적인 순위를 표시하도록 출력하는 함수 입니다.

```sql
SELECT empNo, empName, salary,

ROW_NUMBER() OVER (ORDER BY salary DESC) ROW_NUMBER등수

FROM employee;
```
![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F990EA5425CDBCA2530)

4. **RANK / DENSE_RANK / ROW_NUMBER** - 동시에 사용

```sql
SELECT empNo, empName, salary,

RANK() OVER (ORDER BY salary DESC) RANK등수,

DENSE_RANK() OVER (ORDER BY salary DESC) DENSE_RANK등수,

ROW_NUMBER() OVER (ORDER BY salary DESC) ROW_NUMBER등수

FROM employee;
```
![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9927C6425CDBCABA05)

**요약!!**

> 먼저 **RANK**함수는
중복 값에 대해서 동일한 순위 그리고
중복 값 다음 값에 대해서 중복순위 + 중복값 개수 의 순위를 출력합니다.



> **DENSE_RANK**함수는
중복 값에 대해서 동일한 순위 그리고
중복 값 다음 값에 대해서 중복순위 + 1 의 순위를 출력합니다.



> **ROW_NUMBER**함수는
중복 값에 대해서 순차적인 순위 그리고
중복 값 다음 값에 대해서 또한 순차적인 순위를 출력합니다.

5. **NTILE**
> NTILE 함수도 순위함수로 사용되지만 위에서 사용된 함수들과는 약간 다르게 느껴질 수 있습니다.

NTILE함수는 뒤에 함께 적어주는 숫자 만큼으로 등분을 하는 함수 입니다.

만약 직원들 데이터에 대해서 salary 순서를 기준으로 4등분을 하고자 한다면 다음과 같습니다.

```sql
SELECT empNo, empName, salary,

NTILE(4) OVER (ORDER BY salary DESC) NTILE등분

FROM employee;
```
![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F993D56475CDBCCC10E)

6. **PARTITION BY**
> PARTITION BY 절 또한 어렵지 않습니다.

만약 위와 같은 데이터에서 단순히 모든 사람의 salary를 순위 매기고 싶은 것이 아니라, 직급별 순위를 매기고 싶다면 어떻게 할까요?

직급 별로 구분을 해서 순위를 매기면 됩니다. 이렇게 특정 속성 별로 구분을 하고자 할 때 PARTITION BY절을 사용하면 됩니다.

```sql
SELECT empName, job, salary,

RANK() OVER (PARTITION BY job ORDER BY salary DESC) RANK등수

FROM employee;
```

![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99E82E4E5CDBCCCD41)

**즉 job 안에서 연봉을 내림차순으로 정리한 값을 볼 수 있다.**



#### 피벗의 구현
> 피벗은 행을 열로 변환시켜준다. 필요하면 집계까지 수행한다.
> 출처 (https://gent.tistory.com/343)

![image_description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbkWpxr%2FbtqHZlgK1H9%2FVgw9bQNPsro8kiCiUsWze0%2Fimg.png)

```sql
SELECT *
  FROM ( 피벗할 쿼리문 ) AS result
 PIVOT ( 그룹합수(집계컬럼) FOR 피벗대상컬럼 IN ([피벗컬럼값] ... ) ) AS pivot_result
 ```

#### JSON
> 키와 값으로 결과를 나타내어준다.
