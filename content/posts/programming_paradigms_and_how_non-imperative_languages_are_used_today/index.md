+++
title = "Programming Paradigms and How Non-Imperative Languages are Used Today"
date = "2025-10-19"
+++

A paradigm is typically explained as a pattern or a set of rules, standards and ideas. A programming paradigm is a way for us programmers to conceptualize how we should write software; they are approaches we can use to solve problems. While most programmers concern themselves over the programming languages used to create software, each of these languages conforms to some sort of programming paradigm. If you are in the software development industry, you may be used to using only imperative languages for building software. While you may have learned about, or even studied, languages that inherit concepts from other paradigms, you might be confused as to how those languages are used today in the industry.

In this piece, I will cover the popular programming paradigms of today, the concepts they consist of and how their respective languages are utilised in the industry today. While most popular programming languages of today are able to implement multiple paradigms, such as Java, C++ and Python, and are able to implement even more through extensibility (e.g. Python modules such as sympy or pytholog for Logic programming), there are many more languages out there still in use that adhere to the rules and concepts of specific programming paradigms.

![Programming Paradigms from GeeksForGeeks](https://media.geeksforgeeks.org/wp-content/uploads/1-344.png)

# Background and Definitions

Programming paradigms came about as a means of classifying programming languages. In his paper, “Programming Paradigms for Dummies: What Every Programmer Should Know”, Peter Van Roy describes paradigms as a set of concepts. In turn, a programming language [realises one or more paradigms](https://webperso.info.ucl.ac.be/~pvr/VanRoyChapter.pdf) (1).

Generally, programming paradigms can be split into two distinct groups: Imperative Programming and Declarative Programming.

## Imperative Programming

Many of the earliest programming languages were developed with a very close relationship to the machine’s architecture they ran on. These early languages include Assembly, Fortran and C. While both Fortran and C were developed as abstractions for assembly programming, all three of them are classic examples of imperative languages. Imperative programming relies on statements that control the execution and state of the program. Because of their age and how they were designed intimately with their respective architecture, these older languages were built around directly changing a program’s state.

It allows for side effects, which allow for the state of the program to be changed. For imperative programming, the order of operation is critical when working with side effects. An example is having methods that change a variable’s value outside of their respective scope. The order of execution of these methods could produce different results for the variable.

## Declarative Programming

When discussing declarative programming, it is often in the context of how it differs from imperative programming. The classic example often used is that imperative programming is how a program should solve a problem while declarative programming is what a program should do to solve a problem. With declarative programming, we can create software without specifying the order of execution. Declarative programming does not allow for side effects, which we will explore more once we discuss the functional programming paradigm in the concept of “purity”. Examples of declarative languages include HTML, Haskell and SQL. In the case of HTML, it acts as a declarative language as it does not describe the execution order of how the webpage should be rendered. It merely describes what should appear on the page.

For the purposes of this article, we will be primarily discussing declarative programming languages and where they are used today in the software development industry and what benefits they bring in their differences to imperative languages.

# Core Concepts and Theory

For the scope of this article, we will be exploring two declarative programming paradigms: Functional programming and logic programming. Then, in further sections, we will be exploring functional programming and its use cases in the modern software development industry. This paradigm has some interesting uses in particular and has become quite popular for some specific problem domains in recent years, so we will be exploring it in particular and its respective languages.

## Functional Programming

Functional programming gives away its premise in the name - it relies on functions. Rather than a sequence of assignment statements that change the state of a program, functional programming composes abstract syntax trees (ASTs) with expressions that use a mapping function to assign values. Functional programming relies on the mathematical concept of Lambda Calculus, a formal system for function abstraction and function application. Functions are treated as first-class citizens, which means they can be assigned names, passed as arguments into another function and can be received as outputs from other functions. This essentially means functions act similarly to any other data type in functional programming languages.

Examples of functional programming languages include the previously mentioned Haskell, Lisp and Erlang. Lisp is a classic example of a functional programming language. With its first release in 1960, it is another particularly old high-level language. According to John McCarthy, designer of the language, the way that Fortran programs were written “algebraically” was attractive and “[It was immediately apparent that arbitrary subexpressions of symbolic expressions could be obtained by composing the functions that extract immediate subexpressions, and this seemed reason enough to go to an algebraic language](http://jmc.stanford.edu/articles/lisp/lisp.pdf)” (2). John’s desire to make a list processing language was for the purpose of AI research in 1956 at the Dartmouth Summer Research Project on Artificial Intelligence, the first organised study of AI.

## Logic Programming

In logic programming, software is written within the context of a set of facts about some problem domain. A program will be a set of statements representing knowledge about the problem. “Facts” act as statements of truth while “Rules” act as relationships between facts and define conditions. “Queries” are questions posed towards the system in an attempt to solve a problem. Examples of logic programming languages are Prolog and Datalog. Prolog is arguably the most popular and well known logic programming language. While logic programming may seem simple in scope and suited for a more specific problem domain, Prolog is a Turing-complete general purpose language. Prolog’s intended field of use was natural language processing but it has also effectively been used in other fields. In “[Prolog and Natural-Language Analysis](http://www.mtome.com/Publications/PNLA/prolog-digital.pdf)”, it is stated that Prolog has been popular for the development of artificial intelligence (3).

# Practical Examples and Code Walkthrough

To explain the characteristics of functional programming, let’s take a look at a comparison between some Java code (OOP paradigm) and some Haskell code (Functional paradigm).

```
public class Main {
    static String welcome = "Welcome to my Java app";
    static int[] numbers;

    public static void doubleNum(int[] arr) {
        numbers = arr;
        for (int i = 0; i < arr.length; i++) {
            numbers[i] = numbers[i] * 2;
        }
    }

    public static void main(String[] args) {
        System.out.println(welcome);
        int[] input = {2, 4, 6};
        doubleNum(input);
        for (int i = 0; i < numbers.length; i++) {
            System.out.println(numbers[i]);
        }
    }
}
```

Here in our Java app, we have a simple implementation of doubling the values of the contents of an array of integers. Immediately, we can see aspects of imperative programming. Our method, doubleNum() causes a side effect. We give an argument and assign it to the numbers integer array; a non-local variable. This method operates on both its arguments and a variable outside its scope. Both the variables “welcome” and “numbers” are mutable, which means they can be changed as part of the program’s state via assignment statements.

```
welcome :: String
welcome = "Welcome to my Haskell app" :: String

doubleNum :: Int -> Int
doubleNum x = x * 2

doubledList :: [Int] -> [Int]
doubledList = map doubleNum

main :: IO ()
main = do
    print welcome
    print (doubledList [2,4,6])
```

This is our Haskell app, which functionally does the same thing. It takes a list of integers and doubles the value of its contents. However, you can immediately see some differences in its implementation. First of all, there are no mutable variables. We cannot use any mutable variables in functional programming. The “welcome” string cannot be changed during runtime. This means that we cannot use variables at all in functional programming, we can only work with constants. In our Java app, we modify the “numbers” integer array. This implementation would not be possible in a language like Haskell.

We also have “pure” functions. Pure functions cause no side effects. Generally, when people promote functional programming, they promote pure functional programming as one of its benefits. How our app here works is we have two functions defined. The first, doubleNum, takes an argument and returns it multiplied by 2. If a function only operates on its arguments, it is a pure function. This is why our method, doubleNum() in our Java app is not pure as it assigns a value to a non-local variable. Our next function, doubledList, takes a list as an argument and uses the mapping function to assign the doubleNum function to the values of the input list. This is an example of first-class citizen functions as we use a function as an argument similar to other data types.

This is the output of our Haskell app:

```
"Welcome to my Haskell app"
[4,8,12]
```

## Industry Use of Haskell at Meta

One of the most notable uses of functional programming in the software development industry is the use of Haskell for Meta’s internal spam filtering service, Sigma. Meta previously used an in-house developed language called FXL. However, problems arose at scale as FXL could not handle the growing complexity of Facebook policies. Sigma operated similarly to logic programming, operating on “policies”. Every interaction a user can perform on Facebook (e.g. liking a post) results in Sigma evaluating policies specific to that action. As the site grew and new policies arose, the team at Meta wanted to find an alternative language to their own FXL. To demonstrate the complexity of the growing number of policies, according to a research paper on their Haskell library, Haxl, there are “[about 600 different kinds of request, all implemented by a body of Haskell code of approximately 200,000 lines](https://research.facebook.com/file/116181260674064/there-is-no-fork-an-abstraction-for-efficient-concurrent-and-concise-data-access.pdf)” (4).

They decided on Haskell for its pure functions and strong typing. According to Simon Marlow, “[This ensures that policies can’t inadvertently interact with each other, they can’t crash Sigma, and they are easy to test in isolation. Strong types help eliminate many bugs before putting policies into production](https://engineering.fb.com/2015/06/26/security/fighting-spam-with-haskell/)" (5). There was also the issue of performance; FXL was slow in comparison and any performance-critical code had to be written in C++.

Meta would go on to create the Haxl framework to specifically address issues they had with concurrency regarding Haskell. This greatly helped improve Sigma’s performance. Ultimately, the team created a system that for some request types could handle them up to 3 times as fast.

![A Histogram showing the comparison in performance between FXL and Haxl](https://engineering.fb.com/wp-content/uploads/2015/06/GKsHrgBMRytm82gBAHg6LDAAAAAAbj0JAAAD.png)

# Considerations

From our exploration of functional programming in the industry, the benefits seem attractive looking at the results of Meta’s Sigma rework. However, it is important to keep in mind the specific problem domain. Meta required specific aspects of functional programming for their solution. This was also implemented as early as 2014; many developments across the software development industry have taken place, not to mention the use of AI as a spam-detection filter for social media. There have also been many more attack vectors for bad actors and those that create spam and malware for social media. It is still important to take into consideration the domains that these languages were developed for and understand the concepts their paradigms employ. As previously mentioned, Prolog was created as a logic programming language for natural language processing but has found use in areas like artificial intelligence.

# Conclusion and Takeaways

In conclusion, it is apparent that non-imperative languages are able to find use in modern software development. While most software across the world is and will continue to be written using imperative languages, whether it be procedural languages like C being used to keep legacy software updated or to write operating systems, or object-oriented languages like C++ or C# being used to write Windows desktop apps and games, declarative languages still remain important for problem domains most developers might not realise. While popular declarative languages like HTML and SQL are known by most developers to have a specific problem domain attributed to them (web development and databases respectively), there are still declarative languages discussed in this article that are general-purpose and are capable of being used to write many different kinds of solutions for different problems.

# References and Further Reading

1. Van Roy, Peter (2009). Programming Paradigms for Dummies: What Every Programmer Should Know, pp. 12, https://webperso.info.ucl.ac.be/~pvr/VanRoyChapter.pdf
2. McCarthy, John (1979). History of Lisp, Artificial Intelligence Laboratory Computer Science Department Stanford University Stanford, California 94305, pp. 3, http://jmc.stanford.edu/articles/lisp/lisp.pdf
3. C. N. Pereira, Fernando, M. Shieber, Stuart (2005). Prolog and Natural-Language Analysis, Brookline, Massachusetts: Microtome Publishing, pp. 6, http://www.mtome.com/Publications/PNLA/prolog-digital.pdf
4. Marlow, Simon (2014). There is no Fork: an Abstraction for Efficient, Concurrent, and Concise Data Access, pp. 10, https://research.facebook.com/file/116181260674064/there-is-no-fork-an-abstraction-for-efficient-concurrent-and-concise-data-access.pdf 
5. Marlow, Simon. Fighting spam with Haskell, Last edited: June 26th, 2015. https://engineering.fb.com/2015/06/26/security/fighting-spam-with-haskell/