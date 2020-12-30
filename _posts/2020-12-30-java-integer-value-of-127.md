---
title: Integer.valueOf(127) == Integer.valueOf(127)
date: 2020-12-30 23:12:00 +0900
categories:
  - Java
links:
  - name: Chapter 5. Conversions and Contexts | Oracle
    url: https://docs.oracle.com/javase/specs/jls/se11/html/jls-5.html#jls-5.1.7
  - name: "[Java] Integer.valueOf(127) == Integer.valueOf(127) 는 참일까요? | TOAST Meetup!"
    url: https://meetup.toast.com/posts/185
---
## Integer.valueOf(127) == Integer.valueOf(127)

결론부터 말하면, 위의 비교연산 결과는 `true`이다. 분명 Integer는 reference 타입이므로 `false`가 되야할 것 같지만 그렇지 않다. 이는 `Integer.valueOf` 메서드에서 캐싱한 Integer 객체를 넘겨주기 때문이다.

## Auto-boxing

```java
Integer a = 127; // Integer.valueOf(127)
Integer b = 127; // Integer.valueOf(127)
a == b; // true
```

`Integer` 객체에 바로 127 대입할 때 자동으로 primitive 타입인 `int`에서 reference 타입인 `Integer`로 auto-boxing될 때 `Integer.valueOf`가 호출되기 때문에 위의 비교연산도 `true`가 된다.

## Integer.valueOf
그러면 이제 [Integer.valueOf](https://github.com/openjdk/jdk/blob/aa9c136d67a32cebcdf031f50d8d5cccdd354bed/src/java.base/share/classes/java/lang/Integer.java#L1078) 코드 부분을 살펴보자. 아래와 같이 특정 범위에 있는 경우에 캐싱된 객체를 return하는 것을 확인할 수 있다.

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

[IntegerCache](https://github.com/openjdk/jdk/blob/aa9c136d67a32cebcdf031f50d8d5cccdd354bed/src/java.base/share/classes/java/lang/Integer.java#L1019) 코드에서는 최소값가 -128로 고정되어있고, 최대값은 127보다 큰 값이 필요한 경우 따로 property로 설정 가능하도록 구현되어있다.

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer[] cache;
    static Integer[] archivedCache;

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                h = Math.max(parseInt(integerCacheHighPropValue), 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(h, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;
    // ......
```

최대값을 수정하기 위해서는 아래처럼 property를 설정하거나 JVM setting 값을 변경하면 된다.

```bash
-Djava.lang.Integer.IntegerCache.high=<size>
-XX:AutoBoxCacheMax=<size>
```

## 다른 reference 타입

다른 reference 타입들도 아래와 같은 범위가 캐싱되어 있다. 부동소수점은 캐싱할 수 없을테니 Float, Double 제외. 참고로 Integer를 제외한 다른 reference 타입의 최대값은 고정값이므로 property로 변경되지 않는다.

* `Boolean`: true, false  
* `Byte`: -128 ~ 127
* `Short`: -128 ~ 127
* `Integer`: -128 ~ 127
* `Long`: -128 ~ 127
* `Character`: '\u0000' ~ '\u007f'

## Kotlin에서는?

코틀린에서도 nullable number reference를 사용하는 경우, auto-boxing 되기 때문에 동일하게 결과를 확인할 수 있다.

```kotlin
val a: Int? = 127
val b: Int? = 127
a === b // true

val x: Int? = 128
val y: Int? = 128
x === y // false
```