## SQL 스토어드 프로시저
___

### 스토어드 프로시저의 개요
> 스토어드 프로시저는 한마디로 쿼리문의 집합으로 어떠한 동작을 일괄 처리하기 위한 용도로 사용된다. 즉, 이것을 모듈화 시켜서 필요할 때마다 호출만 하면 훨씬 편리하게 MariaDB를 운영할 수 있다.

```sql
형식:
DELIMITER $$
CREATE PROCEDURE 스토어드 프로시저이름(IN 또는 OUT 파라미터)
BEGIN

        이 부분에 SQL 프로그래밍 코딩 ..
END $$
DELIMITER ;
CALL 스토어드 프로시저이름();
```

```sql
USE sqlDB;
DROP PROCEDURE IF EXISTS userProc;
DELIMITER $$
CREATE PROCEDURE userProc()
BEGIN
    SELECT * FROM userTBL; -- 스토어드 프로시저 내용
END $$
DELIMITER ;

CALL userProc();
```

기존에는 SELECT * FROM userTBL 문을 매번 사용했지만 **CALL userProc()문**으로 호출만 하면 된다.

#### 스토어드 프로시저의 수정과 삭제

수정은 **ALTER PROCEDURE**
삭제는 **DROP PROCEDURE**

**매개 변수의 사용**

- 입력 매개 변수를 지정하는 형식
```sql
IN 입력_매개_변수_이름 데이터_형식
```

- 입력 매개 변수가 있는 스토어드 프러시저를 실행하기 위해서는 다음과 같이 사용

```sql
CALL 프로시저_이름(전달 값)
```
- 출력 매개 변수를 지정하는 방식

```sql
OUT 출력_매개_변수_이름 데이터_형식
```

출력 매개 변수에 값을 대입하기 위해서는 **SELECT...INTO**문 사용

- 출력 매개 변수가 있는 스토어드 프로시저를 실행하기 위해서는
```sql
CALL 프로시저_이름(@변수명);
SELECT @변수명;
```

**스토어드 프로시저 내의 오류 처리**
> 스토어드 프로시저 내부에서 오류 발생 시 DECLARE 액션 HANDLER FOR 오류조건 처리할_문장 구문을 사용