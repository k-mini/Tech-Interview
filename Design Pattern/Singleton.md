

# 싱글톤 패턴(Singleton pattern)

> 애플리케이션이 시작될 때, 어떤 클래스가 최초 한 번만 메모리를 할당(static)하고  
> 해당 메모리에 인스턴스를 만들어 사용하는 패턴


싱글톤 패턴은 '하나'의 인스턴스만 생성하여 사용하는 디자인 패턴.

```

 인스턴스가 필요할 때, 똑같은 인스턴스를 만들지 않고 기존의 인스턴스를 활용
 
```

생성자가 여러번 호출되도, 실제로 생성되는 객체는 하나이며 최초로 생성된 이후에 호출된  
생성자는 이미 생성한 객체를 반환한다.

(java에서는 생성자를 private으로 선언해  다른 곳에서 생성하지 못하도록 만들고,  
getInstance() 메소드를 통해 받아서 사용하도록 구현)

#### 장점?

> 한번의 new로 인스턴스를 사용하기 때문에 메모리 낭비 방지할 수 있다.
>
> 싱글톤으로 만들어진 클래스의 인스턴스는 전역이기 때문에 다른 클래스의 인스턴스들이 데이터를 공유하기 쉽다.
>
> 인스턴스를 절대적으로 한 개만 존재하는 것을 보증하고 싶을 때 사용.

#### 주의점?

> 멀티 스레드 환경에서 동기화 처리를 하지 않으면 인스턴스가 2개가 생성될 수 있다.
> 
> 개방-폐쇄 원칙을 위배할 수 있다.


#### 멀티스레드 환경에서 안전한 싱글톤

##### 1. Lazy Initialization(게으른 초기화)
```
public class ThreadSafe_Lazy_Initialization{

    private static ThreadSafe_Lazy_Initialization instance;
    
    private ThreadSafe_Lazy_Initialization(){}
    
    public static synchronized ThreadSafe_Lazy_Initialization getInstance(){
     
        if (instance == null){
            instance = new ThreadSafe_Lazy_Initialization();
        }
        return instance;
    }
}
```

> 처음 생성이후에도 synchronized될 필요가 없음에도 synchronized되어 성능저하 발생


##### 2. Lazy Initialization + Double-checked Locking

```
public class ThreadSafe_Lazy_Initialization{
  private volatile static ThreadSafe_Lazy_Initialization instance;
  
  private ThreadSafe_Lazy_Initialization(){}
  
  public static ThreadSafe_Lazy_Initialization getInstance(){
      if (instance == null){
          synchronized(ThreadSafe_Lazy_Initialization.class){
              if (instance == null){
                  instance = new ThreadSafe_Lazy_Initialization();
              }
          }
      }
      return insance;
  }
}
```

1번과는 달리, 먼저 조건문으로 인스턴스 존재 여부를 확인한 다음 두번째 조건문에서
synchronized를 통해 동기화를 시켜 인스턴스를 생성하는 방법

스레드를 안전하게 만들면서, 처음 생성 이후에는 synchronized를 실행하지 않기 때문에 성능저하 완화가 가능하다.

> 하지만 완전히 완벽한 방법은 아님


##### 3. Initialization on demand holder idiom (holder에 의한 초기화)   (다시 공부 필요)


클래스 안에 클래스(holder)를 두어 JVM의 클래스 로더 매커니즘과 클래스가 로드되는 시점을 이용한 방법

```
public class Something {
    private Something() {
    }
 
    private static class LazyHolder {
        public static final Something INSTANCE = new Something();
    }
 
    public static Something getInstance() {
        return LazyHolder.INSTANCE;
    }
}

```

JVM 클래스 초기화 과정에서 보장되는 원자적 특성을 이용해 싱글톤의 초기화 문제에 대한 책임을  
JVM에게 떠넘기는 걸 활용

클래스 안에 선언한 클래스인 holder에서 선언된 인스턴스는 static이기 때문에 클래스 로딩시점에서 한번만 호출  
또한 final을 사용해서 다시 값이 할당되지 않도록 만드는 방식을 사용한 것.








