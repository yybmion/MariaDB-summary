## SQL 테이블과 뷰 8장
___
- 우선 테이블 생성

**userTBL**
```sql
DROP TABLE IF EXISTS userTBL; -- userTBL 테이블이 존재한다면 삭제(DROP)
CREATE TABLE userTBL	-- userTBL 테이블 생성
( userID  char(8) NOT NULL PRIMARY KEY,	-- 기본키
   name    varchar(10) NOT NULL, 
   birthYear   int NOT NULL,  
   addr	  char(2) NOT NULL,
   mobile1	char(3) NULL, 	-- 널값 허용
   mobile2   char(8) NULL, 	-- 널값 허용
   height    smallint NULL,	-- 널값 허용
   mDate    date NULL 	-- 널값 허용
);
```

**buyTBL**
```sql
DROP TABLE IF EXISTS buyTBL;	-- buyTBL 테이블이 존재하면 삭제(DROP)
CREATE TABLE buyTBL 	-- buyTBL 테이블 생성
(  num int AUTO_INCREMENT NOT NULL PRIMARY KEY , 	-- 자동 상승, 낫널, 기본키 설정 (자동상승은 기본키만 가능)
   userid  char(8) NOT NULL ,
   prodName char(6) NOT NULL,
   groupName char(4) NULL , 	-- 널값 허용
   price     int  NOT NULL,	
   amount    smallint  NOT NULL,
   FOREIGN KEY(userid) REFERENCES userTBL(userID) -- 현재 테이블의 userid를 외래키로 하고 userTBL 테이블의 userID(기본키)를 연결

);
```

- 자료 입력하기
```sql
INSERT INTO userTBL VALUES('aaa', N'에이', 1997, N'서울', '010', '1111111', 185, '2007-8-8');
INSERT INTO userTBL VALUES('bbb', N'비', 1999, N'경남', '010', '2222222', 175, '2011-4-4');
INSERT INTO userTBL VALUES('ccc', N'씨', 1991, N'전남', '010', '3333333', 176, '2008-7-7');
```

!주의!
> buyTBL 테이블에 아래 쿼리를 입력 할때 외래키-기본키 연결 상태이기 때문에,

> buyTBL 외래키(userid)는 반드시 userTBL에 입력(INSERT)되어 있는 aaa, bbb, ccc 중에 하나를 넣어야 합니다. (아닌 경우 에러 발생)

```sql
INSERT INTO buyTBL VALUES(NULL, 'aaa', N'운동화', NULL  , 30,   2);	-- 정상입력
INSERT INTO buyTBL VALUES(NULL, 'bbb', N'노트북', N'전자', 1000, 1);	-- 정상입력
INSERT INTO buyTBL VALUES(NULL, 'ddd', N'모니터', N'전자', 200,  1);	-- 오류발생(userTBL 기본키에 없는 값)
```


#### 제약 조건
---
> 제약 조건이란 데이터의 **무결성**을 지키기 위한 제한된 조건을 의미한다. 즉, 특정 데이터를 입력할 때 무조건적으로 입력되는 것이 아닌, 어떠한 조건을 만족했을 때 입력되도록 제약할 수 있다.

1. PRIMARY KEY
2. FOREIGN KEY
3. UNIQUE
4. CHECK
5. DEFAULT 정의
6. NULL 값 허용
**6가지** 제약 조건 제공


**기본 키 제약 조건**
> 테이블에 존재하는 많은 행의 데이터를 구분할 수 있는 식별자를 **'기본 키'**라고 부른다.

- 기본 키에 입력되는 값은 중복될 수 없으며, NULL 값이 입력될 수 없다.

```sql
USE tableDB;
DROP TABLE IF EXSISTS buyTBL, userTBL;
CREATE TABLE userTBL
( userID CHAR(8) NOT NULL PRIMARY KEY,
  name VARCHAR(10) NOT NULL,
  birthYear INT NOT NULL
);
```

테이블 정보 보기
```sql
DESCRIBE userTBL;
```

**PRIMARY KEY** 이름 지정해주기
```sql
DROP TABLE IF EXSISTS userTBL;
CREATE TABLE userTBL
( userID CHAR(8) NOT NULL,
  name VARCHAR(10) NOT NULL,
  birthYear INT NOT NULL,
  CONSTRAINT PRIMARY KEY PK_userTBL_userID (userID) -- 이름 지정
);
```
이미 만들어진 테이블을 **수정**
```sql
DROP TABLE IF EXSISTS userTBL;
CREATE TABLE userTBL
( userID CHAR(8) NOT NULL,
  name VARCHAR(10) NOT NULL,
  birthYear INT NOT NULL,
);
ALTER TABLE userTBL
   ADD CONSTRAINT PK_userTBL_userID
      PRIMARY KEY (userID);
```

- **ALTER TABLE userTBL**
   userTBL을 변경하자
- **ADD CONSTRAINT PK_userTBL_userID**
   제약 조건을 추가하자, 추가할 제약 조건 이름은 PK_userTBL_userID이다.
- **PRIMARY KEY(userID)**
   추가할 제약 조건은 기본 키 제약 조건이다. 그리고 제약 조건을 설정할 열은 userID열이다.

**외래 키 제약 조건**
> 외래 키 제약 조건은 주 테이블 가이의 관계를 선언함으로써, 데이터의 무결성을 보장해준다. 외래 키 관계를 설정하면 하나의 테이블이 다른 테이블에 의존하게 된다.
 
- 외래 키 테이블에 데이터를 입력 할 때는 꼭 기준 테이블을 참조해서 입력하므로, 기준 테이블에 이미 데이터가 존재해야한다.
- 외래 키 테이블이 참조하는 기준 테이블의 열은 반드시 Primary key거나, Unique 제약 조건이 설정되어 있어야한다.

