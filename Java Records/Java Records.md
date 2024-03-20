## Конструкторы

Компактный конструктор позволяет производить валидацию и прочие вычисления также как и каноничный.
Присваивание полей происходит неявно в конце конструктора.

Пример:

```java
record Point(int x, int y){
	// компактный конструктор
	public Point {
		if(x < 0 || y < 0){
			throw new IllegalArgumentException("Point is outside the world");
		}
	}

	// каноничный
	public Point(int x, int y) {
        if (x < 0 || y < 0) {
            throw new IllegalArgumentException("Point is outside the world");
        }
        this.x = x;
        this.y = y;
    }
}
```

## Методы и поля

В рекорде можно делать такие же приватные/публичные методы как и в обычных классах. 

В рекорде нельзя делать не статические поля или инциализаторы.

Пример:

```java
// не скомпилируется
record Rectangle(double length, double width) {

    // Field declarations must be static:
    BiFunction<Double, Double, Double> diagonal;

    // Instance initializers are not allowed in records:
    {
        diagonal = (x, y) -> Math.sqrt(x*x + y*y);
    }
}
```

## Generics

Рекорды могут быть генериками.
Например

```java


public record BagOfFruites<Fruit>(List<Fruit> fruits) {
}

public interface Fruit {
    public Color color();
    public enum Color{
        YELLOW, GREEN, RED;
    }
}

public record Banana(Fruit.Color color) implements Fruit  {

}
...

final var bag = new BagOfFruites<>(List.of(new Banana(Fruit.Color.YELLOW)));
```

## Локальные рекорды

Также можно определять рекорды непосредственно в теле метода.

```java

public void method(){
	//,,,
	record ARecord(){}

	final var rec = new ARecord(); // вполне допустимо
	//...
}
```

Стоит отметить что такие рекорды статичны, а значит не имеют доступа к локальным переменным и полям класса и метода,
внутри которых созданы.

После 16-ой джавы внутренние классы могут использовать рекорды в качестве полей - до 16ой не могли,
т.к. рекорды - статические классы.

## Сериализация

Рекорды десериализуются с использованием каноничного конструктора, а классы десериализуются с использованием
конструктора по умолчанию, т.е. конструктора без аргументов. После создания объекта поля класса выставляются 
с использованием рефлексии. Таким образом любые валидации прописанные в конструкторах НЕ применяются. 

А в рекордах такой проблемы нет, т.к. для десериализации используются как раз конструкторы.

## Class java.lang.Record

```javac``` при компиляции файла класса импортирует весь пакет ```java.lang```. Так что обзывать собственные
класса ```Record``` не рекомендуется. Класс импортирующий ```some.other.package.*``` внутри которого
лежит ваш собственный ```Record``` получит ошибку компиляции.

Пример:
```java
import org.example.bad.*;
public class Main {
    public static void main(String[] args) {
        final var rec = new Record("bar");
    }
}
-----------
java: reference to Record is ambiguous
  both class org.example.bad.Record in org.example.bad and class java.lang.Record in java.lang match
```

## Как можно пройтись по полям рекорда?

```java
    final var point = new Point(1,2);
    System.out.println("Is record: " + point.getClass().isRecord());
    RecordComponent[] recordComponents = point.getClass().getRecordComponents();
    for (RecordComponent recordComponent : recordComponents) {
        System.out.println(recordComponent);
    }
```
