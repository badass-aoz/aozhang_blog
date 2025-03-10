---
title: 'ProdSec Interview Prep Guideline: Coding'
author: Ao Zhang
type: post
date: 2024-09-04T22:05:27+00:00
url: /?p=1609
featured_image: /wp-content/uploads/2024/09/image-2.png
reader_suggested_tags:
  - '["Programming","Python","Software Development","Technology","Coding"]'
firehose_sent:
  - 1725487529
wordads_ufa:
  - s:wpcom-ufa-v4:1725487712
categories:
  - Occupation | 营营

---
Security engineering is a hybrid discipline that blends bug finding with coding. While security engineers are typically expected to have coding skills, the bar is often lower than that set for software engineers. That said, I’ve seen many security engineers who write exceptional code, and my own coding ability proved invaluable during my time in SecEng. However, you don’t need to excel at every tough Leetcode problem to succeed in a security engineer coding interview. What’s more important is demonstrating that you can write effective scripts for practical, everyday scenarios, such as:

1. Crafting complex and refined rules with tools like Semgrep or Clang-tidy in a codebase.

2. Setting up a CI/CD pipeline that runs automated detection engines for newly checked-in code.

3. Performing a quick large-scale refactor to replace dangerous APIs with safer alternatives.

4. Writing a script to collect signals from phishy network traffic captured by internal systems.

These are just a few examples—the list goes on.

There’s ongoing debate about whether security engineers should write code themselves or collaborate with software engineers. I’ve seen both approaches, but I’m a firm believer in security engineers being able to write production-level code. The best security engineers I’ve worked with excel not just in security but also in coding. In fact, one of the top security engineers I know ranks among Meta’s highest in terms of code output. Personally, having strong coding skills has always made me feel more empowered.

For interview preparation, I recommend focusing on medium-difficulty Leetcode questions. During the interview, you’ll typically be evaluated on the following aspects:

1. Communication

2. Coding speed and correctness

3. Complexity and optimization

4. Testing



**Communication:**

One common pitfall in coding interviews is overemphasizing the coding itself without adequately explaining the code. That’s why “Communication” is listed as the top priority here. At work, engineers need to effectively communicate what they’ve built. To demonstrate this during an interview, follow these steps:

1. Before coding, briefly explain your algorithm and get the interviewer’s approval.

2. While coding, if possible, pause periodically to explain what you’ve written.

<ul class="wp-block-list">
  <li>
    If this disrupts your flow, let the interviewer know you’ll finish the code first and explain it afterward.
  </li>
</ul>

3. After coding, provide a summary where needed, ask if further clarification is necessary, and offer to run your code with test cases.

4. During testing, walk through the first execution of each line, plugging in variables and explaining how the code behaves.

If you ever feel like you’re over-communicating, don’t worry—it’s unlikely. You can always ask the interviewer if you’re providing the right level of detail.

**Coding Speed and Correctness:**

A security engineer is expected to code at a reasonable pace. For perspective, I’d expect a qualified candidate to **solve a medium-difficulty problem within 45 minutes, providing an optimal solution, thorough tests, and explanations**. A strong candidate can often solve a harder question in that time.

**Complexity and Optimization:**

A common approach in coding interviews is to **start with a brute-force solution, then optimize for efficiency**. This method reduces the risk of getting stuck. I’ve seen many candidates aim for the optimal solution right from the start, only to confuse themselves and waste valuable time. While I’ll usually suggest starting with a brute-force solution to score partial points, it’s best to adopt this mindset by default.

Of course, if you can easily see the optimal solution, that’s great! But if not, don’t gamble—start with the basics. Always assess your solution in terms of [Big-O][1] complexity to demonstrate your understanding.

**Testing:**

Different interviewers have varying expectations regarding testing, so clarify with them when it’s appropriate to run tests on your code. For instance, some interviewers may expect candidates to identify obvious issues without running the code. Once given the green light, list out your test cases and walk through the code for each. Typical test cases include:

• Empty case (empty string/array/tree/graph, etc.)

• Singleton case (one-element array/tree/graph, single-character string, etc.)

• Two-item case (this often relates to recursion base conditions)

• General case (use a convenient test case that saves time—e.g., a 4-character string instead of a 10-character one)

• Null/nil/nullptr case (confirm with the interviewer whether this input type is expected, but it’s often good to check)

• Other edge cases (e.g., std::numeric_limits<int>::min)

