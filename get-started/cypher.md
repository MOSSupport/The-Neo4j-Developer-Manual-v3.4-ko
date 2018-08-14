
## 2.2. Cypher 시작하기

이 가이드에서는 Neo4j의 쿼리 언어 Cypher를 소개합니다. 

+ 그래프와 패턴에 대해 생각해보세요.
+ 이 지식을 간단한 문제에 적용해보세요.
+ Cypher 구문 작성법을 배워보세요.

### 2.2.1. 패턴

+ 노드 구문
+ 관계 구문
+ 패턴 구문
+ 패턴 변수
+ 절

Neo4j의 속성 그래프는 노드와 관계로 구성되며 이 중 하나의 속성이 있을 수 있습니다. 노드는 개념, 이벤트, 장소 및 사물과 같은 속성을 나타냅니다. 관계는 노드 쌍을 연결합니다.

그러나 노드와 관계는 단순 저-레벨 빌딩 블록입니다. 속성 그래프 강점은 연결된 노드와 관계 패턴을 인코딩하는 것 입니다. 일반적으로 단일 노드 및 관계는 정보를 인코딩하지 않지만, 노드 및 관계 패턴은 복잡한 아이디어를 임의로 인코딩 합니다. 

Neo4j의 쿼리 언어인 Cypher는 패턴을 기반으로 합니다. 특히 패턴은 원하는 그래프 구조와 일치시킬 때 사용됩니다. 일치하는 구조가 발견되거나 생성되면 Neo4j는 추가로 처리할 수 있습니다.

관계가 하나인 단순 패턴은 한 쌍의 노드 (때로는 노드 자체)를 연결합니다. 예를 들어, 도시 또는 도시의 사람 ```LIVES_IN```은 ```PART_OF``` 국가입니다.

다중 관계를 사용하는 복잡한 패턴은 임의로 복잡한 개념을 표현하고 다양한 사용 사례를 지원합니다. 예를 들어, Person LIVES_IN이 있는 국가 인스턴스를 일치시킵니다. 다음 Cypher 코드는 두 개의 간단한 패턴들이 일치를 수행하는 (약간) 복잡한 패턴으로 결합합니다.

```
(:Person) -[:LIVES_IN]-> (:City) -[:PART_OF]-> (:Country)
```

패턴 인식은 기본적으로 뇌가 작동하는 방식의 기반이 됩니다. 따라서 인간은 패턴 작업에 능숙합니다. 예를 들어 다이어그램이나 지도에서 패턴을 시각적으로 표현하면 인간이 개념을 인식하여 지정하고 이해할 수 있습니다. 패턴 기반 언어 Cypher는 이 기능을 활용합니다.

관계형 데이터베이스에 사용되는 SQL과 마찬가지로 Cypher는 텍스트 형식의 선언 쿼리 언어입니다. 이것은 ASCII 관련 기술을 사용하여 그래프 관련 패턴을 표현합니다. SQL과 같은 절 및 키워드 (예 : ```MATCH```, ```WHERE``` 및 ```DELETE```)는 이러한 패턴을 결합하고 원하는 조치를 지정할 때 사용됩니다.

이 조합은 일치시킬 패턴과 일치하는 항목 (예 : 노드, 관계, 경로 및 목록)을 처리 할 Neo4j를 알려줍니다. 그러나 Cypher는 Neo4j에 노드 찾기, 관계 트래버스 등을 알려주지 않습니다.

아이콘과 화살표로 구성된 다이어그램은 일반적으로 그래프를 시각화할 때 사용됩니다. 텍스트 주석은 라벨을 제공하고 속성을 정의합니다.

#### 2.2.1.1. 노드 구문

Cypher는 괄호 쌍 (일반적으로 텍스트 문자열 포함)을 사용하여 노드를 나타냅니다 예 : :```()```, ```(foo)```. 둥근 끝이있는 원 또는 직사각형을 연상시킵니다. 다음은 다양한 유형 및 세부 정보를 제공하는 Neo4j 노드와 같은 일부 ASCII-아트 인코딩을 나타냅니다. 

```
()
(matrix)
(:Movie)
(matrix:Movie)
(matrix:Movie {title: "The Matrix"})
(matrix:Movie {title: "The Matrix", released: 1997})
```

