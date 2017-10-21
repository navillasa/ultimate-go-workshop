# Ultimate Go with Bill Kennedy

## my stream of conscious-esque notes of bill advice

- mechanical sympathy— learn about the hardware and operating system to be sympathetic
- “engineering not about writing a piece of software and watching it work in production”
    - anyone can google source code and get code that works
    - it’s more about— writing software that runs when shit hits the fan
#### understand every decision you’re making, the benefit you’re getting from that decision, and the cost (because nothing is free)
- champion quality, efficiency, simplicity
- have a point of view — value introspection and self-review
- easy to adopt new technology but hard to adopt new ways of thinking
- if you’re stuck for twenty minutes, then ask for help
#### is performance the most important priority? (no!!!!!!!)
- C is more bugs.. // trade off between performance and productivity
- “the most amazing achievement of the computer software industry is its continuing cancellation of the steady and staggering gains made by the computer hardware industry.” - henry petroski 2015
- replace C with go and it matches.
- can almost do anything u want by coding very straightforwardly.
- go doesn’t have virtual machine like C#, ruby, etc.
#### you have to visualize how your code will run on the machine.
- optimizing for correctness
- “idiomatic” is whatever— better to have your code in production lol
- Go is 6 years old— tiny; so also a tiny amount of bad code
- guess who’s the expert, since people barely know it lol
- look at it as— you’re doing to have to explain/teach this material in the future
- every problem is a data problem-- or data science problem
    - if you don't understand the data, then you don't understand the problem you're working on
- every 20 lines of code you write produces a bug-- whether you like it or not or if it shows up
#### if you care about integrity, you have to write less code
- the cost of integrity is always performance
#### must make sure that the average developer on your team can comprehend every line of code on your project
- if you're above average, you have to write code that's less clever
#### don't hide the impact of code! when you hide complexity and lose readability, you're hurting yourself
- go doesn't have constructors, destructors, and overloading-- these things <b>hide cost</b>
- you have no clue how this will run just looking at it--
``` C++
Foo f1() {
    return Foo();
}

int main() {
    Foo foo1 = f1();
    std::cout << "*****\n;

    foo1 = f1();
    std::cout << "*****\n";
}
```
- so go wants to get away from that-- the fact that this can have different behavior
- simplicity is about hiding complexity-- therefore is less readable

- go's garbage collector something something memory management