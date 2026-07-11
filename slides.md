---
theme: default
layout: cover
colorSchema: light
transition: slide-left
highlighter: shiki
coverDate: false
fonts:
  sans: "Palatino Linotype"
  mono: "Fira Code"
---

## Robustifying Profile Information Propagation in Profile-Guided Optimization

<div class="absolute bottom-0 right-0">
    <img src="/public/images/logo.png" width="300" height="300">
</div>

<div class="flex items-center mt-20px" style="gap:50px">
<span>
    Nicholas Montana - PhD Admission Interview
</span>
</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
I'm here to present my proposal on a problem that arise in advanced optimization scenarios
-->

---

# Who Am I ?

<v-clicks>

- Cyberchallenge.it 2024 finalist
- Capture-the-Flag player at the official Sapienza CTF team
- Contributed to the LLVM compiler infrastructure by reporting undiscovered bugs 

</v-clicks>

<div class="flex flex-row justify-center align-center mt-50px gap-50px">
  <img v-show="$clicks>=1" src="/public/images/cyberchallenge.png" width="20%">
  <img v-show="$clicks>=2" src="/public/images/trx.png" width="20%">
  <img v-show="$clicks>=3" src="/public/images/llvm.jpg" width="20%">
</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
Over the past two years, I have built a strong background in cybersecurity by completing my Master's degree in Cybersecurity.
During this time, I also took part 
- in the CyberChallenge program, where I reached the final Attack & Defense competition,
- I also participated in several online Capture The Flag competitions as a member of TRX, Sapienza's official CTF team.

Although my primary focus was cybersecurity, my Master's thesis introduced me to the field of compilers, which quickly became a strong research interest.
As part of my thesis, I contributed to the LLVM compiler infrastructure by identifying and reporting several previously undiscovered bugs.
-->

---

# Performance is Critical

<v-clicks>

- Achieving optimal performance is of critical importance today.
  - Small performance improvement can bring substantial economical saving for warehouse-scale deployments.
- **Profile-Guided Optimization** tailors compiler optimizations to the application's measured workload.
  - **Profile** = Execution frequencies of program regions
  - Enables more informed optimization decisions based on real execution behavior.
  - Particularly effective for **performance-critical applications** where representative profiles can be collected.

</v-clicks>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
Today, software performance is more important than ever. In large warehouse-scale deployments, even a small performance improvement can translate into significant savings in infrastructure and operating costs.

One of the most effective techniques for improving performance is **Profile-Guided Optimization**, or PGO.

The main idea is simple: instead of applying optimizations based only on general heuristics, the compiler tailors its optimization decisions to the way the application actually behaves during execution.

To do this, it relies on a **profile**, which records how frequently different parts of the program are executed under a representative workload.
This information allows the compiler to make more informed optimization decisions based on the application's real execution behavior.

Because of this, PGO is particularly attractive for performance-critical applications, where representative execution profiles can be collected and the additional optimization effort is well justified.
-->

---

# Profile-Guided Optimization

<div class="flex justify-center align-center mt-60px">
<v-clicks>

  <div v-show="$clicks === 1" >
      <img src="/public/images/pgo/pgo1.svg">
  </div>
  <div v-show="$clicks === 2">
      <img src="/public/images/pgo/pgo2.svg">
  </div>
  <div v-show="$clicks === 3">
      <img src="/public/images/pgo/pgo3.svg">
  </div>
  <div v-show="$clicks === 4">
      <img src="/public/images/pgo/pgo4.svg">
  </div>
  <div v-show="$clicks === 5">
      <img src="/public/images/pgo/pgo5.svg">
  </div>

</v-clicks>

</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
The PGO workflow can be divided into three main stages.

- First, the source program is compiled in an initial build phase, producing a binary instrumented or prepared for profile collection.

- Second, this binary is executed under a representative workload, during which profile data is collected to capture the program's execution behavior.

- Finally, the collected profile information is fed back to the compiler together with the original source code. The compiler uses this information to guide optimization decisions and generate a more efficient binary tailored to the target workload.-->

---

# Profile Life-cycle

<v-clicks depth=2>

- Within the PGO workflow, profiles go through three main phases.
  - **Collection:** **execution behavior** is measured on a representative workload.
  - **Mapping:** profile information is associated with the corresponding program constructs as **metadata**.
  - **Usage:** the compiler uses this metadata to guide **optimization decisions**.

