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

다음과 같은 텍스트 파일이 저장되어있고 이 파일을 처리하기 위해서는 이러한 과정을 거친다.

1. 파일을 연다.(Open) 그러면 **파일포인터**는 파일의 제일 시작(BOF)을 가리키게 된다.
2. 처음 데이터를 읽는다. 그러면 '이승기'의 데이커가 읽어지고, **파일포인터**는 '김범수'로 이동한다.
3. 파일의 끝(EOF)까지 반복한다.
- 읽은 데이터를 처리한다.
- 현재의 **파일포인터**가 가리키는 데이터를 읽는다. **파일포인터**는 자동으로 다음으로 이동한다.
4. 파일을 닫는다.(Close)

**테이블의 행** 집합으로 생각해보아도 똑같다.

**커서의 처리 순서**
![description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbn7TUB%2Fbtq0bv8QKMj%2Fv0kweZgGkh50altj0hgeW1%2Fimg.png)

쉽게,
1. 커서 선언 (**DECALRE**)
2. 반복 조건 선언 (**DECLARE,HANDLER**)
3. 커서 열기 (**OPEN**)
4. 커서에서 데이터 가져오기 (**FETCH**)
5. 데이터 처리
6. 커서 닫기 (**CLOSE**)


![description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FIjePd%2Fbtq0jNfmXrZ%2FYLVE64j1VykcYc8TtPY3GK%2Fimg.png)

41,42. 커서 선언시 파일을 읽어(select) 파일의 **제일 시작**을 가르키게됨.

>실제라면 이 스토어드 프로시저를 사용하는 것보다 **AVG() 함수**를 사용하는 것이 훨씬 효율적이지만, '**홀수의 평균값**'이나 '**5의 배수에 해당하는 값의 평균**' 같은 특별한 평균은 AVG() 함수에서 사용할 수 없어 **커서**를 사용해야한다.

___

고객 테이블에 고객 등급열을 추가해 구매 테이블에서 고객이 구매한 총액에 따라 등급이 결정되는 스토어드 프로시저를 작성

