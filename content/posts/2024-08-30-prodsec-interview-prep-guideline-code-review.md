---
title: 'ProdSec Interview Prep Guideline: Code Review'
author: Ao Zhang
type: post
date: 2024-08-30T15:35:00+00:00
url: /?p=1600
reader_suggested_tags:
  - '["Security","Cybersecurity","Technology","Programming","Linux"]'
firehose_sent:
  - 1725034121
wordads_ufa:
  - s:wpcom-ufa-v4:1725034367
categories:
  - Occupation | 营营

---
This type of interview is intended to test your code review skill. You will be presented with a piece of codes, review them, identify security vulnerabilities, and suggest fixes. Evaluation usually happen on 4 aspects:

<ol class="wp-block-list">
  <li>
    Code Navigation: <ol class="wp-block-list">
      <li>
        speed: can you read codes in reasonable speed
      </li>
      <li>
        understanding: can you explain what those codes do
      </li>
      <li>
        methodology/habits: do you have a finely tuned method for code review
      </li>
    </ol>
  </li>
  
  <li>
    Bug Identification: <ol class="wp-block-list">
      <li>
        comprehensiveness: can you identify most, if not all, security vulnerabilities
      </li>
      <li>
        exploitation: can you explain how those bugs can be exploited
      </li>
    </ol>
  </li>
  
  <li>
    Fix suggestion: <ol class="wp-block-list">
      <li>
        resourcefulness: can you suggest bug fixes, sometimes in more than one way
      </li>
      <li>
        automation: can you suggest how those bugs can be auto detected and/or prevented
      </li>
    </ol>
  </li>
  
  <li>
    Communication <ol class="wp-block-list">
      <li>
        explanation: can you explain all those to a developer who has little security experience
      </li>
    </ol>
  </li>
</ol>

Those 4 aspects tend to have the same weight, although the hiring manager may also have their own heuristics for different levels. Here’s an example heuristic I&#8217;ve used before:

<ul class="wp-block-list">
  <li>
    Junior: identify 60% bugs, explain those bugs clearly to developers, suggest direct fixes
  </li>
  <li>
    Senior: identify 80% bugs, explain those bugs and preferably know how they can be exploited, suggest automated detection
  </li>
  <li>
    Staff: identify all bugs, use past experience to explain why they could be bad, suggested framework-level changes to prevent bugs from happening
  </li>
  <li>
    Senior Staff and above: all of the above, suggest multiple fixes/prevention/detection methods and compare pros and cons, demonstrate empathy towards developers
  </li>
</ul>

There is a limit to how this interview can go, because I wouldn’t expect a qualified senior staff prodsec engineer (Meta IC6) to differ much from a qualified principal or above(Meta IC7+) engineer in their code review interview performance. This differentiation among very senior levels comes mainly in other interviews.

To better illustrate my point, I’ll use a code snippet to demonstrate how a candidate can be evaluated.



<hr class="wp-block-separator has-alpha-channel-opacity" />

_Interviewer:_ In this interview, you are presented a code snippet. The code snippet may or may not contain security vulnerabilities. I’ll give you some time to review it first. You will then tell me what those codes do, how they are bad, and how they can be fixed. Cool?

_Candidate:_ let’s do it!

_Interviewer_: display the following piece of code.

(Note: I prefer to show this piece of codes in highlighted PDF or on printed paper, instead of allowing candidate to view it in IDE. This gives me more insights into candidates’ **raw** review capability.)

Codes are here: https://github.com/badass-aoz/security-interview/blob/main/prodsec/native\_code\_review.cpp



<span style="text-decoration: underline">Code Navigation</span>:

The code snippet demonstrate a (shitty) CLI software that does reservation. It first authenticates users via username-password credentials, and then offered three options to create new reservation, change reservation, or view reservation.

The code snippet is relatively simple. A candidate may work their way top-down (from `main` function to aux functions, skipping functions in callers and looping back to them in callees) or bottom-up (DFS style, from `main` to aux functions then back to `main`). Either way is fine as long as candidate is methodical and demonstrates proficiency.

<span style="text-decoration: underline">Bug Identification & Fix Suggestion</span>:

<ol class="wp-block-list">
  <li>
    Password stored in plaintext in DB: This is insecure because a compromised database (for example, insider threat) will mean compromised user login credentials. <ol class="wp-block-list">
      <li>
        best practice: salt and hash the password; store password in a separate database from the product database, and store the salt along with the hashed password
      </li>
      <li>
        interviewer can choose to dig deeper: <ol class="wp-block-list">
          <li>
            hashing related: <ol class="wp-block-list">
              <li>
                what hashing algorithm would you recommend? bcrypt, scrypt, PBKDF2, etc.
              </li>
              <li>
                what makes a good password hashing algorithm and why? slow (<em>work factor</em>), cryptographically secure, low collision, etc.
              </li>
              <li>
                why is SHA256 bad? not secure, fast, collision, etc.
              </li>
              <li>
                why salt? rainbow table attack
              </li>
            </ol>
          </li>
          
          <li>
            prevention: <ol class="wp-block-list">
              <li>
                linter on password usage: for example, annotate all the APIs that handle passwords and create an alert that uses password, and have a devsecop pipeline to review usages
              </li>
              <li>
                introduce password policies (e.g. minimum password length + complexity), or even better, don’t use password (e.g. SSO).
              </li>
            </ol>
          </li>
        </ol>
      </li>
    </ol>
  </li>
  
  <li>
    Hardcoded secrets: <code>R00tP@ssw0rd</code> isn’t complex enough. Also don’t store this in plaintext in the code. Secrets in codes will leak. <ol class="wp-block-list">
      <li>
        fix: remove it and load it from a secure place; or better, use an IAM solution with properly scoped role (e.g. a service account with privilege of only changing records to the relevant tables)
      </li>
    </ol>
  </li>
  
  <li>
    <code>strcmp</code> for password comparison: The function does character by character comparison, and returns early when it detects difference. This makes timing attack possible. <ol class="wp-block-list">
      <li>
        fix: use constant time operation. You can either implement your own (by always iterating through all characters) or use an existing one (e.g. libsodium <code>sodium_memcmp</code> , OpenSSL <code>CRYPTO_memcmp</code> etc.)
      </li>
    </ol>
  </li>
  
  <li>
    SQL Injection: the code directly inserts user input into SQL statements without proper sanitization or using appropriate APIs. <ol class="wp-block-list">
      <li>
        exploit: <em>Interviewer:</em> give me an example payload to exploit the SQL injection issue <ol class="wp-block-list">
          <li>
            example: take this particular SQL as an example:
          </li>
          <li>
            <code>SELECT password FROM users WHERE username = \\"%s\\";</code>. We can make the username as <code>1" OR true; DROP TABLE users; --</code> This is not particularly useful or sneaky (just dropping table, no information leakage) but gets the work done.
          </li>
          <li>
            fix: user input sanitization can be useful if you know what to expect for a particular SQL statement. For example, if the SQL only expects an integer as input, then you can easily sanitize it. However, sanitization is generally error-prone and not recommended as the sole defense. Just use prepared statement or parameterized query.
          </li>
          <li>
            prevention: <ol class="wp-block-list">
              <li>
                create a linter to detect insecure usages of SQL statement;
              </li>
              <li>
                alert on production SQL statement syntax errors. The idea is that in order to exploit a SQLi the attacker will usually need to try several times to get a working payload.
              </li>
              <li>
                a strong candidate may also mention creating a secure library that make it super easy to write codes securely, and making the friction of writing buggy codes extremely high
              </li>
            </ol>
          </li>
        </ol>
      </li>
    </ol>
  </li>
  
  <li>
    Insecure <code>sprintf</code> : <code>sprintf</code> doesn’t know about the destination buffer size limit. As a result, it can overflow the destination buffer is supplied a char string larger than the destination buffer. <ol class="wp-block-list">
      <li>
        fix: use <code>snprintf</code> : it takes an additional argument for the size of the buffer and limits how much content can be stored in the buffer.
      </li>
      <li>
        followup: a candidate may also point out that the snippet is mixing modern C++ style with traditional C style coding (e.g. <code>char*</code> v. <code>std::string</code>). That’s correct and considered a bonus point.
      </li>
    </ol>
  </li>
  
  <li>
    Insecure buffer overflow check: closely correlated to above, the integer addition and comparison pattern is a classic recipe for integer overflow and subsequently bypassing buffer overflow check. <ol class="wp-block-list">
      <li>
        followup:<ol>
          <li>
            the interviewer can ask to implement a more secure boundary check (or integer addition with embedded
          </li>overflow checks (
          <a href="https://pastebin.com/LWkQRiTu">example</a>)
        </ol>
      </li>
      
      <li>
        the interviewer may also ask about integer promotion
      </li>
      <li>
        prevention & detection: <ol class="wp-block-list">
          <li>
            sanitizers: UBSan has integer overflow related flags <code>signed-integer-overflow</code> and <code>unsigned-integer-overflow</code> that can detect potential overflows, but they can easily get noisy
          </li>
          <li>
            safe libraries: build a set of libraries (for example, <a href="https://github.com/dcleblanc/SafeInt">SafeInt</a> by my colleague LeBlanc) for common pitfalls such as integer overflow and recommend/enforce the usage of those libraries
          </li>
        </ol>
      </li>
    </ol>
  </li>
  
  <li>
    Unbroken <code>case</code> statement: C/C++ doesn’t stop the execution of <code>case</code> unless it sees a <code>break</code> statement. <ol class="wp-block-list">
      <li>
        fix: add a <code>break</code> statement.
      </li>
      <li>
        followup: <ol class="wp-block-list">
          <li>
            prevent this with a linter
          </li>
          <li>
            if you are using gcc/clang, turn on <code>-Wimplicit-fallthrough</code> flag
          </li>
        </ol>
      </li>
    </ol>
  </li>
  
  <li>
    Not checking return value for errors: for example, <code>changeReservation</code> caller should check the return value and error out in case of error. <ol class="wp-block-list">
      <li>
        fix: check the return value
      </li>
      <li>
        followup: <ol class="wp-block-list">
          <li>
            rely on C/C++ gcc/clang compiler’s <code>-Wunused-result</code> flag (along with <strong><code>[[nodiscard]]</code></strong> attribute).
          </li>
          <li>
            linter, as good as always
          </li>
        </ol>
      </li>
    </ol>
  </li>
  
  <li>
    Double Free: note that the <code>session</code> object can be freed twice (heck, many more times) if you use a choice other than 1, 2 or 3. Double free is undefined and can potentially be used for heap-based buffer overflow. <ol class="wp-block-list">
      <li>
        fix: remove the first <code>free</code> ; <code>null</code> ify the pointer free <code>delete</code>
      </li>
      <li>
        prevention and detection: <ol class="wp-block-list">
          <li>
            use smart pointers consistently (e.g. <code>unique_ptr</code> in this case)
          </li>
          <li>
            Use ASan in your testing/canary CI pipeline
          </li>
          <li>
            static analysis is always a good bet
          </li>
        </ol>
      </li>
    </ol>
  </li>
  
  <li>
    Mismatch between <code>new[]</code> and <code>free</code>: <code>delete[]</code> should be used instead of <code>free</code>. The main difference is around allocating raw memory v. additionally calling constructors/destructors. Mismatch memory (de)allocation can lead to memory corruption (e.g. an object getting <code>free</code> d but it could be managing other objects and those objects not getting properly destructed).
  </li>
  <li>
    Memory leakage: the <code>welcomeMessage</code> is not freed at the end. This is probably fine given the size of the memory buffer and that the OS will eventually collect the memory after <code>main()</code> exits. <ol class="wp-block-list">
      <li>
        followup: candidate may suggest using tools such as Valgrind or LeakSanitizer as part of ASan. As an interviewer I’d consider this relatively minor if candidate doesn’t mention this.
      </li>
    </ol>
  </li>
  
  <li>
    Leaking stack trace in <code>stderr</code>: it’s generally a bad idea to leak stack trace in program output directly to users. It’s more so in a webapp than here, because here the attacker has your binary anyway, whereas in a webapp usually the attacker has no access to the server binary. That said, why handling the attacker extra information when you can help? <ol class="wp-block-list">
      <li>
        followup: taint analysis can be used to detect this type of issue
      </li>
    </ol>
  </li>
  
  <li>
    handling of secret in memory: after a successful login, when the program exits, <code>this-&gt;password</code> is most likely still in memory without be cleared up. This is risky because other programs may try to abuse this by reading into this particular memory. <ol class="wp-block-list">
      <li>
        fix: zero out the <code>password</code> variable in destructor, for example by using <code>std::string::clear()</code> . Alternatively, just don’t store the password as part of the class in the first place.
      </li>
      <li>
        prevention: adding a secure framework class which acts as a wrapper for <code>string</code> -like secrets and automatically wipes memory in the destructor. There are some handy solutions out there such as ones provided by <a href="https://libsodium.gitbook.io/doc/memory_management#guarded-heap-allocations">libsodium</a>.
      </li>
    </ol>
  </li>
</ol>

Phew. This is obviously not a comprehensive list of what you may encounter, and there can be more classic ones such as “return-after-free” and “use-after-free” bugs, but hopefully the above analysis gave you a good overview.

<span style="text-decoration: underline">Communication</span>:

Even though communication is a separate axis of assessment from the technical ones above, compared to other types of interview (in particular, behavior interview), communication performance is more closely correlated to technical ones. In other words, I haven’t seen a case when a candidate performed well in communication without performing at least equally well in technical axes.

It’s rare, but one trap that I have observed in weak candidates was that they’d straightly dive into a concept without explaining why. For example, they might be immediately diving into heap grooming (exploitation technique) without even explaining how the heap overflow was bad (vulnerability impact). Personally I’d cut enough slack for strong technical candidates if they are just too passionate with exploitation to bother with explaining the “obvious” thoroughly, but I’d recommend be considerate of where the interviewer wants you to hit.

Another trap I have observed is that candidates throw out some buzzwords and then fail to meaningfully follow up. For example, a candidate could mention _EternalBlue_ or _Shellshock_. 

&#8220;Great! How do they work?&#8221;

Total blank out.

And that doesn’t look good.

When you say something, make sure you can withstand the interviewer probing 3 layers deep. That’s what I always do when the candidate mentions something fashionable. It’s all fair game to me if it’s the candidate who raised a point.