</v-clicks>

<div v-show="$clicks === 2" class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/collection.svg" width="400px">
</div>
<div v-show="$clicks === 3" class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/mapping.svg" width="400px">
</div>
<div v-show="$clicks === 4" class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/usage.svg" width="400px">
</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
To better understand where my research fits, let's look at the life cycle of a profile within the PGO workflow.
We can identify three main phases.
- The first is **collection**, where the execution behavior of the application is measured while running a representative workload.
- The second is **mapping**. Here, the collected profile information is associated with the corresponding program constructs as metadata. Examples of such metadata are information about how many times a function was invoked or how many times a branch was taken.
- Finally, we have **usage**, where the compiler relies on this metadata to guide its optimization decisions and generate more efficient code.
-->

---

# Practical Obstacles

- For an ideal PGO application

<Highlight class="mt-20px mb-20px">

Profile must be **complete** and **accurate** in each phase of its life-cycle

</Highlight>

<v-clicks>

- Inaccurate profiles may lead the compiler to make suboptimal optimizations.
- In practice, each phase hides sources of inaccuracies
  - **Sampling** strategies are inaccurate by nature
  - The dynamic nature of software leads to **stale profiles**
  - Optimizing passes contain errors in **metadata propagation** logic

</v-clicks>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
Now let's look at the practical challenges of applying Profile-Guided Optimization.

Ideally, the profile should remain both **complete** and **accurate** throughout its entire life cycle. By *accurate*, I mean that it should faithfully represent how the application behaves under its target workload.
Otherwise, the compiler may make optimization decisions based on misleading information, resulting in less efficient code.

In practice, however, every phase of the profile life cycle can introduce inaccuracies.
- During **collection**, if sampling is chosen as a collection strategy, which is inherently an approximation and therefore cannot capture the program's behavior perfectly.
- Over time, profiles can also become **stale**. As software evolves, a profile collected for one version of the program may no longer accurately represent a newer version, reducing its usefulness.
- Finally, inaccuracies can also be introduced during **optimization** itself. As compiler passes transform the program, they must update the associated profile metadata to keep it consistent with the transformed code.
If this propagation is incorrect, subsequent optimization passes will rely on inaccurate profile information, even if the original profile was perfectly accurate.

There are various works in literature that address the first two problems, while the problem that lies within the usage phase remains largely unstudied.
-->

---

# State of the Art

<v-clicks depth=2>

- Significant research effort has addressed these challenges.
  - [^profi] Rectifies inaccuracies introduced by **sampling**.
  - [^stale] Adapts **stale profiles** to newer versions of a program.
  - [^propagation], [^unittesting] Started addressing errors in **profile metadata propagation**, but only partially.

</v-clicks>

[^profi]: Wenlei He, Julián Mestre, Sergey Pupyrev, Lei Wang, and Hongtao Yu. “Profile inference revisited”.
[^stale]: Amir Ayupov, Maksim Panchenko, and Sergey Pupyrev. “Stale Profile Matching”.
[^propagation]: Youfeng Wu. “Accuracy of Profile Maintenance in Optimizing Compilers”.
[^unittesting]: Profile Information Propagation Unittesting: https://discourse.llvm.org/t/rfc-profile-information-propagation-unittesting/73595

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
Several research efforts have tackled the sources of inaccuracies we have just discussed.

- The first work proposes a technique to improve the accuracy of sampled profiles.
- The second work adapts profiles so that they can still be used after the software evolves.

The last two works started exploring the problem of **profile metadata propagation** during optimization.

- The first work highlights that optimization passes may fail to preserve profile information correctly.
- The second work introduces a practical approach to detect cases where metadata is accidentally lost during optimizations.

However, these approaches do not fully address the problem.

- The first work does not provide a systematic way to identify which optimizations are responsible for profile propagation errors.
- The second work focuses only on cases where metadata is lost, but does not address the more challenging scenario where metadata is preserved but becomes **incorrect** after a transformation.

Overall, the problem of ensuring profile propagation correctness remains largely unexplored, despite its importance for the reliability of Profile-Guided Optimization.
-->

---

# Example of Profile Mishandling

