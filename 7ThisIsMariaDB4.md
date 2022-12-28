## SQL 고급 7장 -4
___
#### SQL 프로그래밍
```sql
DELIMITER $$
CREATE PROCEDURE 스토어드 프로시저이름()
BEGIN

        이 부분에 SQL 프로그래밍 코딩..

END $$
DELIMITER ;
CALL 스토어드 프로시저이름();
```
> DELIMITER ; 로 종료 문자를 세미콜론(;)으로 변경해 놓아야 한다. CALL 스토어드 프로시저이름();은 CREATE PROCEDURE로 생성한 스토어드 프로시져를 실행한다.

#### IF--ELSE
```sql
IF <부울 표현식> THEN
        SQL문장들1..
ELSE
        SQL문장들2..
END IF;
```

```sql
DROP PROCEDURE IF EXSIST ifProc; -- 기존에 만든적 있다면 삭제
DELIMITER $$
CREATE PROCEDURE ifProc()
BEGIN
    DECLARE var1 INT; -- var1 변수 선언
    SET var1=100; -- 변수에 값 대입
IF var1=100 THEN -- 만약 @var1이 100이라면
    SELECT '100입니다.';
ELSE
    SELECT '100이 아닙니다.';
END IF;
END $$
DELIMITER ;
CALL ifProc();
```

#### CASE
> 2중 분기

>IF 사용
```sql
DROP PROCEDURE IF EXISTS ifProc3;
DELIMITER $$
CREATE PROCEDURE ifProc3()
BEGIN
    DECLARE point INT;
    DECLARE credit CHAR(1);
    SET point = 77;

IF point >= 90 THEN
            SET credit ='A';
ELSEIF point >=80 THEN 
            SET credit ='B';
ELSEIF point >=70 THEN 
            SET credit ='C';
ELSEIF point >=60 THEN 
            SET credit ='D';
ELSE
            SET credit ='F';
END IF;
SELECT CONCAT('취득점수==>',point),CONCAT('학점==>',credit);
END $$
DELIMITER ;
CALL ifProc3();
```

>CASE 사용
```sql
DROP PROCEDURE IF EXISTS caseProc3;
DELIMITER $$
CREATE PROCEDURE caseProc3()
BEGIN
    DECLARE point INT;
    DECLARE credit CHAR(1);
    SET point = 77;

CASE

    WHEN point >= 90 THEN
                SET credit ='A';
    WHEN point >=80 THEN 
                SET credit ='B';
    WHEN point >=70 THEN 
                SET credit ='C';
    WHEN point >=60 THEN 
                SET credit ='D';
    ELSE
                SET credit ='F';
END CASE;
SELECT CONCAT('취득점수==>',point),CONCAT('학점==>',credit);
END $$
DELIMITER ;
CALL caseProc3();
```

#### WHILE과 ITERATE/LEAVE
```sql
WHILE <부울 식> DO
        SQL 명령문들 ---
END WHILE;
```

```sql
DROP PROCEDURE IF EXISTS whileProc;
DELIMITER $$
CREATE PROCEDURE whileProc()
BEGIN
        DECLARE i INT; -- 1에서 100까지 증가할 변수
        DECLARE hap INT; --더한 값을 누적할 변수
    SET i =1;
    SET hap=0;
        
        WHILE (i<=100) DO
            SET hap=hap+i;
            SET i=i+1;
        END WHILE;

        SELECT hap;
END $$
DELIMITER ;
CALL whileProc();
```

결과값: 5050

> 여기서 7의배수는 덧셈에서 제외시키고 싶다 또는 합계가 1000이 넘으면 더하는 것을 그만두고 싶으면 **ITERATER**과 **LEAVE**를 사용할 수 있다.

```sql
DROP PROCEDURE IF EXISTS whileProc2;
DELIMITER $$
CREATE PROCEDURE whileProc2()
BEGIN
        DECLARE i INT; -- 1에서 100까지 증가할 변수
        DECLARE hap INT; -- 더한 값을 누적할 변수
    SET i =1;
    SET hap=0;
        
    myWhile: WHILE (i<=100) DO -- while문에 label을 지정
        IF(i%7=0) THEN
            SET i=i+1;
            ITERATE myWhile; -- 지정한 label문으로 가서 계속 진행 
    END IF;

    SET hap =hap+i;
    IF(hap>1000) THEN
        LEAVE myWhile; -- 지정한 LABEL 문을 떠남 즉, WHILE 종료
    END IF;
        SET i =i+1;
    END WHILE;

    SELECT hap;
END $$
DELIMITER ;
CALL whileProc2();
```

결과 값 : 1029

#### 오류 처리
---
```sql
DROP PROCEDURE IF EXSISTS errorProc2;
DELIMITER $$
CREATE PROCEDURE errorProc2()
BEGIN
    DECLARE CONTINUE HANDLER FOR SQLEXCEPTION
    BEGIN
    SHOW ERRORS; --오류 메시지를 보여 준다.
    SELECT '오류가 발생했네요. 작업은 취소시켰습니다.' AS '메시지';
    ROLLBACK; -- 오류 발생 시 작업을 롤백시킨다.
    END;
    INSERT INTO userTBL VALUES('LSG','이상구',1988,'서울',NULL,NULL,170,CURRENT_DATE()); -- 중복되는 아이디므로 오류 발생
END $$
DELIMITER ;
CALL errorProc2();
```


#### 동적 SQL
---
```sql
use sqlDB;
PREPARE myQuery FROM 'SELECT * FROM userTBL WHERE userID="EJW"';
EXECUTE myQuery;
DEALLOCATE PREPARE myQuery;
```

> **SELECT * FROM userTBL WHERE userID="EJW"** 문장을 바로 실행시키지 않고, myQuery에 입력시켜 놓은후 EXECUTE문으로 실행할 수 있다.

