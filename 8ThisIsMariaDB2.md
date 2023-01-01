## SQL 테이블과 뷰 8장 -2
___

#### 뷰
> 출처 (https://as-j.tistory.com/113)

**뷰의 장점**
뷰는 SQL에서 아래와 같은 장점을 가진다.

1) **독립성**: 테이블 구조가 변경되어도 뷰에서 조회하는 컬럼이나 컬럼명, 테이블명 등이 그대로라면, 뷰를 사용하고 있는 응용 프로그램은 변경하지 않아도 된다.

2) **편리성**: 자주 사용되는 복잡한 쿼리를 미리 뷰로 정의해두면, 추후 쿼리는 간단한 형태로 표현, 조회 등이 가능하다.

3) **보안성**: 사용자의 권한에 따라 열람 가능한 데이터를 다르게 할 수 있다. 권한에 따라 확인 가능한 컬럼을 정의하여 뷰를 생성하면, 기본 테이블 모든 정보 노출 없이 접근 제어를 할 수 있다.

⇒ 여러 테이블에서 필요한 정보만을 조회하고 제공하기 위한 가상의 테이블. 일종의 함수라고 생각할 수 있다.

 

**뷰의 특징**
1) 생성된 뷰는 또 다른 뷰를 생성하는데 사용될 수 있다.

2) 뷰의 정의는 변경할 수 없으며, 수정을 원한다면 삭제 후 재생성을 해야 한다.

3) 뷰를 통한 **갱신에는 제약**이 따른다. 갱신을 위해서는 기본적으로 원천 테이블의 기본키가 포함되어야 한다.

4) 원천이 되는 테이블이나 뷰가 삭제되면 이를 기반으로 하는 뷰도 함께 삭제된다.

```sql
USE tableDB;
CREATE VIEW v_userTBL
AS
    SELECT userid,name,addr FROM userTBL;
-- 이제는 뷰를 새로운 테이블로 생각하고 접근하면 된다.
SELECT *FROM v_userTBL; --뷰를 테이블이라고 생각해도 무방
```
- 혹시 자주 사용하는 테이블이 있다면 뷰로 생성해두면 좋다.

```sql
CREATE VIEW v_userbuyTBL
AS
SELECT U.userid,U.name,B.prodName,U.addr,CONCAT(U.mobile1,U.mobile2) AS '연락처'
FROM userTBL U
    INNER JOIN buyTBL B
    ON U.userid=B.userid;
```

다음에 접근할 경우 **v_userbuyTBL**을 그냥 테이블이라고 생각하고 접근하면 된다.
**WEHRE 절**도 사용가능!

```sql
SELECT *FROM v_userbuyTBL WHERE name =N'김범수';
```

- UPDATE를 통해 수정은 가능하지만
- INSERT로 데이터를 입할 때도 가능

- 뷰(집계함수가 포함된)를 통해서 데이터 수정 불가
1. INSERT
2. UPDATE
3. DELETE
사용 불가하고 

뷰 내에
1. UNION ALL
2. JOIN
3. DISTINCT
4. GRUOP BY
를 사용한 뷰는 데이터를 변경할 수 없다.


**WITH CHECK OPTION**
WITH CHECK OPTION절은 VIEW에 대한 갱신이나 삽입 연산을 실행할 때 VIEW 정의 조건을 위반하면 실행이 거부된다는 것을 의미하므로

- 투플이 뷰에서 사라지지 않도록 보호하고,

- 데이터 무결성을 유지할 수 있으며,

- INSERT문과 UPDATE문으로 뷰에 정의된 조건에 위배된 투플을 생성할 수 없음.

```sql
ALTER VIEW v_height177
AS
    SELECT * FROM userTBL WHERE height >=177
        WITH CHECK OPTION;
--이제 데이터 삽입시 177 미만으로 입력하면 오류가 생긴다
```