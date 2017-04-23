- title : F# Turtle Tutorial
- description : Introduction to F#
- author : Stuart Lang & Luke Merrett
- theme : night
- transition : default

***

### What is F#?

F# is a strongly typed functional programming language built atop the .NET CLI

Originally developed by Don Syme from Microsoft Research, it has the conciseness of Python, strictness of Scala & ecosystem of .NET.

---

### Benefits 

[Taken from Scott Wlaschin's summary here](https://fsharpforfunandprofit.com/why-use-fsharp/)

The "5Cs" of F#...

---

### Conciseness

F# gets rid of a lot of the clutter / noise, removing curly brackets, semi colons etc:

```fsharp
// Functions are very light weight
let add x y = x + y

// This is a Record Type declaration; an immutable DTO
type Person = {First:string; Last:string}

// Here we create an instance of the Person type
// It's type is inferred by the property names
let bob = {First="Bob"; Last="Baggings"}
```

A lot of this is due to a powerful type inference system the compiler uses than can work out static types without having to explicitly declare them.

---

### Convenience

The lightweight nature of F# makes common programming tasks simpler.  This is especially true for rapidly building domain models.

```fsharp
// Low overhead type definitions
type Coord = {Lat:float; Long:float}

type TimePeriod = Hour | Day | Week | Year
type Temperature = C of int | F of int
type Appointment = OneTime of DateTime | Recurring of DateTime list

// "Railway orientated" concise, easy to read chains of logic
config
|> TableManager.createTable client
|> TableManager.waitForTableCreation client
|> DataManager.streamRowsFromSourceFile
|> DataManager.loadRowsIntoDynamo client
```

As functions are first class citizens, the emphasis is on functional composition of reusable code over inheritance.

----

### Correctness

Values are immutable by default, nulls are replaced with `Option` types, and it supports explicit units of measure, preventing a large class of errors.

```fsharp
let bob = {First="Bob"; Last="Baggings"}
bob.First <- "Jim" // Assignment error

// Option types explicitly capture that a value may not be present
// These can be thought of like Nullable<T> types in .Net
let getResponseBody: string Option = 
    match response.StatusCode with
    | 200 -> Some response.Body
    | 404 -> None

let distance = 10<m> + 10<ft> // errors, can't add meters to feet
```

As F# is strongly typed, you get all the compile time benefits of .Net without the overhead.

---

### Concurrency

F# has a set of built in libraries for asynchronous logic; not only with the `async {}` wrapper but also a built-in actor model.

```fsharp
Async.Parallel [ for i in 0..40 -> async {
    return fib(i)
}]

MailboxProcessor.Start(fun inbox-> async {
    let! msg = inbox.Receive()
    printfn "message is: %s" msg
}) 
```

This is enhanced by the focus on immutable data structures which allow sharing of state & avoid locking of resources.

---

### Completeness

Whilst F# focused on being functional, it is a multi-paradigm language that supports OO if required.

As it is built ontop of the .Net CLI, it has full access to the existing .Net ecosystem, NuGet packages and C# assemblies.

```fsharp
// Values can be made mutable if necessary
let mutable counter = 0

// .Net libaries can be used seamlessly
open Newtonsoft.Json
let person = JsonConvert.DeserializeObject<Person>(content)

// Out parameters are actually handled in a cleaner way
let (successfully, parsedValue) = System.Int32.TryParse("123");
```

Supported in both .Net 4.5+ and .Net Core.  Has tooling for Visual Studio 2012 onwards & Visual Studio Code

***

### The Exercise

We'll be working through an F# script that models a Logo Turtle, allow us to give it basic commands such as `Move 10`, `Turn Left 90`, `Pen Down` and `Set Colour Green`.

The script is incomplete; as we walk through the workshop we will be adding to the code until it is a functional Logo program.

At the end we'll pass a series of commands to our Turtle and check it responds as expected.

---

### Exercise Setup

Clone the [F# Turtle Tutorial](https://github.com/FSharpBristol/FSharpTurtleTutorial)

Walk through the `README.md` file to get F# running locally

Then open `./Exercise/TurtleRunner.fs`

---

### The FSI

The F# tools you've installed include a REPL called "FSI".  This will let us execute each part of the tutorial without having to build each time, whilst allowing us to execute individual lines or sections of code on the fly.

To use this in VS Code, select the lines you wish to execute and press Alt+Enter.  This will send the code to FSI and run it, giving you back the declared types and output.

Don't select the `module TurtleRunner` line when using FSI, it won't be able to interpret it.

---

### Basic Discriminted Unions

```fsharp
type Animal = Cat | Dog | Fish | GuineaPig
```

Otherwise known as option types; in the most basic sense these can be thought of as enums.

Fill in the exercise script so we have:

* Pen states of "Up" and "Down"
* Colours of "Black", "Red", "Blue" and "Green"
* Turn Directions of "Left" and "Right"

--- 

### Record Types

```fsharp
type Person = {firstName:string; lastName:string; age:int}
```

Record types can be thought of as sealed classes with a set of read only properties.

They act like value types, as in when equality is checked the value of all properties is used, not the object reference.

Fill in the Turtle record so it has these properties:

* "xpos" - float type
* "ypos" - float type
* "angle" - float type
* "penState" - PenState type
* "colour" - Colour type

---

### Discriminated Unions with Values

Here's the true power of discriminated unions; they allow us to declare values that must be provided to initialise them.

```fsharp
type Shape =
    | Circle of int 
    | Rectangle of int * int
    | Point of int * int
```

To initialise a `Rectangle` we'd then write `Rectangle(40,20)`.

Fill in the Command type so it has these choices:

* Move which takes in a float
* Turn which takes a TurnDirection and a float 
* SetPen which takes in a PenState
* SetColour which takes in a Colour

---

### Functions

```fsharp
let add x y = x + y
```

Functions in F# are extremely light weight, this is as the compiler can infer types for most cases.

Here note as well we aren't saying return explicitly; instead F# always returns the last value in the function.

```fsharp
let add (x:int) (y:int): int = x + y
```

Above is an example with explicit declarations of the types expected.  The signature would be `int -> int -> int` with the last type being the return type.

---

### Pattern Matching

We've got a set of commands which are of the discriminated union type `Command`; so how do we decompose the values based on the type of command?

```fsharp
match command with
    | Move distance -> printfn "move %f" distance
    | Turn(direction, degrees) -> printfn "turn %A %f" direction degrees
    | SetPen state ->  printfn "set pen to %A" state
    | SetColour colour -> printfn "set colour to %A" colour
```

Pattern matching allows you to check type **and** bind the values inside that type in one step.  Think switch statements on crack.

Apart from being succinct, one of the core benefits of pattern matching is that the compiler warns you if any of the possible states have been missed.

Copy this code into the `processCommand` function for now.

---

### Immutability and the "With" Syntax  

We have the `processCommand` function with the signature `Turtle -> Command -> Turtle`; so we know we need to return a modified turtle based on the command we have received (e.g: the turtle has moved 10 places).

However F# types are immutable by default, so we need a way to create a **new** `Turtle` record type to pass back.

```fsharp
{turtle with xpos=20, ypos=10}
```

The `with` syntax in F# lets us do this without copying each value manually to a new record type, here it's saying:

"Create a new record type instance using all fields in turtle but change xpos and ypos"

---

### Implementing Process Command

Below is the logic you will need to move the turtle based on its current angle.

```fsharp
let angleInRads = turtle.angle * (Math.PI/180.0)
{turtle with 
    xpos = turtle.xpos + (distance * Math.Sin(angleInRads))
    ypos = turtle.ypos + (distance * Math.Cos(angleInRads))}
```

Copy this in after `| Move distance ->` in the `processCommand` function.

Now finish implementing the rest of the pattern match expressions for `Turn`, `SetPen` and `SetColour`

---

### Using the Process Command

Now we've implemented the solution, we can uncomment all code under:

```fsharp
// --- Uncomment this section to run a full test ---
```

---

### Lists

The below logic creates a `List<Command>` to test our domain and function.

```fsharp
let commands = [
    Move 20.0
    Turn(Left, 90.0)
    Move 20.0
    Turn(Right, 90.0)
    SetColour Red 
    Move 20.0
    Turn(Right, 90.0)
    SetPen Up 
    Move 40.0
]
```

---

### Creating Record Types

This creates our initial turtle record:

```fsharp
let turtle = {xpos=0.0; ypos=0.0; angle=90.0; penState=Down; colour=Black}
```

Each value of the record type must be supplied, there are no nulls.

Note how we've not specified the name of the record type being instantiated, the F# compiler infers this from the property names

--- 

### Pipe Forward Operator

The pipe forward operator `|>` takes the preceding value and puts it into the last argument of the following function.

```fsharp
let movedTurtle = commands |> List.fold processCommand turtle
```

This is really syntax sugar however leads to code that states it's intentions clearly, such as

```fsharp
File.ReadAllText "appsettings.json" 
|> JsonConvert.DeserializeObject<AppSettings> 
|> ConnectToDatabase
|> GetUserList 

```

---

### Testing the Logic

In VS Code, select the whole file except the `module TurtleRunner` line and press Alt+Enter to send to F# Interactive

If the exercise is completed, this should run without error returning a Turtle in the state:

```fsharp
{xpos = 40.0; ypos = -20.0; angle = 180.0; penState = Up; colour = Red}
 ```

Next, on the command line, cd into the "Unit-Tests" folder from root and run `dotnet test`.  All tests should now pass.

***

### Where Next?

If you'd like to continue learning F#:

* [Do the "Why Use F#" series on F# For Fun And Profit](https://fsharpforfunandprofit.com/series/why-use-fsharp.html)
* [Do the F# Koans from Chris Marinos](https://github.com/ChrisMarinos/FSharpKoans)
* [Watch Scott's Talk on Domain Driven Design in F#](https://fsharpforfunandprofit.com/ddd/)
* [Come to the F# |> Bristol meetup](https://www.meetup.com/FSharpBristol/)
