# Ultimate Go with Bill Kennedy

following along here!
<https://github.com/ardanlabs/gotraining/tree/master/topics/go/language/pointers>

## my stream of conscious-esque notes of bill's presentation

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
- if you have four cores, go will make 4 processors for each core (modeling the Linux thing)
    - each P is assigned an `m` -- modeling
    - each P also has `G`s
        - one G is always executing
    - this is our CPU capacity! 4 cores, 4 threads
- when your go program is running, the pacing algorithm happens. it's everything!
    - GOGC -- anything there's heap growth, it grows by 100%
        - don't play with 100% unless you need to (minimize resources first! integrity!)
    - GOGC - looks at stats on the heap and makes decisions about when to run
- the heap-- long term memory (size of heap overall)
- GARBAGE = transient values that come into existence because of some function, then later aren't needed anymore
#### garbage collector wants to maintain the smallest heap possible
- let's say, a 4meg heap. on a busy program doing a lot of allocation, can fluctuate from 4-10meg. but NOT one that's constantly growing, or large swings (unless you're working with bursts of data... bottomline: understand what your program does).
- imagine all Ps extremely busy doing work.
- <b>only GO DEBUG will show you if you have a memory leak-- shows you your pace as well</b>
- two stop the world points today: 100GC is bad-- "time to reach out on the mailing list because you got a problem."
- all cooperation in go happens through function calls. if you write a for loop calculating something and no function calls in that loop, <b>you will stall out everything in your program!!!!!</b>
    - everything you're doing NEEDS function calls

### STOP THE WORLD
![visualization of stop the world phases](readme-materials/GC_Algorithm.png)
- [1] for first stop the world, need to bring every P to a safe point, at a function call. within 10 microseconds or less. not a big deal.
- [2] scan stacks - from ? to active frame and heap
    - everything when it starts is PAINTED WHITE
    - after it's scanned, it's painted BLACK then placed into our queue
    - THEN we paint it gray
- [3] then sweep happens because we did everything right
- we're not writing 0 allocation software. we're using the heap. lots of advantages. heap for stuff that NEEDS it, and stack for everything else.
    - FIRST PRIORITY - get program working
    - don't trust anything until it runs (twice ☺️ )
- every go routine has stacks and pointers going to the heap etc.


for testing benchmark:
`go test -run none -bench . -bnechtime 3s`
- will tell you how much time to traverse the linked list-- check out my caching.go file.
- main memory takes a while to access
- from a speed perspective, total amount of memory = total cache
compact data structures that fit in cache are fastest
- non-cache access can slow things down by orders of magnitude (thanks Scott Myers)
    - https://www.youtube.com/watch?v=WDIkqP4JbkE
#### if performance matters, then total amount of memory you have to work with is total cache
- main memory might as well not be there lol

- if you have a 3gigahert clock, you have three clock cycles for every nanosecond of time.
    - you should be able to execute 4 instructions every clock cycle.
    - without these fancy <b>pipeline games</b>, it would take 4 cycles per instruction

```
3GHZ(3 clock cycles/ns) * 4 instructions per cycle = 12 instructions per ns!

L1 - 64KB Cache (Per Core)
    32KB I-Cache
    32KB D-Cache
    2 HW Threads
    4 cycles of latency
    Stalls for 16 instructions or 1.3 ns

L2 - 256KB Cache (Per Core)
    Holds both instructions and data
    2 HW Threads
    11 cycles of latency
    Stalls for 44 instructions or 3.6 ns

L3 - ....

```

#### how do we create predictable access patterns to memory?
- traversal of contiguous blocks of memory --> arrays ❤️
- but in GO --> the most important data structure is the slice
    - slices are dynamic-- a predictable access pattern
    - stack: block of contiguous memory
- java is great, but it has a virtual machine. it lays out memory out contiguously. we don't have a VM here, so to reduce the amount of code we need, we use the slice.
- we want to use slices until we can't because of mechanical sympathy.
- the other cache is the TLB (translation like side buffer cache)
    - a little cache that the OS maintains
    - <b>maps between virtual memory addresses and physical memory addresses</b>
    - what does the OS use to manage memory?????
        - PAGES! usually about 4kb in size (sometimes 2kb)
    - the TLB maintains a page
- hardware has to look in TLB to find the memory piece's physical location
    - if it's not there, then you have to ask the OS
- when we look at the row traversal numbers-- why is it so fast?
    - when we row down the matrix row by row-- the ??? are hiding all of the latency costs
    - but why is column traversal so bad? (8273021 ns/op)
    - linkedlist is in between (5791435 ns/op)
#### it's critical that we use the slice to create predictable access patterns out of the box-- will beat almost anything else in terms of performance
- the most critical data structure for hardware level is the array ❤️❤️❤️❤️❤️
- the problem is getting data INTO the processor, not the clock speed
    - if you're not trying to reduce latency through predictable access patterns, you're not being mechanically sympathetic
- non-uniform memory access-- check out Frank Denneman series: <http://allthingscloud.mscproductions.com/vmware-numa-deep-dive-part-1-uma-numa/>
- facebook doesn't use multisocket-- all single because it's reduces latency
- <b>when you try to do object oriented programming in go, you walk away from mechanical sympathies of predictable access patterns</b>

### STRINGS IN GO
"Strings are, essentially, made up." -bk
(as far as standards across languages)

``` go
var s string
```
* 1st word: pointer
* 2nd word: bytes
- a string with a backing array looks like: `s2 := "hello"`
- two word data structure with a second data structure behind the scenes: bytes
- when the compiler doesn't know the size of something at compile time, it immediately needs to place it on the heap (if literal string)

``` go
var strings [5]string

strings[0] = "Apple"
// this assignment always creates a copy... so what is the cost of this assignment?
// what does index 0 look like after this assignment?
// does index 0 get its own backing array? or do we share one?
// well, pointers are used for SHARING
strings[1] = "Orange"
strings[2] = "Banana"
strings[3] = "Grape"
strings[4] = "Plum"
```
- the 4 range actually implements BOTH semantics: pointer semantics AND value semantics
#### to maintain long-lasting mental models, you NEED to keep your semantics consistent
- pointer semantics --> sharing!!!!
``` go
// iterate over the array of strings
for i, fruit := range strings {
    fmt.Println(i, fruit)
}

// declare an array of 4 integers that is initialized with some values
numbers := [4]int{10, 20, 30, 40}
```
- the string is DESIGNED to stay on your stack
- <b>NEVER EVER take the address of a string and share it</b>
- strings never have to be shared-- they're designed to stay in the stack
- if they end up in the heap, there's wayyyy to much garbage produce that will need to be cleaned up
- when you work with strings, know that you're working with your own copy
- the size of an array is part of its type information
    - the compiler must know the size at compile time since it's part of its type information
- however, a slice is a dynamic data structure
- 

``` go
// using the pointer semantic form of the for range
// the for range makes a copy of the data structure that we're ranging over
// when we ask for v, we're asking for a.. copy of a copy
five := [5]string{"Annie", "Betty", "Charley", "Doug", "Edward"}
fmt.Printf("Bfr[%s] : ", five[1])

for i, v := range five {
    five[1] = "Jack"

    if i == 1 {
            fmt.Printf("v[%s]\n", v)
    }
}
println("\n\n\n\n\n\n\n\n\n")
```
on the other hand, NEVER DO THIS LOL:
```
for i, v := range &five {
    five[1] = "Jack"

    if i == 1 {
            fmt.Printf("v[%s]\n", v)
    }
}
```
- don't share v!!!!!!! it's a local variable, a copy of the original. DONT SHARE IN CONCURRENCY MODE. then you get BAD SIDE EFFECTS.
- ^ new go developers do this sometimes..
- work with slices, get comfortable, they're your core data structure
- all slices are 3-word data structures
    - 1st word = array
    - 2nd word = length
    - 3rd word = capacity
- construction doesn't tell us size, but how it's SHARED
- capacity is total number of elements that can be read from that position
- length is for today
- if you don't know how much memory you need at time of construction, then start at 0
    - if you do, then definitely pre-allocate them!

``` go
func inspectSlice(slice []string) {
    fmt.Printf("Length[%d] Capacity[%d]\n", )
}

func main() {
    // declare a nil slice of strings
    var data []string
    // you could also do this -> data := []string{}

    // capture the capacity of the slice
    lastCap := cap(data)

    // append ~10k strings to the slice
    for record := 1; record <= 102400; record++ {

        // use the built-in function
    }
}
```

### the empty struct!
a zero allocation type!
an empty slice points to the empty struct

``` go
func main() {
    var es struct{}
    
    // declare a nil slice of strings
    var data []string
    data := []string{}

    var u user
    u := user{}
}
```

using the `append` method:

``` go
// capture the capacity of the slice
lastCap := cap(data)

// append ~10k strings to the slice
for record :=1; record <= 102400; record++ {
    
    // use the built-in function `append` to add to the slice
    data = append(data, fmt.Sprintf("Rec: %d", record))

    // when the capacity of the slice changes, display the changes
    if lastCap != cap(data) {

    }
}
```

- memory leaks-----
    - relic new free (in other languages-- need to call all three)
### MEMORY LEAKS IN GO-- it's where there's a reference being held and there shouldn't be
- not necessarily where you're allocating the most.. might get lucky but might not
- are you creating extra go routines when this program is running? where to look if you have a leak!
    - 1. go routine leaks
    - 2. if you're using a map as a cache... sometimes you need to delete some of that cache. you're not cleaning up resources
- once you get to 1000 elements, you're not longer doubling,  no longer 100% growth
- <b>data race</b> is when you have two or more go routines-- where one is doing read, one is doing write, and they're both going to the same place. don't do that!!!!!!
    - compiler will try to do optimizations that it can't do when you're crazy like that.
- 3 index slice allows you to not only ....
    - you know you're about to make an append call that will hopefully attach from the original data structure
- any calls to append in a code review is a RED FLAG---
    - things are already doing append for you. you shouldn't be calling append again
- if performance matters, then you have to write algorithms that are efficient
    - reduce as many allocations as we can
- generic algorithms are too abstract-- hide cost
- have to refactor a little everyday

### DECOUPLING
- decoupling is being used <b>today</b>
- all functions need to be used
- BUT design and architect for tomorrow
    - => BEHAVIOR
* methods
* interfaces
* embedding
* exporting

#### methods allow a piece of data to have a capability
- in go we have both functions and methods (where executed against data itself)
- why will a function always be more readable than any method?
    - functions are methods producing data transformations. if you're writing a function that's almost self-documenting.
    - if you call a method, do you know what state you're using? no! functions are more readable and testable and explicit.
    - a function accepting `v.name` -- A-parameters are better
1) a function is a method when the function has declared a receiver parameter within it. go has two types of receivers:
    * value receivers
    * pointer receivers
