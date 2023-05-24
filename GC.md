### 1. 가비지 컬렉션이란?(JAVA)

GC(Garbage Collection)는 JVM의 주요 기능으로 할당한 메모리 영역 중 사용하지 않는 영역을 탐지하여 해제하는 작업을 한다.  
자바의 GC(Garbage Collection)는 아주 단순한 규칙을 갖고 있는데, Heap 영역의 오브젝트 중 stack 에서 도달 불가능한 (Unreachable) 오브젝트들은 가비지 컬렉션의 대상이 된다.
수동 메모리 관리는 오류가 발생하기 쉽고 시간이 오래 걸리기 때문에 Java를 구동할 때 GC는 프로그래머가 메모리를 명시적으로 할당 해제하는 것을 덜어주고 메모리 누수를 방지하는 데 도움이 된다.

### 2. 자바의 메모리 영역, heap

```java
public class Main {
    public static void main(String[] args) {
        
        int number1 = 10;
        int number2 = 30;
        String name = "zangzangs";
        String gender = "man";
        
        System.out.println(name);
        System.out.println(gender);
    }
}
````

위와 같은 코드의 Main이 동작하면 순차적으로 number1, number2 가 stack 쌓이게 된다.
다음으로 name, gender가 stack에 쌓이고, Object인 String이 heap 영역에 생성되며 name, gender는 heap 영역의 참조값을 갖게 된다.
Main이 종료되면 stack에 저장된 데이터는 삭제 된다. 그러나 heap 메모리 영역에 생성된 객체들은 아래 그림과 같이 여전히 남게 된다.
더 이상 아무도 참조하지 않게 된 heap 영역의 String 객체는 사용되지 않고 남아 있는데 이를 Unreachable Object라 한다. 
반대로 계속해서 참조되고 있는 객체를 Reachable Object 라고 한다.

<img width="100%" src="https://github.com/inchojeong/ihtc_BigData_middle_Assign/issues/1#issue-1723109966"/>
