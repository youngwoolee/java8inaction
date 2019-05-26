
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

* 더 쉽게 변화할 수 있는 인터페이스를 만들 수 있도록 디폴트 메서드라는 기능을 추가 했습니다.
* 기본적으로 인터페이스는 메서드 바디를 가질 수 없지만, default 키워드를 사용하면 인터페이스에
새로운 메서드 구현체를 추가할 수 있습니다
* List 인터페이스 내에 있는 sort 메소드가 대표적인 default interface method 입니다

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
