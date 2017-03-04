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

Todo

--- 

### Record Types

Todo

---

### Discriminated Unions with Values

Todo

---

### Function Signatures

Todo

---

### Pattern Matching

```fsharp
match command with
    | Move distance -> printfn "move %f" distance
    | Turn(direction, degrees) -> printfn "turn %A %f" direction degrees
    | SetPen state ->  printfn "set pen to %A" state
    | SetColour colour -> "set colour to %A" colour
```

---

### With Syntax on Record types

```fsharp
{turtle with penState=state}
```

---

### Note on Move logic

Below is the logic you will need to move the turtle based on it's current angle

```fsharp
let angleInRads = turtle.angle * (Math.PI/180.0) * 1.0
{turtle with 
    xpos = turtle.xpos + (distance * Math.Cos(angleInRads))
    ypos = turtle.ypos + (distance * Math.Sin(angleInRads))}
```

---

### Lists

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

This creates a `List<Command>`

---

### Creating Record Types

```fsharp
let turtle = {xpos=0.0; ypos=0.0; angle=90.0; penState=Down; colour=Black}
```

Each value of the record type must be supplied, there are no nulls.

Note how we've not specified the name of the record type being instantiated, the F# compiler infers this from the property names

--- 

### Pipe Forward Operator

```fsharp
let movedTurtle = commands |> List.fold processCommand turtle
```

The pipe forward operator `|>` takes the preceding value and puts it into the last argument of the following function.

This is really syntax sugar however leads to code that states it's intentions clearly, such as

```fsharp
File.ReadAllText "appsettings.json" 
|> JsonConvert.DeserializeObject<AppSettings> 
|> ConnectToDatabase
|> GetUserList 

```

---

***

### Where Next?

If you'd like to continue learning F#:

* [Do the "Why Use F#" series on F# For Fun And Profit](https://fsharpforfunandprofit.com/series/why-use-fsharp.html)
* [Watch Scott's Talk on Domain Driven Design in F#](https://fsharpforfunandprofit.com/ddd/)
* [Come to the F# |> Bristol meetup](https://www.meetup.com/FSharpBristol/)
