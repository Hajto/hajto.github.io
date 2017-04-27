---
layout: post
title: "What if we had only functions? How would your life look when there was no if statement.."
status: draft
type: post
comments: true
list: etut
---

Few days ago I've got a challenge from one of my teachers to write a simple C code without using loops, if and goto. It's a very nice opportunity to use recursion and some logic magic.

<!--more-->

First of all, the language I had to use is C. It was a pretty easy piece of code that had an instruction before and after checking loop condition. It looks more or less like this:

{% highlight elixir %}
void GoToLoop() {

pocz:
    X();
    if (n > 0) {
        Y();
        goto pocz;
     }
}
{% endhighlight %}

Let's just assume that X and Y are functions that does something that we don't really care at the moment. We can't use `If` statement, but that's really easy to workaround...

# Lazy AND

Have ever wondered what is the difference between `&` and `&&`? First one is called `binary and` and it performs bitwise operation. It will take two things, treat them as sequence of 1s and 0s and perform `AND` on each bit pair. Of course we can say that when it comes to boolean values we can treat it as `logical and`, because trues is represented as `11111111` and zero is `00000000`, but it has one really important property. It is eagerly evaluated so it checks both of its operands first. On the contrary, operator `&&` treats it's operands as boolean values and return a single boolean value. So are probably you are asking what's the real difference and why have I spent so much time explaining it to you? Because `&&` is lazy evaluated... It means that when it receives false as left parameter it won't bother to check the right one...

That's actually key to solving the puzzle. We can implement any `if statement` in such a way:

{% highlight c %}
!(condition && (iftrue() || true)) && ifelse();
{% endhighlight %}

First of all it checks whether condition is true. If that's the case it will try to check `iftrue` function result. `||` is put there to make absolutely sure that `iftrue` return will always be considered true. If condition fails (evaluated to false) it's gets negated so `ifelse` is going to be checked.

Unfortunately C doesn't have anonymous functions and both `iftrue` and `ifelse` must return anything that has to be evaluated to true...

# First solution

Given these restrictions I came up with this code:

{% highlight c %}
void wrapper(){
    rec_petla();
    printf ("\n\tPetla Go To\t%10d %10d%10.4lf%10.4lf\n",i,n,x,y);
}

int rec_petla (){
    (X(), n > 0) && Y()  && rec_petla();
    return 1;
}
{% endhighlight %}
This particular code has to be working on upper scope variables (global in this context) which is less then ideal. The most annoying part though is that you actually have to fake return and make sure to return a value considered true.

# Shall we make our own if?

C ain't language that like having fun "functional way", thus I had to make a few little hacks to make this work.

{% highlight c %}
int return_one(void (*f)(void)){
    f();
    return 1;
}

void my_if(bool condition, void (*iftrue)(), void (*ifnot)()) {
    !(condition && (return_one(iftrue) || true)) && return_one(ifnot);
}
{% endhighlight %}
<a href="http://rextester.com/VSY63537"> Live example </a>

As you can see `myif` receives a boolean and two functions pointers. To not carry about actual returns of callbacks I created helper function, that takes callback as parameter, calls it and returns 1. Of course that's all kinds of bad practice, but we won't free ourselves from side effects until very end of this post.

# Let's sweeten up a bit

The thing that really was annoying me in previous implementations were those named callbacks and exposed helper function. I thought that throwing few lambdas here and there should make it a bit better. First that came upon my mind was scala. She has two important features that should fixed said problems. Anonymous functions and code block evaluation. (evaluates block and returns value from last instruction).

{% highlight scala %}
def compute_no_if(xx: Double, y: Double, n: Int, ii: Int) : Any = {
  X()
  !((n <= 0) && {
    Y()
    true
  }) && {
    compute(new_x, new_y, new_n, new_i)
    true
  }
}
{% endhighlight %}

As you can see I wrapped callback calls with `{}` and followed it with `true`. That's because it will be interpreted as mini function call. It will call actual callback first then return true not carrying about what callback did.

{% highlight scala %}
def my_if(condition : Boolean)( iftrue: () => Any)( ifnot: () => Any) = {
  !(condition && {
    iftrue
    true
  }) && {
    ifnot()
    true
  }
}
{% endhighlight  %}

{% highlight scala%}
def compute_c_if(xx: Double, y: Double, n: Int, ii: Int) : Any = {
  X()

  my_if(n <= 0) { () =>
    println(s"Wynik: $i $n $x $y")
  } { () =>
    Y()
  }
}
{% endhighlight %}

At least now we have actual anonymous functions, there is still one big problem with this code... It does rely on mutating state! Which we don't really want. Therefore..

# Let's do it compile time then

Due to my lacking knowledge of Scala macros I decided to switch to Elixir. It has nice macros we can use. When it comes to writing code that will write code it is good to start with sample result. For sake of simplicity let's abandon our lazy `&&` approach, because it denies possibility of returning value (whole expression has to return `true` or `false`). I would want macro to return something like this:

{% highlight elixir %}
def compute(xx, y, n, i) do
  X()
  myif n > 0 do
    compute(transformed values)
  else
    IO.inspect "Wynik: #{i} #{n} #{x} #{y}"
    result
  end
end
{% endhighlight %}

Looking at the example we know that the macro will need to have at least three arguments including: `condition`, `iftrue`, `iffalse`. Where `iftrue` and `iffalse` are going to be code blocks that are going to be executed when condition is met or not.

{% highlight elixir %}
defmacro myif(condition, do: iftrue, else: ifnot) do
  quote do
    case unquote(condition) do
       x when x in [nil, false] -> unquote(ifnot)
       _ -> unquote(iftrue)
    end
  end
end
{% endhighlight %}

Having that implemented we can use it in our function so it will now return value. Feel free to check <a href="http://elixirplayground.com?gist=00732d5204124bac8758f930f2668cdb"> live example </a>.

# Summing it up
After all of that. Is that code worth anything? Hell no! It's like introducing another layer of unnecessary complexity and denying wonders of tail call optimization (depending on language and compiler). We purposely tried to avoid using `if statement` and we ended up implementing our own... And actually our implementation of If macro is very similar to one that is planted into Elixir kernel.

And please NEVER use presented approach in production code.
