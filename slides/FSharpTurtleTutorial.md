- title : F# Turtle Tutorial
- description : Introduction to F#
- author : Stuart Lang & Luke Merrett
- theme : night
- transition : default

***

### F# Turtle Tutorial

Introduction

***

### Exercise Setup

Clone the [F# Turtle Tutorial](https://github.com/FSharpBristol/FSharpTurtleTutorial)

Walk through the `README.md` file to get F# running locally

Then open `./Exercise/TurtleRunner.fs`

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
    | SetColour colour -> "set colour to %A" colour
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

Below is the logic you will need to move the turtle based on it's current angle.

```fsharp
let angleInRads = turtle.angle * (Math.PI/180.0)
{turtle with 
    xpos = turtle.xpos + (distance * Math.Sin(angleInRads))
    ypos = turtle.ypos + (distance * Math.Cos(angleInRads))}
```

Copy this in after `| Move distance ->` in the `processCommand` function.

Now finish implementing the rest of the pattern match expressions for `Turn`, `SetPen` and `SetColour`

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

Uncomment all code under 

```fsharp
// --- Uncomment this section to run a full test ---
```

In VS Code, select the whole file except the `module TurtleRunner` line and press Alt+Enter to send to F# Interactive

If the exercise is completed, this should run without error returning a Turtle in the state:

```fsharp
{xpos = 40.0
 ypos = -20.0
 angle = 180.0
 penState = Up
 colour = Red}
 ```

Next, on the command line, cd into the "Unit-Tests" folder from root and run `dotnet test`.  All tests should now pass.

***

### Where Next?

If you'd like to continue learning F#:

* [Do the "Why Use F#" series on F# For Fun And Profit](https://fsharpforfunandprofit.com/series/why-use-fsharp.html)
* [Do the F# Koans from Chris Marinos](https://github.com/ChrisMarinos/FSharpKoans)
* [Watch Scott's Talk on Domain Driven Design in F#](https://fsharpforfunandprofit.com/ddd/)
* [Come to the F# |> Bristol meetup](https://www.meetup.com/FSharpBristol/)
