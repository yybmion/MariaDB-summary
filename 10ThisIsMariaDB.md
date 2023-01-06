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
___
**스토어드 프로시저 실습**
:apple: 2개의 입력 매개 변수가 있는 스토어드 프로시저
```sql
DROP PROCEDURE IF EXISTS userProc2;
DELIMITER $$
CREATE PROCEDURE userProc2(
    IN userBirth INT
    IN userHeight INT
)
BEGIN
    SELECT * FROM userTBL
        WHERE birthYear > userBirth AND height > userHeight;
END $$
DELIMITER;

CALL userProc(1970,178);
```

:banana:출력 매개 변수를 설정해서 사용
```sql
DROP PROCEDURE IF EXISTS userProc3;
DELIMITER
CREATE PROCEDURE userProc3(
    IN txtValue CHAR(10),
    OUT outValue INT
)
BEGIN
    INSERT INTO testTBL VALUES(NULL,txtValue);
    SELECT MAX(id) INTO outValue FROM testTBL; --출력 매개 변수에 값을 대입하기 위해서는 주로 SELECT --- INTO문을 사용
END $$
DELIMITER;

CREATE TABLE IF NOT EXISTS testTBL(
    id INT AUTO_INCREMENT PRIMARY KEY,
    txt CHAR(10)
);
```

위 스토어드 프로시저를 사용해보면
1. CALL userProc3('테스트값',@myValue);
2. SELECT CONCAT('현재 입력된 ID 값 ==>',@myValue);

**결과**:
현재 입력된 ID 값 ==> 1 (계속해서 증가한다 **AUTO_INCREMENT**)\

:melon:IF--ELSE문을 사용
```sql
DROP PROCEDURE IF EXISTS ifelseProc;
DELIMITER $$
CREATE PROCEDURE ifelseProc(
    IN userName VARCHAR(10)
)
BEGIN
    DECLARE bYear INT; --변수 선언
    SELECT birthYear into bYear FROM userTBL
        WHERE name = userName;
    IF (bYear >= 1980) THEN
                SELECT '아직 젊군요..';
    ELSE
                SELECT '나이가 지긋하네요..';
    END IF;
END $$
DELIMITER;

CALL ifelseProc('조용필');
```
:peach: CASE문도 사용가능하다.

:watermelon: While문을 활용
```sql
DROP TABLE IF EXISTS guguTBL;
CREATE TABLE guguTBL (txt VARCHAR(100)); --구구단 저장용 테이블

DROP PROCEDURE IF EXISTS whileProc;
DELIMITER $$
CREATE PROCEDURE whileProc()
BEGIN
    DECLARE str VARCHAR(100); -- 각 단을 문자열로 저장
    DECLARE i INT; --구구단 앞자리
    DECLARE k INT --구구단 뒷자리
    SET i=2; --2단부터 계산

    WHILE (i<10) DO -- 바깥 반복문 2단-9단
        SET str=' ' -- 초기화
        SET k=1; --구구단 뒷자리는 항상 1부터 9까지
        WHILE(k<10) DO
            SET str=CONCAT(str,' ',i,'x',k,'=',i*k) --문자열 만들기
            SET k=k+1; --뒷자리 증가
        END WHILE;
        SET i=i+1 --앞자리 증가
        INSERT INTO guguTBL VALUES(str); --각 단의 결과를 테이블에 입력
    END WHILE;
END $$
DELIMITER;

CALL whileProc();
SELECT * FROM guguTBL;
```

