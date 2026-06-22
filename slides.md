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

# Compilers

- Compilers are a crucial component of software development 

<div class="flex gap-30px justify-center mt-20px mb-20px">
  <Card v-click content="Correcteness" color="#d8e2dc"/>
  <Card v-click content="Security" color="#ffe5d9"/>
  <Card v-click content="Performance" color="#fec89a"/>
</div>

<v-click>

- Modern Compiler Architecture

</v-click>

<div class="flex justify-center mt-20px mb-20px">

<v-clicks>

<div v-show="$clicks === 5" class="flex justify-center">
    <img src="/public/images/architecture/frontend.svg" width=80%>
</div>
<div v-show="$clicks === 6" class="flex justify-center">
    <img src="/public/images/architecture/middleend.svg" width=80%>
</div>
<div v-show="$clicks === 7" class="flex justify-center">
    <img src="/public/images/architecture/backend.svg" width=80%>
</div>

</v-clicks>

</div>

<!--
Compilers are a critical component of the modern software development pipeline 
As they impact on the correcteness the security and the performance of generated programs

The modern compiler infrastructure is divided into three main stages
- The front-end, which translates the source program into an internal representation easier to analyze and manipulate.
- The middle-end, which optimizes the internal representation by manipulating it.
- The back-end, which translates the internal representation into the target language of choice.
-->

---
hideInToc: true
---

# Performance is Critical

- As of today achieving optimal performance is of critical importance

<div class="flex gap-30px justify-center mt-20px mb-20px">
  <Card v-click content="Warehouse-scale applications" color="#d8e2dc" width="200px" height="70px"/>
  <Card v-click content="Artificial Intelligence" color="#fcd5ce" width="200px" height="70px"/>
</div>

<v-clicks depth=2>

- **Profile-Guided Optimization** is a way to achieve great performance
  - Optimization process **tailored** to the workload of the compiled program
  - **Profile** = Frequencies of reached program regions while executing
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

<Warning class="mt-20px mb-20px">

Profile must be **complete** and **accurate** in each phase of its life-cycle

</Warning>

- Otherwise bad optimization decisions could be taken by the compiler!
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

# State of the Art

- Lots of effort to solve problems in the collection and mapping phases 
  - [^profi] Proposes a rectification algorithm to rectify profiles 
  - [^stale] Proposes an algorithm to adapt stale profiles to newer versions programs
- The problem of profile inaccuracies introduced by optimizations is still open
  - Recent insights shows that passes are mishandling profile metadata
  - **Nullifies** solutions aiming at correcting profiles in earlier stages  
  - **Static checks** on control-flow are not enough!
- [^propagation], [^unittesting] only partially address failures in metadata propagation

[^profi]: Wenlei He, Julián Mestre, Sergey Pupyrev, Lei Wang, and Hongtao Yu. “Profile inference revisited”.
[^stale]: Amir Ayupov, Maksim Panchenko, and Sergey Pupyrev. “Stale Profile Matching”.
[^propagation]: Youfeng Wu. “Accuracy of Profile Maintenance in Optimizing Compilers”.
[^unittesting]: Profile Information Propagation Unittesting: https://discourse.llvm.org/t/rfc-profile-information-propagation-unittesting/73595

<!--
Lots of effort was put by researchers to smooth out inaccuracies introduced in the collection and mapping phase. But, the problem of profile mishandling remains largely unstudied. Solving this problem is of critical importance because:
- Recent insights shows that performance regressions could be attributed to profile mishandling
- Profile mishandling nullifies effort to correct profiles in earlier stages of the life-cycle, due to the profile degrading incrementally as the pipeline is applied
- Not so easy to spot profile propagation errors. For example static checks like flow-conservation rules are not enough.
-->

---

<!-- FIX: Check these two slides for sloppines, maybe some more details can be added -->

# Research Proposal

- Methodological and practical framework
- **Black-box** fuzzing strategy
  - Validates PGO behavior atop off-the-shelf compiler test methods
  - Constitute a way to spot profile propagation bugs in a systematic way
<!-- FIX: Mention the non-trivial feedback mechanism -->
- **Grey-box** fuzzing strategy with code and profile mutations
  - Uses already existing **test-suites** as a foundation
  - Novel **profile mutation** strategy
  - Lightweight feedback mechanism to guide mutations and cover more codebase

---
hideInToc: true
---

# Impacts and Benefits

- Analysis tools for compiler developers, to asses their PGO implementations
  - Novel tools to study the problem
  - Enhancement of already existing testing-suites
- Faster PGO binaries for the user
  - Reducing the costs of deployed applications
- Energy savings due to optimal binaries
  - Environmental footprint reduced

---
layout: center
---

# Thanks for the attention!
## Questions?
