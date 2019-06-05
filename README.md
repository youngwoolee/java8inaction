
Java8 In Action
=====================================

CHAPTER 1. 자바 8을 눈여겨봐야 하는 이유
--------------------------------------


> java8 이전
<pre>
Collections.sort(inventory, new Comparator() {
   public int compare(Apple a1, Apple a2) {
       return a1.getWeight().compareTo(a2.getWeight());
   }
});
</pre>

> java 8
<pre>
inventory.sort(comparing(Apple:getWeight());
</pre>

### 1.1. 스트림 API : 병렬 연산을 지원하는 스트림이라는 새로운 API
* 스트림이란 한번에 한개씩 만들어지는 연속적인 데이터 항목들의 모임
* 예) unix에서 stdin으로 파일을 읽고 stdout으로 파일을 출력하는 형태
* cat file1 file2 | tr "[A-Z]" | sort | tail -3
* 스트림 파이프라인을 이용해서 입력부분을 여러 CPU 코어에 쉽게 할당 가능(Thread Safe한 병렬성을 얻을 수 있음)

### 1.2. 동작 파라미터화로 메서드에 코드 전달(람다와 메서드 레퍼런스)
* 프로그래밍 언어의 핵심은 값을 바꾸는 것이 아닌가..?(다양한 제어와 조작을 통해서)
* 그리고 전통적인 프로그래밍 언어(대표적인 Java)에서는 이 값을 일급(first-class)또는 시민이라 부름
* 클래스, 메서드등은 이급 자바 시민
* 프로그램을 실행하는 동안 전달 할 수 있는 값은 일급 값 밖에 없음(즉 클래스, 메소드는 전달 불가능)
* 결국 자바8에서 메서드와 람다를 일급시민으로 취급


> 메서드 레퍼런스 예제
<pre>
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
    public boolean accept(File file) {
        return file.isHidden();
    }
}
</pre>

자바8 이전에서는 listFiles의 파라미터로 클래스, 혹은 메서드로 넘길 수 없다 보니 FileFilter라는 객체 레퍼런스를 직접 구현해야 했지만
자바 8에서는 listFiles의 파라미터로 메서드를 넘길 수 있습니다. 이것이 바로 메서드 레퍼런스(:: 이 메서드를 값으로 사용하라는 의미) 입니다


<pre>
File[] hiddenFiles = new File(".").listFiles(File::isHidden)
</pre>

> 람다 예제
<pre>
File[] hiddenFiles = new File(".").listFiles((File f) -> f.isHidden())
</pre>

listFiles()의 파라미터로 코드를 전달했습니다. 즉, 람다 표현이 가능해지면서
파라미터로 코드를 전달 할 수 있게 되었습니다. 람다 문법형식으로 구현된 프로그램을 함수형 프로그래밍,
즉 '함수를 일급값으로 넘겨주는 프로그램'을 구현 할 수 있게 됨

### 1.3. 인터페이스의 디폴트 메서드

* 더 쉽게 변화할 수 있는 인터페이스를 만들 수 있도록 디폴트 메서드라는 기능을 추가
* 기본적으로 인터페이스는 메서드 바디를 가질 수 없지만, default 키워드를 사용하면 인터페이스에
새로운 메서드 구현체를 추가 가능
* List 인터페이스 내에 있는 sort 메소드가 대표적인 default interface method

<pre>
default void sort(Comparator<? super E> c) {
    Collections.sort(this, c);
}
</pre>

default 메소드가 추가되면 List.sort() 와 같은 메소드 호출이 가능해지고,
interface method body에서는 Collections.sort를 호출하게 되므로 인터페이스의 확장이 매우 유연

### 1.4. Optional 클래스 제공
* NullPointer 예외를 피할 수 있도록 도와주는 클래스 입니다


### 1장 요약
1. 자바8은 프로그램을 좀더 효과적이고 간결하게 구현할 수 있는 새로운 개념과 기능을 제공
2. 기존의 자바 프로그래밍 기법으로는 멀티코어 프로세서를 온전히 활용하기 어렵지만 자바8에서는 가능
3. 함수가 일급값이 되었습니다. 메서드 레퍼런스, 람다를 통해 함수를 값으로 취급
4. 스트림을 이용해 복잡한 처리를 간결화 할 수 있고, 병렬 처리도 가능
5. 디폴트 메서드를 통해서 인터페이스에 메서드 바디를 제공 가능
6. NullPointer 예외를 피하고 좀더 심플하게 Null값을 처리 할 수 있는 Optional 클래스를 제공



