# Discussion leader assignment for Comp 417/517

In this assignment you will lead discussions and be
responsible for the following three elements:

1. Fill out paper review form
2. Short presentation highlighting key moves and
   contributions of the paper 
3. Facilitating discussion. 
 
## Review Form (25%)

Fill out and commit by midnight the night before: 

[Standard Review Form](./review.md)

Note for most of these sections, concision is preferred.
Provide 1-2 sentences if possible, of course go over if you
need.

## Presentation (25%)

The goal of leading the presentation is to format your
understanding of the paper from the perspective of a
critical reader. It is more an exercise to capture that
you've extracted the critical elements from the paper, as
well as a quick refresher to the class. 

The following slides are expected:

1. Title Page
2. Problem and Motivation
3. Why isn't it solved?
4. The key hypothesis of this paper?
5. The methods used to achieve it (how)?
6. The evaluation results
7. Key takeaways

The presentation should take between 5-12 minutes.

See presentation materials in `presentation/`.

## Discussion Leading (%25)

Develop 5-7 questions after reviewing the online discussion
and lead an initial 15 minute small group and a larger 15
minute discussion.

Questions posed during discussion:

- Besides performance, what other advantages does software-based isolation have over hardware-based?
- Do you think there are any benefits to hardware-based isolation over software-based? Are there any cases where the former is superior to the latter?
- Was the method of evaluating performance sufficient? What else could the researchers have done to make a more convincing case?
- What similarities can you see between the isolation protocol and the kernel formats we’ve seen so far (exo, micro, monolithic)? What is unique about it?
- The paper goes into detail about the mechanisms used to enforce isolation. Are there any mechanisms you would change given the chance? Can you think of any better/alternative solutions to a particular part of the problem?
- Do you think the authors achieved their goals? What aspects of the paper could have been better developed? What work is there left to do in this topic?

## Discussion Debrief (%25)

Answer the following questions: 

1. What new insights came out of the discussion?

- One interesting case was brought up that we believe SFI as presented in the
paper would not handle: self-modifying code. For instance, if a (probably
malicious) module obfuscated its code through encryption, the static analysis
used by SFI would likely miss instructions that, when decrypted, are actually
unsafe and could reach out of bounds into the memory of other components of
the application. We also had some interesting points around when
hardware-based isolation is better that SFI, particularly when the frequency
of cross-domain calls is low and time spent crossed over is high.

2. Was there disagreement? Why? 

- Not very much. There was some debate over what could have been added to the
performance evaluation to make for a stronger case for SFI over hardware-based
isolation. Other than that, we mostly just exchanged observations.

3. What was the consensus about the work?

- We seemed to agree with the author's conclusion that SFI is superior to
existing hardware-based methods, apart from a few specific cases. Everyone
seemed interested in where SFI could be taken and how it could be modified to
handle some of the more wacky threats encountered in software engineering.
