ch.1_자바 8, 9, 10, 11 - 무슨 일이 일어나고 있는가

https://www.yes24.com/Product/Goods/77125987 <br>
👆책의 목차는 해당 페이지에서 확인 바람.

## 역사의 흐름은 무엇인가?
자바 8은 간단하게 다음과 같은 3가지 기능이 추가되면서 큰 편리함을 제공했다고 볼 수 있다.
- 스트림 API(병렬성)
- 메서드에 코드를 전달하는 기법
- 인터페이스의 디폴트 메서드
뿐만 아니라, 하드웨어가 싱글코어에서 멀티코어로 발전함에 따라 위와 같은 **병렬 프로세스**를 제공할 필요가 있어서 추가로 만들었다고 한다.

### 스트림 처리
🔖스트림: 한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임

예를 들어, 다음과 같은 유닉스 명령어가 있다고 하자. <br>
`cat file1 file2 | tr "[A-Z]" "[a-z]" | sort | tail -3`

cat: file1, file2를 연결해서 스트림을 생성 <br>
tr: 대문자를 소문자로 변환 <br>
sort: 여러 행의 스트림을 정렬 <br>
tail -3:  스트림의 마지막 3개 행을 출력한다. <br>

이때, 유닉스에서는 위와 같은 **여러 명령을 병렬로 실행**하는데, cat이나 tr이 완료되지 않은 시점에서 sort가 행을 처리할 수도 있다는 의미다. <br>
👉스트림이 연속적인 데이터의 흐름이라는 것을 보면, 모든 데이터가 하나의 명령어가 끝난 뒤에 실행되는 것이 아니라, 데이터의 입력을 받을 때마다 해당되는 명령어를 실행하고 다음 명령어로 처리된 데이터를 넘겨주는 것을 의미한다. <br>
🌊쉽게 말해, 흘러들어오는 물(데이터 스트림)을 파이프(특정 명령어)를 통해 처리한 다음, 다음 파이프로 넘겨준다고 보면 된다.

이와 마찬가지로, 자바8에서도 스트림 API가 추가됬는데, 일단은 이전에 살펴본 조립 라인처럼 **어떤 항목을 연속으로 제공하는 어떤 기능**이라고 생각하자.

### 동작 파라미터화로 메서드에 코드 전달하기
이전에 살펴봤던 유닉스 명령어 예제 코드를 봐보자. 
특히 sort 명령어는 동작이 미리 정해져 있기 때문에 프로그래머가 역순 정렬을 한다든지, 어떤 기준을 가지고 정렬을 한다든지와 같이 유연하게 대처할 방법이 없다.

자바8 이전에서는 Comparator라는 객체를 새로 만들어서, sort 파라미터에 전달한 다음, 바디에 비교하는 코드를 짜야하는 복잡성이 있었지만, 자바8 이후에는 메서드를 파라미터화해서 sort 인수로 넘겨줄 수가 있는데 이것을 *동작 파라미터화(behavior parameterization)* 이라고 부른다.

대충 코드로 살펴보면 다음과 같다.
```java
public int compareUsingCustomerId(String inv1, String inv2) {
...
}

sort(compareUsingCustomerId)
```

스트림 API에서는 위와 같이 연산의 동작을 파라미터화해서 병렬적으로 처리할 수 있는 강력한 기능이 있는데 자세한 내용은 2장과 3장에서 다룬다고 한다.

---
### 1.3 자바 함수
자바 프로그램에서 조작할 수 있는 값: 일급 시민에 해당 <br>
1️⃣원시 타입: int형, double형 ... <br>
2️⃣참조 타입: String, new 또는 팩토리 메서드 또는 라이브러리 함수를 활용한 객체의 참조값 <br>
3️⃣배열

🤔자바에서 함수가 필요한 이유? <br>
👉프로그래밍 언어의 핵심은 **<span style='color:#eb3b5a'>값을 바꾸는 것</span>** 인데, 이 값을 *일급 시민(first-class)* 라고 부른다. <br> 
그런데, 메서드, 클래스같은 구조체들은 다음과 같은 제약 사항에 따라 자유롭게 전달할 수 없기 때문에 이급 시민에 해당한다.

