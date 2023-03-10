<h1> java 내용 정리 </h1>

------

<h3> 어떤 버전을 사용해야 할까? </h3>
<p>
    자바는 6개월 단위로 신규 버전 출시 및 새로운 업데이트가 발표되고 있다.<br>
    현재 많은 기업은 자바 8(SE 1.8) 버전을 사용하고 있으며 안드로이드 앱개발은 7버전까지 지원을 하고있으며 현재 코틀린으로 전환하였다.
</p>
<br>

<p>
    다양한 버전이 출시되고 업데이트 되는 만큼 특정 버전을 학습해야 하는지 많은 의문을 가질 수 있으나 기본적인 문법을 베이스로 신규 기능이 추가되는 방식으로 새로운 버전이 출시 되고 있으며 <br>
    지원을 중단하는 문법 또한 안내를 하고 있기 때문에 특정 버전에 종속되어 학습을 하기 보다 보편적인 학습을 한 이후 필요한 문법을 학습하는게 맞다고 생각을 한다. <br>
    <br>
    또한 자바는 과저의 레거시 프로젝트를 신규 jvm에서 동작이 가능하도록 하는 backward compatible(하위 호완성)을 제공하고 있다.<br>
    그러나 신규 버전을 구버전에서 실행하고자 하는 경우 오류가 발생된다. <br>
    <br>
    이러한 상황을 미루어 시장에서 많이 사용되는 버전을 기준으로 학습하고 이후 버전에 추가된 문법을 학습하는 방식으로 하는 것이 올바르다.
</p>

--------

<h3> 자바 distribution </h3>
우리가 사용하는 자바는 선마이크로 시스템의 제임스 고슬링과 다른 연구원이 개발한 객체지향 언어이다. <br> 
1995년에 최초 버전을 발표하였으며 초기 가전제품을 제어하기 위해 만들어진 언어였으나 업데이트를 통해 어플리케이션에서 널리 사용되는 언어가 되었다. <br>
<br>
이러한 배경을 가진 자바는 2009년 오라클의 인수됨을 밝히고 2010년도 인수가 되어 우리가 흔히 알고있는 것 처럼 oracle JDK, Open JDK로 구분되게 되었다. <br>


<h4> Open JDK VS Oracle JDK 차이점</h4>

1. 배포일정 <br>
   Oracle : 3년마다 릴리즈(안정화)를 제공하며 장기적인 지원을 약속한다.<br>
   open JDK : 6개월 마다 릴리즈를 제공하며 다음버전이 릴리즈 될 때까지만 지원을 제공한다. 
2. 라이센스 <br>
   open JDK : GNU GPL 버전 2이므로 완전한 오픈소스이며 자유롭게 사용이 가능하다. <br>
   oracle : Oracle Binary Code License Agreement를 라이센스를 이용하며 2019년 1월 이후 정식라이센스 없이 비즈니스, 상업용으로 사용이 불가능하다.
3. 성능 <br>
   동일한 기술을 기반으로 사용하고 있어 기술적으로는 큰 차이가 없으나 JVM의 성능을 기준으로 생각하면 oracle JDK가 더 높은 성능을 보여주는데 이는 Oracle에서 고객의 안정화에 집중을 하기 때문이며 Open jdk의 경우 잦은 릴리즈를 제공하고 있어 불안정한 요소를 가지고 있다.
4. 개발 및 인지도 <br>
   oracle JDK의 경우 Oracle사에서 전적으로 개발을 하는 방면 Open jdk의 경우 oracle, java Comunity에서 개발을 시작하였으며 최근 다양한 기업에서 개발에 참여 하고 있는 상태이다. <br>
   <br> 
   이러한 현상으로 과거에는 안정화된 oracle JDK가 많이 선호되었으나 점차 Open jdk 진형으로 수요가 증가하고 있다.


-----

<h3> JAVA 8 ~ 17 버전별 특징  </h3>

1. java 8 : 해당 버전은 대규모 릴리즈를 진행하였으며 다음과 같은 핵심 기능이 추가되었다.
    1. ramda : 코드를 간결하게 작성할 수 있는 기능을 제공하는 ramda는 간결하면서 직관적인 코드 작성이 가능하도록 하였다.
       > ramda 적용전 <br>
    Runnable runnable = new Runnable(); <br>
    @Override <br>
    public void run(){ <br>
        System.out.println("hello"); <br>
    }<br>
    <br>
    ramda 적용 <br>
    Runnable runnable = () -> System.out.println("Hello");

   2. Stream : 컬렉션 처리를 하면서 모호성 및 반복성이 발생되는 코드와 멀티코어 활용의 어려운 문제를 개선함
      > Stream 적용전 <br>
   List <String> list = Arrays.asList("1","2","3","4"); <br>
   for(int i =0; i<= list.size(); i++){ <br>
       System.out.println(list[i]); <br>
   } <br>
      <br>
   적용후 <br>
    list.Stream().filter(name -> name.startsWith("f"))
      .map(String::toUpperCase)
      .sorted()
      .forEach(System.out::println);

2. java 9 : 모듈 시스템 기능 추가
   1. 컬렉션 : 컬렉션에는 list, set, map을 쉽게 구성하는 몇 가지 추가
      <br> List<String> list = List.of("1", "2","3");
      <br> Set<String> set = Set.of("1","2","3");
      <br> Map<String,String> map = map.of("1","one","2","Two","3","three");
   2. Stream : takeWhile, dropWhile, iterate 메서드의 형태로 몇 가지 추가 기능
      >Stream<String> stream = Stream.iterate("", s -> s + "s")
      .takeWhile(s -> s.length() < 10);
      optional

3. ifPresentOrElse 추가 기능
    
    user.ifPresentOrElse(this::displayAccount, this::displayLogin);
    인터페이스
    
    인터페이스에 private method 사용 가능
    
        public interface MyInterface {
        
            private static void myPrivateMethod(){
                System.out.println(*"Yay, I am private!"*);
            }
        }

기타 언어 기능

try-with-resources 문 또는 다이아몬드 연산자(<>) 확장, HTTP클라이언트와 같은 몇 가지 다른 개선 사항 존재


