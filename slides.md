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
  - **Profile** = Frequencies of reached program regions while executing
  - Optimization process **tailored** to the workload of the compiled program
  - Eliminates the need to infer such information
  - Perfect for high-load applications

</v-clicks>


<!--
As of today, performance is a crucial property of software, due to the rise
of resource-intensive applications like warehouse-scale or AI applications.
Profile-Guided Optimization is an optimization strategy that promises to achieve greater
performance improvement if compared to classic optimization strategies.
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
  - **Collection**, in which the profile is collected and persisted
  - **Mapping**, in which the binary-to-source mapping is performed
  - **Usage**, in which the profile is used by optimization passes

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

---
hideInToc: true
---

# Collection of Profiles

- The first compilation prepares the binary for profile collection
  - **Instrumentation**: additional code to collect program regions frequencies 
    - **Source-level** or **IR-level**
  - **Sampling**: hardware counters used to monitor flow of executions
  - **Tracing**: trace of executions collected and the used to compute profile
- The output is always a **profile file** encoding information in a specific format

<!-- FIX: Replace with image of profile format -->
<div class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/collection_phase.svg" width="600px">
</div>

---
hideInToc: true
---

# Mapping of Profiles

<!-- FIX: This could be written better -->
- When the source program and the profile file are fed back to the compiler
- The profile is mapped back to program constructs
- The output is a program with **profile metadata** associated with

<div class="flex justify-center align-items mt-20px">
  <img src="/public/images/profile/mapping.svg" width="600px">
</div>

---
hideInToc: true
---

# Usage of Profiles

- The profiled program is ready to be optimized by an **optimization pipeline**
  - Optimization passes will use the profile to guide their decisions
  - Code will be **aggressively** transformed by thousands of passes

---

# Practical Obstacles

- For an ideal PGO application

<Warning class="mt-20px mb-20px">

Profile must be **complete** and **accurate** in each phase of its life-cycle

</Warning>

- In practice, each phase hides sources of inaccuracies
  - **Sampling** strategies are inaccurate by nature
  - Dynamic nature of software leads to **stale profiles**
  - Optimizing passes contain errors in **metadata propagation** logic

---

# State of the Art

- Lots of effort to solve problems in the collection and mapping phases 
  - [^profi] Proposes a rectification algorithm to rectify profiles 
  - [^stale] Proposes an algorithm to adapt stale profiles to newer versions programs
- The problem of profile inaccuracies introduced by optimizations is still open
  - **Nullifies** solutions aiming at correcting profiles in earlier stages  
  - **Static checks** on control-flow are not enough!
- [^propagation], [^unittesting] only partially address failures in metadata propagation

[^profi]: Wenlei He, Julián Mestre, Sergey Pupyrev, Lei Wang, and Hongtao Yu. “Profile inference revisited”.
[^stale]: Amir Ayupov, Maksim Panchenko, and Sergey Pupyrev. “Stale Profile Matching”.
[^propagation]: Youfeng Wu. “Accuracy of Profile Maintenance in Optimizing Compilers”.
[^unittesting]: Profile Information Propagation Unittesting: https://discourse.llvm.org/t/rfc-profile-information-propagation-unittesting/73595

---

# Research Proposal





































