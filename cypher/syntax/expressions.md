### 3.2.3. 표현 


- 일반적인 표현
- 문자열 참고사항
- `CASE` 표현
 
   - 간단한 ```CASE``` 양식 : 여러 값에서 표현식 비교 
   - 일반 ```CASE```양식 : 여러 조건을 표현할 수 있습니다.
   - 간단/ 일반적인 ```CASE``` 사용하는 시기 구분

#### 3.2.3.1. 일반적인 표현

Cypher 내 표현은 다음과 같이 쓸 수 있습니다:

- 십진법(정수 또는 실수) 리터럴 : ```13```, ```-40000```, ```3.14```, ```6.022E23```. 
- 16진수 정수 리터럴(```0x```로 시작): ```0x13zf```, ```0xFC3A9```, ```-0x66eff```.
- 8진법 정수 리터럴(```0```으로 시작) : ```01372```, ```02127```, ```-05671```.
- 시작 리터럴: ```'Hello'```, ```"World"```.
- 논리 리터럴: ```true```, ```false```, ```TRUE```, ```FALSE```.
- 변수: ```n```, ```x```, ```rel```, ```myFancyVariable```, ````A name with weird stuff in it[]!````.
- 속성: ```n.prop```, ```x.prop```, ```rel.thisProperty```, ```myFancyVariable.'(weird property name)'```.
- 다양한 속성: ```n["prop"]```, ```rel[n.city + n.zip]```, ```map[coll[0]]```.
- 변수: ```$param```, ```$0```
- 리스트 표현: ```['a', 'b']```, ```[1, 2, 3]```, ```['a', 2, n.property, $param]```, ```[ ]```.
- 함수 호출: ```length(p)```, ```nodes(p)```.
- 합계 함수: ```avg(x.prop)```, ```count(*)```.
- 경로-패턴: ```(a)-->()<--(b)```.
- 연산 어플리케이션: ```1 + 2``` and ```3 < 4```.
- 술어 표현식은 true 또는 false를 리턴하는 표현식입니다: ```a.prop = 'Hello'```, ```length(p) > 10```,```exists(a.name)```.
- 정규 표현식: ```a.name =~ 'Tob.*'```
- 대소 문자를 구분하는 문자열과 일치하는 식 : ```a.surname STARTS WITH 'Sven'```, ```a.surname ENDS WITH 'son'``` 또는 ```a.surname CONTAINS 'son'```
- ```CASE``` 표현.


#### 3.2.3.2. 문자열 리터럴 참고사항 

문자열 리터럴은 다음 이스케이프 시퀀스를 포함할 수 있습니다. 

| 이스케이프 시퀀스 | 문자열                                                       |
| ----------------- | ------------------------------------------------------------ |
| `\t`              | 탭                                                           |
| `\b`              | 백 스페이스                                                  |
| `\n`              | 새 줄                                                        |
| `\r`              | 캐리지(Carriage) 반환                                        |
| `\f`              | 형식 피드                                                    |
| `\'`              | 작은 따음표                                                  |
| `\"`              | 큰 따음표                                                    |
| `\\`              | 역 슬래시                                                    |
| `\uxxxx`          | 유니 코드 UTF-16 코드 포인트 (4 자리 16 진수는 ```\ u```를 따라야 함) |
| `\Uxxxxxxxx`      | 유니 코드 UTF-32 코드 포인트 (8자리 16진수는  `\U`를 따라야 함) |

#### 3.2.3.3. ```CASE``` 표현 

일반적인 조건은 잘 알려진 ```CASE```을 이용해서 표현할 수 있습니다. Cypher 내 존재하는 ```CASE```의 두가지 유형: 단순 유형- 다수 값과 표현식을 비교합니다. 일반적 유형- 여러 조건문을 표현할 수 있습니다. 

그림 3.2. 그래프 

