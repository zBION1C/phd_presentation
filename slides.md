---
theme: academic
layout: cover
colorSchema: light
transition: slide-left
highlighter: shiki
hideInToc: true
coverDate: false
fonts:
  sans: "Maple Mono"
  mono: "Fira Code"
---

## Robustifying Profile Information Propagation <br> in Profile-Guided Optimization

<div class="flex items-center" style="gap:50px">
<span>
Supervisor<br>
Prof. Daniele Cono D'Elia
</span>
<span>
Co-supervisor<br>
Dr. Cristian Assaiante
</span>
</div>

<div class="absolute bottom-0 right-0">
    <img src="/public/images/logo.png" width="300" height="300">
</div>

---
hideInToc: true
---

# Table of Contents

<Toc maxDepth1 />

---
hideInToc: true
---

# Performance is Critical


<v-clicks>

- As of today achieving optimal performance is of critical importance
  - A 1% performance improvement can save millions of dollars annually in large-scale deployments.
- **Profile-Guided Optimization** is a way to achieve great performance
  - Optimization process **tailored** to the workload of the compiled program
  - **Profile** = Execution frequencies of program regions
  - Eliminates the need to infer such information
  - Perfect for high-load applications

</v-clicks>


<!--
As of today, performance is a crucial property of software, due to the rise
of resource-intensive applications like warehouse-scale or AI applications.
Profile-Guided Optimization, among other optimization strategies, stands-out for achieving great performance.
- It consists of tailoring the optimization process around the actual workload of the application
- And it does so by using a profile for the application, which represents what regions of code were touched during execution
- So it eliminates the need to infer such information, which were computed by classical optimization
- It is perfect for high-load applications, since complete profiles can be collected
-->

---

# Profile-Guided Optimization

<!-- <div style="display:flex; justify-content:center; align-items:center; height:100%"> -->

<div class="flex justify-center align-center mt-90px">
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
  <div v-show="$clicks === 6">
      <img src="/public/images/pgo/pgo6.svg">
  </div>
  <div v-show="$clicks === 7">
      <img src="/public/images/pgo/pgo7.svg">
  </div>

</v-clicks>

</div>

---
hideInToc: true
---

# Profile Life-cycle

<v-clicks depth=2>

- Profiles traverse three main phases within the PGO workflow
  - **Collection**: The profile is collected and persisted as a file
  - **Mapping**: The profile is represented as **metadata** attached to program constructs
  - **Usage**: The metadata is used to guide optimizing transformations

</v-clicks>

<div v-show="$clicks === 2" class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/collection_phase.svg" width="700px">
</div>
<div v-show="$clicks === 3" class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/mapping_phase.svg" width="700px">
</div>
<div v-show="$clicks === 4" class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/usage_phase.svg" width="700px">
</div>

<!--
Looking at the PGO workflow, we can isolate three main phases of the profile life-cycle
- Collection, where the profile is collected and persisted as a file. There are various collection techniques 
- Mapping, where the profile mapped back to program constructs and is represented as metadata attached to them.
- Usage, where the program associated with profile metadata is optimized and the profile is used to guide optimization decisions
-->

---

# Practical Obstacles

- For an ideal PGO application

<Highlight class="mt-20px mb-20px">

Profile must be **complete** and **accurate** in each phase of its life-cycle

</Highlight>

- Inaccurate profiles may lead the compiler to make suboptimal optimization decisions.
- In practice, each phase hides sources of inaccuracies
  - **Sampling** strategies are inaccurate by nature
  - Dynamic nature of software leads to **stale profiles**
  - Optimizing passes contain errors in **metadata propagation** logic

<!--
By **accurate** I mean that the profile has to accurately reflect the actual workload experienced by the binary when executed in the production environment.
Bad profiles could result in bad optimization decisions, leading ultimately to a sub-optimal program generation
Nevertheless, in practice some sources of inaccuracies are hidden within the PGO workflow itself
- Sampling collection strategies are inaccurate by nature, leading to inaccurate profiles collected
- The dynamic nature of software leads to stale profiles. In fact before we assumed that the program version on which we collected the  profile is the same as the program version on which we want to use the profile.
In practice this rarely happens, and the structural mismatch between the two versions make some part of the program unusable.
- Finally, optimization pipeline themselves need to carefully update profile metadata after code transformations, so that later passes could still benefit from an accurate profile.
-->

---
hideInToc: true
---

# Example of Profile Mishandling

<div style="display:flex; flex-direction:row; justify-content:space-evenly; align-items:center; height:80%;">

<div style="display:flex; flex-direction:column; justify-content:space-evenly; align-items:center;">

```c{all}
// Before pass
if (x > 0) { // Then branch taken 80 times
    handle_positive(x) 🔥
} else { // Else branch taken 20 times
    handle_negative(x) ❄️
}
```

```mermaid {theme: 'neutral', scale: 0.8}
graph TD
entry(if x > 0) -->|"T:80%"| else("handle_positive(x)")
entry -->|"F:20%"| then("handle_negative(x)")

style then fill:#bae1ff,color:#fffff
style else fill:#ffb3ba,color:#fffff
```

</div>

<div v-click style="display:flex; flex-direction:column; justify-content:space-evenly; align-items:center;" >

```c{all}
// After pass
if (x <= 0) { // Then branch taken 80 times
    handle_negative(x) 🔥
} else { // Else branch taken 20 times
    handle_positive(x) ❄️
}
```

