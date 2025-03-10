---
title: 'The basics: Primer on C++ Type Casting'
author: Ao Zhang
type: post
date: 2024-07-09T00:13:55+00:00
url: /?p=1404
reader_suggested_tags:
  - '["Security","C#","Politics","Technology","News"]'
firehose_sent:
  - 1720485087
wordads_ufa:
  - s:wpcom-ufa-v4:1722781262
timeline_notification:
  - 1720485088
categories:
  - Occupation | 营营
tags:
  - security

---
In C++ we usually deal with 6 types of type casting:

<ul class="wp-block-list">
  <li>
    implicit type cast
  </li>
  <li>
    explicit type cast
  </li>
  <li>
    <code>static_cast</code>
  </li>
  <li>
    <code>dynamic_cast</code>
  </li>
  <li>
    <code>reinterpret_cast</code>
  </li>
  <li>
    <code>const_cast</code>
  </li>
</ul>

For the sake of my discussion, I’ll exclude `const_cast` upfront. `const_cast` sets/unsets the `const`-ness of a pointer, and it has limited security implication.

For the remaining 5 types of casts, it’s useful to put them in a table for easy reference:<figure class="wp-block-image alignwide size-large">

<img loading="lazy" decoding="async" width="1057" height="286" src="http://wp.docker.localhost:8000/wp-content/uploads/2024/07/screenshot-2024-07-08-at-17.24.02-1.png?w=1024" alt="" class="wp-image-1410" srcset="http://wp.docker.localhost:8000/wp-content/uploads/2024/07/screenshot-2024-07-08-at-17.24.02-1.png 1057w, http://wp.docker.localhost:8000/wp-content/uploads/2024/07/screenshot-2024-07-08-at-17.24.02-1-300x81.png 300w, http://wp.docker.localhost:8000/wp-content/uploads/2024/07/screenshot-2024-07-08-at-17.24.02-1-1024x277.png 1024w, http://wp.docker.localhost:8000/wp-content/uploads/2024/07/screenshot-2024-07-08-at-17.24.02-1-768x208.png 768w" sizes="auto, (max-width: 1057px) 100vw, 1057px" /> </figure> 

Now let’s dive into each:

## Implicit Type Cast {.wp-block-heading}

Implicit type cast supports fundamental type conversion. For example, you can implicitly convert between integer based types such as `char`, `unsignted int`, etc.. Caveats here are several, and come with security implications. This gets especially complicated when it comes to arithmetic conversions, signedness, floating points and long types. For simplicity, I’ll mark this out of scope and redirect readers to just avoid doing this.

## Explicit Type Cast {.wp-block-heading}

Explicit type cast is truly wild west. Just don’t use it.

## `reinterpret_cast` {.wp-block-heading}

`reinterpret_cast` is useful for pointer manipulation. It’s very powerful and prone to abuse as it supports:

<ul class="wp-block-list">
  <li>
    type casting between pointers to irrelevant classes
  </li>
  <li>
    type casting between integers and pointers
  </li>
</ul>

## `static_cast` {.wp-block-heading}

Enters `static_cast`. At compile time, it has type information from the compiler, so it’s able to tell irrelevant class type pointers apart. Apart from that though, developers are still left on their own to handle class inheritance correctly.

The biggest benefit? Type checking happens at compile type, so it doesn’t do any runtime type check and hence incur no runtime cost.

## `dynamic_cast` {.wp-block-heading}

This is the safest you get among all the choices. It relies on RTTI (RunTime Type Information) to work. It will also correctly tell you whether a polymorphic downcasting is successful. However, it also comes with downsides:

<ul class="wp-block-list">
  <li>
    it inserts additional CPU instructions, hence incurs runtime cost
  </li>
  <li>
    it doesn’t handle diamond inheritance situation
  </li>
  <li>
    it only supports <code>public</code> inheritance
  </li>
</ul>

## Type Confusion {.wp-block-heading}

As security professionals, we care about type casting because type confusion is a legitimate vulnerability that’s shown in cause real-world attacks. There are numerous ways this can be exploited, such as

<ul class="wp-block-list">
  <li>
    unsafe type casting: for example, <code>static_cast</code>-ing incorrectly between class pointers can lead to the program assuming that certain class fields’ existence, whereas in reality those memory addresses can be pointing to other data. Hence it can cause memory corruption.
  </li>
  <li>
    unions: <code>union</code> is a language struct that can make data of different types to coexist in the same memory location. Confusion between types will cause a program to interpret the union data incorrectly.
  </li>
  <li>
    v-table: C++ supports polymorphism through virtual function tables (v-table). As <code>dynamic_cast</code> isn’t effective for correct polymorphic type casting, it’s prone to exploitation.
  </li>
</ul>

## Reference codes for each casting {.wp-block-heading}

Below I included codes for illustrating each conversion scenarios. For simplicity I’m using only one type each for illustration. If you wanna play with other types of casting, just substitute the type cast with the desired casting type.

### Fundamental Type Conversion {.wp-block-heading}

Implicit Casting

<pre class="wp-block-syntaxhighlighter-code">#include &lt;iostream&gt;
using namespace std;

int main() {
  int c = 40;
  char d = c;
  cout &lt;&lt; d &lt;&lt; endl;
  return 0;
}</pre>

### Base → Derived {.wp-block-heading}

`reinterpret_cast`

<pre class="wp-block-syntaxhighlighter-code">#include &lt;iostream&gt;
using namespace std;

class Base {};
class Derived: public Base {
  public: int a = 999;
};

int main() {
  Base b;
  Derived *d = reinterpret_cast&lt;Derived*&gt;(&b);
  cout &lt;&lt; d-&gt;a &lt;&lt; endl;  // This compiles, but what does it output?
  return 0;
}
</pre>

### Non-polymorphic Derived → Base {.wp-block-heading}

`static_cast`

<pre class="wp-block-syntaxhighlighter-code">#include &lt;iostream&gt;
using namespace std;

class Base {};
class Derived: public Base {};

int main() {
  Derived d;
  Base *b = static_cast&lt;Base*&gt;(&d);
  return 0;
}</pre>

### Polymorphic Derived → Base {.wp-block-heading}

`dynamic_cast`

<pre class="wp-block-syntaxhighlighter-code">#include &lt;iostream&gt;
using namespace std;

class Base {
public:
  virtual void dummy(){};
};
class Derived : public Base {
public:
  int a = 999;
};

int main() {
  Base b;
  Derived *d = dynamic_cast&lt;Derived *&gt;(&b);
  if (d != 0) {
    cout &lt;&lt; d-&gt;a &lt;&lt; endl;
  } else {
    cout &lt;&lt; "incorrectly doing downcasting" &lt;&lt; endl;
  }
  return 0;
}</pre>

### Irrelevant Type Casting {.wp-block-heading}

Explicit casting

<pre class="wp-block-syntaxhighlighter-code">#include &lt;iostream&gt;
using namespace std;

class A {
public:
  int a, b;
  A(int v, int s) : a(v), b(s) {}
};

class B {
public:
  char c, d;
};

int main() {
  A ptra(1, 2);
  B *ptrb = (B*)&ptra;  // still compiles. This is wild west
  cout &lt;&lt; ptrb-&gt;c &lt;&lt; endl;
  return 0;
}</pre>