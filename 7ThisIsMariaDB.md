## SQL 고급 7장
___
#### 7.1.2 변수의 사용
> Sql도 다른 일반적인 프로그래밍 언어처럼 변수를 선언하고 사용할 수 있다. 변수의 선언과 값의 대입은 다음의 형식을 따른다.

```sql
SET @변수이름 = 변수의 값;  -- 변수의 선언 및 값 대입
SET @변수이름 ; -- 변수의 값 출력
```
#### 7.1.3 데이터 형식과 형 변환
> 가장 일반적으로 사용되는 데이터 형식 변환과 관련해서는 **CAST(), CONVERT()** 함수를 사용한다. 

```sql
CAST ( expression AS 데이터 형식 [ (길이) ] )
CONVERT ( expression , 데이터 형식 [ (길이) ] )

SELECT CAST(AVG(amount) AS SIGNED INTEGER) AS '평균 구매 개수' FROM buyTBL ;
```

> 암시적 형변환은 **CAST(), CONVERT()** 함수를 사용하지 않고 형이 변환되는 것을 말한다.
```sql
SELECT '100'+'200'; -- 문자와 문자를 더함 (정수로 변환되서 연산됨)
SELECT CONCET('100','200'); --문자와 문자를 연결 (문자로 처리)
SELECT CONCAT(100,'200'); --정수와 문자를 연결(정수가 문자로 변환되서 처리)
SELECT 1 > '2mega'; -- 정수2로 변환되어서 비교
```

___

#### 7.2.1 MariaDB 내장 함수

1. **제어 흐름 함수**

- **IF(수식,참,거짓)**
> 수식이 참 또는 거짓인지 결과에 따라 2중 분기한다.
```sql
SELECT IF (100>200, '참이다','거짓이다');
```
거짓이 출력된다.

- **IFNULL(수식1,수식2)**
  > 수식1이 NULL이 아니면 수식1이 반환되고, 수식1이 NULL이면 수식2이가 반환된다.
  ```sql
  SELECT IFNULL(NULL,'널이군요'),IFNULL(100,'널이군요');
  ```
  첫 번째는 '널이군요'가 출력되고, 두 번째는 100이 출력된다.

- **NULLIF(수식1,수식2)**
  > 수식1과 수식2가 같으면 NULL을 반환하고, 다르면 수식1을 반환한다.
  ```sql
  SELECT NULLIF(100,100), NULLIF(200,100);
  ```
  첫 번째는 NULL이, 두 번째는 200이 반환된다.

- **CASE~ WHEN ~ELSE ~ END**
  ```sql
  SELECT        CASE 10
                WHEN 1 THEN '일'
                WHEN 5 THEN '오'
                WHEN 10 THEN '십'
                ELSE '모름'
        END;
  ```
  CASE 뒤의 값이 10이므로 세 번째 WHEN이 수행되어 '십'이 반환된다.

2. **문자열 함수** 
- **ASCII(아스키코드),CHAR(숫자)**
  > 문자의 아스키 값을 돌려주거나, 숫자릐 아스키 코드값에 해당하는 문자를 돌려준다.
  ```sql
  SELECT ASCII('A'),CHAR(65);
  ```
  65와 'A'를 돌려준다.

- **BIT_LEGNTH(문자열), CHAR_LENGTH(문자열), LENGTH(문자열)**
  > 할당된 Bit의 크기, 또는 문자 크기를 반환한다. CHAR_LENGTH()는 문자의 개수를 반환하며 LENGTH()는 할당된 Byte수를 반환한다.
  ```sql
  SELECT BIT_LENGTH('abc'), CHAR_LENGTH('abc'), LENGTH('abc');
  ```

- **Concat(문자열1,문자열2, --- ),CONCAT_WS(구분자,문자열1,문자열2,---)**
  > 문자열을 이어준다.
  ```sql
  SELECT CONCAT_WS('/','2022','01','01');
  ```
  구분자 '/'를 추가해서 2022/01/01이 반환된다.

