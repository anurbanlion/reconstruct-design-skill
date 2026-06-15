---
name: reconstruct-designs
description: Reconstruct persistent design knowledge from existing code into openspec/specs/[domain]/design.md. Use when the user wants to infer architecture, conventions, abstractions, flows, dependencies, or design debt from a bounded code context or repository.
license: MIT
metadata:
   author: cazdemun
   version: "1.0"
--------------------

# Reconstruct Designs

Reconstruct persistent design knowledge from existing code.

This is an agent-driven operation. You will inspect code, infer durable architectural knowledge, validate uncertain conclusions with the user when needed, and create or update design documents under `openspec/specs/[domain]/design.md`.

The goal is not to document implementation trivia. The goal is to capture persistent domain-level knowledge that future agents can use when exploring, proposing, designing, or planning changes.

**Input**:
- Existing domain index (found, if exists, on `openspec/config.yaml`, the context section).
- Files or folders explicitly provided by the user.
- Relevant domain design files.
- Context inherited from the invoking skill or session, when applicable.

**Steps**

1. **Read OpenSpec context first**

   Before analyzing code, read:

   ```text
   openspec/config.yaml
   ```

   especially:

   ```yaml
   context: |
   ```

   Treat this section as a project domain design files index.

2. **Resolve reconstruction scope**

   Determine the operating mode:

   - Bounded Mode (default)
      - Read only the files, folders explicitly provided.
      - After that, read the relevant domain design files identified by the domain index.

      ```text
      openspec/specs/[domain]/design.md
      ```

  - Expanded Mode
      - If the skill determines that additional files or domains are required to produce a reliable design reconstruction or cannot confidently reconstruct the design from the current scope, it must stop and explain:
         - Why additional exploration is required.
         - Which files, folders, or domains should be inspected.
         - The proposed dependency depth (3 levels by default).
         - The expected benefit of expanding the analysis.
      - The user must explicitly approve any expansion beyond the current scope.
      - If the approved depth is exhausted and further exploration is required, repeat the approval process before continuing.

   If AskUserQuestion tool is available and the agent is requesting scope expansion, use the tool to ask the minimum set of questions needed to determine whether the ambiguity can be resolved without expanding the scope. The response should include both the justification for the proposed scope expansion and the clarifying questions that could eliminate the need for that expansion.

3. **Inspect code and collect evidence**

   From the selected scope, infer durable design knowledge such as:

   * domains and responsibilities
   * architectural conventions
   * reusable abstractions
   * major flows
   * shared patterns and recipes
   * dependencies between domains
   * main files and folders structure
   * technical observations
   * design debt
   * future refactoring opportunities

   Domains should be inferred from responsibilities and business capabilities, not folder names alone.

   Shared snippets, recipes, or conventions may belong inside a domain design document or inside a shared domain such as:

   * general-conventions
   * shared-patterns
   * platform

4. **Validate uncertain conclusions**

   Use the **AskUserQuestion tool** when important conclusions are not fully supported by code evidence.

   Ask about high-impact ambiguities such as:

   * domain boundaries
   * intended abstractions
   * architectural conventions
   * ownership of responsibilities
   * naming patterns
   * suspected design debt
   * historical or transitional patterns

   Do not ask for confirmation on conclusions that are strongly supported by code.

   Distinguish clearly between:

   * confirmed by code
   * confirmed by user
   * hypothesis requiring future validation

5. **Create or update design documents**

   Existing design documents should be updated intelligently rather than replaced wholesale.

   For each relevant domain, create or update:

   ```text
   openspec/specs/[domain]/design.md
   ```

   The document structure is intentionally flexible.

   Domains differ significantly and should not be forced into a rigid template.

   Common sections may include:

   ```markdown
   # Design Domain: <domain>

   ## Purpose

   ## Responsibilities

   ## Architectural Conventions

   ## Key Abstractions

   ## Flows

   ## Shared Patterns

   ## Dependencies

   ## Main Files and Folders

   ## Search Guidance

   ## Technical Observations

   ## Design Debt

   ## Future Refactoring Opportunities

   ## Confidence Notes

   ## useful search guidance
   ```

   Only include sections that provide meaningful persistent value.

6. **Update OpenSpec project context when useful**

   If stable reconstructed design knowledge should guide future agents, update:

   ```text
   openspec/config.yaml
   ```

   especially:

   ```yaml
   context: |
   ```

   Example:

   ```yaml
   context: |
     Before proposing authentication changes,
     review openspec/specs/auth/design.md.

     Before modifying payment workflows,
     review openspec/specs/payments/design.md.

     Before changing shared service conventions,
     review openspec/specs/general-conventions/design.md.
   ```

8. **Show summary**

   After writing or updating files, summarize:

   * domains created or updated
   * files written or modified
   * main architectural findings
   * user-confirmed assumptions
   * unresolved hypotheses
   * config context entries added or skipped

**Design Document Guidance**

A good `design.md` should help future agents answer:

* What is this domain responsible for?
* Where is the relevant code?
* What conventions should be followed?
* What abstractions already exist?
* What flows matter?
* What dependencies should be respected?
* What design debt should not be worsened?
* What should be validated before changing this area?

When implementations are widely distributed, prefer adding:

```markdown
## Search Guidance
```

rather than enumerating every file.

When implementations are concentrated, prefer adding:

```markdown
## Main Files and Folders
```

or both sections.

**Example Output**

```markdown
## Designs Reconstructed

Updated design docs:

**auth**:
- Updated openspec/specs/auth/design.md
- Captured token validation flow
- Captured middleware conventions
- Added search guidance for AuthService and requireAuth
- Identified duplicated session handling as design debt

**general-conventions**:
- Created openspec/specs/general-conventions/design.md
- Captured shared service/repository conventions
- Added confidence notes because findings came from bounded inspection

Updated project context:

- Added auth design reference to openspec/config.yaml
- Skipped general-conventions context entry due to insufficient evidence
```

**Guardrails**

* Bounded mode is the default.
* Do not perform broad repository exploration unless explicitly requested.
* If invoked from openspec-explore or openspec-propose, inspect only the files already identified, immediate neighboring files, and first-level architectural context.
* Read openspec/config.yaml before any design reconstruction.
* Treat context references as a project design index.
* Read existing design documents before updating them.
* Preserve existing validated design knowledge unless contradicted by stronger evidence.
* Do not create duplicate domains already represented in context or existing design docs.
* Do not overwrite design.md wholesale when an intelligent merge is possible.
* Clearly mark assumptions and confidence levels.
* Use AskUserQuestion for high-impact ambiguity.
* Avoid implementation trivia.
* Include main files/folders or search guidance when the design is distributed.
* Keep generated documents useful for future proposals, designs, and tasks.
* The operation should be idempotent: running it twice over the same scope should not duplicate content.
