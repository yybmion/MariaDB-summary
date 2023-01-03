## SQL 고급 9장 인덱스
___

#### 인덱스의 장점과 단점
- **장점**
1. 검색은 속도가 무척 빨라질 수 있다.
2. 그 결과 해당 쿼리의 부하가 줄어들어서, 결국 시스템 전체의 성능이 향상된다.
- **단점**
1. 인덱스가 데이터베이스 공간을 차지해서 추가적인 공간이 필요해지는데, 대략 데이터베이스 크기의 10% 정도의 추가 공간이 필요하다.
2. 처음 인덱스를 생성하는 데 시간이 많이 소요될 수 있다.
3. 데이터의 변경 작업이 자주 일어날 경우에는 오히려 성능이 많이 나빠질 수도 있다.
___
#### 인덱스의 종류
1. **클러스트형 인덱스** 
> 영어사전처럼, 책의 내용 자체가 순서대로 정렬되어있음
- **'클러스트형 인텍스는 테이블당 한 개만 생성'**
- **Primary key로 지정하면 자동으로 클러스형 인덱스로 저장**
2. **보조 인덱스**
> <찾아보기>가 별도로 있고, <찾아보기>를 찾은 후에 표시된 페이지로 가는것


> 테이블 생성 시에 제약 조던 Primary key 또는 Unique를 사용하면 자동으로 인덱스가 생성된다.

```sql
CREATE TABLE TBL4
    ( a INT UNIQUE NOT NULL,
      b INT UNIQUE,
      c INT UNIQUE,
      d INT
    );
```

다음과 같이 적었을 시 a열이 **클러스트 인덱스**로 지정된다.

만약 UNIQUE NULL과 PRIMARY 가 같이 나왔다면 **Primary**가 클러스트형 인덱스로 지정된다.

즉 테이블을 생성하고 데이터를 삽입했을 때 삽입한 순어와 달리 Primary key로 지정한 열이 사전순으로 열거된다(**오름차순**).

- 요약
1. Primary key로 지정한 열은 클러스트형 인덱스가 생성된다.
2. UNIQUE NOT NULL로 지정한 열은 클러스형 인덱스가 생성된다.
3. UNIQUE(또는 UNIQUE NULL)로 지정된 열은 보조 인덱스가 생성된다.
4. PRIMARY KEY와 UNIQUE NOT NULL이 동시에 있으면 PRIMARY KEY로 지정된 열에 우선 클러스트형 인덱스가 생성된다.
5. PRIMARY KEY로 지정된 열로 데이터가 오름차순 정렬된다.

### 인덱스의 내부 작동
**B-TREE(BLANCED-TREE, 균형트리)**

