# Elm - Introduction

## Installation, Editors and Plugins

To install Elm, follow the official [installation guide](https://guide.elm-lang.org/install.html). There is also a section about how to configure different editors to work with Elm.

Make sure to install [elm-format](https://github.com/avh4/elm-format) to your editor as well.

There is also an online live editor for Elm called [Ellie](https://ellie-app.com).

## What is Elm

Elm is language **and** a framework for building front-end web applications.

It is a reactive pure functional programming language with syntax inspired by Haskell that compiles to JavaScript. It is designed for building reliable web applications with no runtime exceptions. The [compiler](https://github.com/elm/compiler) is implemented in Haskell.

### Advantages

- static strong type system, no `null` or `undefined` (static code analysis, when it compiles, it works)
- pure functions (no side effects, allows tree shaking on a function level)
- everything necessary for building front end apps is already included in the language
- standard ways how to do things, so it is easy to understand other people's code
- easy refactoring

### Disadvantages

- need for learning a new language
- sometimes, more code is needed than it would be in JavaScript (e.g., when parsing JSONs)
- harder to find developers for Elm projects than for JavaScript projects.

### Compared to JavaScript

Elm has built-in common tools and features that are part of typical JavaScript stack.

| JavaScript      | Elm      |
| --------------- | -------- |
| npm/yarn        | built-in |
| Webpack         | built-in |
| React           | built-in |
| Redux           | built-in |
| TypeScript/Flow | built-in |
| Immutable.JS    | built-in |

## Elm Language

### Types

### Record

Records contain keys and values. Each value can have a different type.

```elm
> vector4 = { w = 4, x = 10, y = 12, z = 3 }
{ w = 4, x = 10, y = 12, z = 3 }
    : { w : number, x : number1, y : number2, z : number3 }

> scatterChart = { points = [ { x = 11, y = 8 } ) ], title = "Bar chart", xAxis = "x", yAxis = "y" }
{ points = [ { x = 11, y = 8 } ) ], title = "Bar chart", xAxis = "x",   yAxis = "y" }
    : { points : List { x : number1, y : number2 }, title : String, xAxis : String, yAxis : String }
```

For accessing record properties, Elm has by default accessors defined as `.<key>`. They can be used as `<record>.<key>`, but it is just syntactic sugar, they are just functions.

```elm
> vector4.x
10 : number

> .x vector4
10 : number

> List.map .x [ vector4, vector4, vector4 ]
[ 10, 10, 10 ] : List number
```

If we have a look at the type of `.x` accessor, it says it is any record that has a field `x` of type `a` and returns `a`.

```elm
> .x
<function> : { b | x : a } -> a
```

Since everything is immutable, records cannot be updated. We can create updated records though:

```elm
> { vector4 | x = 20 }
{ w = 4, x = 20, y = 12, z = 3 }
    : { w : number1, x : number, y : number2, z : number3 }
```

We can use pattern matching (desctructuring) for record keys:

```elm
> length { w, x, y, z } = sqrt (w * w + x * x + y * y + w * w)
<function> : { b | w : Float, x : Float, y : Float, z : a } -> Float
> length vector4
16.61324772583615 : Float
```

### Type alias

Type aliases are used to give a new name to existing types. It is useful for naming record types.

```elm
type alias Name =
    String


type alias Age =
    Int


type alias Person =
    { name : Name
    , age : Age
    }


isAdult : Person -> Bool
isAdult { age } =
    age >= 18


getName : Person -> Name
getName { name } =
    name


getName2 : Person -> String
getName2 { name } =
    name
```

```elm
> import Lib exposing (..)

> joe = { name = "Joe", age = 21 }
{ age = 21, name = "Joe" }
    : { age : number, name : String }

> isAdult joe
True : Bool

> joe = Person "Joe" 21
{ age = 21, name = "Joe" } : Person
```

_Note_: Type aliases are resolved in compiled time. Therefore, they cannot be recursive. For recursion, we need to use custom types.

### Custom Types

We can define custom types that have several variants. We can also associate data with a variant.

```elm
type Animal
    = Cat
    | Dog


type Tree a
    = Leaf a
    | Branch (Tree a) (Tree a)


type Profile
    = Loading
    | Error String
    | Success Person



animal = Dog

tree = Branch (Leaf 1) (Branch (Leaf 2) (Leaf 0))

profile = Error "Cannot load profile"

```

_Note_: There are two more complex techniques, how to design data structure - opaque types and phantom types.

### Pattern Matching

```elm
isDog : Animal -> Bool
isDog animal =
    case animal of
        Cat ->
            False

        Dog ->
            True
```

We can use wildcard `_` for all other branches in the `case` statement or for the variables we don't need.

```elm
isLoading : Profile -> Bool
isLoading profile =
    case profile of
        Loading ->
            True

        _ ->
            False


isLoading2 : Profile -> Bool
isLoading2 profile =
    profile == Loading


isLoading3 : Profile -> Bool
isLoading3 profile =
    case profile of
        Loading ->
            True

        Error _ ->
            False

        Success _ ->
            False


profileStatus : Profile -> String
profileStatus profile =
    case profile of
        Loading ->
            "Loading"

        Error error ->
            "Error: " ++ error

        Success _ ->
            "Success!"

```

We can use `::` operator for matching first element and rest of the list.

```elm
sum : List number -> number
sum list =
    case list of
        head :: tail ->
            head + sum tail

        [] ->
            0
```

### Maybe

`Maybe` is used when a result doesn't have to exist. Unlike `null` in JavaScript, we are forced to handle that case.

```elm
type Maybe a
  = Just a
  | Nothing
```

For example empty list doesn't have a head.

```elm
> List.head
<function> : List a -> Maybe a
```

We can use the `Maybe` type in `case` statement as any other custom type.

```elm
hello : String -> String
hello name =
    "Hello, " ++ name ++ "!"


greet : Maybe String -> String
greet maybeName =
    case maybeName of
        Just name ->
            hello name

        Nothing ->
            "Nobody's here"
```

[Maybe](https://package.elm-lang.org/packages/elm/core/latest/Maybe) package contains a handful of useful functions to simplify working with maybes.

```elm
> Maybe.withDefault
<function> : a -> Maybe a -> a

> Maybe.withDefault "default" (Just "value")
"value" : String

> Maybe.withDefault "default" Nothing
"default" : String
```

```elm
> Maybe.map
<function> : (a -> b) -> Maybe a -> Maybe b

> Maybe.map ((*) 2) (Just 4)
Just 8 : Maybe number

> Maybe.map ((*) 2) Nothing
Nothing : Maybe number
```

```elm
greet2 : Maybe String -> String
greet2 maybeName =
    Maybe.withDefault "Nobody's here" (Maybe.map hello maybeName)
```

### let expressions

It is sometimes handy to define some expression within a function to avoid repetition. We have let expressions for that.

```elm
cubeArea : Float -> Float
cubeArea edge =
    let
        face =
            edge ^ 2
    in
    6 * face
```

### Operators |>, <|, >>, <<

Elm has several operators for chaining functions and function calls together.

#### |>

`|>` operator takes a value and a function and applies the function to the value.

```elm
> (|>)
<function> : a -> (a -> b) -> b
```

It is useful when chaining more steps together to write readable code.

```elm
greet3 : Maybe String -> String
greet3 maybeName =
    maybeName
        |> Maybe.map hello
        |> Maybe.withDefault "Nobody's here"
```

#### <|

`<|` operator is the opposite. It takes a function and a value and apply the function to the value.

```elm
> (<|)
<function> : (a -> b) -> a -> b
```

It is useful to avoid parentheses, the same as `$` in Haskell.

```elm
greet4 : Maybe String -> String
greet4 maybeName =
    Maybe.withDefault "Nobody's here" <| Maybe.map hello maybeName
```

#### >>

`>>` is used for function composition - `(f >> g) x == g(f x)`.

```elm
> (>>)
<function> : (a -> b) -> (b -> c) -> a -> c
```

```elm
greet5 : Maybe String -> String
greet5 =
    Maybe.map hello >> Maybe.withDefault "Nobody's here"
```

#### <<

`>>` is used for function composition in an opposite direction - `(f << g) x == f(g x)`. This is same as `.` in Haskell.

```elm
> (<<)
<function> : (b -> c) -> (a -> b) -> a -> c
```

```elm
greet6 : Maybe String -> String
greet6 =
    Maybe.withDefault "Nobody's here" << Maybe.map hello
```

## The Elm Architecture (TEA)

The Elm Architecture is a pattern used by Elm applications to define the architecture. It is perfect for modularity, refactoring, code reuse and testing. It is easy to keep even the complex applications clean and maintainable with the TEA.
The Elm application has three main parts:

- **Model** - The state of the application.
- **Update** - How to change the state.
- **View** - How to display the state.

There are also Subscribers and Commands. We will talk about them later.

![The Elm Architecture](./images/tea.png)

## Elm program

Elm program is set up using [Browser](https://package.elm-lang.org/packages/elm/browser/latest/Browser) module from [elm/browser](https://package.elm-lang.org/packages/elm/browser/latest/) package. There are several functions to do that based on the use case.

- `sandbox` - Program that interacts with the user input but cannot communicate with the outside world.
- `element` - HTML element controlled by Elm that can talk to the outside world (e.g. HTTP requests). Can be embedded into a JavaScript project.
- `document` - Controls the whole HTML document, view controls `<title>` and `<body>` elements.
- `application` - Creates single page application, Elm controls not only the whole document but also Url changes.

## JSON

It is very common to use JSON format when communicating with different APIs. In JavaScript, JSON is usually turned into a JavaScript object and used within the application. However, this is not the case in Elm since we have a strong type system. Before we can use JSON data, we need to convert it into a type defined in Elm. There is the [elm/json](https://package.elm-lang.org/packages/elm/json/latest/) package for that.

### Decoders

Elm use decoders for that. It is a declarative way how to define what should be in the JSON and how to convert it into Elm types. Functions for that are defined in [Json.Decode](https://package.elm-lang.org/packages/elm/json/latest/Json-Decode) module.

For example, we have this JSON representing a TODO:

```json
{
  "id": 24,
  "label": "Finish the home",
  "completed": false
}
```

To get the `label` field, we can define a decoder like this:

```elm
import Json.Decode as Decode

labelDecoder : Decode.Decoder String
labelDecoder =
    Decode.field "label" Decode.string
```

There are functions to decode other primitives, like `bool` or `int`. However, we usually need more than just one field. We can combine decoders using `map` functions from `Json.Decode` module, e.g. `map3`.

```elm
import Json.Decode as Decode

map3 :
    (a -> b -> c -> value)
    -> Decode.Decoder a
    -> Decode.Decoder b
    -> Decode.Decoder c
    -> Decode.Decoder value
```

We can then define our own type for TODO and a decoder.

```elm
import Json.Decode as Decode

type alias Todo =
    { id : Int
    , label : String
    , completed : Bool
    }

todoDecoder : Decode.Decoder Todo
todoDecoder =
    Decode.map3 Todo
        (Decode.field "id" Decode.int)
        (Decode.field "name" Decode.string)
        (Decode.field "completed" Decode.bool)
```

There is a package [NoRedInk/elm-json-decode-pipeline](https://package.elm-lang.org/packages/NoRedInk/elm-json-decode-pipeline/latest) for more convenient JSON decoders. It is especially useful for large and more complex objects. We could rewrite the previous example using pipeline:

```elm
import Json.Decode as Decode exposing (Decoder)
import Json.Decode.Pipeline as Pipeline

type alias Todo =
    { id : Int
    , label : String
    , completed : Bool
    }

todoDecoder : Decode.Decoder Todo
todoDecoder =
    Decode.succeed Todo
        |> Pipeline.required "id" Decode.int
        |> Pipeline.required "name" Decode.string
        |> Pipeline.required "completed" Decode.bool
```

It is not that big change in this case, however, we only have `map8` function in `Json.Decode` so this library comes handy if we need more. Moreover, it has other functions to define for example [optional](https://package.elm-lang.org/packages/NoRedInk/elm-json-decode-pipeline/latest/Json-Decode-Pipeline#optional) or [hardcoded](https://package.elm-lang.org/packages/NoRedInk/elm-json-decode-pipeline/latest/Json-Decode-Pipeline#hardcoded) values.

### Encoders

When we want to send something to an API we need to do the opposite -- turn the Elm value into JSON value. We use functions from [Json.Encode](https://package.elm-lang.org/packages/elm/json/latest/Json-Encode) package for that. There is a type called `Value` which represents a JavaScript value and functions to convert Elm primitives, lists and objects into `Value` type.

Here's an example using the TODO from decoders example.

```elm
import Json.Encode as Encode

type alias Todo =
    { id : Int
    , label : String
    , completed : Bool
    }

encodeTodo : Todo -> Encode.Value
encodeTodo todo =
    Encode.object
        [ ( "id", Encode.int todo.id )
        , ( "label", Encode.string todo.label )
        , ( "completed", Encode.bool todo.completed )
        ]
```

The object is representend as a list of key value tuples.

## Http

There is [Http](https://package.elm-lang.org/packages/elm/http/latest/Http) module in [elm/http](https://package.elm-lang.org/packages/elm/http/latest/) package for making HTTP requests in Elm. The functions creating requests create a command for Elm runtime which defines what request should be made, what is the expected response and what message should be send to update function when the request is done.

Here is an example for getting TODO using the decoder defined in previous section.

```elm
import Http

type Msg =
    GotTodo (Result Http.Error Todo)

getTodo : Cmd Msg
getTodo =
    Http.get
        { url = "http://example.com/todo"
        , expect = Http.expectJson GotTodo todoDecoder
        }
```

The function `getTodo` creates a command with HTTP request that expect JSON to be returned and uses `todoDecoder` to get `Todo` type from the returned JSON. Once the request is finished, we get `GotTodo` message containing the `Result` with either `Http.Error` if the request failed or `Todo` if the request was successful.

There are other functions we can use for expected response like `expectString` to get the string as is or `expectWhatever` when we don't really care about the response as long as it's ok.

When we want to do a POST request we also need to define the body. Here's an example of posting TODO to the server, using encoder function from previous section.

```elm
import Http

type Msg =
    TodoSaved (Result Http.Error ())

postTodo : Todo -> Cmd Msg
postTodo todo =
    Http.post
        { url = "http://example.com/todo"
        , body = Http.jsonBody <| encodeTodo todo
        , expect = Http.expectWhatever TodoSaved
        }
```

Of course, we can send different types of body, not just JSON, e.g., `stringBody` for plain string or `emptyBody` when we don't want to send anything.

When we want to do a different type of request than GET and POST or we want to set headers, we need to use `Http.request` function (`Http.post` and `Http.get` are actually just a shorthand for calling `Http.request`).

```elm
request :
    { method : String
    , headers : List Http.Header
    , url : String
    , body : Http.Body
    , expect : Http.Expect msg
    , timeout : Maybe Float
    , tracker : Maybe String
    }
    -> Cmd msg
```

## Subscriptions

[Subscriptions](https://package.elm-lang.org/packages/elm/core/latest/Platform-Sub) are used to tell Elm that we want to be informed if something happend (e.g., web socket message or clock tick).

Here's an example of subscriptions defining that a message `Tick` with current time should be send to update function every 1000 milliseconds.

```elm
import Time


type alias Model =
    ()

type Msg =
    Tick Time.Posix


subscriptions : Model -> Sub Msg
subscriptions model =
    Time.every 1000 Tick
```
