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
- go wants to get away from that-- the fact that this can have different behavior
- simplicity is about hiding complexity-- therefore is less readable
- go is a structured programming language
    - functional tries to eliminate possibility of side effects in your software
    - functional programming langs put integrity first
    - HOWEVER, go is a blended language-- 
        - value semantics are functional
        - pointer semantics are NOT functional... introduces possibility of side effects
        - both have pros and cons

- go's garbage collector something something memory management
#### always code from correctness and readability.. simplicity comes later with refactoring

.......

next we're gonna learn

### language syntax
* variables
* struct types
* pointers
* constants
* functions

#### data structures
* arrays
* slices
* maps

#### decoupling
* methods
* interfaces
* embedding
* exporting

.......

## POINTERS

- process - a container of resources for that running program. those resources:
    - memory: addressable space of memory-- 64bits
    - thread: the main thread. when your main thread dies, your process dies. <b>a path of execution</b> for the instructions that you're writing.
    - core: your hardware threads have a core. the job of the operating system to schedule the threads.
- go has a scheduler that sits on top of the operating system-- a <b>GO ROUTINE</b> -- not only is it an independent path of execution, like a thread, it also has its own block of memory we call <b>the stack</b>
-in our programming model:
 * [1] read-only type data go here
 * [2] stacks -- each routine is given its own stack. every thread is given stack space as well. that stack is about one mb. starts out at 2k.
 * [3] heap -- you're gonna have to make decisions about which it's going to be-- stack or heap
 #### "go is a aws sales reps nightmare"
 - we can run on the smallest boxes
 - cloud computing costs can go way down
 - the playground is running on amd64p32 -- integer and wordsize (length of data.. a generic) and addresses are 64bit. or 4 bytes.
 - also it's a <b>single-threaded</b> environment. all concurrency on the playground...
 - because this go routine is running.
    - remember the stack is 2k.
    - we work stacks DOWN --> so addresses going lower in memory as we make these function calls.
    - we also call them stacks because there are push and pop things (but these are very inefficient compared to the <b>POINTER</b>)
- so.. the go routine is ready to execute `main`. which needs to be given some space on the stack called the frame.
#### every function operates within the scope of its own frame.
- that function only has direct memory access to that memory in its frame. can only read and write within the scope of that sandbox or frame.
- every piece of code you write is doing one of the three:
    - allocating memory
    - reading memory
    - writing memory
``` go
func main() {
    // declare variable of type int with a value of 10
    count := 10

    // display the 'value of' and 'address of' count
    // 'value of' is like WHATS IN THE BOX
    // ampersand says 'address of'-- WHERE IS THE BOX
    println("count:\tValue Of[", "]\tAddr Of[", &count, "]")

    // pass the 'value of' the count
    increment(count)

    println("count:\tValue Of[", count, "]\tAddr Of[", &count, "]")
}

// increment declares count as a pointer variable whose value is always an address and points to values of type int
// go:noinline
func increment(inc int) {
    // increment the 'value of' inc
    inc++
    println("inc:\tValue Of[", inc, "]\tAddr Of[", &inc, "]")
}
```
- everything in go is passed by value!!!! what you see is what you get. java, c# have pass by reference, but go is <b>pass by value</b>.
- pointer semantics say: a function absolutely only has access to memory within frame-- but pointer says OUTSIDE-- but only if its directly shared with that function. gives function indirect memory access to memory outside of its frame.
#### pointer benefit is efficiency-- but potential side effects and potential allocation. when a value is not on the stack, but ENDS UP IN THE HEAP.
- not every use of a pointer will cause allocation, but there is potential.
- with value semantics, everyone has its own copy.
- every time we call a function, what are we doing on the stack?
    - taking a frame.
    - memory gets cleaned up-- no need for garbage collector.
    - use--
- when you return an increment, the previous is no longer the active frame.
- what is the size of the frame?
#### every function's frame is calculated by compile time. we know exactly the size of each frame and what's in it at compile time.
- addresses are values (64 or 32bit in the playground)
    - stored inside variable called inc in our example.
