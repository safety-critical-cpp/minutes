# Safety Critical C++
## 2019-10-18 Telecon

**Attendance:**
- BAL: Bryce Adelstein Lelbach (NVIDIA)
- JSL: Jean-Simon Lapointe (Irystec)
- AS:  Aditya Sreekumar (Aptiv, autonomous vehicles)
- PR:  Philipp Robbel (Aptiv, autonomous vehicles)
- RM:  Ryan McDougall (Aurora)
- GV:  Geoffrey Viola (AutoX, autonomous vehicles)
- FP:  Frank Perron (NVIDIA)
- AC:  Andy Condie (Canon Medical Research, medical)
- BR:  Bryce Ready (Zenuity, autonomous vehicles)
- AP:  Andreas Pasternak (Apex.AI, autonomous vehicles)
- JP:  Janis Peukert (Mercedes)
- KD:  Kenneth Dunne
- NR:  Nicolas Rey (AutoX, autonomous vehicles)
- MB:  Matthew Butler (Laurel Lye)
- AC:  Ali Cehreli (Mercedes, autonomous vehicles)
- KB:  Kurt Bruneau
- MS:  Mehul Sagar
- SS:  Scott Sweeny

**Agenda:**

Meeting of the Safety Critical C++ group to make progress on D1904, our document describing the requirements, needs, and challenges of safety critical programming in C++.

At this meeting, we will deep-dive on a topic from the draft of D1904, either:

- Exceptions in safety critical code.
  - Philipp Robbel and Aditya Sreekumar.
  - https://docs.google.com/document/d/19v6leRLuvezIxwS_GaS3ehANjcxtpTV-Zs4MsAOy36A/edit?usp=sharing
- Formal definition of determinism.
- Safety critical use of the standard library,
- Memory allocation in safety critical code.
- Another topic based on discussion on the mailing list.

We'll select the topic closer to the meeting, based on the direction of mailing list/slack discussions.

### Exceptions in Safety Critical Code

Reviewing: https://docs.google.com/document/d/19v6leRLuvezIxwS_GaS3ehANjcxtpTV-Zs4MsAOy36A/edit?usp=sharing

BR: On this question of violating the determinism principle, I'm not sure we're getting to the bottom of the issue. The issue isn't that memory allocation can fail, it's the way in which it can fail. You don't have the right control.

AS: Yah, I agree. Also, for example, for stack unwinding, it's not clear who exactly will handle this.

BR: I have a similar concern there. If it really is true that in your code, you have level A that calls level B, C, D, etc, and level D has a failure that only level A can handle, then you still have a problem, regardless of whether you are using exceptions or not. It's a structural problem with your code.

AP: In our example code, we have unit tests that ensure that you do not actually allocate memory. It's a POC that we can throw exceptions without allocating memory.

PR: What compilers does it work with?

AP: It's designed for the Itanium ABI, but also has some compiler specific bits, so it's tied to GCC right now.

BR: Your tooling question is a good one. Multiple control flow paths might not be exceptions; it may just be your code. The nice thing with error codes is that it explicitly forces you to deal with this.

AC: We have a policy in our codebase that we don't throw exceptions. The effect of that policy is that every single function returns a status code. So every function call you make has the same boilerplate around it. This means people aren't even really thinking about it; they're just using the boilerplate. Why is that better than throwing the exception?

BR: I agree.

AP: I agree, too. It makes things more visible, but the end result may be the same.

AS: If you can solve the memory allocation issues with exceptions, then it might end up being the same as error codes.

AS: Most code coverage tools can handle error codes. Not sure that they can handle exceptions.

AP: I believe with gcov, etc, you can get to 100% coverage. But the tool isn't very helpful in diagnosing issues. With return codes, you'd see the branch; with exceptions, the branches are invisible. So, the code coverage tool tells you there is an unhandled branch, but doesn't explain that it's because of exceptions. The tools could be more helpful here.

PR: There's also the risk of not dealing with error codes when calling functions. You don't have that with exceptions.

AP: Compiler warnings can help here, and make it difficult to ignore errors.

AC: Not every function/framework agrees on what error codes representations are. Often, you have to translate between error codes, and sometimes you end up doing it wrong; e.g. 0 means no error in one framework and means an error in another. Fundamentally, error codes are not type safe.

RM: Exceptions can have the same translation issue.

JP: We find that we have to rely on code coverage tools when we use exceptions. This means they have to be qualified/certified in the same way as the compiler. With error codes, we can just rely on the compiler and unused return value warnings. 

SE: Regarding worst case execution time (WCET) - relying on some statistic is not enough. You have to at least consider some uncertainty/boundaries.

RM: I think a lot of what we think of error has a lot more to do with exceptation and uncertainty.

AP: Several compiler vendors are working on ASIL-B and ASIL-B certified compilers. But, it's very difficult to get information about of them.

Would be good to get someone from QNX involved.

AS: How can we get in contact GCC/LLVM maintainers?

BAL: The GCC/LLVM mailing list. 