가장 간단한 형식인 ```()```은 특성화되지 않은 익명 노드를 나타냅니다. 노드를 다른 곳에서 참조할 떄 변수 (예 : : 행렬)를 추가 할 수 있습니다. 변수는 단일 명령문으로 제한됩니다. 이것은 다른 명령문에서 의미가 다르거나 없을 수도 있습니다.

```Movie``` 레이블 (콜론과 함께 사용되는 접두어)은 노드 유형을 선언합니다. 이것은 패턴을 제한하여 이 위치의 액터 노드와 구조를 매칭하지 않도록 합니다. Neo4j의 노드 인덱스는 레이블도 사용합니다. 각 인덱스는 레이블과 속성의 조합에 따라 다릅니다.

노드의 속성 (예 : ```title```)은 ```{name : "Keanu Reeves"}```와 같이 중괄호 쌍으로 묶인 키 / 값 쌍의 목록으로 표시됩니다. 속성은 정보를 저장하거나 패턴을 제한하는 데 사용할 수 있습니다.

#### 2.2.1.2. 관계 구문

Cypher는 한 쌍의 대시 (```--```)를 사용하여 방향이 없는 관계를 나타냅니다. 방향이 있는 관계는 한쪽 끝 (```<--```, ```-->```)에 화살 촉이 있습니다. 브라켓 표현식 (```[...]```)을 사용하여 세부 사항을 추가 할 수 있습니다. 여기에는 변수, 속성 및 / 또는 유형 정보가 포함될 수 있습니다.

```
-->
-[role]->
-[:ACTED_IN]->
-[role:ACTED_IN]->
-[role:ACTED_IN {roles: ["Neo"]}]->
```

관계의 괄호 쌍 안에있는 구문의 의미는 노드의 괄호 사이에 사용되는 구문과 매우 유사합니다. 변수 (예 : 역할)를 정의하여 명세서의 다른 곳에서 사용할 수 있습니다. 관계 유형 (예 : ```ACTED_IN```)은 노드의 레이블과 유사합니다. 속성 (예 : 역할)은 노드 속성과 완전히 동일합니다. (속성 값은 배열 형태로도 가능합니다.)

#### 2.2.1.3. 패턴 구문

노드와 관계에 대한 구문을 결합하여 패턴을 표현합니다. 이것은 도메인에서 간단한 패턴 (또는 사실)일 수 있습니다.

```
(keanu:Person:Actor {name:  "Keanu Reeves"} )
-[role:ACTED_IN     {roles: ["Neo"] } ]->
(matrix:Movie       {title: "The Matrix"} )
```

노드 레이블과 마찬가지로, 관계 유형 ```ACTED_IN```은 기호로 콜론: 이 접두사가 붙게되어 :```ACTED_IN```이 됩니다. 변수 (예 : 역할)는 관계를 나타내기 위해 명령문의 다른 곳에서 사용될 수 있습니다. 노드 및 관계 속성은 표기법이 같습니다. 이 경우 역할의 배열 속성을 사용하여 지정할 수 있습니다.

노드가 패턴으로 사용될 때, 데이터베이스가 0개 이상의 노드를 설명합니다. 마찬가지로, 각 패턴은 노드와 관계 0개 이상 경로를 설명합니다.

#### 2.2.1.4. 패턴 변수

모듈성을 높이고 반복을 줄이기 위해 Cypher에서는 패턴을 변수에 할당 합니다. 동일한 경로를 검사하고 다른 표현식 등에서 사용할 수 있습니다.

```
acted_in = (:Person)-[:ACTED_IN]->(:Movie)
```

acted_in 변수는 2개의 노드와 각 경로에서 찾아지거나 생성된 연결 관계를 포함합니다. 노드 (경로), 궤도 (경로) (관계 (경로)와 동일) 및 길이(경로)를 포함하여 경로의 세부 정보에 액세스하는 여러 가지 기능이 있습니다.

#### 2.2.1.5. 절(Clauses)

Cypher 문에는 일반적으로 여러 개의 절이 있으며, 각 절은 특정 작업을 수행합니다. 예는 다음과 같습니다.

+ 그래프에서 패턴을 만들고 일치 시킴
+ 필터, 프로젝트, 정렬 또는 결과 매김
+ 부분 문장 작성

Cypher 절을 결합하여 우리가 알고 싶은 것을 표현하거나 복잡한 것을 표현할 수 있습니다. Neo4j는 효율적인 방식으로 원하는 목표를 달성하는 방법을 파악합니다.