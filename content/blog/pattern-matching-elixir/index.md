---
title: Elixir Pattern Matching in a nutshell
date: "2017-08-15T20:00:00.000Z"
---

Before being introduced to [Elixir](http://elixir-lang.org), a functional programming language built on top of [Erlang](https://www.erlang.org/), I had no idea what pattern matching was. Hopefully, by the end of this article you will have at least a rudimentary understanding of how awesome it is.

In most programming languages, you will assign a value to a variable using something like:

```js
const myVariable = 'my value';
console.log(myVariable); // 'my value'
```

Now, `myVariable` is bound to the value you assigned to it and you can continue living your life.

When you need to check the value of a variable, in most other languages you would use conditional “if statements”, which can get unreadable as soon as you add more than 2 or 3. This is because it’s difficult to see the flow of logic, especially if the function spans many lines.

Technically you can do the same thing in Elixir, but how the compiler interprets it is significantly different. The `=` sign is actually called the 'match' operator. It will use the value on the left and compare it to the value on the right to determine if they are a match.

Tuples are used frequently in Elixir code to enable returning multiple values from a function. Typically, you would come across a {status, value} tuple, for example:

```elixir
{:ok, return_value} = do_stuff()
```

`do_stuff()` must return a tuple which matches that structure (otherwise Elixir will raise a ‘MatchError’), and return_value is now bound to the second item in the tuple returned from this function.

This is basically how pattern matching works, but the real beauty is how you use it in various contexts, for example:

When a function can return multiple values, such as the {status, value} tuple we came across earlier:

```elixir
case do_stuff() do
 {:ok, value} -> value
 {:error, _} -> raise "Oh no!"
end
```

In function heads you can pattern match on parameters, to only run when particular requirements are met:
```elixir
def my_func({:ok, value}), do: value
def my_func({:error, _}), do: raise "Oops!"
IO.puts my_func({:ok, "hello"}) # "hello"
```

You can even match on lists:

```elixir
[first, second, third] = [1, 2, 3]
```

And decompose data structures
```elixir
%{value: value} = map_func()
```

There are so many examples of pattern matching in Elixir because it’s incredibly useful and powerful, and also very performant when compared to traditional methods.

In a nutshell, that’s pattern matching!