<div style="display:flex; flex-direction:row; justify-content:space-evenly; align-items:center; height:80%;">

<div style="display:flex; flex-direction:column; justify-content:space-evenly; align-items:center;">

```c {all}{class:'!children:text-[15px]'}
// Before pass
if (x > 0) { // Then branch taken 80 times
    handle_positive(x) 🔥
} else { // Else branch taken 20 times
    handle_negative(x) ❄️
}
```

```mermaid {theme: 'neutral', scale: 1}
graph TD
entry(if x > 0) -->|"T:80%"| else("handle_positive(x)")
entry -->|"F:20%"| then("handle_negative(x)")

style then fill:#bae1ff,color:#fffff
style else fill:#ffb3ba,color:#fffff
```

</div>

<div v-click style="display:flex; flex-direction:column; justify-content:space-evenly; align-items:center;" >

```c {all}{class:'!children:text-[15px]'}
// After pass
if (x <= 0) { // Then branch taken 80 times
    handle_negative(x) 🔥
} else { // Else branch taken 20 times
    handle_positive(x) ❄️
}
```

```mermaid {theme: 'neutral', scale: 1}
graph TD
entry(if x <= 0) -->|"T:80%"| else("handle_negative(x)")
entry -->|"F:20%"| then("handle_positive(x)")

style then fill:#bae1ff,color:#fffff
style else fill:#ffb3ba,color:#fffff
```

</div>
</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
So to better understand the problem, let's take a look at this simple example.
Consider the following snippet of C code, which checks if a variable x is greater then 0.
If is, it calls the handle_positive function, while if it is not, it calls the handle_negative function.
Lets assume that there's a profile associated to this program, which tells the compiler that the then branch was taken 80 times
while the else branch was taken 20 times, thus rendering handle_positive a hot function worth of optimizing and handle_negative a cold function.

Lets also assume that there exists a fictional optimization that the only transformations that performs is that if flips the condition of the if check.
Now instead of checking if x is greater than 0 the program will check if x is less than or equal then 0. It also flips the content of the then and else code blocks
in order to preserve program semantics. But, doing this transformations, it forgets to also update the profile.

After this missed update, the compiler will erroneously think that handle_negative function is hot and will optimized the program for the wrong case,
prioritizing the cold path over the hot one, with the result of a suboptimal final program.
-->

---

# Profile Mishandling Research Gap

<v-clicks>

- Recent studies have linked performance regressions to **incorrect profile propagation**. [^regressions]
- It can **undermine** the benefits of accurate profile collection and adaptation.
- Ensuring correct profile propagation is **non-trivial**.
  - There is no obvious correlation between the original and transformed code.
  - Compiler passes **interact**, so testing passes in isolation is not enough.
  - There are **no dedicated tools** to validate profile propagation.

</v-clicks>


<Highlight v-click class="mt-10px mb-10px">

Profile information is **transformed** together with the program, but its correctness cannot be directly observed.

</Highlight>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

[^regressions]: Performance Regression in LLVM - A SPEC CPU 2017 Study: https://discourse.llvm.org/t/performance-regression-in-llvm-a-spec-cpu-2017-study/84812

<!--
This brings us to the research gap that motivates my work.

Recent studies have shown that some performance regressions can be traced back to incorrect profile propagation during compilation. In other words, even if we start with a high-quality profile, mistakes introduced by optimization passes can gradually reduce its accuracy.

As a result, the benefits of all the effort spent collecting and improving the profile can be progressively lost as the optimization pipeline executes.

Addressing this problem, however, is far from straightforward.

- When compiler passes transform the program, there is often no direct correspondence between the original code and the transformed code, making it difficult to determine how profile information should be updated.
- Moreover, optimization passes interact with one another, so an incorrect update performed by one pass can affect all subsequent passes.
- There are currently no dedicated tools to systematically verify that profile information is propagated correctly throughout the optimization pipeline.

So to summarize the problem: Profile information is transformed together with the program, but its correctness cannot be directly observed.
-->

---

# Research Proposal

<Highlight color="#ffdfba" icon="/public/images/question.svg" class="mt-10px mb-10px">

Can profile propagation accuracy be assessed systematically?

</Highlight>

<v-clicks>

