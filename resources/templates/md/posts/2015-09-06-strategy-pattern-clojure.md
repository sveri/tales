{:title "Strategy pattern in clojure"
 :layout :post
 :tags  ["strategy pattern" "clojure"]}
 

This is the first part of an ongoing series. I am currently working through Heads First Design Patterns to refresh my
memory and while the examples are mostly in Java I want to take a look at them and see how to code them in clojure.

## Java Implementation

In the book we have two different kinds of classes: 

1. An abstract `Duck` class with it's concrete implementations: 
  * `MallardDuck`
  * `ReadheadDuck`
  * ...
2. Behaviors `FlyBehavior` and `QuackBehavior` as interfaces with it's concrete implementations:
  * `FlyWithWings`
  * `FlyNoWay`
  * `Quack`
  * `Squeak`
  * `MuteQuack` 
  
The Duck class contains references to the behaviors:
 
* `performQuack`
* `performFly`

A `swim` method with a concrete implementation valid for all subclasses, a `setXXX` Method to change the behavior during 
runtime and finally an abstract `display` method to be defined by the implementors.

## Clojure implementation

3
2,33
2568