🔖일급 시민이 되기 위한 조건 <br>
1️⃣모든 일급 객체는 **변수나 데이터**에 담을 수 있어야 한다. <br>
2️⃣모든 일급 객체는 **함수의 파라미터**로 전달할 수 있어야 한다. <br>
3️⃣모든 일급 객체는 **함수의 리턴값**으로 사용할 수 있어야 한다. <br>

그런데, 메서드는 위의 3가지 조건을 모두 충족시키지 못해서 이급객체에 해당한다.

💡람다식을 활용해서 일급 객체로 사용하자! <br>
자바8에 람다 함수라는 새로운 기능이 추가되었는데, 이를 활용해서 위의 3가지 조건을 모두 충족시킬 수 있다.

1️⃣람다식을 **변수나 데이터**에 담을 수 있다.
```java
import java.util.function.Consumer;

public class Main {
    public static void main(String[] args) {
        Consumer<String> c = (t) -> System.out.println(t); // 람다식을 인터페이스 타입 변수에 할당
        c.accept("Hello World");
    }
}
```
람다 함수는 수학적인 함수와 기능이 동일하기 때문에, 어떤 변수에 대입할 수 있다.

2️⃣람다 함수는 **함수의 파라미터**로 전달할 수 있다.
```java
import java.util.function.Consumer;

public class Main {
    // 메소드 매개변수로 람다 함수를 전달
    public static void print(Consumer<String> c, String str) {
        c.accept(str);
    }

    public static void main(String[] args) {
        print((t) -> System.out.println(t) ,"Hello World");
    }
}
```
위의 첫번째 조건에서 `Consumer<String> c = (t) -> System.out.println(t);` 로 대입이 가능하기 때문에, 파라미터에서도 동일하게 적용할 수 있다.("Hello world"를 str에 전달하는 것도 마찬가지)

3️⃣람다 함수는 **함수의 리턴값**으로 사용할 수 있다.
```java
import java.util.function.Consumer;

public class Main {
    public static Consumer<String> hello() {
        // 람다 함수 자체를 리턴함
        return (t) -> {
            System.out.println(t);
        };
    }

    public static void main(String[] args) {
        Consumer<String> c = hello();
        c.accept("Hello World");
    }
}
```
hello()를 호출하면 람다 함수 자체를 리턴하기 때문에, 이를 변수 c에 대입할 수 있다.

따라서, 위의 3가지 조건을 모두 충족하기 때문에 람다 함수는 일급 객체에 해당한다고 볼 수 있다.

📑참조 링크: https://inpa.tistory.com/entry/CS-%F0%9F%91%A8%E2%80%8D%F0%9F%92%BB-%EC%9D%BC%EA%B8%89-%EA%B0%9D%EC%B2%B4first-class-object

### 1.3.1 메서드와 람다를 일급 시민으로
메서드 참조(method reference): 이 메서드를 값으로 사용하라는 의미로, 위의 일급 객체 조건들을 활용하라는 의미로도 볼 수 있다.

다음과 같은 예를 통해 메서드 참조가 어떻게 사용되는지 살펴보자.

📜디렉토리에서 모든 숨겨진 파일을 필터링하고 싶다. <br>
이때, File 클래스의 숨겨진 파일의 여부를 알려주는 isHidden() 메서드는 기본으로 제공해준다.

1.자바8 이전의 익명클래스를 사용할 때
```java
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
	public boolean accept(File file) {
		return file.isHidden();
	}
});
```
isHiddden()의 메서드 값을 전달하기 위해, FileFilter라는 객체를 만들고, 그것을 listFiles 파라미터에 전달하는 복잡한 과정을 거친다.

2.자바8 이후의 메서드 참조를 사용할 때
```java
File[] hiddenFiles = new File(".").listFiles(File::isHidden) // boolean 값을 listFiles에 전달
```
파일 클래스에 isHidden() 함수가 이미 있으니까, isHidden() 함수 값을 listFiles에 직접 전달할 수가 있어서(함수 파라미터에 전달하는 2번째 조건에 해당) 코드가 굉장히 직관적으로 변하였다.

