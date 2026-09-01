
# CSE Interview Mentor — Evaluation Log

## Overview

CSE Interview Mentor is an adaptive technical interview agent designed
to help computer science students practise technical interviews.

The agent is intended to:

- Ask one technical question at a time
- Evaluate candidate responses
- Assess correctness, completeness, clarity, and conceptual depth
- Identify strengths, gaps, and misconceptions
- Adapt interview difficulty according to candidate performance
- Provide concise and constructive feedback
- Generate a useful final assessment

This document records the testing, failures, improvements, and validation
performed across multiple agent versions.

---

# Version 1 — Initial Deployment

## Objective

Create an adaptive technical interviewer capable of conducting a
technical interview for computer science students.

## Initial Design

The initial version used:

- A dedicated technical interviewer role
- Three workflow stages:
  1. Start interview
  2. Evaluate answer
  3. Adapt next question
- No external tools
- Structured output requirements
- Adaptive difficulty based on candidate performance

## Initial Test Strategy

The agent was tested using progressively more difficult Data Structures
and Algorithms questions.

The testing covered:

- Arrays
- Two Sum
- Three Sum
- Four Sum
- Streaming constraints
- Binary Trees
- Lowest Common Ancestor
- Complexity analysis
- Edge-case reasoning

---

## V1 Test Results

### Test 1 — Interview Initialization

The agent was asked:

> I want to practice Data Structures and Algorithms for a technical
> interview. Start with medium difficulty.

### Result

PASS — the agent correctly identified:

- Topic: Data Structures and Algorithms
- Difficulty: Medium

### Observation

The agent immediately started a technical interview.

However, it initially presented **three questions in a single turn** rather
than strictly asking one question at a time.

### Issue Identified

The intended behavior was:

> Ask one question → wait for answer → evaluate → ask next question

The initial response partially violated this requirement by presenting
multiple questions together.

---

## Test 2 — Strong Technical Answer

The candidate provided a correct Hash Map solution for Two Sum.

### Result

PASS

The agent correctly evaluated:

- Correctness
- Clarity
- Time complexity
- Space complexity

The agent also identified a missing part of the original multi-part question
and generated a follow-up.

### Observation

The underlying technical evaluation was strong.

---

## Test 3 — Difficulty Adaptation

The candidate gave a correct two-pointer solution for sorted Two Sum.

### Result

PASS

The agent:

- Recognized the solution as correct
- Provided useful feedback
- Increased difficulty from Medium to Medium-Hard
- Asked a single follow-up question

### Observation

Adaptive difficulty was working as intended.

---

## Test 4 — Incorrect Candidate Answer

The candidate intentionally provided an incorrect complexity analysis
for a brute-force Three Sum solution.

### Result

PASS

The agent correctly identified:

- The incorrect O(n) complexity claim
- The correct O(n^3) complexity
- The missing duplicate-handling requirement
- The distinction between output space and auxiliary space

### Observation

The agent did not blindly praise an incorrect answer and correctly
identified multiple technical issues.

---

## Test 5 — Conceptual Progression

The agent progressed through:

Two Sum
→ Three Sum
→ Four Sum
→ Streaming constraints

### Result

PASS

The agent correctly increased difficulty and connected related algorithmic
patterns.

---

## Test 6 — Topic Transition

The interview moved from array problems to Binary Trees and then to
Lowest Common Ancestor.

### Result

PASS

The agent successfully changed topics while maintaining the interview
context and evaluating the candidate's technical answer.

---

## V1 Major Failure

### Test

A complex question combined:

- Binary Search Tree LCA
- General binary-tree LCA
- Missing-node handling
- Complexity comparison

### Result

FAIL

The deployed agent returned:

> I couldn't produce a response for that request.

### Impact

The agent failed to evaluate a valid technical response even though the
underlying concepts were within the scope of the interview.

### Identified Weakness

The agent was not sufficiently robust when a candidate response contained
multiple related constraints and edge cases.

---

# Version 2 — Multi-Part and Edge-Case Robustness

## Objective

Improve the agent's ability to handle:

- Multi-part interview questions
- Edge cases
- Changed problem assumptions
- Incomplete candidate responses
- Complex technical constraints

## Changes

Version 2 introduced stronger instructions for:

- Evaluating every explicit part of a question
- Handling multiple constraints independently
- Continuing even when a candidate answer is incomplete
- Explicitly reasoning about changed assumptions
- Handling edge cases
- Avoiding silent failures

The evaluation workflow was also strengthened to explicitly consider:

- Correctness
- Completeness
- Clarity
- Conceptual depth
- Complexity
- Assumptions
- Edge cases

---

# V2 Validation

## Test 1 — Binary Tree LCA

The agent was retested with a standard Lowest Common Ancestor question.

### Result

PASS

The agent correctly evaluated:

- Null base case
- Target-node base case
- Both-subtrees-found case
- Single-subtree case
- Ancestor relationship
- O(n) time complexity
- O(h) recursive space

### Observation

The previous LCA-related failure from V1 was not reproduced on this
standard LCA test.

---

## Test 2 — Binary Tree Diameter

The agent successfully evaluated a recursive diameter solution.

### Result

PASS

The agent correctly identified:

- Correct diameter formulation
- O(n) time
- O(h) space
- Missing base case
- Need to check diameter at every node

### Observation

The agent demonstrated useful gap detection rather than simply marking
the answer as perfect.

---

## Test 3 — Diameter Path Representation

The problem was extended from finding the diameter length to returning
the actual diameter path.

### Result

PASS

The agent correctly recognized:

- Need to track the deepest path
- Path construction through a node
- Reversal of the left path
- Global best diameter path
- Base case for null nodes
- Additional path storage requirements

### Observation

The agent successfully evaluated a more complex variation of the
original problem.

