## What about stack overflow? ##

As soon as programmers learn about recursive functions, they are immediately taught about stack overflow and infinite recursion.  The concept of memory corruption almost immediately turns many programmers off to the idea of recursion altogether.

The risk of stack overflow is very small.  Each stack frame should take up few dozen bytes, while the entire stack is usually at least two memory pages large.  On x86 chips, that's at least 8 KB, and platforms such as Windows give you 1 MB or more.  Things have to get incredibly complex before stack overflow becomes a real problem.

As an example, we have a C++ source code highlighter that uses an earlier version of the YARD library.  We would feed that highlighter the entire [Boost](http://www.boost.org) codebase for correctness and stress testing.  Although C++ is notorious for having a complex grammar, and Boost is notorious for having complex code -- including deeply nested templates -- the highlighter never crashed due to stack overflow.

But if you are seriously concerned, write your rules like so:

```
typedef yard::Or<RuleA_T, RuleB_T> MyRule_impl;
struct MyRule_T : MyRule_impl
{
    template<typename ParserState_T> static bool Match(ParserState_T& p) {
         char c; char* cp = &c;
         if (::check_for_stack_overflow(c)) // "::" for free function
         {
               /* stack overflow if things continue, return false to
                  begin backtracking, or throw an exception and unwind
                  stack completely, or do something else
               */
         };

         return MyRule_impl::Match(p);
     }
     ... // anything else you want in the class
};
```

In this case, it will be up to you to determine the size of the stack and how much of that stack may be safely used.  You may consider using the functions in ucontext.h (if implemented on your platform) or the Windows `CreateFiber` function to create your own stack of a predetermined size.  Or you may know of some way of setting or finding the size of the stack on your platform.

If it's implemented correctly, `alloca` can handle this:

```
bool probe_stack(size_t needed_stack_frame_size) {
    return NULL != alloca(needed_stack_frame_size);
};
```

`alloca` is defined to return a `void*`, and if there is not enough space on the stack, it is supposed to return `NULL`.  Once `probe_stack` returns, the memory `alloca` allocated from the stack is returned to the stack, which we can then use because the stack is not shared with anything else.  Unfortunately, many implementations of `alloca` never return `NULL`, so this does not work on those platforms.

There are many interesting suggestions regarding fancier ways to handle stack overflow.  [One such proposal](http://lambda-the-ultimate.org/node/1599) is to create a stack of continuations.  However, implementing continuations in C++ can be tricky.  Simply allocating objects on the heap instead of on the stack isn't enough.  The memory where an object is allocated refers to where the data of that object is stored.  Stack overflow refers to running out of the memory that a function (or method) "runs in" -- a completely separate concept.  Creating a continuation in C++ requires either that the method or function in question not run in the main program stack (possibly by running in some other stack -- maybe through ucontext.h or `CreateFiber`) or that the program stack be saved and cleared whenever a function is called.

[People have tried to abuse setjmp/longjmp](http://www.google.com/search?q=c+continuations+setjmp) to save and restore the stack for this.  However, the restrictions on `setjmp`/`longjmp` exist so that `setjmp` doesn't actually have to save the stack.  Instead, it leaves the stack in place and stores a pointer to the current position on the stack.  `longjmp` can only be called while this position is valid; so it never has to restore the stack.

We believe that implementing continuations in C++ introduces much more complexity than the simple bounds checking demonstrated above.

  * [General Questions](YARDGeneralFAQ.md)
  * [Recursive Descent](RecursiveDescentFAQ.md)
  * [Administrative Questions](AdministrativaFAQ.md)


