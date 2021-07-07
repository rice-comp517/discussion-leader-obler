# Review

## Info

Reviewer Name: Andrew Obler

Paper Title: _Efficient Software-Based Fault Isolation_

## Summary

### Problem and why we care

Large applications are often developed with extensibility in mind, allowing for
their functionality to be extended through the installation of independently
developed modules. However, this brings with it a greater potential for
critical software faults; a flaw in any one module can permanently damage the
rest of the application or even the system as a whole. So, it is desirable to
have some way to isolate these modules as they run, in order to both lessen the
impact of a fault and help identify the source of the fault so that it may be
removed or repaired.

### Gap in current approaches

The most common approach to fault isolation is hardware-based: Each module gets
its own address space, thus it cannot alter the internal data or code of any
other module (the MMU prevents it through paging). The modules are still able
to safely request services from each other through Remote Procedure Calls.
However, this is very costly, potentially slowing execution by several orders
of magnitude, and is ineffective for applications where the modules are tightly
integrated and need to communicate frequently.

### Hypothesis, Key Idea, or Main Claim

The paper proposes a software-based approach to fault isolation which relies on
logical separation of a single address space, rather than distinct address
spaces for each module, as well as modification of the module's code to prevent
it from reaching outside its designated portion of the address space (its
so-called 'fault domain'). Compared to purely hardware-based isolation, this
results in programs which run slightly slower on the whole, but which can
communicate much more quickly, since e.g. they do not have to perform context
switches so frequently. It also allows for more selective 'sandboxing' of only
those modules which are untrustworthy, allowing the rest of the application to
run at the usual speed. So, this approach is judged superior to
hardware-based isolation, especially when modules need to communicate often.

### Method for Proving the Claim

The paper walks through some of the details of the approach, and explains how
they make for secure and efficient fault isolation.

Section 3 describes how isolation of modules is achieved:
- The application's address space is divided into segments, and each fault
domain gets two uniquely identified segments (code and data/stack/heap,
respectively)
- 'Unsafe' instructions, which cannot be guaranteed to not target a foreign
segment, have special instructions inserted before them, which raise an
exception if the target segment is forbidden ('segment matching'). This allows
compromising instructions to be located within the module
- Alternatively, we can insert code which forces the target address into the
appropriate segment ('sandboxing'). This prevents the module from reaching
outside its fault domain without raising an exception
- The above has a minimal impact on performance, and can be further optimzed
through various methods as described in Section 3.3
- Resources shared by the entire address space (e.g. open files) are
protected by converting system calls into RPC requests to a trusted domain of
'arbitration code'
- Read-only sharing of data is easy, since load instructions are 'safe';
read-write sharing can be achieved by mapping a region of shared memory into
every segment that requires access ('lazy pointer swizzling')
- Section 3.6 lays out two implementations of these methods: modifying the GCC
compiler to enforce the isolation at compile-time; and modifying the object
code directly at load-time ('binary patching')

Section 4 describes an efficient approach to 'crossing fault domains' and
allowing modules to communicate: An untrusted domain exposes 'stubs' for each
of its procedures that can be called from the outside. It also contains a
'jump table', which is read-only, and which is the only way for code within the
module to escape into another domain.

### Method for evaluating

Section 5 of the paper is dedicated to performance evaluation of this approach.
First, the authors measure the overhead incurred by encapsulation and compare
the measurements to a model of the expected amount of overhead. Second, they
measure the time taken to complete several types of cross-domain procedure
calls. Finally, they test all of the above using a real-world database
application, `POSTGRES`, which dynamically loads user-provided code to allow
for custom data types.

### Contributions: what we take away

Based on the evaluation results, we can conclude that this software-based
approach is superior to existing native hardware-based approaches when a
significant amount of time is spent in cross-domain calls. Importantly, the
results also demonstrate that isolation overhead remains small regardless of
the behavior of the software encapsulated, so developers are free to choose
which modules to isolate without needing to worry about disproportionate impact
on performance.

## Pros (3-6 bullets)

- Thorough breakdown of each portion of the solution for performance evaluation
- Exposition of the system's inner workings is technical, but well-structured
and easy to follow
- Overall persuasive argument for software-based being better than hardware
(at least, better than existing hardware approaches)
- Occasional typos were funny :-) (`the the` ; `techinically`)

## Cons (3-6 bullets)

- Would have been nice to see benchmarks with a couple of other real-world
applications on the scale of `POSTGRES`--further stress-test the approach with
a more diverse pool of software
- Also would be great to see how an actively malfunctioning module is handled
(perhaps create an intentionally misbehaving add-on to `POSTGRES`)
- No effective solution to recover a module that attempts a bad jump/store;
even when sandboxed, would just lead to unpredictable writes within the
module's fault domain

### What is your analysis of the proposed?

I think this paper has largely convinced me of the efficacy of software-based
isolation. The greatest weakness was the rather shallow benchmarking, as
mentioned in the Cons section above. Nonetheless, the ideas are sound and I am
excited to look into further research that has come of this.

## Details, Comments, Observations, Questions

I've seen a couple of other projects that involve directly modifying the
object/assembly code ('patching') of an executable to achieve some property, so
it was nice to see it mentioned again here. I think it's a natural way of
working with software, as it has exceptional portability with minimal
prerequisites. I'm curious what other major projects have made use of methods
like this?
