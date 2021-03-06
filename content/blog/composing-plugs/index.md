---
title: Composing Elixir Plugs in a Phoenix application
date: "2018-03-23T09:00:00.000Z"
---

Elixir is a functional language, so it’s no surprise that one of the main building blocks of the request-response cycle is the humble Plug. A Plug will take connection struct (see [Plug.Conn](https://hexdocs.pm/plug/Plug.Conn.html)) and return a new struct of the same type. It is this concept that allows you to join multiple plugs together, each with their own transformation on a Conn struct.
A plug can be either a function or a module that implements the Plug behaviour.

## What is a Plug?
Before we get to know Plug, add it as a dependency to your project’s mix.exs file: `{:plug, "~> 1.5"}` and run `mix deps.get` to install it.
A Plug has the following structure:

```elixir
defmodule MyApp.Plug.AuthenticateUserSession do
  import Plug.Conn

  def init(options), do: options

  def call(conn, _opts) do
    put_session(conn, :user, %{name: "Jack"})
  end
end
```

In this example, we’re adding a :user map to the current session data. The two main functions are `init/1` and `call/2` both of which are part of the Plug behaviour, defined in `plug.ex`.
`init/1` is used to initialise the plug with options that can be used in the `call/2` function. `call/2` is the meat of the plug, where you take a `%Plug.Conn{}` struct and return a new one.

## How to use a Plug
You can use a Plug in a your application’s router. It might look something like this:

```elixir
defmodule MyApp.Router do
  use Plug.Router

  alias MyApp.Plug.AuthenticateUserSession

  plug AuthenticateUserSession

  get("/", do: send_resp(conn, 200, "Welcome\n"))
end
```

Now, when you visit `/` in your application, it will run `AuthenticateUserSession.call/2` and as we defined earlier, it will add a user map into the session data.

## How to combine multiple Plugs
When building an application, you might need to do more than just adding a user to a session. Rather than extending upon the AuthenticateUserSession plug, you can create a new plug that will do the specific job you need it to. This allows us to compose plugs that are discrete and makes sure we’re following the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle).

With Elixir plugs, we can combine multiple plugs in a `:pipeline` that can group functionality together.

```elixir
defmodule MyApp.Router do
  use WebRoot.Web, :router
  pipeline :auth do
    plug AuthenticateUserSession
    plug AuthoriseUserSession
  end
  scope "/", do
    pipe_through :app
    get "/page", MyApp.PageController, :index
  end
end
```

Order is important when using a pipeline, as plugs will be called in the order that they are defined. Now, in MyApp when `/page` is requested, `AuthenticateUserSession.call/2` will be called, which can either return a `%Plug.Conn{}` and let the request continue to the next plug, or it can halt the request and return a different response. When the former happens, the `AuthoriseUserSession.call/2` function will run. Finally, if this plug returns successfully, `MyApp.PageController` will be called and the user can receive a response.

The Plug concept has many applications beyond just the request-reponse cycle. It stands as a metaphor to explain how Elixir applications can be built up with many parts, all performing their job and handing off to the next module.
You can read more about plugs on the [docs of the Plug package](https://hexdocs.pm/plug).