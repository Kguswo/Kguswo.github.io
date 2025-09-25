---
title: "[Effective Java] - 생성자에 매개변수가 많다면 빌더를 고려하라"
date: 2025-09-25T00:38:48.253Z
tags: ["Java"]
slug: "Effective-Java-생성자에-매개변수가-많다면-빌더를-고려하라"
image: "../assets/posts/1d34335aeec1e43ae3399b99e63b461775cc4882581b00076978d5d937668c92.png"
categories: 이펙티브자바
toc: true
velogSync:
  lastSyncedAt: 2025-09-25T01:27:04.177Z
  hash: "67c5d22c4ebfa798721ac2336683197059e88c658cb7a651b76013c0ee1eb3bc"
---

## Item 2 생성자에 매개변수가 많다면 빌더를 고려하라

### 들어가며

생성자에 매개변수가 많으면 사용하기도 어렵고, 매개변수의 의미를 파악하기 힘들다는 문제가 있다. 

예를 들어, 또 비슷한 타입의 매개변수가 여러 개일 때는 순서를 잘못 넣으면 컴파일 시점에서 오류가 발생하지 않아 실수를 유발하기도 한다. 이 경우 점층적 생성자 패턴과 자바빈즈 패턴이 전통적인 해결책으로 사용되었으나 각각 단점이 존재한다.

이번 글에서 빌더 패턴의 구체적인 구조와 사용법, 그리고 장점을 자세히 살펴보자.

## 점층적 생성자 패턴

생성자에 매개변수가 많은 객체를 만들 때 전통적인 점층적 생성자 패턴을 사용하면, 매번 모든 매개변수를 나열하는 생성자가 필요해져 생성자의 수가 급격하게 늘어난다. 

**점층적 생성자 패턴(telescoping constructor pattern)**은 불변 객체 구현에 적합하지만, 매개변수가 많아질수록 생성자의 수가 폭발적으로 증가해서 가독성이 떨어진다.

아래와 같은 가상의 `NutritionFacts` 클래스가 있다고 하자.

```java
public class NutritionFacts {
    private final int servingSize;  // 필수
    private final int servings;     // 필수
    private final int calories;     // 선택
    private final int fat;          // 선택
    private final int sodium;       // 선택
    private final int carbohydrate; // 선택

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }
    // ... 계속 생성자 증가
}
```

생성자의 수가 많아지고 인자의 순서와 타입을 기억해야 해 불편하다. 또한 가독성이 나빠지고 오류가 생길 여지가 크다.

## 자바빈즈 패턴

**자바빈즈 패턴(JavaBeans pattern)**에서는 기본 생성자와 setter 메서드를 통해 객체를 생성한 뒤 여러 속성을 설정할 수 있다.

```java
public class NutritionFacts {
    private int servingSize;
    private int servings;
    private int calories;
    private int fat;
    private int sodium;
    private int carbohydrate;

    public NutritionFacts() {}

    public void setServingSize(int val) { servingSize = val; }
    public void setServings(int val) { servings = val; }
    public void setCalories(int val) { calories = val; }
    // ... setters 생략
}
```
자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다.

그렇기 때문에 자바빈즈 패턴은 가독성과 유연성은 좋아도, 객체가 불완전한 상태로 존재할 수 있고 불변성을 보장하지 못한다.

## 빌더 패턴

이러한 두 패턴의 문제를 해결하기 위해 빌더 패턴을 권장한다.

