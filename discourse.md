# Slide "Performance is Critical"

As of today, performance is a crucial property of software, due to the rise
of resource-intensive applications like warehouse-scale or AI applications.
Profile-Guided Optimization, among other optimization strategies, stands-out for achieving great performance.
- It consists of tailoring the optimization process around the actual workload of the application
- And it does so by using a profile for the application, which represents what regions of code were touched during execution
- So it eliminates the need to infer such information, which were computed by classical optimization
- It is perfect for high-load applications, since complete profiles can be collected

# Slide "Profile Life-cycle"

Looking at the PGO workflow, we can isolate three main phases of the profile life-cycle
- Collection, where the profile is collected and persisted as a file. There are various collection techniques 
- Mapping, where the profile mapped back to program constructs and is represented as metadata attached to them.
- Usage, where the program associated with profile metadata is optimized and the profile is used to guide optimization decisions

# Slide "Practical Obstacles"

By **accurate** I mean that the profile has to accurately reflect the actual workload experienced by the binary when executed in the production environment.
Bad profiles could result in bad optimization decisions, leading ultimately to a sub-optimal program generation
Nevertheless, in practice some sources of inaccuracies are hidden within the PGO workflow itself
- Sampling collection strategies are inaccurate by nature, leading to inaccurate profiles collected
- The dynamic nature of software leads to stale profiles. In fact before we assumed that the program version on which we collected the  profile is the same as the program version on which we want to use the profile.
In practice this rarely happens, and the structural mismatch between the two versions make some part of the program unusable.
- Finally, optimization pipeline themselves need to carefully update profile metadata after code transformations, so that later passes could still benefit from an accurate profile.

# Slide "State of the Art"

Lots of effort was put by researchers to smooth out inaccuracies introduced in those phase.
- The first work proposes an algorithm to rectify sampled profile by using flow-conservation rules
- The second work proposes an algorithm to adapt stale profile to newer versions of the program by structurally matching the two versions.
- The third work is a study on the scale of profile propagation errors within optimization pipeline and does not provide a way to asses profile correctness in practice.
- The fourth is a practical step towards the unit-testing of profile information, and proposes a way to understand if profile are dropped by optimization passes
but does not provide a way to understand wrong updates were made by them.

# Slide "Profile Mishandling Research Gap"

The problem of profile mishandling remains largely unstudied, even though solving it is of critical importance because:
- Recent insights shows that performance regressions could be attributed to profile mishandling
- Profile mishandling nullifies effort to correct profiles in earlier stages of the life-cycle, due to the profile degrading incrementally as the pipeline is applied
- Not so easy to spot profile propagation errors.

# Slide "Research Proposal"

So the research question I want to answer is: "Can profile propagation accuracy be assessed systematically?". I intend to answer this question by developing a methodological and practical framework that brings two contributions:
- The validation of profile propagation logic using random C programs
- The coverage improvement of the tested compiler infrastructure, focusing on PGO specifically 

# Slide "Stress-Test approach"

The first direction I intend to explore is the usage of random programs.
By using off-the-shelf random program generators and exploiting the complexity of generated programs I intend to uncover superficial profile propagation errors as a starting point.
The framework would include an automatic-bug triaging mechanism to minimize the manual effort needed to analyze the result.

# Slide "Coverage-Guided Testing"

The second direction I intend to explore is the coverage improvement of profile propagation logic. I intend to do so by designing a novel coverage-guided mechanism which uses program drawn from 
existing test-suite as a foundation, and consist of:
- Classical code mutations and a novel profile mutator to explore untested regions of the compiler 
- A lightweight feedback mechanism that instantiates a coverage metric to guide mutations

# Slide "Evaluation of the Proposed Directions"

I will evaluate the proposed methodologies on the LLVM compiler infrastructure being open source but also used in industry.
The evaluation will be performed by checking if
- I can find new bugs, and if so how many. 
- Fixing those bugs lead to a performance improvement of generated binaries
- Code and profile mutations lead to an improvement in the coverage of optimization passes

# Slide "Impacts and Benefits"

The final impacts of my research consist of
- New means for compiler developers to asses their PGO implementations
- Faster PGO binaries for the users
- Energy savings, thus reduced environmental footprint of large-scale applications