- **ELT(위치,문자열1,문자열2,---), FIELD(찾을 문자열, 문자열1,문자열2,---),FIND_IN_SET(찾을 문자열, 문자열 리스트), INSTR(기준 문자열,부분 문자열),LOCATE(부분 문자열, 기준 문자열)**

  > **ELT()**는 위치 번째에 해당하는 문자열을 반환한다. **FIELD()**는 찾을 문자열의 위치를 찾아서 반환한다. FIELD()는 매치되는 문자열이 없으면 0을 반환한다. **FIND_IN_SET()**은 찾을 문자열을 문자열 리스트에서 찾아서 위치를 반환한다. 문자열 리스트는 콤마(,)로 구분되어 있어야 하며, 공백이 없어야한다. **INSTR()**은 기준 문자열에서 부분 문자열을 찾아서 그 시작 위치를 반환한다. **LOCATE()**와 동일하지만 파라미터의 순서가 반대로 되어 있다.
  ```sql
  SELECT ELT(2,'하나','둘','셋'), FIELD('둘','하나','둘','셋'), FILD_IN_SET('둘','하나,둘,셋'), INSTR('하나둘셋','둘'), LOCATE('둘','하나둘셋');
  ```
  '둘',2,2,3,3을 반환한다.

- **FORMAT(숫자,소수점 자리수)**
  > 숫자를 소수점 아래 자릿수까지 표현된다. 또한 1000단위마다 콤마(,)를 표시해준다.
  ```sql
  SELECT FORMAT(123456,123456,4);
  ```
  '123.456.1234'을 반환한다.

- **BIN(숫자), HEX(숫자), OCT(숫자)**
  > 2진수, 16진수, 8진수의 값을 반환한다.
  ```sql
  SELECT BIN(31),HEX(31),OCT(31);
  ```
  2진수 11111,16진수 1F, 8진수 37을 반환한다.

- **INSERT(기준 문자열, 위치,길이,삽입할 문자열)**
  > 기준 문자열의 위치부터 길이만큼을 지우고 삽입할 문자열을 끼워 넣는다.
  ```sql
  SELECT INSERT('abcdefghi',3,4,'@@@@'),INSERT('abcdefghi',3,2,'@@@@');
  ```
  'ab@@@@ghi'와 'ab@@@@efghi'를 반환한다.

- **LEFT(문자열,길이),RIGHT(문자열,길이)**
  > 왼쪽 또는 오른쪽에서 문자열의 길이만큼 반환한다.
  ```sql
  SELECT LEFT('abcdefghi',3), RIGHT('abcdefghi',3);
  ```
  'abc'와 'ghi'를 반환한다.

- **UPPER(문자열),LOWER(문자열)**
  > 소문자를 대문자로, 대문자를 소문자로 변경한다.
  ```sql
  SELECT LOWER('abcdEFGH'),UPPER('abcdEFGH');
  ```
  'abcdefgh'와 'ABCDEFGH'를 반환한다.

- **LPAD(문자열,길이,채울 문자열), RPAD(문자열,길이,채울 문자열)**
  > 문자열을 길이만큼 늘린 후에, 빈 곳을 채울 문자열로 채운다.
  ```sql
  SELECT LPAD('이것이',5,'##'), RPAD('이것이',5,'##');
  ```
  '##이것이'와 '이것이##'을 반환한다.

- **LTRIM(문자열), RTRIM(문자열)**
  > 문자열의 왼쪽/오른쪽 공백을 제거한다. 중간의 공백은 제거되지 않는다.
  ```sql
  SELECT LTRIM('    이것이'),RTRIM('이것이    ');
  ```
  둘다 공백이 제거된 '이것이'를 반환한다.

- **TRIM(문자열),TRIM(방향 자를_문자열 FROM 문자열)**
  > TRIM(문자열)은 문자열의 앞뒤 공백을 모두 없앤다. TRIM(방향 자를_문자열 FROM 문자열)에서 방향은 LEADING(앞),BOTH(양쪽),TRAILING(뒤)이 나올 수 있다.
  ```sql
  SELECT TRIM('    이것이    '), TRIM(BOTH 'ㅋ' FROM 'ㅋㅋㅋ재밌어요.ㅋㅋㅋ');
  ```
  '이것이'와 '재밌어요'를 반환한다.

- **REPEAT(문자열,횟수)**
  > 문자열을 횟수만큼 반복한다.
  ```sql
  SELECT REPEAT('이것이',3);
  ```
  '이것이이것이이것이'를 반환한다.

