## What is YARD? ##

YARD is a library that generates [recursive descent parsers](RecursiveDescentFAQ.md) through the C++ template mechanism.

## So why would I use YARD? ##

Recursive descent parsers are known for being easy to implement, and for running fast. Bjarne Stroustrup once wrote:

> In 1982 when I first planned Cfront, I wanted to use a recursive descent parser because I had experience writing and maintaining such a beast, because I liked such parsers' ability to produce good error messages, and because I liked the idea of having the full power of a general-purpose programming language available when decisions had to be made in the parser. However, being a conscientious young computer scientist I asked the experts. ... (They) convinced me that writing a parser by hand was most old-fashioned, would be an inefficient use of my time, would almost certainly result in a hard-to-understand and hard-to-maintain parser, and would be prone to unsystematic and therefore unreliable error recovery. The right way was to use an LALR(1) parser generator, so I used Al and Steve's YACC. ...

> My bias towards top-down parsing has shown itself many times over the years in the form of constructs that are hard to fit into a YACC grammar. To this day, Cfront has a YACC parser supplemented by much lexical trickery relying on recursive descent techniques (_Design and Evolution of C++_, Section 3.3.2).

YARD addresses all of the drawbacks Stroustrup mentions in this passage. YARD generates a recursive descent parser from a BNF-like grammar, just as Yacc generates a table-driven parser from a BNF-like grammar. Creating a grammar in YARD is as efficient for the programmer as is creating one in Yacc. Additionally, C++ exceptions make error recovery regular. Together, these facts make maintaining a YARD grammar much easier than maintaining a hand-written recursive descent parser.

  * [Recursive Descent](RecursiveDescentFAQ.md)
  * [Stack Overflow Concerns](StackOverflow.md)
  * [Administrative Questions](AdministrativaFAQ.md)