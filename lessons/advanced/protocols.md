---
layout: page
title: Protocols
category: advanced
order: 12
lang: en
---

In this lesson we are going to look at Protocols, and how to leverage them in Elixir.

{% include toc.html %}

## What Are Protocols
Protocols are the chosen method for polymorphism in Elixir.
One of the primary pains of Erlang is that extending a given API for a newly defined type is quite difficult.
What Elixir does to avoid this pain is dispatch function calls dynamically based on the type of a value provided.
This typically is not an issue in Object Oriented languages, but the workaround is necessary in our environment here.
If a data type implements a given protocol, it will have it available to dispatch to.
Let's demonstrate this with a simple example looking at `to_string/1`.

```elixir
iex> to_string(5)
"5"
iex> to_string(12.4)
"12.4"
iex> to_string("foo")
"foo"
```

As you can see, we have called this on multiple types.
We've demonstrated that the function works on integers, floats, and strings as well.
If we try to do it on a tuple (or anything else that has not implemented it), we get a different result:

```elixir
to_string({:foo})
** (Protocol.UndefinedError) protocol String.Chars not implemented for {:foo}
    (elixir) lib/string/chars.ex:3: String.Chars.impl_for!/1
    (elixir) lib/string/chars.ex:17: String.Chars.to_string/1
```

As you can see, we get a protocol error.
This is because there is no implementation for the Protocol in the tuples.
To implement a protocol, `defimpl/3` is our tool of choice.

## Working With Protocols
We can define a very basic protocol quite simply.
Here is a trivial example:

```elixir
iex> defprotocol HasATwoInIt do
...    def has_a_two_in_it?(data)
...  end
...
...  defimpl HasATwoInIt, for: List do
...    def has_a_two_in_it?(data) do
...      data
...        |> Enum.join
...        |> String.match? ~r/2/
...    end
...  end
iex> IO.puts(HasATwoInIt.has_a_two_in_it? [1, 2, 3])
true
iex> IO.puts(HasATwoInIt.has_a_two_in_it? [1, 3])
false
```

What we are doing here has two parts.
First we define a protocol, then we implement it for lists.
If we call this on another type, we will get an error:

```elixir
iex> IO.puts(HasATwoInIt.has_a_two_in_it? "123")
** (Protocol.UndefinedError) protocol HasATwoInIt not implemented for "123"
    iex:1: HasATwoInIt.impl_for!/1
    iex:2: HasATwoInIt.has_a_two_in_it?/1
```

# To be continued... (WIP)