If you’re coding in C/C++ (though I’d ask, why?), be sure to watch for common pitfalls such as buffer overflows and integer overflows. After all, you’re supposed to catch security vulnerabilities, not introduce them—especially in an interview!



**Other Tips:**

• **Get familiar with the coding environment:** It’s increasingly common for candidates to write code in an online, virtual environment rather than using a whiteboard, especially since COVID. This shift generally benefits candidates, as online IDEs offer syntax highlighting and make editing code easier. However, it’s important to familiarize yourself with the environment beforehand:

<ul class="wp-block-list">
  <li>
    If you use Emacs or Vim keybindings, adjust the settings to match your preference ahead of time.
  </li>
  <li>
    Practice organizing your screen layout so you can see both the interviewer and your code simultaneously.
  </li>
  <li>
    Know whether you’ll be allowed to run your code during the interview. Some coding interviews don’t allow it, so be prepared for either situation.
  </li>
</ul>

• **Front-load edge testing cases:** Many coding problems require identifying edge cases and clarifying how they should be handled with the interviewer. Think about these cases early on and address them at the start of your solution process.

• **Use a language you’re comfortable with and that has strong library support:** Choose a language that you are proficient in, but also one that allows you to leverage built-in libraries to simplify your code. For instance, you don’t want to write 20 lines of code to implement a binary tree if a library or another language can do it in 5 lines. Python is a popular choice, but I’ve seen successful candidates code in Erlang as well.



<hr class="wp-block-separator has-alpha-channel-opacity" />

It’s best to put all those words in practice by looking at a real coding question. [I randomly picked a medium one from Leetcode.][2]

_Interviewer:_ <Describing the question prompt>

_Candidate_: The question seems straightforward enough. Can the `numRows` go below 1 or be negative? What if `numRows` is larger than `s.length` ?

_Interviewer:_ No, the smallest `numRows`can be is 1. If `numRows` is larger than `s.length`, what do you think should be a reasonable output?

_Candidate_: The same as `input`

_Interviewer:_ Correct. Let’s proceed with that.

_Candidate_: okay. The most straightforward way to think about it is to build the output matrix. With the row size being `s.length/numRows` and column size being `numRows`, and then we just use the zigzag pattern to fill in the matrix, and eventually read the output string out of the matrix. This is bad because it use excessive space and also runs slow. A better way of doing this is to figure out the math between `numRows` and each letter’s position in the output. However, that can be complicated for me to figure out up front. Mind if I start coding up the brute-force solution first?

_Interviewer_: go for it!

<[coded up v1][3]>

_Candidate:_ <explain through codes> And the time complexity is `O(NM)`, with `N` being the length of the input array, and `M` being `numRows`. The space complexity is the same. The time complexity is bad because we needed to iterate through all elements in the output matrix. We should be able to find a better solution which is more efficient.

_Interviewer:_ Sounds right. Do it.

_Candidate:_ I noticed that we were wasting a lot of time iterating through the output matrix. What if we can directly put the output character into the return string instead of having an intermediate matrix? … <scratching on paper> This may be still a bit more complicated. However, I have a better solution, which is to group output characters into individual rows while iterating through the input string. This way, we’ll just need to iterate those rows in order and join all the characters in those rows to get the output string.

_Interviewer_: Can you elaborate?

_Candidate:_ In the intermediate representation, group each row together. Think of those groups as individual string builder or array. Then you’ll just need to iterate through each character in the input string, and put each in the corresponding string builder/array. At the end, join those characters from those arrays together and you’ll have the output.

_Interviewer_: Sounds right. Do it.

<[coded up v2][3]>

_Candidate:_ <explain what each line does briefly> The time complexity here is better because we only need to iterate through the input once, and that’s gonna be O(N). Space is also O(N). Do you want me to test this?

_Interviewer:_ Sure

_Candidate:_ To test this, you’ll have test cases such as empty case, `numRows` being 1, very long input string, `numRows` being greater than `len(s)`. My codes should be able to handle all that. Do you want me to run through it?

_Interviewer_: No, it’s good. That should be the interviewer. Let’s wrap up here….

 [1]: https://en.wikipedia.org/wiki/Big_O_notation
 [2]: https://leetcode.com/problems/zigzag-conversion/description/
 [3]: https://github.com/badass-aoz/security-interview/blob/main/prodsec/coding.py