```mermaid {theme: 'neutral', scale: 0.8}
graph TD
entry(if x <= 0) -->|"T:80%"| else("handle_negative(x)")
entry -->|"F:20%"| then("handle_positive(x)")

style then fill:#bae1ff,color:#fffff
style else fill:#ffb3ba,color:#fffff
```

</div>
</div>

---

# State of the Art

<v-clicks depth=2>

- Lots of effort to solve problems in the collection and mapping phases 
  - [^profi] Proposes a rectification algorithm to rectify sampled profiles 
  - [^stale] Proposes an algorithm to adapt stale profiles to newer versions programs
  - [^propagation], [^unittesting] only partially address failures in metadata propagation

</v-clicks>

[^profi]: Wenlei He, Julián Mestre, Sergey Pupyrev, Lei Wang, and Hongtao Yu. “Profile inference revisited”.
[^stale]: Amir Ayupov, Maksim Panchenko, and Sergey Pupyrev. “Stale Profile Matching”.
[^propagation]: Youfeng Wu. “Accuracy of Profile Maintenance in Optimizing Compilers”.
[^unittesting]: Profile Information Propagation Unittesting: https://discourse.llvm.org/t/rfc-profile-information-propagation-unittesting/73595

<!--
Lots of effort was put by researchers to smooth out inaccuracies introduced in those phase.
- The first work proposes an algorithm to rectify sampled profile by using flow-conservation rules
- The second work proposes an algorithm to adapt stale profile to newer versions of the program by structurally matching the two versions.
- The third work is a study on the scale of profile propagation errors within optimization pipeline and does not provide a way to asses profile correctness in practice.
- The fourth is a practical step towards the unit-testing of profile information, and proposes a way to understand if profile are dropped by optimization passes
but does not provide a way to understand wrong updates were made by them.
-->

---

# Research Gap

<v-clicks>

- Recently discovered **regressions** could be attributed to profile mishandling
- It **nullifies** solutions aiming at correcting profiles in earlier stages  
- **Non-trivial** solution
  - What it means to correctly propagate profiles?
  - No immediate correlation between before and after code
  - **Interaction** between passes needs to be taken into account
  - **Static checks** on control-flow are not enough!
  - **No dedicated tools** to validate propagation logic

</v-clicks>

<Highlight v-click class="mt-10px mb-10px">

Profile information is **transformed** together with the program, but unlike the program itself, its correctness cannot be directly observed.

</Highlight>

<!--
The problem of profile mishandling remains largely unstudied, even though solving it is of critical importance because:
- Recent insights shows that performance regressions could be attributed to profile mishandling
- Profile mishandling nullifies effort to correct profiles in earlier stages of the life-cycle, due to the profile degrading incrementally as the pipeline is applied
- Not so easy to spot profile propagation errors.
-->

---

# Research Proposal

<Highlight color="#ffdfba" icon="/public/images/question.svg" class="mt-10px mb-10px">

Can profile propagation accuracy be assessed systematically?

</Highlight>

<v-clicks>

- Methodological and practical framework 
- **Black-box** fuzzing strategy
  - Stress test profile propagation logic in order to elicit bugs
  - Off-the-shelf random program generators
- **Grey-box** fuzzing strategy
  - Lightweight **feedback mechanism** to guide coverage
  - Based on code and **profile** mutations
  - Uses programs from already existing **test-suites** as foundation

</v-clicks>

<!--
So the research question I want to answer is: "Can profile propagation accuracy be assessed systematically?"
I want to do so by designing and implementing a methodological and practical framework consisting of
- A black-box fuzzing strategy that uses off-the-shelf random program generators to stress test the profile propagation logic in order to elicit bugs
- A grey-box fuzzing strategy, implementing a Lightweight feedback mechanism with a custom coverage metric for the guidance of  code and profile mutations in order to 
analyze the problem more in depth.
-->

---

# Evaluation of the Proposed Directions

<v-clicks depth=2>

- **LLVM** compiler infrastructure as evaluation target
- Can I with the developed methodologies:
  - Find new **bugs**? If so, how many?
  - **Improve the performance** of generated binaries?
  - **Improve the coverage** of the LLVM codebase? By how much?

</v-clicks>

<div class="flex flex-row justify-center align-center gap-20px mt-70px mb-20px">

  <Card v-show="$clicks>=3" content="Bugs" color="#ffb3ba"/> 
  <Card v-show="$clicks>=4" content="Performance" color="#baffc9" />
  <Card v-show="$clicks>=5" content="Coverage" color="#ffdfba" />

</div>

<!--
I will evaluate the proposed methodologies on the LLVM compiler infrastructure being open source but also used in industry.
The evaluation will be performed by checking if
- I can find new bugs, and if so how many. 
- Fixing those bugs lead to a performance improvement of generated binaries
- Code and profile mutations lead to an improvement in the coverage of optimization passes
-->

---

# Impacts and Benefits

- **Analysis tools** for compiler developers, to asses their PGO implementations
  - Novel tools to study the problem
  - Enhancement of already existing testing-suites
- **Faster** PGO binaries for the user
  - Reducing the costs of deployed applications
- **Energy savings** due to optimal binaries
  - Environmental footprint reduced

<!--
The final impacts of my research consist of
- New means for compiler developers to asses their PGO implementations
- Faster PGO binaries for the users
- Energy savings, thus reduced environmental footprint of large-scale applications
-->

---
layout: center
hideInToc: true
---

# Thanks for the attention!
## Questions?
