---
title: Why syntactic sugar benefits developers and AI
slug: syntactic-sugar
publishDate: 5 May 2025
description: Syntactic sugar in code intents to make it more expressive and hide complexity. I've heard sentiment that it is bad - and I disagree.
tags: [ java, kotlin, AI, programming, opinion ]
---

> [Syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar) is syntax within a programming language that is
> designed to make things easier to read or to express. It makes the language "sweeter" for human use: things can be
> expressed more clearly, more concisely, or in an alternative style that some may prefer.

When I first started programming for my Technical Computing bachelor, the two programming languages I learned were
Java and C. It started with 100% "magic" and 0% understanding. Copying and pasting code from Stack Overflow helped me
through the first few assignments - no AI chatbots to help me back then.

Slowly, I started to understand the code I was writing, and the underlying principles of programming languages.
The magic-percentage started to drop, and I was able to explain what was happening behind the scenes.
However, reading code was still a challenge. Especially when I was confronted with C code that was written by
someone else. I had to read the code line by line, often multiple times, to grasp what was going on.

## Understanding code

Understanding code is a skill that takes time to develop. I would split it in two parts.

### 1. Know your facts

You need to know the syntax of a language, get familiar with its keywords, the libraries, data structures, best
practices,
design patterns. This is a mix of language-specific and general programming knowledge. Much of this knowledge is
transferable between languages, but some is not.

I see this as an enabler for understanding the flow of the code. There is no threshold for this, but
the more you know, the better equipped you will be to understand the flow.

### 2. Read the flow like a book

The flow of the code is its logic. Its intention. Its algorithms. Its inputs and outputs. Its context. Its side effects.
Its bugs.

This is a skill that will take time to develop, but is essential for _writing good code_. Good meaning: it does what it
is supposed to do, and is easy to read and understand.

![Syntactic Sugar blog header - sugar cube sprinkling computer with sugar](/assets/blog/2-syntactic-sugar/syntactic-sugar.webp)

## Syntactic sugar

Different programming languages have different use cases and objectives, logically leading to different syntax. You will
not want to write a REST API in C (trust me), and you will not write a UI in SQL.

But regardless which language you use, what is universal is the need to express logic in a way that your
successors will understand. This is where syntactic sugar comes in. By removing boilerplate (code that is executed but
not directly related to the business logic), it allows you to focus on the intention of the code. It doesn't only help
in
reading the code later, but also writing it.

> Bugs will become more obvious

To explain why syntactic sugar is important, I will use some real-world examples in Java and Kotlin.
Switching from Java to Kotlin was an eye-opener for me in this regard. Where in Java I would need to think much more on
_how_ I could write the intended code, in Kotlin I was much more thinking of _what_ I should write.
I felt like Kotlin allowed me to express the flow much more naturally, like a book.

For example, let's say I was asked to write a function that:

- for premium users
- adds a user's email domain based on their email address
- groups users by their domain
- filters groups with domains that are not in a given list

In Java, I would write something like this:

```java
public Map<String, List<User>> getPremiumUserDomains(List<User> users, List<String> allowedDomains) {
  return users.stream()
      .filter(User::isPremium)
      .map(user -> {
        String email = user.getEmail();
        String domain = (email != null && email.contains("@"))
            ? email.substring(email.indexOf('@') + 1)
            : "unknown";
        return new User(user.getName(), email, user.isPremium(), domain);
      })
      .collect(Collectors.groupingBy(User::getDomain))
      .entrySet()
      .stream()
      .filter(entry -> allowedDomains.contains(entry.getKey()))
      .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
}
```

This is a lot of code for a simple task. I need to think about the data structures, the flow, the null checks. There are
many characters that are not relevant for the business logic, like `new`, `;`, `stream()`, `return`. To make the
function immutable, I need to create a new `User` object, which forces me to think about the constructor and
all properties of the `User` class. I need to remember the api of the `Collectors` class, and how to use it.

In Kotlin, I would write something like this:

```kotlin
fun getPremiumUserDomains(users: List<User>, allowedDomains: List<String>) =
    users
        .filter { it.isPremium }
        .map { it.copy(domain = it.email?.substringAfter('@') ?: "unknown") }
        .groupBy { it.domain }
        .filterKeys { it in allowedDomains }
```

This is much more expressive. It tells me step by step what the logic is, given that I know the Kotlin syntax.
The standard library provides easy-to-read functions like `filter`, `map`, `copy`, `substringAfter` and `groupBy`.
Kotlin's copy() function creates a new object with modified fields, keeping the rest intact — useful for
immutability.
This syntactic sugar allows me to focus on the business logic, and not on the implementation details. I still need to
think
about the data structures, null checks (although Kotlin's compiler will help me with that) and the flow, but the
boilerplate is gone. I can focus on _what_ instead of _how_.

## With power comes responsibility

Although the above example shows the power of syntactic sugar, it makes it ever so important to use it well. Syntax
features can be abused - and I have seen that happen many times. To illustrate, here is a Kotlin example that is
overly complex, even though it uses powerful syntactic sugar:

```kotlin
fun Int.tmag(y: Int) = this * y
fun <T> List<T>.hmm(pred: T.() -> Boolean) = filter { it.pred() }
inline fun <A, B> ((A) -> B).thenDo(crossinline action: B.() -> Unit) =
    { a: A -> action(this(a)) }

fun twist(dsl: Twist.() -> Unit) = Twist().apply(dsl)
class Twist {
    fun doMagic(data: List<Int>) = data.hmm { this % 2 == 1 }
    fun combine(transform: (Int) -> Int, consumer: (Int) -> Unit) =
        transform.thenDo(consumer)
}

twist {
    combine({ it.tmag(3).dec() }, { println("result = $it") })
        .thenDo { print("!") }(
        doMagic(listOf(1, 2, 3, 4, 5)).sum()
    )
}
```

Uhhh... what? Indeed, this is incredibly hard to read. Especially given that it could be rewritten like this:

```kotlin
fun processNumbers(numbers: List<Int>) {
    val tripledOddsSum = numbers
        .filter(Int::isOdd)
        .sumOf { it * 3 }

    val result = tripledOddsSum - 1

    println("result = $result!")
}

fun Int.isOdd() = this % 2 != 0

// example usage
processNumbers(listOf(1, 2, 3, 4, 5))
```

## AI chatbots and syntactic sugar

AI researchers have found that LLMs form complex internal relationships between words and phrases
(see [On the biology of a Large Language Model][1] or [this explainer][2]).
Although the internals of these models are yet to be fully understood, I can't help but think that the more code looks
like natural language, the better the AI will be able to understand it.

With the rise of [vibe-coding](https://en.wikipedia.org/wiki/Vibe_coding) and new tools popping up like mushrooms, good
prompt writing (by the dev) and prompt adherence (by the AI) is becoming more and more important. I can see a future
where we will be more of a reviewer than a writer of code, and although I am not a fan of the idea, reading code
written by AI will be much easier if it is written in a more natural way.

# Conclusion

We've learned that syntactic sugar is more than a "cool feature" of a programming language. It allows us to write code
faster, read code easier, and understand the business logic of the code better. It’s not a silver bullet that will
prevent bad code, but when used wisely, it makes a big difference.

> Choose your tools with care, and use them even more carefully.


[1]: https://transformer-circuits.pub/2025/attribution-graphs/biology.html

[2]: https://youtu.be/-wzOetb-D3w?t=20
