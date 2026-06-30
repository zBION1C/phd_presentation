# Slide "Who am I?"

- Very passionate cybersecurity and computer science topics.
- I participated in the cyberchallenge program, reaching the final attack and defense competition.
- I participated at various online CTF challenges together with the TRX team, which is the official sapienza CTF team.
- I contributed to the LLVM compiler infrastructure by submitting reports of previously undiscovered bugs as part of my master thesis work.

# Slide "Performance is Critical"

- As of today, performance is a crucial property of software. For large scale applications even a 1% performance improvement can save millions of dollars annually.

- Profile-Guided Optimization, among other optimization strategies, stands-out for achieving great performance.
- The idea behind it is the tailoring of the optimization process around the workload of the application
- It does so by using a profile for the application, which represents the execution frequencies of program regions
- This eliminates the need to infer such information, which were computed by classical optimization in order to take decisions
- It is perfect for high-load applications, since complete and robust profiles can be collected

# Slide "Profile Life-cycle"

Looking at the PGO workflow, we can isolate three main phases of the profile life-cycle
- Collection, where the profile is collected and persisted as a file for later usage. 
- Mapping, in which the profile file is read and profile metadata is generated and attached to program constructs
- Usage, where the program associated with profile metadata is optimized and the profile is used to guide optimization decisions

# Slide "Practical Obstacles"

There are some practical obstacles when applying PGO. For an ideal PGO application:
The profile must be complete and accurate in each phase of its life-cycle

By **accurate** I mean that the profile has to accurately reflect the actual workload experienced by the binary when executed in the production environment.
Indeed inaccurate profiles could result in bad optimization decisions, leading ultimately to a sub-optimal program generation.

Nevertheless, in practice some sources of inaccuracies are hidden within the PGO workflow itself
- Sampling collection strategies are inaccurate by nature, leading to inaccurate profiles collected
- The dynamic nature of software leads to stale profiles. Before we assumed that the program version on which we collected the profile is the same as the program version on which we want to use the profile.
In practice this rarely happens, and the structural mismatch between the two versions make some part of the profile unusable.
- Finally, optimization pipeline themselves need to carefully update profile metadata after code transformations, so that downstream passes could still benefit from an accurate profile.

# Slide "State of the Art"

Lots of effort was put by researchers to smooth out inaccuracies introduced in those phases.
- The first work proposes an algorithm to rectify sampled profile by using flow-conservation rules
- The second work proposes an algorithm to adapt stale profile to newer versions of the program by structurally matching the two versions.
- The third work is a study on the scale of profile propagation errors within optimization pipelines and does not provide a way to asses profile correctness in practice.
- The fourth is a practical step towards the unit-testing of profile information, and proposes a way to understand if profile are dropped by optimization passes but does not provide a way to understand if wrong updates were made by them.

# Slide "Profile Mishandling Research Gap"

The problem of profile mishandling remains largely unstudied, even though solving it is of critical importance because:
- Recent insights shows that performance regressions could be attributed to profile mishandling
- Profile mishandling nullifies effort to correct profiles in earlier stages of the life-cycle, due to the profile degrading incrementally as the pipeline is applied
- Not so easy to spot profile propagation errors.

# Slide "Research Proposal"

So the research question I want to answer is: "Can profile propagation accuracy be assessed systematically?".

I intend to develop 2 research directions to answer this question:
- The validation of profile propagation logic using random C programs
- The coverage improvement of the tested compiler infrastructure, focusing on PGO specifically 

The resulting scientific contributions would be:
- A methodology to systematically asses profile propagation correctness
- A novel coverage-guided testing strategy targeting profile propagation logic

# Slide "Stress-Test Approach"

The first direction I intend to explore is the usage of random programs. This direction 
- Uses off-the-shelf random program generators to generate structurally complex programs
- These programs are profiled and optimized obtaining the profile propagated by the compiler
- The propagated profile is validated to understand if profile propagation errors were made

The framework would include an automatic-bug triaging mechanism to minimize the manual effort needed to analyze the result.
The effectiveness of this methodology is bounded by the program complexity the generators can achieve. 

# Slide "Coverage-Guided Testing"

That's why I intend to explore a second direction by designing a novel coverage-guided mechanism which:
- Uses program drawn from existing test-suite as a foundation
- These programs would be mutated using code mutation strategies and novel profile mutation strategies
- The compiler would be instrumented in order to implement a lightweight feedback mechanism that instantiates a novel coverage metric to guide mutations
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