외래 조건 테이블 생성
```sql
DROP TABLE IF EXSISTS buyTBL,userTBL;
CREATE TABLE userTBL
( userID CHAR(8) NOT NULL PRIMARY KEY,
  name VARCHAR(10) NOT NULL,
  birthYear INT NOT NULL,
);

CREATE TABLE buyTBL
( num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
 userID CHAR(8) NOT NULL,
 prodName CHAR(6) NOT NULL,
 FOREIGN KEY(userID) REFERENCES userTBL(userID)
 -- CONSTRAINT FK_userTBL_buyTBL FOREIGN KEY(userID) REFERENCES userTBL(userID) 외래 키 이름 지정해주기
);
```
만들어진 테이블 수정

```sql
DROP TABLE IF EXISTS buyTBL;
CREATE TABLE buyTBL
( num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
 userID CHAR(8) NOT NULL,
 prodName CHAR(6) NOT NULL,
);

ALTER TALBE buyTBL
   ADD CONSTRAINT FK_userTBL_buyTBL
   FOREIGN KEY (userID)
   REFERENCES userTBL(userID);
```
- ALTER TALBE buyTBL
   buyTBL을 수정한다.
- ADD CONSTRAINT FK_userTBL_buyTBL
   제약 조건을 더한다. 제약 조건 이름은 FK_userTBL_buyTBL이다.
- FOREIGN KEY (userID)
   외래 키 제약 조건을 buyTBL의 userID에 설정한다.
- REFERENCES userTBL(userID)
   참조할 기준 테이블은 userTBL 테이블의 userID열이다.

**CASCADE**

- ON DELETE CASCADE / ON UPDATE CASCADE있음
- 예로, ON UPDATE CASCADE로 설정하면 회원 테이블의 김범수의 ID인 KBS가 kim으로 변경될 경우에, 구매 테이블의 KBS kim도 자동 변경된다.
- 별도로 지정하지 않으면 ON UPDATE NO ACTION / ON DELETE NO ACTION을 지정한것과 같음 즉, 테이블의 회원 아이디가 변경되어도 아무런 일이 일어나지 않음

**UNIQUE 제약조건**
> UNIQUE 제약 조건은 **'중복되지 않는 유일한 값'** 에서 PRIMARY KEY와 비슷하지만 UNIQUE는 **NULL값을 허용**한다.

**CHECK 제약 조건**
> CHECK 제약 조건은 입력되는 데이터를 점검하는 기능이다. 키에 마이너스 값이 들어올 수 없게 한다든지 등을 관리한다.

```sql
DROP TABLE IF EXISTS userTBL
CREATE TABLE userTBL;
CREATE TABLE userTBL
( userID CHAR(8) PRIMARY KEY,
 name VARCHAR(10),
 birthYear INT CHECK (birthYear >= 1900 AND birthYear <= 2020),
 mobile1    char(3) NULL,
 CONSTRAINT CK_name CHECK (name IS NOT NULL) -- 첫번째와 두번째 다른 CHECK 지정 방식
 --ADD CONSTRAINT CK_mobile1 CHECK (mobile1 IN ('010','011'));
);
```
**DEFUALT 정의**
> 값을 입력하지 않았을 때, 자동으로 입력되는 기본값을 정의

```sql
DROP TABLE IF EXISTS userTBL;
CREATE TABLE userTBL
(userID char(8) NOT NULL PRIMARY KEY,
 birthYear int NOT NULL DEFAULT -1,
 addr char(2) NOT NULL DEFAULT '서울'
 -- ALTER COLUMN birthYear SET DEFAULT -1;
 -- 로 테이블을 변경도 가능하다.
);
```

**임시테이블**

```sql
CREATE TEMPORARY TABLE [IF NOT EXISTS] 테이블 이름
```
> 일반테이블처럼 영구적으로 저장되는 테이블이 아니라 임시테이블을 만들고 데이터를 잠깐 처리할 때, 사용할 때 유용하다.
-  즉, 일시적으로 처리할 데이터들을 한번에 모아놓고 처리하고싶다? 
그럴 때 임시테이블 사용하면 좋다.	
예를 들어, 문자 알림을 보내고 싶은데 특정 조건에 있는 사람한테만 보내고 싶고, 
중복된 번호들은 한번만 보내고 싶다! \
해당 조건을 select 후 임시테이블에 저장해서 한번 가공된 데이터들을 쫘락 select하면 편하다!

**테이블 삭제**

```sql
DROP TABLE 테이블이름;
```
> 외래 키 제약 조건의 기준 테이블은 삭제할 수 없다. 먼저, 외래 키가 생성된 외래 키 테이블을 삭제해야한다.


**테이블 수정**
1. 열의 추가
```sql
USE tableDB;
ALTER TABLE userTBL
   ADD homepage VARCHAR(30) -- 열추가
      DEFAULT 'http://www.hanbit.co.kr' --디폴트 값
      NULL; --NULL허용함
```

2. 열의 삭제
```sql
ALTER TABLE userTBL
   DROP COLUMN mobile1;
```

3. 열의 이름 및 데이터 형식 변경
- 회원 이름의 열 이름을 uName으로 변경하고 데이터 형식을 VARCHAR(20)으로 변경하고, NULL값도 허용하려면 다음과 같이 사용한다.

```sql
ALTER TABLE userTBL
   CHANGE COLUMN name uName VARCHAR(20) NULL; -- name은 기존이름 uName은 새 이름
```

4. 열의 제약 조건 추가 및 삭제
```sql
ALTER TABLE userTBL
   DROP PRIMARY KEY;
```


