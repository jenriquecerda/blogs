# Proper Abstraction Identifications

For almost two years, I have had the privilege to work in an environment where order, cleanliness and caring for, really matters. And I'm not talking about social values, I'm talking about software.

Early on when I joined Dynamic, two years to date, I was given the **Manifesto for Software Craftsmanship**. Without going into details, it states that someone should care about his profession and the final product it brings into the world. The process of creation should be passionate about superior quality.

So, this is the environment which I've worked in for the past couple of years. I wanted to write about some stuff that I've learned over this period and hopefully, for someone either starting his career as a programmer or someone who wants to upgrade his game, will make some sense.

The first thing I want to write about is the **O** in **SOLID**. If you don't know what I'm talking about I strongly suggest to go and read about it first and then come back.

## Open-Closed Principle

It is difficult to think of stuff in an abstract way. I'll be the first to admit that it took me some time to really sink into this principle, probably the basis for good programming. I will not quote papers nor repeat in a scientific way what the principles say. Instead, I'll try to explain them as for someone that comes from a simple programming background, as myself.

What this principle says, in understandable words, is that you should try to avoid details as you write your code. Of course, details will have to finally emerge but not until the last possible moment at the concrete class. So, let's say that you are writing a test for a class that displays a given data somewhere. This device could be a monitor, it could be a TV, or it could be **_whatever has the ability to display_**. Pay attention to this last note.

A new programmer, oblivious to this principle, could write something like:

```java
public class TV {
  String data;

  public TV(String data){
    data = data;
  }

  public boolean show() {
    /// whatever process needed to show data, returns True if show is successful
  }
}

public class DisplayTest {
  @Test
  public void testItDisplaysDataOnScreen() {
    String data = "content to display";

    TV tv = new TV(data);

    assertTrue(tv.show());
  }
}
```

This test would pass and someone with no knowledge of what's to come in the future if this code is kept, would be happy, as was I. But what's wrong with this? Remember what I said before, write your code as if you where talking to something abstract. A human tends to fail this because everything we see with our eyes is something concrete, we don't see the invisible layer of abstraction that surrounds an object. Each object that we see is part of something bigger, or something more general like a category.

Remember the note, what is a TV? It's merely a displayer, this could be its abstraction. What else has the ability to "display"? Lots of other things, you are not bound to a TV only and writing code this way will be easier for you to plug any other object with this ability. Let's see an example:

```java
public interface Displayer {
  public boolean display();
}

public class TV implements Displayer {
  String data;

  public TV(String data){
    data = data;
  }

  @Override
  public boolean display() {
    /// whatever process needed to display data that returns True if successful
  }
}

public class Monitor implements Displayer {
  String data;

  public Monitor(String data){
    data = data;
  }

  @Override
  public boolean display() {
    /// whatever process needed to display data, that returns True if successful
  }
}

public class DisplayTest {
  @Test
  public void testItDisplaysDataOnDisplayer() {
    String data = "content to display";

    Displayer tv = new TV(data);
    Displayer monitor = new Monitor(data);

    assertTrue(tv.display());
    assertTrue(tv.display());
  }
}
```

Writing tests first is really helpful to detect these abstractions that otherwise would be hard for a simple human, accustomed to work with concrete objects, to detect. So, are we happy here? Is there something you have learned about abstractions so far? We are not there yet, there is something that bugs me that goes hand in hand with what we have learned so far. There is another abstraction waiting to be discovered here. Mull over it and come back.

## Dependency Inversion

Back in our Displayer example, as we wrote the second test we should have detected something. Remember, try to work with abstractions as far as you can. What is `String data = "content to display"`? This is really another concrete detail that should not appear until later on an implementation of such an object that does use `String` as its... **_Content_**.

So we have discovered another abstraction, and this brings us back to the **D** in **SOLID**. A **Displayer** should not be bound only to a type **String** but to a more general type, I'll name if for now as **Content**. Remember, we as humans tend to summarize and identify what we are working with to its lower level of detail, but we are programming to model a whole world, and that's where abstractions come into play.

A **Content** could be anything from a String, a Figure, a ThreeDImage, Pixels, etc.

```java
public interface Displayer {
  public boolean display(Content content);
}

public class TV implements Displayer {
  @Override
  public boolean display(Content image) {
    /// whatever process needed to display the concrete detail of an image
    /// that returns True if successful or false otherwise
  }
}

public class ThreeDMonitor implements Displayer {
  @Override
  public boolean display(Content threeDImage) {
    /// whatever process needed to render a 3D image that returns True if successful and False otherwise
  }
}

public class DisplayTest {
  @Test
  public void testItDisplaysContentOnDisplayer() {
    /// Concrete use of a Displayer(TV) with Content(Image)
    Content image = new Image();
    Displayer tv = new TV();
    assertTrue(tv.display(image));

    /// Concrete use of a Displayer(ThreeDMonitor) with Content(ThreeDFigure)
    Content threeDFigure = new ThreeDFigure();
    Displayer threeDMonitor = new ThreeDMonitor();
    assertTrue(threeDMonitor.display(threeDFigure));
  }
}
```

