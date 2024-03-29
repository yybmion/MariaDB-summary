반구조화된 데이터?? 반정형 데이터
구조화된 데이터??
> https://philip1994.tistory.com/61

반구조화된 데이터는 자기-기술형(Self-describing data) 데이터라고 부르기도 한다. 
반구조화된 데이터는 그래프로 표현할 수 있다.
중간 노드들은 개별 객체나 복합 속성이되고,이프 노드는 단순 속성의 값을 나타낸다.
1. 반구조화된 모델에서 스키마 정보(속성의 이름,관계,클래스)에는 값까지 혼합되어 있다.
2. 반구조화된 모델에서는 모든 데이터가 준수해야 하는 스키마를 미리 정의할 필요가 없다.
3. HTML이 그 예다.

12.1 주요내용
반구조화된 데이터 = 자기-기술형 데이터
반구조화된 데이터는 형식은 있지만 데이터 모델을 따르지 않는것.
방향을 가진 그래프로 표현될 수 있다.
1. 반구조화된 모델에서 스키마 정보에는 값까지 혼합되어 있다.
2. 반구조화된 모델에서는 모든 데이터가 준수해야 하는 스키마를 미리 정의할 필요가 없다.
3. html은 비구조화된 데이터
___
12.2
1. XML을 생성하기 위해 요소와 속성이라고 하는 두 가지 구조화된 개념을 사용한다.
2. 단순요소와 복합요소가 있다.
3. xml에서는 문서 내에서 요소의 의미를 나타내도록 태그 이름을 정의해 나가지만
4. html은 태그가 해당 텍스트를 어떻게 디스플레이 할것인가 만을 다룬다.
5. 데이터 중심 / 문서 중심 / 하이브리드 XML 문서가 있다.
___
12.3
1. WELL FORMED XML??
2. 트리 모델의 구문 규칙 준수해야 함
3. 즉 하나의 루트 요소를 가져야하며 모든 요소는 부모 요소의 시작과 종료 태그 안에서 자신의 시작과 종료 태그 쌍을 가져야한다.
4. WFX 는 SCHEMALESS가 될 수 있다.
5. 즉, 그 문서 내에서 요소들에 대하여 임의의 태그 이름을 가질 수 있다.
6. 즉, 문서를 처리하는 프로그램에서 미리 알고 있어야 하는 미리 정의된 요소집합을 가지고 있지 않다.
7. 이렇게 함으로서 문서 생성자는 새로운 요소를 정의하는 것이 자유롭지만
8. 문서 내에서 그 요소를 자동으로 해석하는 게는 어려움이 발생한다.
9. 유효한 문서?? VALID DOCUMENT??
10. 유효한 무서는 WFX이면서 XML스미카에 정의된 구조를 따라야한다.
11. 그것이 XML DTD 파일의 구조를 따라야한다.
12. 먼저 문서의 루트 태그에 대하여 이름을 주어야 한다.
13. * 선택적 다중값 요소 / + 필수적 다중값 요소 / ? 선택적 단일값 요소 / 없음 필수적 단일값 요소
14. #PCDATA는 트리에서 리프 노드 뜻함 (375P)
15. STANDALONE = "NO"면 DTD 검사한다는 뜻
16. IDREF가 참조한다는 뜻이네 무언갈 즉 외래키 개념
17. DTD는 타입이 제한적이다.
18. DTD 별도의 구문을 가지므로 특별한 처리기를 가져야함
19. DTD는 특정한 순서를 지켜야하며 순서가 없는 요소는 혀용되지 않는다.
20. https://myeonguni.tistory.com/1089
21. XML 스키마 언어란??
22. XML 문서와 동일한 구문 규칙을 사용하므로 동일한 처리기를 사용하여 스키마와 문서를 모두 처리할 수 있다.
23. XML DTD와 마찬가지로 XML 스키마도 트리 데이터 모델을 가진다.
24. 요소와 속성 또한 중요한 개념
25. 다른점은 스키마에서는 키,참조,식별자 등 데이터베이스와 객체 모델로부터 추가적인 개념들을 도입
26. 변수는 xsd
27. xsd:documentation xml:lang="en"
28. xsd:element 태그는 루트 요소 표현
29. xsd:complexType 으로 루트 요소의 구조가 나타냄. 복합요소
30. xsd:sequence 구조를 이용하여 departments,employees,projects 시퀀스 명시
31. employee, department, project 요소중 태그가 속성만을 가지며 더 이상의 서브요소나 데이터를 그 안에 가지지 않으면 종료 태그 없이 바로 />로 나타낸다.
32. minOccurs / maxOccurs
33. xsd:unique 와 xsd:key 와 xsd:keyref 키의 명시
___
12.5 xml 질의어
1. XPath / XQuery
2. XPath식은 식에 표시된 특정 패턴을 만족하는 속성 혹은 요소들의 컬렉션을 리턴한다.
3. XPath에 나타난 이름들은 XML 문서 트리에 포함된 노드 이름으로 태그 이름이거나 속성 이름이며, 패턴을 만족하는 노드를 더욱 제한하기 위해 제한 조건을 가질 수 있다.
4. 경로를 명시할 때 구분자 / 와 // 두 가지가 사용된다.
5. 예시를 책으로 공부하자 (382P)
6. WILDCARD * 는 요소의 타입과 무관하게 루트의 자식 요소들을 나타냄
7. axes 현재노드의 자식 (/) , 현재 노드 그 자신과 자식 노드(//) , 현재 노드의 속성(@)
8. XPath 식의 주요 제한점은 경로가 패턴을 명시할 뿐 아니라 검색될 항목들도 나타낸다.
9. XQUERY??
10. FOR / LET / WHERE / ORDER BY / RETURN
11. XQUERY에는 0개 이상의 FOR-CLAUSE와 LET-CLAUSE가 포함될 수 있다.
12. WHERE 및 ORDER BY CLAUSE는 선택적이며, 많아야 한 번 나타난다.
13. 그리고 RETURN-CLAUSE는 반드시 한 번만 나타내야 한다.
14. 변수들은 $기호를 prefix로 사용하고 있다.
15. LET-CLAUSE는 특정 식에 변수를 지정하여 이후 질의에서 시용될 수 있도록 한다.
16. 다수의 변수를 지정함으로서 다수의 문서를 참조하는 질의를 작성할 수 있다.
17. FOR_CLAUSE는 시퀀스의 각 항목을 범위로 하는 변수를 지정할 수 있다.
18. WHERE-CLAUSE는 항목을 선택하는 데 필요한 추가적인 조건을 명시한다.
19. ORDER-BY 절은 결과 요소를 정렬한다.
20. RETURN 은 어떤 요소를 반환할것인가.
21. 예시를 보자 (384P)
___
12.7
XML 데티어 생성을 위한 SQL 기능
1. XMLEELEMENT: XML 결과에 나타날 요소(태그) 이름을 명시하는데 사용
2. 각 칼럼과 복합우너소에 대하여 대그 이름을 명시한다.
3. XMLFOREST 다수의 태그(요소들)가 XML 결과에 필요한 경우 이 기능은 XMLELEMENT보다 다수의 요소 이름들을 편리하게 생성.
4. XMLAGG 여러 요소들을 하나로 그루핑해서 서브요소들의 컬렉션으로 만들어 지고, 부모 요소 아래에 연결
5. XMLROOT 선택된 요소들이 하나의 루트 요소를 가지는 XML 문서로 변환된다.
6. XMLATTRIBUTES: XML 결과 요소들에 대하여 속성을 생성
7. 예시를 보자 (391P)

HTML은 표현에 목적을 두지만 XML은 교환에 목적을 둔다.
HTML 문서를 컴퓨터 프로그램에서 자동으로 읽고 해석하는 것은 스키마 부재로 매우 어려움.
그래서 제안된것이 xml
___
XML문서는 요소와 속성이라고 하는 두 가지 구조화 개념을 사용한다.
단순요소는 데이터값을 포함하며,복합요소는 다른요소로부터 계층적으로 생성된다.
XML은 문서 내에서 요소의 의미를 나타내도록 태그이름을 정의해 가지만 HTML은 태그가 해당 텍스트를 어떻게 디스플레이 할 것인가 만을 다룬다.
HTML은 모든 태그 이름이 미리 정의되어 있고 고정되어 있어,확장이 불가능하다.
그림 12.3참고 372P

___

WELL FORMED XML / VALID XML
> https://soft.plusblog.co.kr/93

XML DTD에 대해 알자.
> https://araikuma.tistory.com/769

XSD
> https://mystyle1057.tistory.com/entry/XMLXSD

xml 질의어중 첫번째
XPath 
xPath는 트리 구조의 xml 문서로부터 노드들을 선택하는 데 사용된다.
> http://www.tcpschool.com/xml/xml_xpath_pathExpression

XQuery
p383 참고

___
ER다이어그램으로부터 XML 문서를 추출하는 방법
P.385

SQL 질의를 통해 XML형태로 데이터를 추출할 수 있다.
P.391