![마리아디비](https://velog.velcdn.com/images%2Fsem%2Fpost%2Fbe813e7f-041f-4470-ade8-644f01b84a5f%2Fimg%20(1).png)

다음과 같이 최악의 경우 시간복잡도는 O(N)이 결릴 수 있다.

>🔎 밸런스 트리(Balanced Tree)란?

트리의 노드가 한 방향으로 쏠리지 않도록,
노드 삽입 및 삭제 시 특정 규칙에 맞게 재정렬되어 왼쪽과 
오른쪽 자식 양쪽 수의 밸런스를 유지하는 트리이다. 

항상 양쪽 자식의 밸런스를 유지하므로, 
무조건 O(logN)의 시간 복잡도를 가지게 된다.

하지만, 노드 삽입 및 삭제 시 발생하는 재정렬 작업 때문에
탐색을 제외한 작업에서는 일반 Tree보다 성능이 좋지 않다

> 출처 (https://www.notion.so/CH9-9892a2005f6a4e0fb914b7d2b5699bab)

![description](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/63d7ef59-e807-49de-a510-d3f81497dffc/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T120924Z&X-Amz-Expires=86400&X-Amz-Signature=f3e7f6278478a28ff6cec494f52c123677260e231930c2e79c61aa8b567385f3&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Untitled.png%22&x-id=GetObject)

다음과 같이 MMM을 검색한다고 생각한다면 B-TREE구조가 아니라면 AAA부터 MMM까지 8건의 데이터를 건색해야 그 결과를 알 수 있다.

이번에는 B-tree 구조는 우선 루트 페이지를 검색하면 AAA,FFF,LLL 세 개를 읽으니 MMM은 LLL다음에 나오므로 세 번째 리프로 바로 이동해서 찾으면 된다. 즉 검색 속도가 매우 향상된다.

**페이지 분할**
>인덱스는 변경 작업(INSERT,UPDATE,DELETE)시에 성능이 느려질 수 있다.

특히 INSERT 삽입시에 (B트리에 III가 삽입되었다고 가정)
![DESCRIPTION](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f82c7f0b-6e29-433c-8815-fddec4a39076/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T121401Z&X-Amz-Expires=86400&X-Amz-Signature=da1890274a2b5866b91e3b050c4ca98bdf72786c7f5024788741251d9b0b1203&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Untitled.png%22&x-id=GetObject)

여기서는 그저 JJJ가 한 칸 밀려나고 III가 삽입되었지만 만약 한번더 GGG를 삽입한다면 

![DESCRIPTION](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f82c7f0b-6e29-433c-8815-fddec4a39076/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T121401Z&X-Amz-Expires=86400&X-Amz-Signature=da1890274a2b5866b91e3b050c4ca98bdf72786c7f5024788741251d9b0b1203&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Untitled.png%22&x-id=GetObject)

더 이상 두 번째 리프 페이지에는 빈 공간이 없다. 이럴 떄 **페이지 분할**이 일어나는데 MariaDB는 우선 비어있는 페이지를 한 개 확보한 후에, 두 번째 리프 페이지의 데이터를 공평하게 나눈다.

심하면
![description](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b5e2bd03-2f20-48cb-92c5-2d6b5ddeb6d0/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T121826Z&X-Amz-Expires=86400&X-Amz-Signature=ebc7b76bb992a558760cbff5b86adb17262cb144ee0bcf4d9c92ae899977ff59&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Untitled.png%22&x-id=GetObject)

다음과 같이 새로운 페이지가 많이 할당되어 작업이 느려질 수 있다.

### 클러스트형 인덱스와 보조 인덱스의 구조

1. 클러스트형 인덱스
   
(앞에 테이블 생성하고 삽입까지 마쳤다고 가정)
```sql
ALTER TABLE clusterTBL
    ADD CONSTRAINT PK_clusterTBL_userID
        PRIMARY KEY(userID);
```
하면 userID가 오름차순 정렬이 될 것이다.

**B-TREE 형태의 인덱스가 형성된다!**
![DESCRIPTION](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/93080d6a-0f7b-4873-986b-a51e00d2d307/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2022-12-29_%EC%98%A4%EC%A0%84_7.36.16.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T122525Z&X-Amz-Expires=86400&X-Amz-Signature=573baba93228319d11dd7c70ff9eeb71643a8ec5c2422478a20e0a7041477522&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22%25EC%258A%25A4%25ED%2581%25AC%25EB%25A6%25B0%25EC%2583%25B7%25202022-12-29%2520%25EC%2598%25A4%25EC%25A0%2584%25207.36.16.png%22&x-id=GetObject)

2. 보조 인덱스

UNIQUE제약 조건은 보조 인덱스를 생성한다.
```sql
ALTER TABLE secondaryTBL
    ADD CONSTRAINT UK_secondaryTBL_userID
        UNIQUE (userID);
```

먼저 데이터를 확인해보면 순서는 삽입 순서와 같다.

보조 인덱스 구성 후
![description](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d6e398ca-b92b-4407-8ac3-76fee0d3d3c3/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2022-12-29_%EC%98%A4%EC%A0%84_7.36.33.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T123208Z&X-Amz-Expires=86400&X-Amz-Signature=af94c702d434c0ed5c427367a5274d4cabb83c7c03ff65455bc4e28ad0e397ce&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22%25EC%258A%25A4%25ED%2581%25AC%25EB%25A6%25B0%25EC%2583%25B7%25202022-12-29%2520%25EC%2598%25A4%25EC%25A0%2584%25207.36.33.png%22&x-id=GetObject)

우선 인덱스 페이지의 리프 페이지에 인덱스로 구성된 열(userID)을 정렬한다.그리고 데이터 위치 포인터를 생성한다. 데이터의 위치 포인트는 크러스트형 인덱스와 달리 주소 값이 기록되어 바로 데이터의 위치를 가리키게 된다.

> SELECT 문으로 JKW(조관우)를 검색시 클러스트형이 더 빠르다는 것은 그림만 봐도 알 수 있다.

**하지만!!**
**데이터 삽입시**에는 **클러스트형 인덱스**는 **페이지 분할**이 일어나지만 보조 인덱스는 **페이지 분할**이 안일어나 성능에 주는 부하가 더 적었다.

- 보조 인덱스 데이터 삽입
![DESCRIPTION](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/732e4ed6-6951-46cc-987b-9f4e8cfd96bf/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2022-12-29_%EC%98%A4%EC%A0%84_8.05.22.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T124251Z&X-Amz-Expires=86400&X-Amz-Signature=a9ca4ed567a6176336acf8ca4046f6a46555d6be875755790323e01b01036b2a&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22%25EC%258A%25A4%25ED%2581%25AC%25EB%25A6%25B0%25EC%2583%25B7%25202022-12-29%2520%25EC%2598%25A4%25EC%25A0%2584%25208.05.22.png%22&x-id=GetObject)

보조 인덱스는 데이터 페이지를 정렬하는 것이 아니므로 그냥 데이터 페이지의 뒤쪽 빈 부분에 삽입된다.

그리고 인덱스의 리프 페이지에도 약간의 위치가 조정된 것일뿐 페이지 분할은 일어나지 않았다.

- 클러스트형 인덱스 데이터 삽입
![description](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d5afb65e-e042-446a-9638-90c2c9e08974/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T124457Z&X-Amz-Expires=86400&X-Amz-Signature=21bfbb9d226f06897403ba86f66245348835fe50a128f280a69ce6ca863a553e&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22Untitled.png%22&x-id=GetObject)
___
### 클러스트형 인덱스와 보조 인덱스가 혼합되어 있을 경우

먼저 클러스트형 인덱스를 생성
![description](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/52489854-e10a-4ab9-a839-ca3c60c96434/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2022-12-29_%EC%98%A4%EC%A0%84_8.13.05.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T133816Z&X-Amz-Expires=86400&X-Amz-Signature=fa0edc09235b4e1495577c2fe1783a09466d9fc970a0373efff8d46d94d014e1&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22%25EC%258A%25A4%25ED%2581%25AC%25EB%25A6%25B0%25EC%2583%25B7%25202022-12-29%2520%25EC%2598%25A4%25EC%25A0%2584%25208.13.05.png%22&x-id=GetObject)

이후 UNIQUE제약 조건으로 보조 인덱스 추가

![DESCRIPTION](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/6005e21f-b6f1-451e-853f-5755ca86b513/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7_2022-12-29_%EC%98%A4%EC%A0%84_8.13.58.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230103%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230103T134016Z&X-Amz-Expires=86400&X-Amz-Signature=e7411eceef1192342c098b0947ca80b4219ebae73308d99f918dd0a7e0bd9ac9&X-Amz-SignedHeaders=host&response-content-disposition=filename%3D%22%25EC%258A%25A4%25ED%2581%25AC%25EB%25A6%25B0%25EC%2583%25B7%25202022-12-29%2520%25EC%2598%25A4%25EC%25A0%2584%25208.13.58.png%22&x-id=GetObject)

추가! 보조인덱스는 리프페이지에는 정렬된 상태고 데이터페이지에는 정렬이 안된 상태이다.

그래서 이 둘을 추가했을 때를 보자면 클러스트형 인덱스의 경우에는 그대로 변함이 없다. 우선 보조 인덱스의 루트 페이지와 리프 페이지의 키 값(NAME)이 이름으로 구성되었으므로 일단 이름으로 정렬되었다. 특히, 관심이 가는 것은 보조 인덱스의 리프 페이지다. 클러스터형 인덱스 페이지가 없었다면 아마도 '**데이터 페이지의 주소 값**'으로 구성되어 있었겠지만, 지금은 **클러스터형 인덱스의 키 값**(여기서는 userID)을 가지게 된다. 

만약
```sql
SELECT addr FROM mixedTBL WHERE name='임재범';
```

쿼리를 실행한다면 다음 순서로 검색을 하게 될것이다.

1. (페이지 번호 10번 읽음) 보조 인덱스의 루트 페이지에서 '은지원'보다 큰 값이므로 200번 페이지에 있다는 것 확인
2. (페이지 번호 200번 읽음) '임재범'은 클러스터형 인덱스의 키 값 LJB임을 확인한 후 , 무조건 클러스터 인덱스의 루트 페이지로 가서 찾음
3. (페이지 번호 20번 읽음) 'LJB'는 'KBS'보다 크고 'ssk'보다 작으므로 1001번 페이지에 있는 것 확인
4. (페이지 번호 1001번 읽음) 'LJB'값을 찾고 그 주소인 '서울'을 찾아냄

>결론적으로 보조인덱스의 리프 페이지에 기존처럼 '**데이터페이지+#오프셋**'으로 구성하면 치명적인 단점 때문에 사용하지 않는다.