![DESCRIPTION](https://blog.kakaocdn.net/dn/rjqYV/btq0ehorPFr/1H2hIeAvQDfW6I66xvb1K1/img.png)

10~15. 커서 선언 : 파일을 읽어(select) **시작부분**을 가르킨다.

17,18. **행의 끝**일 때 endOfRow 변수에 true 대입.

19 커서를 연다.

21 커서에서 읽은 1줄은 **id, hap** 변수에 대입

___

#### 트리거

트리거란?
> 트리거는 사전적 의미로 방아쇠를 뜻한다. 방아쇠를 당기면 총알이 나가듯 테이블에 무슨일이 발생하면 자동으로 실행된다.

> 테이블에서 **INSERT나** **UPDATE** 또는 **DELETE** 작업이 발생되면 실행되는 코드

> 누군가 고의 혹은 실수로 테이블을 삭제한다면 누가 지웠는지 추적하는 일이 쉽지 않다. 이럴 때 트리거를 걸어놓는다면 이러한 문제를 해결할 수 있다.

> 트리거는 스토어드 프로시저와 작동이 비슷하지만 직접 실행은 불가하고 오직 **해당 테이블에 이벤트가 발생할 경우**에만 실행

> **IN,OUT 매개 변수 사용 불가**


트리거 예제
```sql
USE sqlDB;
CREATE TABLE IF NOT EXISTS testTBL (id INT, txt VARCHAR(10));
INSERT INTO testTBL VALUES(1,'이엑스아이디');
INSERT INTO testTBL VALUES(2,'애프터스쿨');
INSERT INTO testTBL VALUES(3,'에이오에이');
```

```sql
DROP TRIGGER IF EXISTS testTrg;
DELIMITER //
CREATE TRIGGER testTrg --트리거 이름
    AFTER DELETE --삭제 후에 작동하도록 지정
    ON testTbl --트리거를 부탁할 테이블
    FOR EACH ROW --각 행마다 적용시킴
BEGIN
    SET @msg = '가수 그룹이 삭제됨' ; --트리거 실행 시 작동되는 코드들
END //
DELIMITER;
```

이제 삭제를 해보자
```sql
SET @msg = '';
INSERT INTO testTbl VALUES(4,'나인뮤지스');
SELECT @msg;
UPDATE testTbl SET txt = '에이핑크' WHERE id =3;
SELECT @msg;
DELETE FROM testTbl WHERE id=4;
SELECT @msg;
```

다른 **INSERT UPDATE** 문은 @msg 변수에 아무것도 나오지 않지만 **DELETE** 실행시 '가수 그룹이 삭제됨'이 출력되는 것을 확인할 수 있다.

**트리거의 종류**

:house: **AFTER 트리거**
테이블에 **INSERT,UPDATE,DELETE**등의 작업이 일어났을 때 작동하는 트리거 즉, **작업 후에 작동**

:bank: **BEFORE 트리거**
이벤트가 **발생하기 전에 작동**하는 트리거, 이 또한 **INSERT,UPDATE,DELETE** 세가지 이벤트로 작동한다.

- AFTER 트리거 실습

```sql
CREATE TABLE backup_userTBL
( userID  	CHAR(8) NOT NULL PRIMARY KEY, 
  userName  NVARCHAR2(10) NOT NULL,
  birthYear NUMBER(4) NOT NULL,  
  addr	  	NCHAR(2) NOT NULL, 
  mobile1  CHAR(3), 
  mobile2  CHAR(8), 
  height   NUMBER(3), 
  mDate    DATE,
  modType  NCHAR(2), -- 변경된 타입. '수정' 또는 '삭제'
  modDate  DATE, -- 변경된 날짜
  modUser  NVARCHAR2(256) -- 변경한 사용자
);

SELECT * FROM backup_userTBL;
```
![description](https://mblogthumb-phinf.pstatic.net/MjAyMDA0MDRfMjQ1/MDAxNTg2MDA2OTMzMjEz.t0JzklWso0g5iy8-FFpQacQ-WGEO4o3HFnRJN8iyxnQg.1Xup6FHU2ZC2qewQl87NNXRsyB_X09m_hH78G1SdVG0g.PNG.chas0302/image.png?type=w800)

```sql
CREATE OR REPLACE TRIGGER trg_BackupUserTBL  -- 트리거 이름
   AFTER  UPDATE OR DELETE  -- 삭제,수정 후에 작동하도록 지정
   ON userTBL -- 트리거를 부착할 테이블
   FOR EACH ROW -- 각 행마다 적용됨
DECLARE 
   v_modType NCHAR(2); -- 변경 타입
BEGIN
   IF UPDATING THEN  -- 업데이트 되었다면
      v_modType := '수정';
   ELSIF DELETING  THEN -- 삭제되었다면,
      v_modType := '삭제';
    END IF;
   -- :OLD 테이블의 내용(변경전의 내용)을 백업테이블에 삽입
    INSERT INTO backup_userTBL VALUES( :OLD.userID, :OLD.userName, :OLD.birthYear, 
        :OLD.addr, :OLD.mobile1, :OLD.mobile2, :OLD.height, :OLD.mDate, 
        v_modType, SYSDATE(), USER() );
END trg_BackupUserTBL;
```

여기서 한 가지 **OLD테이블은 update,delete**가 **수행되기 전**에 **데이터가 잠깐 저장되어 있는 임시 테이블**이라고 생각하면 된다.

>참고로 이러한 데이터 변경 이력 기록을 위해서는 우리는 먼저 "**NEW**", "**OLD**"라는 키워드를 알아 두어야 한다. "NEW"는 "**NEW.[컬럼 이름]**"과 같은 형태로 사용 되어 **새롭게 입력 되거나 변경된 레코드의 컬럼 값**을 의미한다. "OLD"는 "**OLD.[컬럼 이름]**"으로 쓰일 수 있고 **삭제 혹은 수정되기 이전의 컬럼 값**을 나타낸다.

```sql
UPDATE USERTBL SET ADDR = '몽고' WHERE USERID ='JKW';
DELETE USERTBL WHERE HEIGHT >= 177;

SELECT * FROM BACKUP_USERTBL;
```

![DESCRIPTION](https://mblogthumb-phinf.pstatic.net/MjAyMDA0MDRfOCAg/MDAxNTg2MDA3MjI3MDgx.1ohtzCqcs6IqqDPTHKG6VctIKNLC-x-pb345xDqSLo8g.pf4VekAfIXz8HJalaAuo9Oej4d2D71O7XNBk9YbYz34g.PNG.chas0302/image.png?type=w800)


**트리거가 생성하는 임시 테이블**
![description](https://mblogthumb-phinf.pstatic.net/MjAyMDA0MDRfMTk1/MDAxNTg2MDA2MTU3OTM2.D3KNFnXe6Qbnk9Fc0jH50uRobIT9-Tml3ADhdc0VlU8g.wT3ZCrJoolBhj4RRsMjn_HQtKQwYFIDSmUkNwcr_qLQg.PNG.chas0302/image.png?type=w800)

1) **NEW 테이블**

**INSERT와 UPDATE 작업 시**에 변경할 새로운 데이터를 잠깐 저장해 놓는다.

즉, 테이블에 INSERT 트리거나, UPDATE 트리거를 부착시켜 놓았다면, 해당 테이블에 **INSERT나 UPDATE 명령이 수행되면 입력/변경될 새 값이 :NEW 테이블에 정한 후에, :NEW 테이블의 값을 테이블에 입력/변경되는 것이다.** 그러므로 

:NEW 테이블을 조작하면 입력되는 새로운 값을 다른 값으로 대치시킬 수 있다.

​

2) **OLD 테이블**

**DELETE 작업 시 데이터를 잠깐 저장해 놓는다.**

쉽게 말해 DELETE와 UPDATE 실행시 OLD테이블에 값이 저장되면 그 값을 이용해 백업테이블에 값을 저장시킬 수 있다.

> new old 테이블의 상세 설명 영상 (https://www.youtube.com/watch?v=9Hm7qaN3xhQ)


**BEFORE TRIGGER**
```sql
USE sqlDB;
DROP TRIGGER IF EXISTS userTBL_BeforeInsertTrg;
DELIMITER //
CREATE TRIGGER userTBL_BeforeInsertTrg --트리거 이름
    BEFORE INSERT --입력 전에 작동하도록 설정
    ON userTBL --트리거를 부착할 테이블
    FOR EACH ROW
BEGIN
    IF NEW.birthYear < 1900 THEN
        SET NEW.birthYear =0;
    ELSEIF NEW.birthYear > YEAR(CURDATE()) THEN
        SET NEW.birthYear = YEAR(CURDATE());
    END IF;
END //
DELIMITER;
```


**9~13행**은 입력되는 값이 들어있는 **NEW테이블**의 값을 검사해서 1900미만인 경우 0으로, 현재 연도보다 초과하면 현재 연도로 변경하도록 설정해 놓았다.

___
> 출처(https://title-developer.tistory.com/146)
> 
**다중 트리거**
**하나의 테이블에 동일한 트리거가 여러 개 부착되어 있는 것.** 예로 AFTER INSERT 트리거가 한 개 테이블에 2개 이상 부착되어 있을 수도 있다.

![description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbaWnBg%2FbtqZctCNnvw%2FtxsKKdkwrdbpt0j3NWPGqK%2Fimg.png)

**중첩 트리거**
**트리거가 또 다른 트리거를 작동하는 것.**

물건을 구매하면, 물품테이블에서 남은 개수를 감소시키고, 배송 테이블 건수 입력

- 예제

![description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcSPT2y%2FbtqY8GCFq1h%2FYj59rDyp5jgxiu0pLNUonk%2Fimg.png)

![description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fqath2%2FbtqZfhVViZI%2F7xLpkTJe5RCN4g2SUty8J0%2Fimg.png)

**3~12.** 구매테이블에 구매가 발생하면 물품테이블에 주문한 개수를 빼고

**16~28.** 물품테이블에 수정이 발생하면 배송테이블에 배송할 물건과 개수를 입력

![description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcHZEcX%2FbtqYZ7uABVb%2FDFwTuAZoFznpuvUgVKKZTk%2Fimg.png)


 



