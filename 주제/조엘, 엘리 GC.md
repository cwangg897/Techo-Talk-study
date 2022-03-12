## 학습내용
- 조엘의 GC <https://www.youtube.com/watch?v=FMUpVA0Vvjw>
- 엘리의 GC <https://www.youtube.com/watch?v=Fe3TVCEJhzo>
https://jeong-pro.tistory.com/148

### GC(Garbage Collectior)란?
<pre>
GC 는 JVM 상에서 더 이상 사용되지 않는 데이터가 할당되어있는 메모리를 해제시켜주는 장치입니다. 
JVM 에서 자동으로 동작하기 때문에 Java 는 특별한 경우가 아니면 메모리 관리를 개발자가 직접 해줄 필요가 없습니다. 
</pre>

### GC(Garbage Collection)란?, 왜 필요한가?
가비지 컬렉터와 컬렉션은 다르다 <br>
컬렉션은 컬텍터가 하는행위라고 보면된다.
GC는 프로그램이 동적으로 할당했던 메모리 영역 중 필요 없게된 영역을 알아서 해제하는 기법이다 <br>
저기 동적으로 할당했던 메모리 영역은 프로그램 런타임에 사용되는 heap영역을 의미한다.
c나 c++는 코드레벨에서 할당 받고, 해제해야 했다. <br>
그러나 자바는 GC가 알아서해준다. 
### 장점
- 메모리 누수 방지
- 해제된 메모리에 접근 하는 오류 막음
- 해제한 메모리 또 해제 멈춤

### 단점
- GC작업은 순수 오버헤드(오버헤드는 어떤 처리를 하기 위해 들어가는 간접적인 처리 시간 · 메모리 등을 말한다) stop-the-world
- 개발자는 언제 GC가 메모리를 해제하는지 모름

### GC가 해제할 메모리 영역들을 판단할가?
루트로 부터 연결된 객체를 Reachale, 연결되지 않은것을 Unreachable이라고 부른다  <br>
연결되지 않은것을 GC로 수거하는데 그 방법2가지의 알고리즘이 있다. <br>
- Reference Counting
- Mark And Sweep 
Mark And Sweep 은 Reference Counting 보완한것(순환 참조 문제 해결)
<img src="https://user-images.githubusercontent.com/79621675/152629020-7fe084e7-fafe-4045-aeff-dfc002e63235.png" width=400/>
<img src="https://user-images.githubusercontent.com/79621675/152629050-a713776e-0264-4d4a-b409-1575f15139a1.png" width=400/> <br>

### GC의 동작 순서 ( Mark and Sweep 알고리즘) 
- 1. Mark 먼저 GC는 GC Root로부터 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹한다.(Reachable과 Unreachable한 객체식별과정) <br>
- 2. Sweep Unrechable 객체들을 Heap에서 제거한다.
- 3. 알고리즘에 따라 Compact과정이 추가되기도함 ( sweep후에 분산된 객체들을 Heap의 시작 주소로 모아 메모리가 할당된 부분과 그렇지 않은 부분으로 나눈다.) 메모리 단편화 막아줌

### Mark And Sweep 단점이자 특징
- 의도적으로 GC를 실행시켜야 한다. (즉 어느순간 실행중인 애플리케이션이 GC에게 리소스를 내주어야한다)
- 애플리케이션 실행과 GC실행이 병행된다. (stop-the-world)
애플리케이션 실행하면서 의도적으로 GC를 실행시켜야한다(병행된다) 둘다 같은내용인듯

### JVM의 GC - Heap
<img src="https://user-images.githubusercontent.com/79621675/152629429-f0227cf6-c2e6-4ba9-99df-d4eef6e66b40.png" width=400/>
Runtime data areas의 JVM의 메모리라고도 부른다
<img src="https://user-images.githubusercontent.com/79621675/152629493-c7a107e7-ad14-491e-a6f0-cc7b5d9bdec8.png" width=400/>
이렇게 Heap으로 모이는데 Heap영역을 보면 이렇게 나누어져있다. Young generation 새로운 객체들이 할당되는영역, Old genveration은 young generation에서 오랫동안 살아남은 객체들이 존재하는 영역
<img src="https://user-images.githubusercontent.com/79621675/152629361-db8f8933-493c-4cf0-8c01-4f99a243fe94.png" width=400/>

### GC는 언제 일어날까? (Minor GC, Major GC)
Young gneration에서 발생하는 GC가 Minor GC, Old generation에서 발생하는 GC가 Major GC 라고부른다. <br>
- 1. 새로운 객체가 Eden 영역에 할당된다.
- 2. Ed이 꽉차면 Minro GC발생 (Mark and Sweep과정이일어남)
- 3. Eden영역에서 Mark된거 이제 Survivor0로 이동 (단 Survivor은 무조건 하나만 사용할 수 있다) 
- 4. 살아남은 객체들 age증가
- 5. 다시 Eden시작 그런데 자리없으면 이제 살아남은 객체들은 Survivor1로이동
- 6. 위와같은 반복되고 그러다가 이제 age가 특정 임계점 도달하면 Old Generation으로 이동  (이과정 promotion이라고함)
- 7. Old generation도 다차면 Major GC가 동작

### Minor GC, Major GC나눈 이유
애플리케이션을 분석해보니 대부분의 객체가 수명이 짧다는 것을 깨달알았다. <br>
GC도 결국 비용인데, 메모리의 특정 부분만을 탐색하며 해제하면 더 효율적이라고생각 어차피 대다수 객체가 사라지니
Young generation 안에서 최대한 처리하도록 하는 것이다. <br>
파란색이 메모리 차지하는 객체라고 생각 <br>
<img src="https://user-images.githubusercontent.com/79621675/152629721-3ab36114-ef13-49f6-a445-8c7c53b5b29b.png" width=400/>


### stop-the-world
GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것(시간) : GC를 실행하는 쓰레드 외의 모든 쓰레드가 작업을 중단한다.
stop-the-world시간을 최소화 하는게 좋은거인듯

### GC(Garbage Collection)의 종류 (여러가지가있는데 3개만 추림)
- Serial GC : gc를 처리하는 쓰레드가 single thread 
- Paraller GC : Java8의 default GC Youg영역의 GC를 멀티 쓰레드로 수행
- G1 GC : Java 9+의 default GC Heap을 일정한 크기의 Region으로 나눔

### JVM GC 튜닝(성능 개선의 최종단계 코드에서 먼저 이루어진후 마지막임) 맛보기 영상보기
목표는
- Old generation으로 넘어가는 객체 최소화하기
- Major GC 시간을 짧게 유지하기