- A framework to systematically validate profile propagation.
  - Generate **synthetic test programs** with automatically verifiable profile behavior.
  - Drive test generation using **coverage-guided techniques** to exercise profile propagation logic.
- Expected scientific contributions
  - A methodology to systematically assess profile propagation correctness.
  - A coverage-guided testing strategy for improving the validation of profile propagation.

</v-clicks>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
This brings me to the central research question of my proposal: Can profile propagation accuracy be assessed systematically?

To answer this question, I propose to develop a framework for validating profile propagation throughout the optimization pipeline.

- The first research direction is the generation of synthetic test programs whose expected profile behavior can be automatically verified after compiler transformations.
- The second direction builds on this idea by using coverage-guided testing to automatically generate and prioritize tests that exercise as much profile propagation logic as possible.
In this way, coverage-guided testing is not an objective on its own, but a means to validate a broader range of compiler transformations.

Together, these two directions lead to two main scientific contributions:
- a methodology for systematically assessing profile propagation correctness,
- and a novel coverage-guided testing strategy specifically designed to improve the validation of profile propagation.
-->

---

# Stress-Test approach

<v-clicks depth=2>

- Stress-test profile propagation using **synthetic test programs**
  - Generate structurally complex programs using **off-the-shelf** test generators.
  - Optimize them with PGO and construct the **ground-truth profile**.
  - Compare the propagated profile against the ground truth for **validation**.

</v-clicks>

<div v-show="$clicks === 1" class="flex justify-center align-items mt-20px mb-20px ">
  <img src="/public/images/blackbox/blackbox0.svg">
</div>
<div v-show="$clicks === 2" class="flex justify-center align-items mt-20px mb-20px ">
  <img src="/public/images/blackbox/blackbox1.svg">
</div>
<div v-show="$clicks === 3" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/blackbox/blackbox2.svg" >
</div>
<div v-show="$clicks >= 4" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/blackbox/blackbox3.svg">
</div>

<v-clicks>

- Automated **bug triaging** to identify the responsible compiler component.
- Preliminary results: **multiple confirmed LLVM bugs**, among the first reported for profile propagation.

</v-clicks>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
The first research direction builds upon the work I started during my Master's thesis.

1. The idea is to stress-test profile propagation using automatically generated **synthetic test programs**.
2. For each generated program, the compiler first performs Profile-Guided Optimization, producing a profile that has been propagated throughout the optimization pipeline.
3. Then, instead of trusting that propagated profile, we independently execute the optimized program to reconstruct its actual execution profile. This gives us a **ground truth** describing how the transformed program really behaves.
4. Finally, we compare this ground truth with the profile propagated by the compiler. Any discrepancy indicates that profile information was not propagated correctly during optimization.

Since this process can uncover a large number of issues, the framework also requires an automated triaging mechanism to identify which compiler transformation is responsible for introducing each propagation error.

I have already prototyped this approach during my Master's thesis, where it led to the discovery of several previously unknown profile propagation bugs in LLVM.
These bugs were confirmed by the LLVM developers and, to the best of my knowledge, were among the first reported specifically for profile propagation, providing encouraging evidence of the effectiveness of this methodology.
-->

---

# Coverage-Guided Testing 

<v-clicks depth=2>

- Extend the stress-test approach with **coverage-guided testing**
  - Start from existing compiler **test suites**.
  - Apply classic code mutations and **novel profile-aware mutations**.
  - Guide mutations using a **coverage metric** targeting profile propagation logic.
  - Validate propagated profiles to detect profile mishandling.

</v-clicks>

<div v-show="$clicks === 1" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/greybox/greybox0.svg">
</div>
<div v-show="$clicks === 2" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/greybox/greybox1.svg">
</div>
<div v-show="$clicks === 3" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/greybox/greybox2.svg" >
</div>
<div v-show="$clicks === 4" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/greybox/greybox3.svg">
</div>
<div v-show="$clicks >= 5" class="flex justify-center align-items mt-20px mb-20px">
  <img src="/public/images/greybox/greybox4.svg">
</div>

<v-click>

- Enables exploration of **previously untested** profile propagation logic.

</v-click>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
While the stress-test approach is effective, its coverage ultimately depends on the complexity of the generated synthetic programs.
To overcome this limitation, I propose a second, complementary research direction based on coverage-guided testing.