:lollipop: **DECALRE -- HANDLER**이용해 오류 처리
> 출처(https://blog.duveen.me/23)

1. 테이블 생성
```sql
CREATE TABLE article_tags (
    article_id INT,
    tag_id     INT,
    PRIMARY KEY(article_id,tag_id)
);
```

```sql
DELIMITER $$

CREATE PROCEDURE insert_article_tags(IN article_id INT, IN tag_id INT)
BEGIN

 DECLARE CONTINUE HANDLER FOR 1062
 SELECT CONCAT('duplicate keys (',article_id,',',tag_id,') found') AS msg;

 -- insert a new record into article_tags
 INSERT INTO article_tags(article_id,tag_id)
 VALUES(article_id,tag_id);

 -- return tag count for the article
 SELECT COUNT(*) FROM article_tags;
END
```
2. 데이터 삽입
```sql
CALL insert_article_tags(1,1);
CALL insert_article_tags(1,2);
CALL insert_article_tags(1,3);
```
3. 이후, handler가 호출이 잘 되는지 중복 키를 삽입해 본다.
```sql
CALL insert_article_tags(1,3);
```
우리는 에러 메세지를 얻었다. 하지만 **CONTINUE 핸들러**로 handler를 선언했기 때문에 저장 **프로시저는 실행을 계속** 할 것이다. 결과로 에러 메시지 뿐만 아니라 기사들의 tag수도 얻을 수 있었다.

따라서 CONTINUE 핸들러 대신 **EXIT핸들러**를 사용한다면 오직 에러메시지만 얻을 수 있다.

:tangerine: 테이블 이름을 파라미터로 전달 (**동적쿼리**)

!<스킵>!

**스토어드 프로시저 특징**
1. MariaDB의 **성능**을 향상시킬 수 있다.
2. **유지관리**가 편하다.
3. **모듈식** 프로그래밍이 가능하다.
4. **보안**을 강화할 수 있다.

```sql
DELIMITER $$
CREATE PROCEDURE delivProc(
    IN id VARCHAR(10)
)
BEGIN
    SELECT userID,name,addr,mobile1,mobile2
        FROM userTBL
        WHERE userID=id;
END $$
DELIMITER;
```
```sql
CALL delivProc('LJB');
```
**위 쿼리는 보안을 위한 프로시저**

___
#### 스토어드 함수

```sql
DELIMITER $$
CREATE FUNCTION 스토어드 함수이름(파라미터)
    RETURNS 반환형식
BEGIN
        이 부분에 프로그래밍 코딩..
        RETURN 반환값;
END $$
DELIMITER ;
SELECT 스토어드_함수이름();
```

**스토어드 함수와 스토어드 프로시저의 차이점**
1. 스토어드 프로시저의 파라미터와 달리 **IN,OUT**을 사용할 수 없다. 스토어드 함수의 파라미터는 모두 **입력 파라미터**로 사용된다.
2. 스토어드 함수는 **RETURN문**으로 반환할 값의 데이터 형식을 지정하고, 본문 안에서는 **RETURN문**으로 하나의 값을 반환해야 한다. 스토어드 프로시저는 별도의 반환하는 구문이 없으며, 꼭 필요하다면 여러 갸의 **OUT파라미터**를 사용해서 값을 반환할 수 있다.
3. 스토어드 프로시저는 **CALL**로 호출하지만, 스토어드 함수는 **SELECT** 문장 안에서 호출된다.
4. 스토어드 프로시저 안에는 **SELECT문**을 사용할 수 있지만, 스토어드 함수 안에서는 집합 결과를 반환하는 **SELECT**를 사용할 수 없다.

- 2개의 숫자의 합계를 계산하는 스토어드 함수
```sql
USE sqlDB;
DROP FUNCTION IF EXISTS userFunc;
DELIMITER $$
CREATE FUNCTION userFunc(value1 INT,value2 INT)
    RETURNS INT
BEGIN
    RETURN value1+value2;
END $$
DELIMITER ;

SELECT userFunc(100,200);
```
___

#### 커서

> **커서**는 테이블에서 여러 개 행을 쿼리한 후에, 쿼리의 결과인 행 집합을 **한 행씩 처리**하기 위한 방식


![111111111111](https://user-images.githubusercontent.com/113106136/210940753-b4bf3f6f-dd35-41df-ac86-8d3799811b4f.png)



