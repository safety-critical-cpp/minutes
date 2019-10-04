# Safety Critical C++
## 2019-10-04 Telecon

**Attendance:**
- BAL: Bryce Adelstein Lelbach (NVIDIA)
- AC:  Ali Cehreli (Mercedes, autonomous vehicles)
- JP:  Janis Peukert (Mercedes, autonomous vehicles)
- AV:  Aleksandar Veselinovic (Mercedes, autonomous vehicles)
- AS:  Aditya Sreekumar (Aptiv, autonomous vehicles)
- AC:  Andy Condie (Canon Medical Research, medical)
- BR:  Bryce Ready (Zenuity, autonomous vehicles)
- GV:  Geoffrey Viola (AutoX, autonomous vehicles)
- NR:  Nicolas Rey (AutoX, autonomous vehicles)
- JSL: Jean-Simon Lapointe (Irystec)
- KD:  Kenneth Dunne
- MB:  Matthew Butler (Laurel Lye)
- NV:  Neal Venditto (Naval Nuclear Laboratory)
- MW:  Michael Wong (Codeplay)
- PR:  Philipp Robbel (Aptiv)
- IB:  Ilya Burylov (Intel)
- AW:  Anuya Welling (Intel)
- AP:  Andreas Pasternak (Apex.AI, autonomous vehicles)
- VD:  Victor Diec (Klocwork, static analysis)
- RS:  Rohit Salem
- AD:  Aaron Dalton

**Agenda:**

- Explain our overall purpose, ISO process, etc (15 minutes)
- Future telecon scheduling (5 minutes)
- Review the requirements document we started at CppCon (40 minutes)
  - Identify any additional requirements.
  - Identify and prioritize which specific items we need to explore further (e.g. talk about at future meetings).
  - Identify "volunteers" and stakeholders who are interested in doing the homework of preparing material on the identified focus areas.

JSL: Is educating safety critical programming in scope?

BAL: Yes.

AS: Is SG12 the right group for this?

BAL & MW: We'll do whatever is right. Either expand the scope of SG12, or form a new group.

BAL: We should probably plan to do a call every month.

### Safety Critical Requirements Document Review

[Safety Critical Requirements Draft](https://docs.google.com/document/d/1kpnfHrVNlCoevQh7GJrMR8WUlZ3Lc8OqLM_dGsboSTw/edit#heading=h.n62bdli8t6cz)

#### Safety Critical/Certified Standard Library

BR & AP: We think this is important.

AP: Need 2 things. Making sure that the standard library is certifiable. Do the actual work to actually get the standard library certified.

AP: Going through the standard library and asking "could this be safety certified" would be a good start.

NV: I agree with all of that, but should there be some subset of the standard library that we focus on? There's a lot of stuff that can be tweaked to be good for safety critical, or we could try to identify a subset that is certified.

PR: What does "ceritifable" mean. Unit testable? Memory safety? Exception safety?

AP: Someone needs to review the safety standards and identify the core commonalities/rules that may be applicable to the standard library.

AP: Actual certification may be outside of the work; different fields have different guidelines/certification requirements.

GV: Some cross-over with freestanding. Is it worth looking into that/contributing to that?

BAL: Freestanding is not well defined today. It may be the case that it makes sense for us to collaborate with them, but we should be careful to make sure that our needs are still met. If our requirements are fundamentally different than others who care about freestanding, we might need a separate subset. That said, fewer subsets is better.

#### Tools

BR: Not sure what is needed here.

AP: We should have one theme/topic that deals with exceptions. The tools, memory allocation, standard library use, etc.

AP: Another theme/topic would be memory allocation in general.

AS: I'm fine with a strategy where we talk about tools as a sub-theme of other topics like memory and exceptions.

#### Memory Allocation

PR: Should memory allocation allowed in safety critical at all? E.g. if there's no defragmentation, is it okay?

PR: Question to the group: should memory allocation be forbidden?

AD: I agree. If memory allocation is deterministic, it could be okay. There is a place for some subsets/regions that allow no memory allocation.

NV: What is a deterministic memory allocator?

BAL: Isn't a bump allocator that never reclaims a deterministic allocator?

AP: We should not limit our discussion here. It should be fine to create a map and free it to a memory pool, if it's deterministic.

AV: We usually do allow heap allocation during the startup/init phase. After that phase, we need a way to disallow any addition allocations.

MW: Autosar attempts to allow some form of deterministic allocation after startup; MISRA just disallows it entirely, but we're trying to change that. It would be great if we could try to define deterministic allocation.

AP: How can we tell standard library facilities to use our allocators.

IB: The current C++ standard library has two types of APIs: ones that accept allocators, and one that uses new/delete underneath. There's two approachs to dealing with those different facilities. Example: std::function, which no longer supports allocators.

AP: For safety critical applications, everything must parameterize memory allocation.

BAL: This is good info to give to the committee. There are some on the committee who don't believe in allocators; some features down the road, like std::text, may not support allocators.

BR: Not clear if today's allocators give us enough control.

#### Exceptions

NV: Herbceptions offer some solutions there.

BAL: Do we like Herbceptions? Does that solve problems for us?

AP: Yes, that makes sense for us. But we'd need to ensure that standard library facilities uses Herbceptions.

AS: Some temporary solutions/solutions that work for C++ today would be good.

PR: Can compilers help make catching exceptions more deterministic?

JSL: What is the current recommendation for exceptions? Can they be used?

Various: Autosar has some guidance for how they can be used. Other standards say don't use them. There's no consensus today.

AP: A lot of those guidelines aren't reasonable. "You can use exceptions, just don't allocate" or "You can use exceptions, but you have to implement a deterministic dynamic_cast themselves".

PR: Can we get insight from tool vendors on what they could do here? E.g. how could they improve determinism.

AP: Core problems with exceptions:
- Memory allocation
- Non-determinism
- Tooling support

PR: When you saying tooling, do you mean compiler toolchain, or measuring execution time?

AP: Everything. Validation, measuring execution time, coverage, etc.

### Conclusion

BAL: We haven't gotten through the whole document; that's okay. Please review the document. If you want something to get attention, start a discussion about it.

BAL: Topics that we need to discuss at future meetings:
- Safety critical standard library
- Memory allocation
- Exceptions

BAL: We need to define what we need by determinism.

BAL: Is anyone planning to go to ISO C++ meetings?

IB: I am.

**Action Items:**
- Everyone: Review the document, start discussions on anything you think is important/needs attention.
- BAL: Setup a github
- BAL: Schedule next meeting.