``` go
// if we just use * and not *int.. no go
// the whole point of sharing with count is we need to share representation
// so we need to specify type
// provides amount of memory we're working with and what that memory represents-- without type, we can't do anything with any level of integrity
// we're saying give me an address-- but not just any address!!! an integer address!!!
func increment(inc *int) {}
```
#### all pointer vars are same size depending on architecture-- and they all store address
- go having such a strong type system is nice
- when a go program panics, you want it shut down (because you're probably done something against integrity)
#### POINTERS HELP US WITH SHARING
``` go
// if we use the variable by itself-- what's in the box
// & is WHERE is the box
// * is VALUEEEEE -- this is our indirect memory access-- can only happen through direct read of what that address is
// gives us the indirect read operator; we've just changed memory outside of our own frame
*inc++
```
- you probably won't go too far down your function calls in your programming
- that's why we're using smallish stacks
#### if we go to make another function call and there isn't enough space for the frame-- WHAT HAPPENS?
    - we need a new stack!!!
    - we grow the stack 2k times whatever the percentage is (25%)
    - now we have all new addressable space
    - so now we have to copy everything that's in every frame-- has to be copied over to the new stack, and POINTERS have to be chased out too
    - when your stack has to grow, those values are moving
    - pointer's address is now changed
- you're taking the cost for this (which is good you should be penalized for this)
- if garbage collection notices you're using 25% or less than current stack space, then it will cut it down (behind the scenes.. thanks go routine)
``` go
func main() {
    s := "HELLO"
    stackCopy(&s, 0, [size]int{})
}

// stackCopy recursively runs increasing size of the stack
func stackCopy(s *string, c int a[size]int) {
    println(c, s, *s)

    c++
    if c == 10 {
        return
    }

    stackCopy(s, c, a)
}
```
#### contiguous memory is our friend

### escape analysis

escape analysis-- value can no longer be on the stack. the value must ESCAPE to the heap.
go wants for all values to be on stacks, because then you have self-cleaning data and the garbage collector doesn't have to be involved.
allocations in go WILL CAUSE PERFORMANCE ISSUES.

- if you share something in such a way that it's unsafe to leave it on the stack, then the compiler decides it must be moved to the HEAP
- all about convention over integration----- will directly influence what decisions that the compiler will make

``` go
package main

// user represents a user in the system
type user struct {
    name string
    email string
}

// main is the entry point for the application
func main() {
    u1 := createUserV1()
    u2 := createUserV2()

    println("u1", &u1, "u2", &u2)
}

// createUserV1 creates a user value and passed a copy back to the caller
// go:noinline
func createUserV1() user {
    u := user {
        name: "Bill",
        email: "bill@ardanlabs.com",
    }

    println("V1", &u)

    return u
}

// createUserV2 creates a user value and shares the value with the caller
// go:noinline
// ^ this is throwing the default functionality away to get the function call working-- not inline-- gets rid of optimizational
func createUserV2() *user {
    u := user {
        name: "Bill",
        email: "bill@ardanlabs.com"
    }
    println("V2", &u)

    return &u
}
```

- construction of a value doesn't tell you where it will be
- how it's shared determines its location
- if you're sharing something <b>down</b> the call stack, no escape, no problem, because we know it will always be at the bottom
- but if the compiler notices you're sharing up the call stack, then we can't do that thing where all the pointers point to the main frame (haha)
- `u` represents a value on the heap--
    - so a pointer secretly points to the value on the heap
    - we know it's on the heap because it was shared up the call stack
#### no go routine can have a pointer to another go routine's stack--- think of the mess!!!!
- you're the go routine. your notebook is a stack. every page is a frame. to share, you have to copy to the whiteboard (the stack).
    - if someone else needs a piece of information, it needs to go on the whiteboard.
- escape analysis determines whether a value can stay on the stack or needs to go to the heap.
- we will be using the heap!!!! don't be afraid!!!!
    - the benefit is efficiency-- having a copy of something that everyone can share, instead of multiple copies across diff stacks
    - the cost is performance-- once something's on the heap, the garbage collector has to get involved

- if you use `go build -gcflags "-m -m"`
    - compiler will tell you its escape analysis decisions
    - inlining tells you-- we're not going to make a function call, we're going to inline it in place
    - a lot of factory functions can be inlined
- any time you see `&` think SHARE
    - ALWAYS CONSTRUCT VALUES WITH VALUE SEMANTICS
    - leverage `&` for sharing-- DON'T USE `&` IN CONSTRUCTOR OR BILL WILL CRY
``` go
// NEVER DO THIS--- NOTE THE USE OF &user and *u
// THIS MAKES EVERYTHING INSTANTLY UNREADABLE BECAUSE REFERRING SOMEWHERE ELSE AND MAKING POINTER USELESS
func createUserV2() user {
    u := &user {
    name: "Bill",
    email: "bill@ardanlabs.com"
}
println("V1", &u)

return *u

}
```
