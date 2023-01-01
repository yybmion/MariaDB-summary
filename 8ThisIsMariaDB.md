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