The idea is inspired by existing compiler testing methodologies, where a set of carefully designed test programs serves as a starting point.

1. Rather than generating programs from scratch, these existing tests are automatically mutated to explore new compiler behaviors.
2. In my case, I plan to combine traditional code mutations with novel profile-aware mutation strategies, specifically designed to exercise profile propagation logic.
3. The mutations are guided by a dedicated coverage metric, implemented by instrumenting the compiler, that measures how much of the profile propagation logic has been exercised.
This allows the framework to continuously generate new tests that target previously unexplored behaviors.
4. The resulting test cases are then validated using the same profile validation methodology introduced in the previous approach.

Beyond improving the validation of profile propagation, this methodology could also provide a foundation for applying coverage-guided testing to other compiler testing problems.
-->

---

# Evaluation of the Proposed Directions

<v-clicks depth=2>

- **LLVM** compiler infrastructure as evaluation target
- Evaluate whether the proposed methodologies can:
  - Detect new **profile mishandling bugs**.
  - Identify bugs with a measurable **performance impact**.
  - Improve the exploration of LLVM's **profile propagation logic**.

</v-clicks>

<div class="flex flex-row justify-center align-center gap-20px mt-70px mb-20px">

  <Card v-show="$clicks>=3" content="🕸️Bugs" color="none"/> 
  <Card v-show="$clicks>=4" content="⚡Performance" color="none" />
  <Card v-show="$clicks>=5" content="🧭Coverage" color="none" />

</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
The proposed methodologies will be evaluated on the **LLVM compiler infrastructure**.

LLVM is a widely adopted compiler framework, used in industrial environments by companies such as Google and Apple, while also being extensively used by researchers and the open-source community to develop and evaluate new compiler techniques.

The evaluation will focus on three main aspects.

- First, whether the proposed approaches can discover new **profile mishandling bugs** in LLVM.
- Second, whether fixing these issues leads to a measurable improvement in the performance of the generated binaries.
- Finally, whether the proposed testing methodologies can improve the exploration of profile propagation logic, allowing us to exercise compiler behaviors that are otherwise difficult to reach.
-->

---

# Impacts and Benefits

<v-clicks>

- **Analysis tools** to help compiler developers assess and improve PGO implementations.
- **More effective PGO** leading to faster binaries and reduced execution costs.

</v-clicks>

<div class="flex flex-row justify-center align-center gap-20px mt-70px mb-20px">

  <Card v-show="$clicks>=1" content="🛠️Analysis Tools" color="none" width=""/> 
  <Card v-show="$clicks>=2" content="🚀Faster Binaries" color="none" width=""/>
  <Card v-show="$clicks>=2" content="🌱Energy Savings" color="none" width=""/>

</div>

<SlideCurrentNo class="absolute top-5 right-10" style="opacity:50%"/>

<!--
The expected impact of this research is twofold.

- First, it will provide compiler developers with new analysis tools to assess the correctness of their PGO implementations and identify potential profile propagation issues.
- Second, by improving the reliability of profile-guided optimization, this work can enable more effective optimized binaries.
This translates into performance improvements for users and, especially in large-scale deployments, can provide both economic benefits through reduced computational costs and environmental benefits through improved energy efficiency.

Moreover, this research direction is aligned with industrial interests, as the reliability and effectiveness of PGO are relevant topics also explored in collaboration between academic and industrial partners, including Google.
-->

---

# Takeaway

<v-clicks>

- **Problem**:

  <Highlight iconwidth="0px" class="mb-10px">

  Profile propagation is **not reliably** validated

  </Highlight>

- **Proposal**:

  <Highlight color="#ffdfba" iconwidth="0px" class="mb-10px">

    - Stress-testing via **synthetic test**
    - **Coverage-guided exploration** of compiler passes
    - **Automated validation** of profile correctness

  </Highlight>

- **Contribution**:

  <Highlight color="#baffc9" iconwidth="0px" class="mb-10px">

  **Better** optimizations and **improved** compiler coverage

  </Highlight>

</v-clicks>

<!--
Based on discussions with LLVM developers, profile propagation validation is currently performed in a best-effort manner, or in some cases not systematically performed, due to the lack of dedicated mechanisms to verify it.
-->
