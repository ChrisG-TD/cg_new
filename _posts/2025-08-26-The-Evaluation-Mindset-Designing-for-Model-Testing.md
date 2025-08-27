---
layout:
title: The Evaluation Mindset, Designing for Model Testing
date: 2025-08-26
author: 
pin: false
categories: [Prompt Complexity]
description: Why prompt evaluation is as critical as generation. This section introduces levels of difficulty, failure design, YAML contracts, reproducibility scaffolds, and evaluation goals for structured LLM testing.
tags: [promptEngineering, AItools, devlog, testing, promptEvaluation, structuredPrompts]
---

# Section 3: The Evaluation Mindset, Designing for Model Testing

Creating prompts is only half the battle. The real work starts when you use those prompts to **test large language models consistently** across versions, architectures, domains, and production scenarios.

This section explains the philosophy and structure of the evaluation system. Prompts are treated not as isolated curiosities but as **benchmarkable, reproducible, domain-specific test cases**. A prompt that works once is interesting. A prompt that holds under variation is engineering.

---

## Prompt Evaluation vs Prompt Generation

Prompt generation is the art of writing useful input for an LLM.  
Prompt evaluation is the science of determining how reliably that input produces **expected, interpretable, and bounded** output.

- **Prompt generation asks:** How do I get the best possible result?  
- **Prompt evaluation asks:** What does this prompt reveal about the model that answered it?

> **Key distinction:** Evaluation is not creative. It is empirical.  
{: .callout .info}

Our evaluation prompts are:

- Domain-specific  
- Instructionally scoped  
- Outcome-aware  
- Repeatable  

And they must reveal a measurable property of model behavior.

---

## Levels Are Lenses

Every prompt type spans **10 levels of difficulty**. This is not just a gradient of hard-to-harder. Each band has a different **shape** of difficulty that targets different cognitive behaviors.

**Complexity Bands**

| Level band | Characteristics | Example capabilities | Metadata tags |
|---|---|---|---|
| 1–3 | Lexical substitution, factual recall, short completions | Vocabulary swap, fact lookup, direct mapping | `complexityLevel=low` |
| 4–6 | Multi-part responses, reasoning with examples, ordering logic | Summarization plus ordering, structured transforms | `complexityLevel=mid` |
| 7–9 | Compound instructions, synthesis across formats, goal planning | Multi-step reasoning, cross-evidence synthesis | `complexityLevel=high` |
| 10 | Long-context navigation, multi-turn inference, cross-domain | State tracking across 2K+ tokens, dialog memory | `complexityLevel=max` |

Each example is tagged with `complexityLevel`, `estimatedTokens`, and `wordCount` so downstream tools can match test difficulty to model capacity.

---

## Designing for Failure (On Purpose)

Not every prompt should succeed. Some are designed to be too hard, too ambiguous, or too long for the target context. These stress tests surface brittleness early.

**Failure Modes We Observe**

| Failure type | What it looks like | What it reveals |
|---|---|---|
| Overconfidence | Fabricated citations and false certainty | Hallucination tendency and calibration |
| Inconsistency | Different answers to minor rephrasings | Stability of reasoning under paraphrase |
| Control break | Ignores system or role instructions | Instruction adherence and prompt loyalty |
| Ambiguity collapse | Arbitrary choice among valid readings | Disambiguation strategy and bias |

> **Why it matters:** Robustness is not about always getting it right. It is about knowing **what** breaks, **when**, and **why**.  
{: .callout .info}

Prompts that break **cleanly** teach us something. Prompts that break **inconsistently** get flagged for redesign.

---

## YAML: The Evaluation Contract

Each prompt file carries full YAML frontmatter. This is not decoration. It is the machine-readable **contract** that binds the test to its evaluation logic.

**Example YAML**

```yaml
title: Few-shot Prompt Example 7
model: gpt-4o
promptType: fewshot
tokenizer: cl100k_base
complexityLevel: 7
estimatedTokens: 405
wordCount: 288
promptFormat: chatML
useCase: internalTesting
domains: [programming, math]
tags: [fewshot, reasoning, promptEvaluation]
```

**How the contract is used**