---

## Test 4 — Maximum Path Sum

The candidate provided a mostly correct solution but did not explicitly
discuss the all-negative tree case.

### Result

PARTIAL / FAILURE IN RESPONSE COMPLETION

The agent began generating:

> Gap to address:

but did not complete the evaluation.

### Important Observation

A separate follow-up test was later performed asking the agent directly
about the all-negative maximum-path-sum edge case.

The agent correctly explained:

- A path must contain at least one node
- The answer for an all-negative tree is the least-negative node
- Initializing max_sum to 0 is incorrect
- Negative infinity or a valid node value should be used for initialization

### Diagnosis

The underlying technical reasoning was present, but the agent failed to
complete the evaluation response in the original complex interaction.

This indicated a **response-completion problem**, not necessarily a lack
of technical knowledge.

---

# Version 3 — Evaluation Completion and Structured Follow-Ups

## Objective

Improve reliability of evaluation responses so that the agent does not
leave sections incomplete.

## Changes

Version 3 introduced explicit evaluation-completion requirements.

Every completed evaluation should contain:

1. What the candidate did well
2. Gaps to address
3. Overall assessment
4. Exactly one follow-up question

Additional requirements included:

- Never output a section heading without content
- Explicitly state when no significant gaps exist
- Identify subtle edge-case omissions
- Always provide one follow-up question unless the interview is ended
- Continue evaluating partial answers rather than failing silently

---

# V3 Validation

## Test — Binary Tree Serialization and Deserialization

The candidate provided a complete explanation using:

- Preorder traversal
- Explicit null markers
- Recursive deserialization
- Empty-tree handling
- Structure preservation
- O(n) time complexity
- O(h) recursive space

### Result

FAIL — response generation issue

The candidate's answer was successfully submitted, but the deployed
agent returned only:

> `**`

The Smartly performance panel identified:

> Major gap: Output format

### Impact

The agent did not produce the expected evaluation sections or follow-up
question.

### Important Observation

The failure occurred despite the candidate response being technically
sound.

This suggests the issue was not primarily the interviewer's knowledge
of tree serialization, but the reliability of its evaluation-response
generation/output structure.

---

# Current Findings

## Strengths

The agent has demonstrated strong performance in:

- Technical correctness evaluation
- Complexity analysis
- Identifying conceptual gaps
- Difficulty adaptation
- DSA pattern recognition
- Topic transitions
- Edge-case awareness
- Constructive interview feedback

## Weaknesses Identified

### 1. Initial multi-question behavior

The first V1 interaction contained multiple questions in one turn,
despite the desired one-question-at-a-time behavior.

### 2. Complex multi-part response handling

A complex LCA/BST question caused a complete response failure in V1.

### 3. Evaluation response completion

A maximum-path-sum evaluation stopped after generating:

> Gap to address:

without completing the evaluation.

### 4. Output-format robustness

A later V3 test returned:

> `**`

instead of a complete evaluation.

Smartly identified output format as a major gap.

---

# Current Hypothesis

The agent's underlying technical reasoning appears stronger than its
reliability in producing a complete structured evaluation response.

The next iteration should therefore focus on:

- Stable output formatting
- Complete response generation
- Required-field validation
- Consistent evaluation sections
- Reliable follow-up generation
- Testing the same failure cases after each change

---

# Planned Next Iteration

## Goals

Before considering the agent stable, retest:

1. Complex multi-part technical questions
2. Edge-case-heavy problems
3. Long candidate answers
4. Short candidate answers
5. Incomplete candidate answers
6. Incorrect answers
7. Strong answers
8. Topic transitions

## Success Criteria

A successful version should:

- Always produce a complete evaluation
- Never leave a heading empty
- Never return an empty or malformed evaluation
- Ask exactly one follow-up question
- Correctly identify candidate strengths
- Correctly identify candidate gaps
- Preserve appropriate difficulty adaptation
- Maintain technical accuracy

---

## V4 Planned Improvement

### Diagnosis

V3 testing identified an output-generation failure. A technically valid
candidate response resulted in incomplete output (`**`), and the
Smartly performance panel identified output format as the major gap.

### Hypothesis

The evaluation instructions and structured output schema were not
sufficiently aligned.

### V4 Change

- Simplified the output schema
- Aligned the system prompt directly with the schema
- Required valid JSON output
- Required every evaluation field to be populated
- Preserved multi-part question and edge-case handling

### Validation

Pending deployment and retesting.

## V4 Validation — Serialization Test

### Result
PASS

### Observed behavior

The agent successfully generated a complete structured evaluation
containing strengths, gaps, feedback, assessment, next question, and
next difficulty.

The previous V3 behavior of returning only `**` was not reproduced.

### Additional issue identified

The agent listed "did not provide code or pseudocode" as a gap even
though the question only requested an approach, complexity analysis,
and edge cases.

This suggests the evaluator may sometimes penalize candidates for
requirements that were not explicitly requested.

### Next validation

Retest the previously failing complex LCA/BST scenario.

---

# Evaluation Philosophy

The purpose of this evaluation process is not to make the agent appear
perfect.

Instead, the goal is to:

1. Establish a baseline
2. Find realistic failure cases
3. Identify why the failure occurred
4. Modify the agent
5. Re-test the same failure
6. Verify whether the change actually helped

The development cycle is:

**Build → Test → Observe → Identify Failure → Improve → Retest**

---

# Portfolio Evidence

Important evidence generated during development includes:

- Version history
- V1 failure on complex LCA/BST reasoning
- V2 improvement in multi-part technical evaluation
- Detection of the all-negative maximum-path-sum edge case
- V3 output-generation failure
- Smartly performance observations
- Before/after behavior comparisons

These observations are maintained as engineering evidence for the
GSSoC 2026 Agents for India contribution.
