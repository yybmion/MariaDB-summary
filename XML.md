반구조화된 데이터?? 반정형 데이터
구조화된 데이터??
> https://philip1994.tistory.com/61

반구조화된 데이터는 자기-기술형(Self-describing data) 데이터라고 부르기도 한다. 
반구조화된 데이터는 그래프로 표현할 수 있다.
중간 노드들은 개별 객체나 복합 속성이되고,이프 노드는 단순 속성의 값을 나타낸다.
1. 반구조화된 모델에서 스키마 정보(속성의 이름,관계,클래스)에는 값까지 혼합되어 있다.
2. 반구조화된 모델에서는 모든 데이터가 준수해야 하는 스키마를 미리 정의할 필요가 없다.
3. HTML이 그 예다.

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

