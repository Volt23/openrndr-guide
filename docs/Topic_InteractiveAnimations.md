# Interactive Animations

This section explains how to use OPENRNDR's `Animatable` class as a tool to create interactive animations.
In order to use `Animatable` your project needs to depend on the `rndr-animatable`.

```
dependencies {
    compile "org.openrndr:openrndr-animatable:0.3.0-SNAPSHOT"
}
```

## Animatable
Anything that should be animated inherits the Animatable class. The Animatable class provides animation logic.

Displayed below is a very simple animation setup.

```!java

class MyAnimatable extends Animatable {
    double x;
    double y;
}

MyAnimatable myAnimatable = new MyAnimatable();
public void setup() {
    // sequence 1: animate x then y
    myAnimatable.animate("x", 100.0, 1000).complete().animate("y", 100.0, 1000).complete();

    // sequence 2: animate x and y simultaneously
    myAnimatable.animate("x", 0.0, 1000).animate("y", 0.0, 1000).complete();

    // wait 2 seconds
    myAnimatable.delay(2000);

    // sequence 3, animate x, then animate y a bit later
    myAnimatable.animate("x", 100.0, 1000).delay(100).animate("y", 100.0, 1000).complete();

}

public void draw() {
    myAnimatable.updateAnimations();
    drawer.circle(myAnimatable.x, myAnimatable.y);
}
```

## Animation concepts

### Animating
```!java
Animatable.animate(String variable, double target, long duration);
Animatable.animate(String variable, double target, long duration, Easing easing);

```

Animatable.animate() cues a single animation for a single value. An animation consists of a target value and a duration and optionally a method of easing the animation.

In cases in which the target value is unknown but the increment is known one can use Animatable.add()

```java
Animatable.add(String variable, double increment, long duration);
Animatable.add(String variable, double increment, long duration, Easing easing);

```

It is also possible to animate elements in an array or List:

```java
class MyAnimatable extends Animatable {
    double values[] = new double[4];
}

MyAnimatable myAnimatable = new MyAnimatable();
myAnimatable.animate("values[0]", 1.0, 1000);
```


## Waiting

### Waiting for a period of time
```java
Animatable.delay(long duration);
```

Animatable.delay() instructs the animator to wait for the given amount of time.


### Waiting for a moment in time

Animatable.waitUntil(long time);


### Waiting for an animation to finish
```java
Animatable.complete();
Animatable.complete(String variable);
```

Animatable.complete() instructs the animator to wait until the previously queued animation has completed.

Animatable.complete(String variable) instructs the animator to wait until the last queued animation for the given variable has completed.

This can be used in cases where two animations with different lengths are queued. For example:

```!java
animatable.animate("x", 400, 2000).animate("y",500,1200).complete("x");
```

### Waiting for an animation to almost finish

```!java
Animatable.beforeComplete(long time);
// equivalent:
animatable.complete().delay(-time);
```
Instruct the animator to wait until the given amount of time before the previously queued animation ends.

## Stopping

In interactive applications it may be needed to stop animations to accommodate user actions. Animatable has three options to stop animations.
```!java
Animatable.cancel();
```

Animatable.cancel() cancels all running and queued animations. Animated values remain at their current value. Animatable.cancel() is the most commonly used method for stopping animations.

```!java
Animatable.cancelQueued();
```

Animatable.cancelQueued() cancels all queued animations. Running animations will continue to run.

```!java
Animatable.end();
```

Animatable.end() cancels all running and queued animations. Animated values of running animations will be set to the target value, this will introduce animation pops.