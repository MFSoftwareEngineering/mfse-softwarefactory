---
name: MFSE MinimalSquad plan-driven implementation
description: Implement from an approved plan by forcing the MinimalSquad pipeline: coding, adversarial functional review, adversarial security review.
argument-hint: Provide a plan file path or paste the approved plan plus optional focus areas.
agent: agent
model: GPT-5.3-Codex (copilot)
---

Implement using the MinimalSquad system only.

Use exactly these sub-agents:
- MFSE-MinimalSquad-Coder
- MFSE-MinimalSquad-Reviewer
- MFSE-MinimalSquad-Security-Reviewer

Approved plan input:
- ${input:plan:Path to approved plan or pasted plan text}

Optional implementation focus:
- ${input:focus:Optional constraints, files, or risks to prioritize}

## Mandatory Workflow

1. Parse the approved plan and restate the executable scope in concise bullets.
2. If plan scope is ambiguous or conflicting, stop and ask targeted clarification questions before coding.
3. Delegate implementation to MFSE-MinimalSquad-Coder.
4. Delegate adversarial functional review to MFSE-MinimalSquad-Reviewer using the plan and implementation output.
5. Delegate adversarial security review to MFSE-MinimalSquad-Security-Reviewer using the plan and implementation output.
6. Declare delivery complete only when both reviewers return a non-blocking outcome.
7. Produce a final report that separates implementation status, functional findings, security findings, and residual risks.

## Hard Rules

- Run the implementation through the sub-agents workflow end-to-end.
- Execute both review stages for every implementation.
- Report adversarial findings explicitly and transparently.
- Keep scope strictly tied to the approved plan and include only related enhancements.

## Output Format

1. `Implementation status`
2. `Functional review findings`
3. `Security review findings`
4. `Residual risks`
5. `Ready for delivery: yes|no`
