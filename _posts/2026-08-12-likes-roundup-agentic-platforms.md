---
layout: post
title: "July Likes Roundup: Agentic Platform Programming"
author: "Tim"
tags: Agents Platforms Harness Evals Roundup
excerpt_separator: <!--more-->
---
What 1,100 likes say about where agent engineering actually is.
<!--more-->

I use my Twitter likes as bookmarks. Everything I think I might want to read again gets a like, and a little pipeline of mine ingests them, fetches the linked articles, and summarises them. This means that every so often I can interrogate my own taste.

This month I pulled the last four weeks of likes, around 180 tweets, and asked: what did I  think was worth keeping? Below is what came out, sifted into themes. 

Despite the excitement peaks at each newly released model, the Twitter chatter between releases has mostly been about one layer out.

## The harness is a business decision

The most bookmarked thing in the window was [Databricks benchmarking coding agents against their own multi-million-line codebase](https://www.databricks.com/blog/benchmarking-coding-agents-databricks-multi-million-line-codebase) (via [@alighodsi](https://x.com/alighodsi/status/2074996561306955958)). They built tasks from their own merged PRs, graded by tests not vibes, and found that the same model at the same effort level can differ by more than 2x in cost per task depending on the harness it runs in. The lean harness (pi, of which I am a huge fan) fed the model about 3x less context per turn at equal quality.

Two other findings from the same post deserve more attention than they got. GLM 5.2 statistically tied Opus 4.8 on quality at $1.28 vs $1.94 per task. And Sonnet 5, although cheaper per token, was more expensive per task because it read more and worked longer to get there. Token price is a poor proxy for task cost, which is the sort of thing everyone nods at and then ignores when choosing models by the price list.

Composio piled on with [the same model through three harnesses on 28 identical tasks](https://x.com/composio/status/2082452269522378858): similar success rates, up to 30x difference in token cost. Vendor-reported, and they sell agent tooling, so take that with a pinch of salt..  but it points the same way as Databricks.

Also filed under harnesses: [OpenAI's server-side compaction is quietly why Codex keeps going on long tasks](https://x.com/kunchenguid/status/2079824709240455241), and other harnesses don't inherit it; and a [deep dive on pi's minimalism](https://x.com/ShenSeanChen/status/2081118331097284801) - four tools, no MCP, a 792-line agent loop, on the grounds that one MCP server can mean tens of thousands of tokens of context for a tool you use a tenth of the time.

## Your repo is the training data

Boris Cherny, who created Claude Code, posted [the most-quoted thread of my window](https://x.com/bcherny/status/2077460395279692197). The gist: the highest-leverage engineering work right now is encoding domain knowledge as infrastructure - CLAUDE.md files, skills, lint rules, CI checks - so that agents, and by extension new hires and non-specialists, can contribute on day one. His line that stuck with me: when a designer's PR gets rejected for not following architectural patterns, that's a failure of automation, not of the designer.

Practical corollaries I liked:

- Put durable guidance in AGENTS.md, which is always loaded, rather than in skills, which the model only loads when it judges them relevant ([@kaushikgopal](https://x.com/kaushikgopal/status/2079391464631742475)).
- [Review a small traceable slice of agent output properly, hand the issues back, then ask the agent to derive general rules from your review](https://x.com/adamwarski/status/2081772336223514835) (@adamwarski). Review once, encode forever.
- Skills should graduate: start flexible, then [lower the stable parts into deterministic code](https://x.com/rseroter/status/2077977105778831582) once you know the workflow's real shape.

The natural follow-on, someone shipping [their full AGENTS.md distilled from ~60B tokens of agent usage](https://x.com/MarcosHernanz/status/2083954734487212511), suggests this genre is maturing from personal superstition into shared, battle-tested artifact.

## Trust is expensive 

The landmark moment of the window: [OpenAI audited SWE-Bench Pro and found roughly 30% of its 731 public tasks broken](https://openai.com/index/separating-signal-from-noise-coding-evaluations/), formally retracting their own earlier recommendation. A few points' difference on that leaderboard was always noise. The lesson generalises uncomfortably: if the widely-trusted public benchmark is this shabby, what does your internal "we tried it on a few tasks and it seemed good" amount to?

The constructive answer doing the rounds is that your own merged PRs are the benchmark no model has trained on. Databricks did exactly this. And if full agentic evals are too slow and expensive, [PACE](https://arxiv.org/abs/2607.02032) predicts agentic benchmark scores from a small set of cheap atomic proxy tasks - MAE under 4% at less than 1% of the cost of running the real thing.

Two more eval-flavoured keepsakes. [Basis and Braintrust are proposing an open standard for scoring long-running agents on process, not just outcome](https://x.com/pitdesi/status/2082525345425784973) - a right answer reached by an unreliable path is not something you want in production. And a secondhand account of [an Anthropic engineer's advice on eval loops](https://x.com/mikenevermiss/status/2084910738783494474) (Aug 5, hearsay but plausible): if your agent passes 80%+ of your evals, your eval is too easy; aim for ~50% and feed every failure transcript back into the set.

The counter, which I appreciated for its honesty: [one team abandoned their vibecoded internal tool and went back to Linear](https://x.com/thericebowlgirl/status/2081575149334335552), because maintaining it ate the bandwidth it saved. Not everything _should_ exist.

## Subagents are for context, not cosplay

The best framing of the month came via [Martin Fowler amplifying a simple point](https://x.com/martinfowler/status/2082092824170840270): what justifies subagents isn't time saved or parallelism, it's protecting the orchestrator's context. Once you see it that way, a lot of multi-agent theatre looks like hiring five people to stand between you and your desk.

Around the same week the vocabulary visibly shifted from "loops" to "graphs" - deterministic pipelines where agents route on structured data ([@Nek__12](https://x.com/Nek__12/status/2078335458606489978)). The grown-up caveat came from [@badlogicgames](https://x.com/badlogicgames/status/2078774545108926791): every step in a stochastic pipeline succeeds with probability less than one, so compounding error will eat your system unless you design explicit control schemes. FSMs and actor DAGs are the useful mental models here, not vibes.

A concrete instance I liked: [AutoTrainess](https://x.com/VukRosic99/status/2074880437504368962)encodes human training expertise as "Agent-Computer Interfaces" - checklists, constraints, logging - instead of giving the agent a raw shell. Their logging-and-plan interface alone cut closed-loop handoff collapses from 86 to 30, and the weaker the model, the more the scaffolding matters. Plus ça change.

## The platform layer is real now

Perplexity published [how SPACE, the sandbox platform behind Computer, actually works](https://research.perplexity.ai/articles/making-space-secure-and-efficient-runtimes-for-long-running-agents)  (worth reading properly). Per-sandbox VMs, btrfs copy-on-write clones, frequent disk snapshots plus less frequent full VM checkpoints shipped to object storage. Median sandbox creation went from 185ms to 60ms, p90 from 447ms to 89ms, on 100% of Computer's production traffic. Credentials live outside the sandbox entirely and are injected at the network layer.

Meanwhile [Kimi's paper apparently showed agents crashing container-isolated hosts via kernel panics](https://x.com/rauchg/status/2081842439304995169) - microVMs are the boundary that holds - and [@felipehuici](https://x.com/felipehuici/status/2083598787159691708) delivered my favourite sentence of the month: a great many warm pools are Kubernetes clusters kept warm to hide how slow the control plane is, a workaround for a workaround.

The strategic question, posed by [@jhleath](https://x.com/jhleath/status/2082470484911022244): does someone deliver full-VM semantics at a fraction of the cost so agents can keep assuming Linux, or does everything get rewritten for the edge stack? He notes, correctly, that Linux isn't getting smaller.

## Security grew up a bit

Two data points in the same window. [Indirect prompt injection can reportedly be driven to roughly zero on unseen attacks](https://x.com/bcherny/status/2085860677990883454) by stacking defences - model training, input probes, an intent classifier - which is now shipping as default behaviour in Claude Code. Vendor-reported, but from the people carrying the liability. And [1Password integrated with Claude](https://x.com/1Password/status/2077743858239094854) so agents can use stored credentials without passwords ever reaching the model or Anthropic's systems.

The inversion also arrived: [@arafatkatze](https://x.com/arafatkatze/status/2083236726676615535) showed you can [distill a harness's prompts by asking the agent to MITM-proxy its own requests](https://x.com/swyx/status/2083073422410821846), which it will cheerfully do. Assume anything in your harness is extractable, because it is.

## The frontier is a mixture, and it's left the codebase

On deep research, [the top five systems on the DRACO benchmark are all fusions of models](https://x.com/iamtrask/status/2079955998890930503), with the best single frontier-lab model sixth - and the result held when re-run independently a month later. Young benchmark, but consistent with Databricks' Pareto frontier spanning OpenAI, Anthropic and open weights. Routing between models is now a config file, not a project.

And the pattern escaped software entirely. A 190-room hotel's entire MEP coordination [modelled by an agent reading blueprint photos through MCP](https://x.com/Solvaix/status/2082190359539712344). A contractor in Shenzhen [pricing a ¥12.5M hospital tender solo in an afternoon](https://x.com/Argona0x/status/2082860603866284426). [Agent-native views over UniProt, PDB and ChEMBL](https://x.com/james_y_zou/status/2077038779202953651) making molecular deep research 10x faster. The recipe is identical each time: re-index the domain's data into something an agent can navigate, keep a human confirming the outputs, and the economics flip.

## And what happens to the engineers?

Buried in the same window was the tweet I've thought about most since, [via @rseroter](https://x.com/rseroter/status/2079997655590068477):

> A growth review should not ask how much of the engineer's work was generated. It should ask what this engineer can now decide that they could not decide six months ago, and what evidence shows they can carry that judgment into the next problem.

This cuts through most of the "will AI replace engineers" discourse, which is obsessed with the wrong unit of measurement. Lines of code generated is a vanity metric, like measuring a chef by the weight of ingredients chopped. What compounds is judgement - the ability to look at an agent's output and know whether the soufflé is about to collapse, and why. The uncomfortable flip side: if an engineer's growth plan for the next six months is "get better at prompting", that is not a growth plan. If everything above is true - harnesses decide cost, repos carry the knowledge, verification is the bottleneck - then the scarce skill is deciding what to build, what to trust, and what to check. Train that, review that, and hire for that.

## What I take from all this

If I compress the month into homework:

1. Benchmark harnesses on tasks built from our own merged PRs. Cost per passing task, not per token.
2. respect AGENTS.md more. It's production infrastructure.
3. Every agent failure becomes a graded eval case. If the pass rate climbs past 80%, make the eval harder.
4. Before delegating a workflow, ask PostHog's two questions: is the work easy to check, and is the mistake cheap to undo?
5. Assume anything an agent can read will eventually leak, and size credential scope accordingly.
6. Update how we review growth. Ask what someone can now decide, what they produced, and what they can go on to produce.

The meta-observation: a year ago my likes were full of model releases and prompt tricks. This window is almost entirely harnesses, evals, sandboxes, credentials and review loops. The centre of gravity has moved from the model to the platform around it, which is either reassuring or terrifying depending on how much platform you've built.

Until next month's likes. 🍳
