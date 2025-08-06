---
layout: post
title: "AIME Adaptable Multi Agent Systems"
author: "Tim"
tags: Agent Improvement AIME ByteDance
excerpt_separator: <!--more-->
---
A practical implementation of Multi-Agent Systems (MAS)

<!--more-->

# The Problem with Current Multi-Agent Systems

Most teams building multi-agent systems today follow a similar playbook: create a planner that breaks down tasks, assign those tasks to specialised agents, and hope everything works out. This **plan-and-execute framework** has become the default approach, but it's not entirely suited to all real-world applications.

[The AIME research paper](https://arxiv.org/pdf/2507.11988) from ByteDance identifies why these systems fail and outlines an alternative framework

## Three Critical Failures of Traditional Multi-Agent Systems

### 1. Rigid Plan Execution - When Plans Meet Reality

Traditional systems generate a plan once and stick to it, no matter what happens during execution. The planner sits idle while executor agents work, creating a dangerous blind spot. When an executor encounters an unexpected issue or discovers new information that should change the strategy, there's no mechanism for real-time adaptation.

**Real-world example**: You're building a travel planning agent. The planner decides to book flights first, then hotels. But the flight booking agent discovers that all direct flights are cancelled due to weather. In a traditional system, this critical information doesn't propagate back to update the plan - you're stuck following a strategy that no longer makes sense.

### 2. Static Agent Capabilities - The Fixed Role Trap

Most frameworks assign agents predefined roles with fixed toolsets. Need a "code reviewer" agent? Let's hope you made the right agent for that already. When requirements emerge that demand different skills or tools, these systems hit a wall.

**The reality**: Complex tasks don't fit neatly into predefined boxes. You might need an agent that can read code, understand business requirements, and interact with APIs all in the same task flow.

### 3. Inefficient Communication - The Context Loss Problem

Task handoffs between agents are problematic - context can get missed. Agent A completes a subtask and passes results to Agent B, but fine details, intermediate findings, and context get lost in translation. Without a central state manager, agents operate with incomplete views of overall progress, leading to redundant work and coordination failures. (as also pointed out in the [Self Evolving Agents Survey paper](https://timajwilliams.com/2025-08-04/agent-self-improvement))

## AIME's Solution: Dynamic Everything

AIME replaces a brittle plan-and-execute model with four core components that work together to create adaptive multi-agent systems:

### The Dynamic Planner: Continuous Strategic Thinking

Instead of planning once and hoping for the best, AIME's Dynamic Planner operates in a continuous loop. At each step, it:

- Maintains a global view of the task hierarchy. (This is also a **useful pattern for users of Cursor/Claude Code!**)
- Processes real-time feedback from executing agents  
- Makes both strategic adjustments (updating the overall plan) and tactical decisions (what to do next)

**Implementation insight**: The planner manages two outputs simultaneously - `Lt+1` (updated global task list) and `gt+1` (immediate next action). This dual-level reasoning enables easy adaptation when execution reality diverges from initial assumptions.

### The Actor Factory: On-Demand Specialisation  

This is where AIME gets really clever. Instead of maintaining a fixed roster of specialised agents, the Actor Factory assembles purpose-built agents on-demand for each subtask.

**How it works**:
1. Receives a subtask specification
2. Analyses requirements to determine needed capabilities
3. Selects appropriate tool bundles (not individual tools - bundles ensure functional completeness)
4. Constructs a customised system prompt from modular components:
   - **Persona**: Expert role aligned with the task
   - **Tools**: Precise toolkit without cognitive overhead of irrelevant options
   - **Knowledge**: Dynamically retrieved relevant information
   - **Environment**: Context like time constraints, permissions, system details
   - **Format**: Required output structure for automated processing

You get agents with exactly the right capabilities for each task, without the complexity of managing pre-configured agent types.

### Dynamic Actors: Autonomous Execution with Proactive Communication

Each Dynamic Actor follows the ReAct pattern (Reasoning → Action → Observation) but with an addition: proactive progress reporting. Actors autonomously decide when to communicate status updates using the `Update_Progress(status, message)` tool.

**Key benefit**: The Dynamic Planner gets near real-time visibility into execution without interrupting agent workflows. No more waiting until task completion to discover problems.

### Progress Management Module: Single Source of Truth

This central state manager maintains a hierarchical view of all tasks using a simple but powerful data structure. A markdown task list that's both human-readable and machine-parsable:

```markdown
- Objective 1: Perform Initial Research
  - [x] Sub-objective 1.1: Research top attractions  
  - [x] Sub-objective 1.2: Investigate transportation options
- Objective 2: Finalise Itinerary and Budget
  - [ ] Sub-objective 2.1: Research hotel accommodations
  - [ ] Sub-objective 2.2: Calculate total estimated budget
```

**Implementation advantage**: Every component operates on the same shared understanding of progress, eliminating coordination failures and context loss.

## The AIME Workflow: How It All Fits Together

AIME operates through a six-step iterative cycle:

![Aime Planner](/assets/images/aime-planner.png)

1. **Task Decomposition**: Dynamic Planner breaks down user request into structured subtasks
2. **(Sub) Task Dispatch**: Planner identifies next executable subtask and sends specification to Actor Factory
3. **Actor Instantiation**: Factory assembles specialised agent with tailored capabilities
4. **ReAct Execution**: Actor autonomously executes subtask using reasoning-action loops
5. **Progress Updates**: Actor proactively reports status changes to maintain global awareness
6. **Evaluation and Iteration**: Upon completion, planner evaluates outcome and adapts plan for next iteration

This cycle continues until the top-level objective is achieved, with the system dynamically adapting at every step based on real execution feedback.

## Proven Results: Outperforming Specialised Systems

AIME was tested against state-of-the-art specialised agents in their own domains:

- **GAIA (General Reasoning)**: 77.6% success rate vs. 71.5% for best baseline
- **SWE-bench Verified (Software Engineering)**: 66.4% resolution rate vs. 65.8% for OpenHands  
- **WebVoyager (Web Navigation)**: 92.3% success rate vs. 89.1% for Browser use

AIME achieves these results as a general framework, while the baselines are highly specialised systems optimised for their specific domains.

### Start With the Progress Management Module

The central progress list is the foundation. Implement as your shared state layer. Use a format that's both human-readable for debugging and machine-parsable for automated processing. They use markdown, which I have also been using in Cursor

### Design Tool Bundles, Not Individual Tools

Organise your tools into functional bundles (WebSearch bundle, FileSystem bundle, Database bundle). This approach ensures actors get complete capabilities for their domain while reducing the cognitive load of tool selection.

### Implement Proactive Progress Reporting

Give agents the ability to communicate status updates autonomously. This isn't just logging - it's about enabling the planner to make informed decisions based on real execution feedback.

### Plan for Dynamic Prompt Assembly

Build modular prompt components that can be composed on demand. Actor Factory should be able to mix and match personas, knowledge, tool bundles, and output formats to create purpose-built agents.

## Key Observations

**Reduced System Brittleness**: Traditional multi-agent systems break when reality doesn't match the initial plan. AIME systems adapt and recover.

**Better Resource Utilisation**: Agents are created with exactly the capabilities they need, reducing computational overhead and improving focus.

**Easier Debugging**: The central progress list provides clear visibility into what's happening across your entire system.

**Improved Extensibility**: Adding new capabilities means creating new tool bundles or knowledge modules, not redesigning entire agent architectures.

**No Verification steps**: The paper looks to address the majority of the failure patterns identified in [MAST](https://timajwilliams.com/2025-08-05/agent-failure) however they do not add any formal verification steps to the flow beyond the comparison between central progress list and task outcome. 