빌더 패턴을 사용하면 복잡한 생성자 인자의 문제를 깔끔하게 해결하면서도 불변 객체를 만들 수 있다. 다음으로 빌더 패턴의 구체적인 구조와 사용 예를 살펴보자.

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;
        // 선택 매개변수 - 기본값 설정
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) 
        	{ calories = val; return this; }

        public Builder fat(int val) 
        	{ fat = val; return this; }

        public Builder sodium(int val) 
        	{ sodium = val; return this; }

        public Builder carbohydrate(int val) 
        	{ carbohydrate = val; return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```

`NutritionFacts` 클래스는 불변이며 빌더의 `setter` 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다. 이런 방식을 **플루언트 API(fluent API) 혹은 메서드 연쇄(method chaining)**라 한다.

빌더 사용법은 다음과 같다.

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                                .calories(100)
                                .sodium(35)
                                .carbohydrate(27)
                                .build();
```

클라이언트 입장에서 빌더는 쓰기 쉽고, 읽기 쉽다.
빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것이다.

### 불변(Immutable) vs 불변식(Invariant)

**불변** : 어떠한 변경도 허용하지 않는다는 뜻. 주로 변경을 허용하는 가변 객체와 구분하는 용도로 사용된다. `String` 이 대표적인 불변 객체다.

**불변식** : 프로그램이 실행되는 동안, 혹은 정해진 기간 동안 반드시 만족해야 하는 조건. 변경을 허용할 수는 있으나 주어진 조건 내에서만 허용한다는 뜻. 예를 들어, 리스트의 크기는 반드시 0 이상이어야하니, 만약 한순간이라도 음수값이 된다면 불변식이 깨진 것이다.

## 계층적으로 설계된 빌더 패턴

빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기 좋다.                                

추상 클래스는 추상 빌더를, 구체 클래스는 구체 빌더를 갖게 한다.

#### 피자 클래스

```java
public abstract class Pizza {
    public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);

        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }
        abstract Pizza build();
        
        // 하위클래스는 이 메서드를 재정의(overriding) 해서 "this"를 반환해야한다.(자기자신)
        protected abstract T self(); // self() 메서드를 하위클래스에서 구현 강제
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
}
```

#### 뉴욕피자

```java
public class NyPizza extends Pizza {
	public enum Size { SMALL, MEDIUM, LARGE }
	private final Size size;

	public static class Builder extends Pizza.Builder<Builder> {
		private final Size size;

		public Builder(Size size) {
			this.size = Objects.requireNonNull(size);
		}

		@Override
		public NyPizza build() {
			return new NyPizza(this);
		}

		@Override
		protected Builder self() { // self() 메서드 구현해서 this 반환
			return this;          
		}
	}
    
    public NyPizza(Builder builder) {
		super(builder);
		size = builder.size;
	}
}
```

#### 칼조네피자

```java
public class Calzone extends Pizza {
	private final boolean sauceInside;

	public static class Builder extends Pizza.Builder<Builder> {
		private boolean sauceInside = false;

		public Builder sauceInside() {
			sauceInside = true;
			return this;
		}
		
		@Override
		public Calzone build() {
			return new Calzone(this);
		}

		@Override
		protected Builder self() { // self() 메서드 구현해서 this 반환
			return this;
		}
	}
    
	private Calzone(Builder builder) {
		super(builder);
		sauceInside = builder.sauceInside;
	}
}
```

각 하위 클래스의 빌더가 정의한 `build` 메서드는 해당하는 구체 하위 클래스를 반환하도록 선언한다. 즉 `NyPizza.Builder`는 `NyPizza`를, `Calzone.Builder`는 `Calzone`를 반환한다.

하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환하는 기능을 공변 반환 타이핑(covariant return typing)이라 한다. (`@Override` 해서 재정의 하고 -> 하위타입을 반환)

이 기능을 사용하면 클라이언트가 형변환에 신경 쓰지 않고도 빌더를 사용할 수 있다.
```java
NyPizza pizza = new NyPizza.Builder(SMALL)
	.addTopping(SAUSAGE)
	.addTopping(ONION)
	.build();

Calzone pizza = new Calzone.Builder()
	.addTopping(HAM)
	.sauceInside()
	.build();
```

생성자로는 누릴 수 있는 장점으로, 빌더를 이용하면 가변인수(varargs; 매개변수 개수 정해지지 않음) 매개변수를 여러 개 사용할 수 있다. 
```java
addToppings(Topping... toppings)
			타입        변수명
            
// 예시
NyPizza pizza = new NyPizza.Builder(SMALL)
    .addToppings(Topping.HAM, Topping.ONION, Topping.MUSHROOM)
    .build();
```

### 마치며

빌더 패턴은 상당히 유연하다. 빌더 하나로 여러 객체를 순회하면서 만들 수 있고, 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있다. 

#### 하지만 장점만 있는것은 아니다.

객체를 만들려면, 그 전에 반드시 빌더부터 만들어야한다. 빌더 생성비용이 크진 않지만 성능에 민감한 상황에서는 문제가 될 수 있다.

또한 점층적 생성자 패턴보다는 코드가 장황해서 **매개변수가 4개 이상은 되어야** 의미있다. (일반적으로는 빌더가 이득이라는 의미)

즉 생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 것이 더 낫다.

---

### References

- 이펙티브 자바 3/E