![alt](https://neo4j.com/docs/developer-manual/3.4/images/%60CASE%60%20expressions-1.svg)

##### 단일 ```CASE``` 유형: 다양한 값에 대하여 표현 비교 

표현은 ```WHEN```절 과 함께 일치 항목이 발견될 때까지 계산되고 비교됩니다. 일치하는 항목이 없을 경우, ```ELSE```절의 표현식이 반환됩니다. ```ELSE``` 케이스가 없고 일치하는 항목이 없을 경우 ```null```을 반환합니다. 


**구문론:**

```
CASE test
 WHEN value THEN result
  [WHEN ...]
  [ELSE default]
END
```

**변수:**

| 이름      | 설명                                                         |
| --------- | ------------------------------------------------------------ |
| `test`    | 가능한 표현식                                                |
| `value`   | `test`와 비교될 표현 결과                                    |
| `result`  | 만약 ```value```가 ```test```와 일치할 경우 이 표현을 출력합니다. |
| `default` | 일치하는 항목이 없을 경우, ```default```를 반환합니다.       |


**쿼리**

```
MATCH (n)
RETURN
CASE n.eyes
WHEN 'blue'
THEN 1
WHEN 'brown'
THEN 2
ELSE 3 END AS result
```

| result |
| ------ |
| 5 rows |
| `2`    |
| `1`    |
| `3`    |
| `2`    |
| `1`    |

##### 일반적인 ```CASE``` 형식: 여러 조건을 만족하도록 허용

술부는 '참'값이 발견 될 때까지 순서대로 확인하며 결과 값이 사용됩니다.
일치하는 항목이 없다면, ```ELSE``` 절의 표현식을 리턴합니다. ```ELSE``` 케이스 및 일치하는 항목이 없을 경우에는 ```null```을 리턴합니다. 

**구문:**

```
CASE
WHEN predicate THEN result
  [WHEN ...]
  [ELSE default]
END
```

**요소:**

| 이름        | 설명                                                  |
| ----------- | ------------------------------------------------------------ |
| `predicate` | 유효한 대안을 찾을 때 테스트되는 술어.      |
| `result`    | ```predicate```가`true`일 때 출력으로 반환되는 표현식. |
| `default`   | 일치하는 항목이 없을 때 ```default```를 반환합니다.                |

**쿼리** 

```
MATCH (n)
RETURN
CASE
WHEN n.eyes = 'blue'
THEN 1
WHEN n.age < 40
THEN 2
ELSE 3 END AS result
```

| result |
| ------ |
| 5 rows |
| `2`    |
| `1`    |
| `3`    |
| `3`    |
| `1`    |

##### 단순하고 일반적인 ```CASE```형식을 사용할 때 

두 구문의 형식이 비슷하기 때문에 어떤 형식을 사용할 지 정확하지 않을 수도 있습니다.

이 시나리오는`n.age`가`null` 인 경우`age_10_years_ago`가`-1`로 예상되는 다음 쿼리를 사용해서 설명합니다 :

**쿼리**  

```
MATCH (n)
RETURN n.name,
CASE n.age
WHEN n.age IS NULL THEN -1
ELSE n.age - 10 END AS age_10_years_ago
```

 
이 쿼리가 간단한 ```CASE```형식을 사용해서 쓰여짐에 따라 Daniel 노드에는 ```age_10_years_ago``` 대신 ```-1```이 아닌 ```null1```값을 가집니다. 이것은```n.age```와```n.age IS NULL```을 비교하기 때문입니다. ```n.age IS NULL```는 논리 값이고 ```n.age```는 정수 값이므로 ```WHEN n.age IS NULL THEN -1```는 절대 사용하지 않습니다. 결과적으로 ```ELSE n.age - 10```가 대신 사용되어 ```null```을 반환합니다.

| n.name      | age_10_years_ago |
| ----------- | ---------------- |
| 5 rows      |                  |
| `"Alice"`   | `28`             |
| `"Bob"`     | `15`             |
| `"Charlie"` | `43`             |
| `"Daniel"`  | `<null>`         |
| `"Eskil"`   | `31`             |
 
수정 된 쿼리는 다음과 같은 일반적인 CASE 형식으로 제공됩니다.

**쿼리** 

```
MATCH (n)
RETURN n.name,
CASE
WHEN n.age IS NULL THEN -1
ELSE n.age - 10 END AS age_10_years_ago
```

이제 ```age_10_years_ago```가 노드명 ```Daniel```에 대하여 ```-1```을 리턴하는 것을 확인할 수 있습니다. 

| n.name      | age_10_years_ago |
| ----------- | ---------------- |
| 5 rows      |                  |
| `"Alice"`   | `28`             |
| `"Bob"`     | `15`             |
| `"Charlie"` | `43`             |
| `"Daniel"`  | `-1`             |
| `"Eskil"`   | `31`             |