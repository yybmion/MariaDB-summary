데이터베이스에서 정규화를 수행하지 않으면, 데이터의 중복이 발생하고 전체적인 무결성이 저하됩니다. 이러한 원인은 데이터 이상 현상(Anomaly)에 의해 발생하며,
이상 현상으로 인해  현실세계의 실제 값과 데이터베이스에 저장된 값이 일치하지 않는 문제가 발생합니다.

데이터 이상은 삽입 이상, 업데이트 이상, 삭제 이상이라는 세 가지 유형이 존재합니다.
> https://developer-talk.tistory.com/256

투플의 널 값
널값이 많은 값은 가능한 기본 릴레이션 애트리뷰트에 포함하지 않는다.

- p.406 EMP_PROJ를 EMP_LOCS와 EMP_PROJ1로 나누는 것은 바람직하지 않다.
- 그 이유는 두 릴레이션을 자연 조인하여 원래의 정확한 정보를 얻을 수 없기 때문이다.
- 올바르게 얻을 수 없는 이유는 Plocation 애트리뷰트가 EMP_LOCS나 EMP_PROJ1의 기본키도 아니고 외래키도 아니기 때문이다.
- 고로 가짜 투플이 생기지 않도록 하기 위해서 적절하게 관련된(외래키,기본키) 애트리뷰트를 가지고 동등 조건으로 조인할 수 있는 릴레이션 스키마를 설계한다.

___
함수적 종속
> https://been2.tistory.com/40
> https://dodo000.tistory.com/20

데이터의 정규화는 1. 중복을 최소화하고
2. 삽입,삭제,수정 이상을 최소화하기 위해서 함수적 종속성과 기본키를 기반으로, 주어진 릴레이션 스키마를 분석하는 과정이다.

비정규화??
> https://chankim.tistory.com/7

정규화
> https://mr-dan.tistory.com/10      <-- 이 링크가 중요!
> https://jiyoungtt.tistory.com/47

그니까 P.417 보면 b그림에서 SSN하고 ENAME만 놓고 보면 겹치는게 없어서 딱 좋은데 PNUMBER까지 들어가면 SSN하나에 PNUMBER2개가 있어 SSN이 중복되는 테이블이 만들어진다.
근데 SSN만으로 모든 행이 구분되지 않습니다. 그래서 SSN말고 PNUMBER도 기본키로 설정.

결정자란?
> https://ybdeveloper.tistory.com/86


1-5정규화 보고 ,
BCNF
> https://3months.tistory.com/193
그림을 보면 3정규화를 마치면 학생과 과목을 합쳐 후보키가 된것을 알 수 있다. 근데 여기서 결정자는 교수이다. 교수가 정해져야 과목이 정해지기 때문이다. 근데 교수는 후보키가 아니므로 BCNF가 만족하지 못한다.
![image](https://github.com/yybmion/MariaDB-summary/assets/113106136/fa764dac-6614-40b4-b341-7809b6d0907b)