- **REPLACE(문자열,원래 문자열, 바꿀 문자열)**
  > 문자열에서 원래 문자열을 바꿀 문자열로 바꿔준다.
  ```sql
  SELECT REPLACE ('이것이 MariaDB다','이것이','This is');
  ```
  'This is MariaDB다'를 반환한다.

- **REVERSE(문자열)**
  > 문자열의 순서를 거꾸로 만든다.
  ```sql
  SELECT REVERSE ('MariaDB');
  ```
  'BDairaM'을 반환한다.

- **SPACE(길이)**
  > 길이 만큼 공백을 반환한다.
  ```sql
  SELECT CONCAT('이것이',SPACE(10),'MariaDB다');
  ```
  '이것이          MariaDB다'를 반환한다.

- **SUBSTRING(문자열,시작위치,길이) 또는 SUBSTRING(문자열 FROM 시작위치 FOR 길이)**
  > 시작 위치부터 길이만큼 문자를 반환한다. 길이가 생략되면 문자열의 끝까지 반환한다.
  ```sql
  SELECT SUBSTRING('대한민국만세',3,2);
  ```
  '민국'을 반환한다.

- **SUBSTRING_INDEX(문자열,구분자,횟수)**
  > 문자열에서 구분자가 왼쪽부터 횟수 번째 나오면 그 이후의 오른쪽은 버린다. 횟수가 음수면 오른쪽부터 세고 왼쪽을 버린다.
  ```sql
  SELECT SUBSTRING_INDEX('cafe.naver.com','.',2), SUBSTRING_INDEX('cafe.naver.com','.',-2);
  ```
  'cafe.naver'와'naver.com'을 반환한다.

#### 수학 함수
> 출처 https://ahnty0122.tistory.com/55

- ABS(숫자) : 절대값 계산
- CEILING(숫자) : 올림
- FLOOR(숫자) : 내림
- ROUNG(숫자) : 반올림
- CONV(숫자, 기존 진수, 바꿀 진수) : 기존 진수에서 다른 진수로 바꿔주는 함수

```sql
SELECT ABS(-100);

SELECT CEILING(4.7), FLOOR(4.7), ROUND(4.7);

SELECT CONV('AA',16,2), CONV(100,10,8); 
-- 16진수 AA를 2진수로 변경, 10진수의 100을 8진수로 변경
```
(여기에 결과)

- MOD(숫자1, 숫자2), 숫자1 % 숫자2 : 숫자1을 숫자2로 나눈 나머지 반환
- POW(숫자1, 숫자2) : 숫자1을 숫자2만큼 거듭제곱한 값 반환
- SQRT(숫자) : 숫자의 제곱근 반환 

```sql
select mod(228, 10), 228%10, 228 mod 10;

select pow(3, 3), sqrt(25);
```
(여기에 결과)

- RAND() : 0이상 1미만의 실수 랜덤값 출력
- SIGN(숫자) : 숫자가 양수, 0, 음수인지 구해 1, 0, -1 중 하나 반환
- TRUNCATE(숫자, 정수): 소수점 기준으로 정수 위치까지 구하고 나머지 버림
```sql
select rand(), floor(1 + (rand() * 6));
-- 랜덤값 출력, 오른쪽 쿼리문은 주사위 숫자 랜덤하게 출력해주는 쿼리문

select sign(123),sign(0),sign(-123);

select truncate(2222.2272,2), truncate(2222.2272,-2);
```
(여기에 결과)

#### 날짜/시간 함수

- ADDDATE(날짜, 차이), SUBDATE(날짜, 차이): 날짜를 기준으로 차이를 더하거나 뺀 날짜 반환
- ADDTIME(날짜/시간, 시간), SUBTIME(날짜/시간, 시간): 날짜/시간을 기준으로 시간을 더하거나 뺀 결과 반환

```sql
select adddate('2020-02-01', interval 15 day);
select adddate('2020-02-01', interval 15 month);
select adddate('2020-02-01', interval 15 year);
-- 주어진 날짜로부터 그 후의 날짜 리턴

select subdate('2020-02-01', interval 15 day);
select subdate('2020-02-01', interval 15 month);
select subdate('2020-02-01', interval 15 year);
-- 주어진 날짜로부터 그 전의 날짜 리턴

select addtime('10:10:10', '1:1:1');
-- 시간 더하기

select subtime('10:10:10', '1:1:1');
-- 시간 빼기
```
(여기에 결과)