| Capability | YAML fields that drive it | Outcome |
|---|---|---|
| Select tests by type | `promptType`, `promptFormat` | Batch runs for few-shot, CoT, ToT, ReAct |
| Match tokenizer constraints | `tokenizer`, `estimatedTokens` | Avoids boundary truncation and unfair failures |
| Target a domain | `domains`, `tags` | Narrow evaluation to Python, logic, finance, and more |
| Scope difficulty | `complexityLevel`, `wordCount` | Aligns test shape to model context size |

---

## Prompt Structure for Reproducibility

Each example includes:

- A fully written, domain-specific prompt  
- Escalation from level 1 to level 10 with fresh logic at each level  
- No template clones  
- Token and word estimates  
- Explicit format marker: instruction, chatML, CoT, multi-turn

**Required Structure Fields**

| Field | Purpose | Notes |
|---|---|---|
| `promptBody` | The actual instruction or chat turns | Stored in source file, referenced by YAML |
| `expectedShape` | Format constraints for output | Freeform, JSON schema, table, or bullet list |
| `assertions` | Checks for evaluation | Regex, JSON schema, keyphrase, or custom function |
| `scaffoldType` | Structural frame used | System, role, contextual, or none |

> **Rule:** You cannot version a prompt if you do not version its **intent** and **expected shape**.  
{: .callout .warning}

---

## Run Parameters and Controls

Determinism and fairness require explicit run controls. We pin defaults, then vary them intentionally.

| Parameter | Default | When we vary it | Why it matters |
|---|---|---|---|
| `temperature` | 0.2 | Creativity or exploration tests | Affects diversity and stability |
| `top_p` | 1.0 | Sampling studies | Changes tail behavior of token choice |
| `max_tokens` | task-specific | Boundary tests | Truncation detection |
| `seed` | fixed per batch | Reproducibility checks | Repeatable comparisons |
| `tools_enabled` | false by default | ReAct and agent tests | Tool hallucination control |
| `stop_sequences` | explicit per task | Format compliance tests | Prevents run-on outputs |

---

## Scoring and Instrumentation

Outputs are scored with both automated and human-in-the-loop checks. The goal is to measure **shape**, **truth**, and **cost**.

| Metric | Definition | Measurement |
|---|---|---|
| Accuracy | Correctness against ground truth | Unit tests, golden answers, or programmatic checks |
| Completeness | Presence of all required parts | Keyphrase or section checks |
| Format compliance | Output matches expected shape | JSON schema or regex assertions |
| Token efficiency | Useful output per token | Tokens to reach valid answer |
| Hallucination risk | Unsupported claims or citations | Heuristic flags and human spot checks |
| Determinism | Stability across reruns | Variance over fixed seeds |

---

## Filtering by Evaluation Goals

Prompts are tagged so that test runs can be **goal-driven** rather than monolithic.

| Goal | Tags and domain filters | What it reveals |
|---|---|---|
| Reasoning depth | `chainofthought`, `planning`, `react` | Whether multi-step logic holds or collapses |
| Code-specific ability | `python`, `programming`, `fewshot` | Syntax adherence and runtime plausibility |
| Tool use simulation | `toolformer`, `rag`, `react` | Reliability of API-style calls and tool paths |
| Resistance to hallucination | `selfask`, `instruction`, `cot` | Confidence calibration and citation quality |
| Structured output | `multi-turn`, `chatML`, `reflective` | Schema control and output discipline |

---

## Anti-Patterns We Avoid

We deliberately avoid practices that inflate scores without improving reliability.

| Anti-pattern | Why we avoid it | What we do instead |
|---|---|---|
| Synthetic cloning of templates | Looks like coverage without new behavior | Write new logic per level and type |
| Blind token padding | Longer prompts that do nothing better | Short, targeted scaffolds with measurable effect |
| Placeholder injection | Meta-prompts about prompts | Real instructions with explicit assertions |
| Unpinned run params | Hidden variance across runs | Fixed seeds and documented parameter sweeps |

---

## What Comes Next

With the evaluation mindset and contract in place, the next section turns technical. We move from prompt-level design to **token-aware evaluation**: measuring token cost, tokenizer alignment across model families, and performance at boundary lengths.  

Because even the strongest prompt fails if it silently breaks at 2,049 tokens.
