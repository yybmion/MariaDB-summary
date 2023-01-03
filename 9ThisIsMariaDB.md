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