- CURDATE(), CURTIME(), NOW(), SYSDATE()
- CURDATE(), CURTIME(): 현재 연-월-일 반환, 현재 시:분:초 반환
- NOW(), SYSDATE(): 현재 연-월-일 시:분:초 반환
- YEAR(날짜), MONTH(날짜), DAY(날짜), HOUR(시간), MINUTE(시간), SECOND(시간), MICROSECOND(시간)
날짜 또는 시간에서 연, 월, 일, 시, 분 초, 밀리초 구함
- DATE(), TIME() : DATETIME 형식에서 연-월-일과 시:분:초 출력
- DATEDIFF(날짜1, 날짜2), TIMEDIFF(날짜1 또는 시간1, 날짜1 또는 시간2) : 날짜1-날짜2 결과 반환

```sql
select curdate(), curtime(), now(), sysdate();

select year(curdate()), month(curdate()), day(curdate());
-- year()는 날짜, month()함수는 월, day()는 일을 추출해서 출력

select hour(curtime()), minute(curtime()), second(curtime());

select datediff('20200101', sysdate());
select datediff(now(), '20200101');
-- 현재부터 인자값까지의 날짜 차이 리턴

select timediff('10:10:10', curtime());
select timediff(curtime(), '10:10:10');
-- 시간 차이 리턴
```

- DAYOFWEEK(날짜), MONTHNAME(), DAYOFYEAR(날짜)
- DAYOFWEEK(): 요일(1~7: 일요일부터 토요일) 반환
- MONTHNAME(): 월의 영문이름 반환
- DAYOFYEAR(): 1년 중 몇 번째 날인지(1~366) 반환
- LAST_DAY(날짜): 입력한 월의 마지막 날짜 반환
- MAKEDATE(연도, 정수): 연도의 첫날부터 정수만큼 지난 날짜 반환
- MAKETIME(시, 분, 초): 시, 분, 초 이용해 시:분:초 TIME 형식 만들어줌
- PERIOD_ADD(연월, 개월수): 연월부터 개월수만큼 지난 연월 반환
- PERIOD_DIFF(연월1, 연월2): 연월1-연월2 개월 수 반환
- QUARTER(날짜): 날짜가 4분기 중에서 어느 분기에 해당하는지 반환
- TIME_TO_SEC(시간): 시간을 초 단위로 반환

```sql
select dayofweek(curdate()), monthname(curdate()), dayofyear(curdate());
-- dayofweek()요일을 상수값으로 리턴한다. 일요일 1, 월요일 2...
-- monthname()함수는 달의 이름을 영어로 출력
-- dayofyear()함수는 1년 중에 몇일 째인지 리턴

select last_day(curdate());
select last_day('20200101');
-- last_day() 주어진 달의 마지막날 출력

select makedate(2020, 60);
-- makedate()함수는 2020년에서 60일째 되는 날을 리턴

select maketime(12,10,10);
-- maketime() 인자값으로 시간을 만들어 리턴

select period_add('202001', 11), period_diff(202001, 201812);

select concat(quarter(curdate()), '사분기') as '분기';
-- quarter() 몇사분기인지 리턴

select time_to_sec('12:12:12');
-- 시간을 초로 환산
```

#### 시스템/정보 함수

- USER(), DATABASE(): 현재 사용자와 현재 선택된 데이터베이스 반환
- FOUND_ROWS(): 바로 앞 쿼리문에서 조회된 행의 개수 반환
- ROW_COUNT(): 바로 앞의 INSERT, DELETE, UPDATE 문에서 삽입, 삭제, 수정된 행의 개수 반환
- SLEEP(초): 쿼리 실행 잠깐 멈춤
- VERSION(): 현재 MySQL 버전 출력

```sql
use sqldb;
select current_user(), database();

select *
  from buytbl;
  
select found_rows();
-- found_rows()는 이전 조회된 rows의 건수를 리턴한다.

update buytbl
   set price = price/2;

select row_count();
-- row_count() 이전 update,delete,insert한 건수를 리턴한다.(버전별로 상이함)

select sleep(5);
select '5초후에 이게 보입니다';
select version();
-- sleep()는 주어진 인자값만큼 멈추어준다.
-- version 출력
```








                

