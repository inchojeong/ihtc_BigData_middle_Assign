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

<img width="80%" src="https://github.com/inchojeong/ihtc_BigData_middle_Assign/assets/100347858/b20f3568-7805-48e9-992b-c595f9f1247c"/>

Mark-and-Sweep 알고리즘, Copying 알고리즘 및 Generational 알고리즘과 같이 Java의 GC에서 사용하는 다양한 알고리즘이 있다. 
이러한 알고리즘에는 효율성과 메모리 사용 측면에서 고유한 장단점이 있다.

### 3. GC사용 예시

```java
public class GarbageCollectionExample {
    public static void main(String[] args) {
        // Creating a new object
        MyClass obj = new MyClass();

        // 객체 사용

        // 객체가 더 이상 필요하지 않을 때
        obj = null;

        // 가비지 컬렉션을 명시적으로 트리거 (선택 사항)
        System.gc();

    }
}

class MyClass {
    // Class implementation
}
```

위 코드에서 obj 변수가 null로 설정되면 참조하는 개체가 GC의 대상이 된다. 
System.gc()에 대한 호출은 선택 사항이며 즉각적인 실행을 보장하지는 않지만 GC기 실행을 조정한다.

### 4. 메모리 누수

그러나 GC를 사용하더라도 메모리 누수가 여전히 발생할 수 있다.  
더 이상 필요하지 않은 개체가 여전히 참조되어 GC가 작동하지 않는 경우 메모리 누수가 발생한다.  
한 가지 대표적인 예로 개체가 컬렉션(예: 목록)에 추가된 다음 제거되지만 해당 참조가 제대로 지워지지 않는 경우로 볼 수 있다. 
이로 인해 메모리에 불필요한 개체가 축적되어 메모리 사용량이 증가하고 잠재적으로 응용 프로그램 속도가 느려질 수 있다.

```java
import java.util.ArrayList;
import java.util.List;

public class MemoryLeakExample {
    private static List<Object> objects = new ArrayList<>();

    public static void main(String[] args) {
        while (true) {
            Object object = new Object();
            objects.add(object);

            // 다음 줄을 주석 처리하면 메모리 누수가 발생한다.
            // objects.remove(object);

            // 가비지 컬렉션을 명시적으로 트리거 (선택 사항)
            System.gc();
        }
    }
}
```

이 코드에는 객체를 계속 생성하고 이를 objects라는 ArrayList에 추가하는 MemoryLeakExample 클래스가 있다. 
본래 의도는 GC가 메모리를 회수할 수 있도록 더 이상 필요하지 않을 때 목록에서 개체를 제거하는 것이다.

그러나 'objects.remove(object)'라는 주석 처리된 코드 줄이 있는데, 이 줄을 주석 처리하면 개체가 더 이상 필요하지 않더라도 ArrayList에서 개체가 제거되지 않는다. 
결과적으로 개체 목록이 계속해서 무한정 증가하여 메모리 누수가 발생한다.
GC를 명시적으로 트리거하기 위해 System.gc()를 호출했음에도 불구하고 객체가 objects 목록에 의해 참조되기 때문에 여전히 메모리 누수가 발생한다. 
GC는 이러한 개체가 여전히 도달할 수 있기 때문에 이 개체가 차지하는 메모리를 회수할 수 없게 된다.

이 메모리 누수를 수정하려면 objects.remove(object) 줄의 주석을 제거해야 한다. 이렇게 하면 더 이상 필요하지 않은 개체가 목록에서 제거된다.
이를 통해 GC는 이를 가비지로 식별하고 메모리를 회수할 수 있게 된다.

실제 시나리오에서 메모리 누수는 더 복잡하고 식별하기 어려울 수 있다는 점에 유의해야 한다. 
가비지는 수명이 긴 데이터 구조, 캐시 또는 이벤트 리스너가 보유하고 있는 객체를 포함하는 경우가 많다. 
GC가 사용 중인 경우에도 메모리 누수를 방지하려면 참조를 적절하게 관리하고 개체가 더 이상 필요하지 않을 때 해제되도록 하는 것이 중요다.