We have found a way to pass on, or inject, objects in an abstract way. This allow our classes to be able to "talk" to abstractions and not as we would normally think, in a concrete way. We have created a **Content** class that encapsulates contents such as images, figures, strings, and much more.

## Interface Segregation Principle

Finally, I would like to talk about the **I** in **SOLID**. This principle rounds up the two principles already explained above. Thinking in an abstract way may lead to other problems, such as getting the incorrect abstraction. Let's imagine a **Car** and an **Airplane**, both concrete objects can be encapsulated in a **Movable**, right? What does a Movable object does? It moves.

With the new knowledge and skills provided by Open-Closed and Dependency Inversion principles, someone could write the following:

```java
public interface Movable {
  public int move(int distance);
}

public class Car implements Movable {
  @Override
  public int move(int distance) {
    /// make the Car move whatever distance needed by its means
  }
}

public class Airplane implements Movable {
  @Override
  public int move(int distance) {
    /// make the Airplane move whatever distance needed by its means
  }
}

public class VehicleTest {
  @Test
  public void testItMovesTheDesiredDistance() {
    int distance = 10;

    /// Concrete use of a Car
    Movable car = new Car();
    assertEquals(car.move(distance), distance);

    /// Concrete use of an Airplane
    Movable airplane = new Airplane();
    assertEquals(airplane.move(distance), distance);
  }
}
```

That's a correct use of Open-Closed and Dependency Inversion. But think about it, an Airplane could have other attributes or actions that a Car may not, for example lifting off, landing, or many others. For now, we are happy with our implementations since our needs are only for a Movable object. A time will come when we will want to land the Airplane and write the following:

```java
public class AirplaneTest {
  @Test
  public void testItLandsSafely() {
    /// Concrete use of an Airplane
    Movable airplane = new Airplane();
    assertTrue(airplane.land());
  }
}

```

This will affect the Car class having to implement a false action, which does not abide to its real abstraction:

```java
public interface Movable {
  public int move(int distance);
  public boolean land();
}

public class Car implements Movable {
  ...

  @Override
  public boolean land() {
    return false;
  }
}

public class Airplane implements Movable {
  boolean crashed = false;
  ...

  @Override
  public boolean land() {
    return crashed ? false : true;
  }
}
```
Why is the Car forced to use the ```land``` method when it does not even fly? This is what's called, having a "fat" interface. This interface can continue to grow in an uncontrolled way, til we have a big blob of mixed methods, leaving us with something different than that abstraction we first conceived. So what to do? I'd argue that an Airplane is really a **Flyable** and a Car is really a **Drivable**.

```java
public interface Drivable {
  public boolean drive(Address address);
}

public interface Flyable {
  public void fly(Vector vector);
  public boolean land();
}

public class Car implements Drivable {
  @Override
  public boolean drive(Address address) {
    /// drive the Car to the desired Address, return True when done
  }
}

public class Airplane implements Flyable {
  Vector currentVector;

  public Vector currentVector() {
    return currentVector;
  }

  @Override
  public void fly(Vector vector) {
    /// fly the Airplane on the desired Vector
  }

  @Override
  public boolean land() {
    /// land the airplane and return True if it landed safely, false otherwise
  }
}

public class VehiclesTest {
  @Test
  public void testCarGetsDrivenToTheDesiredAddress() {
    Address address = "My House 123";

    /// Concrete use of a Car
    Drivable car = new Car();
    assertTrue(car.drive(address));
  }

  @Test
  public void testAirPlaneGetsFlownCorrectly() {
    Vector vector = (1, 2, 3);

    /// Concrete use of an Airplane
    Flyable airplane = new Airplane();

    airplane.fly(vector);
    assertEquals(airplane.currentVector(), vector);

    assertTrue(airplane.land());
  }
}
```

We have separated and segregated interfaces, now we can have any type of flyable objects as well as drivable objects. I have learned that this principle is one of the most difficult to implement and equally important as the Open-Closed and Dependency Inversion Principles. More than often than not, I have had to go back and re-implement my interfaces or abstractions cause I got them wrong since the beginning. Having such interfaces is like bringing too many luggage to a simple field trip. Eventually, having to carry the extra weight will get tiring and slow you down.

I hope I was able to level this three principles to a learnable and readable way and urge the reader to practice and keep his eyes open for abstractions. Remember, writing tests first will definitely help you identify them.
