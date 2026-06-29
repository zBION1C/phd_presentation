# Slide "Performance is Critical"

As of today, performance is a crucial property of software due to the rise of resource-intensive applications such as warehouse-scale and AI applications.
Profile-Guided Optimization, among other optimization strategies, stands out for achieving great performance.
- It consists of tailoring the optimization process around the actual workload of the application.
- It does so by using a profile for the application, which represents which regions of code were executed during runtime.
- This eliminates the need to infer such information, as was done by classical optimization techniques.
- It is particularly well suited for high-load applications, since complete profiles can be collected.

# Slide "Profile Life-cycle"

Looking at the PGO workflow, we can isolate three main phases of the profile life-cycle:
- Collection, where the profile is collected and persisted as a file. There are various collection techniques.
- Mapping, where the profile is mapped back to program constructs and represented as metadata attached to them.
- Usage, where the program associated with the profile metadata is optimized, and the profile is used to guide optimization decisions.

# Slide "Practical Obstacles"

By **accurate**, I mean that the profile has to accurately reflect the actual workload experienced by the binary when executed in the production environment.
Bad profiles could result in poor optimization decisions, ultimately leading to sub-optimal code generation.
Nevertheless, in practice, some sources of inaccuracy are hidden within the PGO workflow itself.
- Sampling-based collection strategies are inherently inaccurate, leading to inaccurate profiles being collected.
- The dynamic nature of software leads to stale profiles. Before, we assumed that the program version on which we collected the profile is the same as the program version on which we want to use it.
  In practice, this rarely happens, and the structural mismatch between the two versions makes some parts of the program unusable.
- Finally, optimization pipelines themselves need to carefully update profile metadata after code transformations so that later passes can still benefit from an accurate profile.

# Slide "State of the Art"

A significant amount of effort has been devoted by researchers to smoothing out the inaccuracies introduced during these phases.
- The first work proposes an algorithm to rectify sampled profiles by using flow-conservation rules.
- The second work proposes an algorithm to adapt stale profiles to newer versions of a program by structurally matching the two versions.
- The third work is a study on the scale of profile propagation errors within optimization pipelines and does not provide a way to assess profile correctness in practice.
- The fourth work is a practical step toward the unit testing of profile information and proposes a way to determine whether profiles are dropped by optimization passes, but it does not provide a way to determine whether incorrect updates were made by them.

# Slide "Profile Mishandling Research Gap"

The problem of profile mishandling remains largely unstudied, even though solving it is of critical importance because:
- Recent insights show that performance regressions can be attributed to profile mishandling.
- Profile mishandling nullifies the effort to correct profiles in earlier stages of the life-cycle, as the profile degrades incrementally throughout the optimization pipeline.
- Profile propagation errors are not easy to spot.

# Slide "Research Proposal"

The research question I want to answer is: **"Can profile propagation accuracy be assessed systematically?"**
I intend to answer this question by developing a methodological and practical framework that brings two contributions:
- The validation of profile propagation logic using random C programs.
- Improved coverage of the tested compiler infrastructure, with a specific focus on PGO.

# Slide "Stress-Test Approach"

The first direction I intend to explore is the use of random programs.
By using off-the-shelf random program generators and exploiting the complexity of the generated programs, I intend to uncover superficial profile propagation errors as a starting point.
The framework would include an automatic bug-triaging mechanism to minimize the manual effort required to analyze the results.
The effectiveness of this methodology is bounded by the complexity of the programs the generators can achieve.

# Slide "Coverage-Guided Testing"

That is why I intend to explore a second direction by designing a novel coverage-guided mechanism, which uses programs drawn from existing test suites as a foundation and consists of:
- Classical code mutations and a novel profile mutator to explore untested regions of the compiler.
- A lightweight feedback mechanism that instantiates a coverage metric to guide mutations.

This second direction should improve the coverage of the tested compiler's profile propagation logic.

# Slide "Evaluation of the Proposed Directions"

I will evaluate the proposed methodologies on the LLVM compiler infrastructure, which is open source but also widely used in industry.
The evaluation will be performed by checking whether:
- I can find new bugs, and if so, how many.
- Fixing those bugs leads to performance improvements in the generated binaries.
- Code and profile mutations lead to improved coverage of optimization passes.

# Slide "Impacts and Benefits"

The final impacts of my research consist of:
- New means for compiler developers to assess their PGO implementations.
- Faster PGO-generated binaries for users.
- Energy savings, resulting in a reduced environmental footprint for large-scale applications.