CHAPTER 2. 동작 파라미터화 코드 전달하기
--------------------------------------

고객의 요구사항은 계속해서 바뀌고, 변화에 따라 프로그래머는 지속적으로 코드를 수정해야함
'동작 파라미터화'를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응 가능

동작 파라미터화는 아직 어떻게 실행할 것인지 결정하지 않는 코드 블록을 의미

> 첫번째 시도 : 녹색 사과 필터링
<pre>
public static List filterGreenApples(List inventory){
    List result = new ArrayList<>();
    for(Apple apple: inventory){
        if("green".equals(apple.getColor())){
            result.add(apple);
        }
    }
    return result;
}
</pre>

> 두번째 시도 : 색을 파라미터화
<pre>
public static List filterApplesByColor(List inventory, String color){
    List result = new ArrayList<>();
    for(Apple apple: inventory){
        if(apple.getColor().equals(color)){
            result.add(apple);
        }
    }
    return result;
}
</pre>

다른 조건이 추가 될때마다 
90% 이상이 동일한 코드가 반복되고 있고, 이는 소프트웨어 공학의 DRY(don’t repeat yourself) 원칙 어김

> 세번째 시도 : 가능한 모든 속성으로 필터링
<pre>
public static List filterAppes(List inventory, String color, int weight, boolean flag){
    List result = new ArrayList<>();
    for(Apple apple: inventory){
        if((flag && apple.getColor().equals(color)) ||
            (!flag && apple.getWeight() > weight)) {
 
            result.add(apple);
        }
    }
    return result;
}
</pre>

기존 코드를 버리고 Predicate(프레디케이트) - 선택조건을 결정하는 인터페이스를 도입
예를들면 선택조건을 대표하는 여러 버전의 Predicate를 설계합니다.

> 네번째 시도 : 추상적 조건 필터링
<pre>
public static List filterApples(List inventory, ApplePredicate p){
    List result = new ArrayList<>();
    for(Apple apple : inventory){
        if(p.test(apple)){
            result.add(apple);
        }
    }
    return result;
}
</pre>

하지만 새로운 조건을 만들기 위해서는 기존 Predicate를 Copy & Paste 해서 구현해야함
물론 파라미터를 Predicate 타입으로 통일화 했지만, 여전히 조건이 추가될 때 마다 
새로운 인터페이스를 구현해야하는 문제 발생

> 다섯번째 시도 : 익명 클래스 사용
<pre>
filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return "red".equals(apple.getColor());
    }
});
</pre>

핵심 코드는 "red" Apple을 찾아내는 것인데, 익명클래스를 구현하면서 코드가 길어짐

> 여섯번째 시도 : 람다 표현식 사용
<pre>
public interface Predicate {
    boolean test(T t);
}
 
public static  List filter(List list, Predicate p) {
    List result = new ArrayList<>();
    for(T e : list) {
        if(p.test(e)) {
            result.add(e);
        }
    }
    return result;
}
 
List redApples = filter(inventory, (Apple apple) -> "red".equals(apple.getColor());
 
List heavyOranges = filter(inventory, (Orange orange) -> orange.getWeight() > 500);
</pre>

어떠한 타입이든(T) 다양한 조건으로 (람다) 과일을 필터링 가능

### 1.2. 동작 파라미터화로 메서드에 코드 전달(람다와 메서드 레퍼런스)


### 2장 요약
1. 동작 파라미터화란 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달하는 것을 의미
2. 동작 파라미터화를 이용하면 변화하는 요구사항에 좀더 유연하게 대응 할 수 있고, 결국 엔지니어링 비용을 줄일 수 있음
3. 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달 할 수 있는데, 자바 8 이전에는 코드를 지저분하게 구현해야 했지만,
자바 8부터는 인터페이스를 상속받아 여러 클래스를 구현해야 하는 수고를 없애고, 람다표현을 사용해 코드를 직접 파라미터화 가능