you're going to see a mix of receivers until we get to design. in production, you don't want to see a mix of semantics. they hit everyone in the face.
- when do you use one receiver over the other? you cannot have a mix!!!!
#### when to use value semantics or pointer semantics?
* if you're working with the built-in types (integers, strings, bools)
    - ALWAYS USE VALUE SEMANTICS
    - unless you have a VERY good reason
* reference types are functions (maps, slice, ...)
    - VALUE SEMANTICS
    - marshalling is the big exception. otherwise NEVER take the address of these things!
* struct types -- user defined types
    - you need to decide when you're writing the type!!!!
    - we're not mixing! know now!

new type IP is based on a slice of bytes
``` go
type IP []byte
type IPMask []byte

func (ip IP) Mask(mask IPMask) IP {
    if len(mask) == IPv6len....
}
```
lots of value of.
``` go
// what semantic is in play? you must always ask when writing your own type!!!!!!
// two different times are not the same.
// VALUE-- because any mutation is a new data point, not modifying an existing one.
type Time struct {
    sec int64
    nsec int32
    loc *Location
}
```
if you're not sure, always use pointer semantics lol. because it's always safer to share something. sometimes copying is going to cost you.
- you can't make copies of mutexes.
- readability and intuitiveness FIRSTTTTT.

``` go
// factory functions dictate the semantics that will be used. the Now function returns are value of type Time. this means we should be using value semantics and copy Time values.
func Now() Time {
    sec, nsec := now()
    return Time{sec + unixToInternal, nsec, Local}
}
```
Now gives you a copy of a time value
value semantics-- everyone can have their own copy

``` go
func (t Time) Add(d Duration) Time {
    t.sec += int64(d / 1e9)
    nsec := int32(t.nsec) + int32(d%32)
}
```