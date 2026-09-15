# Akamai Internship --- Crypto-AI Engineering Automation Agent

> Primary sources: uploaded Akamai internship showcase and
> `README_TLSCS_AGENT.md`.

## 1. One-minute explanation

> "During my Akamai internship, I worked on Crypto-AI, an autonomous
> engineering automation agent for TLS CryptoServer workflows. The goal
> was to reduce the manual relay involved in resolving TLSCS tickets.
> The agent runs inside GitHub Copilot Agent Mode in VS Code and uses a
> local MCP server to connect to Jira, Confluence and Git source. We
> separated decision-making from execution: Markdown skills define the
> workflow, while audited wrapper scripts perform the actual engineering
> operations. The workflow takes a ticket through sandbox creation,
> source checkout, analysis, code modification, lab compilation,
> recovery if necessary, testing, approval, commit, pull-request
> creation and knowledge capture. We deliberately kept human approval
> gates before code modification and before commit/push and added
> guardrails such as file allowlists, dry-run support, structured
> logging and persistent workflow state."

The documentation describes Crypto-AI as a system that takes a Jira
ticket toward a PR-ready change and pauses where a human must decide or
environment-specific information is required.

------------------------------------------------------------------------

# 2. Problem

TLSCS ticket resolution involved a long manual relay:

``` text
Read Jira
→ create sandbox
→ sync source
→ locate code
→ patch
→ compile on lab
→ test
→ commit
→ PR
```

Problems:

-   context-heavy
-   repetitive
-   error-prone
-   environment-specific
-   credentials/paths/versions must be remembered
-   build/test failures require recovery

------------------------------------------------------------------------

# 3. Solution

``` mermaid
flowchart TD
    T[Jira Ticket] --> C[GitHub Copilot Agent]
    C --> M[MCP Server]
    M --> J[Jira]
    M --> CF[Confluence]
    M --> G[Git Source]
    C --> S[Skills]
    S --> W[Audited Wrapper Scripts]
    W --> SB[Sandbox]
    SB --> CO[Checkout]
    CO --> AN[Analyze]
    AN -->|Human approval| FX[Modify]
    FX --> BC[Lab Compile]
    BC -->|failure| RC[Build Recovery]
    BC --> TE[Test]
    RC --> BC
    TE -->|Human approval| CM[Commit/Push]
    CM --> PR[Pull Request]
    PR --> KC[Knowledge Capture]
```

------------------------------------------------------------------------

# 4. Architecture: four layers

## Layer 1 --- Copilot Agent

The developer uses GitHub Copilot Agent Mode in VS Code.

The agent:

-   reads the natural-language task
-   follows skills
-   chooses available tools
-   orchestrates phases

## Layer 2 --- MCP server

The MCP server is the controlled bridge to Akamai systems.

The documentation lists 11 tools across:

### Jira

-   jira_get_issue
-   jira_search
-   jira_get_comments
-   jira_get_attachments
-   jira_download_attachment
-   jira_create_issue

### Confluence

-   confluence_get_page
-   confluence_search
-   confluence_create_page

### Git source

-   git_get_file
-   git_list_files

## Layer 3 --- Skills

Skills are Markdown workflows.

Examples:

``` text
crypto-sandbox
crypto-fetch-code
tlscs-solve
```

They define **what to do**.

## Layer 4 --- Wrappers

Examples:

``` text
create_crypto_sandbox.sh
fetch_crypto_code.sh
analyze_repo.sh
apply_fix.sh
lab_compile.sh
run_tests.sh
scm_commit.sh
create_pr.sh
resolution_doc.sh
```

They perform the actual work.

------------------------------------------------------------------------

# 5. Skills vs tools vs wrappers

This distinction is a likely interview question.

``` text
Skill
= workflow/instructions: WHAT should happen?

Tool
= controlled capability/interface: WHAT external system can be accessed?

Wrapper
= deterministic implementation: HOW the operation is executed?
```

This separation prevents the model from directly inventing arbitrary
shell operations for every step.

------------------------------------------------------------------------

# 6. MCP

MCP = Model Context Protocol.

In this project, MCP provides an explicit interface between the agent
and internal systems.

Conceptually:

``` mermaid
sequenceDiagram
    participant A as Agent
    participant M as MCP Server
    participant J as Jira/Git/Confluence

    A->>M: Tool request
    M->>J: Authorized operation
    J-->>M: Structured result
    M-->>A: Tool result
```

### Why MCP?

-   explicit tool boundaries
-   consistent interfaces
-   auditable actions
-   easier integration with multiple systems
-   agent does not need to know every low-level API detail

------------------------------------------------------------------------

# 7. Why not let the agent directly use shell commands?

A strong answer:

> "Direct arbitrary shell access would make the system harder to audit
> and constrain. We wrapped operations into parameterized scripts with
> validation, structured logging, dry-run support and state reuse. This
> gives the agent a controlled action surface."

------------------------------------------------------------------------

# 8. Workflow state file

The system uses:

``` text
<sync-dir>/.crypto-ai-workflow.env
```

It stores validated workflow state such as:

-   Jira ticket
-   component
-   base version
-   sandbox version
-   platform
-   LDAP
-   certificate path
-   sync directory
-   lab host/user/destination
-   fix branch

The design principle is:

> Ask once, reuse everywhere.

No secrets are stored in the state file according to the README.

------------------------------------------------------------------------

# 9. 11 phases

``` mermaid
flowchart TD
    A[1 Create Sandbox] --> B[2 Checkout Source]
    B --> C[3 Analyze]
    C -->|Approve fix| D[4 Modify Code]
    D --> E[5 Compile]
    E -->|failure| F[6 Build Recovery]
    F --> E
    E --> G[7 Test]
    G --> H[8 Approval]
    H -->|Approve| I[9 Commit / Push]
    I --> J[10 Pull Request]
    J --> K[11 Knowledge Capture]
```

## Phase 1 --- Create Sandbox

Reads ticket/configuration and creates sandbox.

The sample run assigns a sandbox version such as `4.17.756`.

## Phase 2 --- Checkout Source

Uses stored state and syncs source repositories/codeline into workspace.

## Phase 3 --- Analyze

Read-only analysis.

For a security ticket, findings are converted into search terms and
candidate files/functions/lines.

**Human gate:** approve proposed fix.

## Phase 4 --- Modify

Agent modifies only approved files.

## Phase 5 --- Compile

Code is transferred to a lab machine and compiled.

## Phase 6 --- Build Recovery

Runs only if Phase 5 fails.

It analyzes compiler errors, makes allowed changes and retries, with a
bounded number of iterations.

## Phase 7 --- Test

Runs assigned test command and returns:

-   passed
-   failed
-   skipped

## Phase 8 --- Approval

Build/test/change summary is shown.

**Human gate:** approve changes.

## Phase 9 --- Commit/Push

Supports Git or Perforce.

## Phase 10 --- Pull Request

Creates PR through the configured provider/API.

## Phase 11 --- Knowledge Capture

Creates a resolution document containing:

-   ticket
-   root cause
-   changed files
-   build status
-   PR

------------------------------------------------------------------------

# 10. Why two human approval gates?

This is one of the strongest design decisions.

### Gate 1

Before changing source code.

### Gate 2

Before commit/push.

Reason:

-   code modification is high-impact
-   security fixes need review
-   commit/push changes shared state
-   autonomous agents can make incorrect changes
-   human-in-the-loop provides control

A strong answer:

> "I automated deterministic and repetitive work but retained human
> control at irreversible or high-risk boundaries."

------------------------------------------------------------------------

# 11. Guardrails

## File allowlist

`apply_fix.sh` accepts approved files.

If an unapproved file changes, the wrapper fails.

This limits blast radius.

## Dry-run

Wrappers support `--dry-run`.

Useful for:

-   testing
-   previewing
-   debugging
-   safe validation

## Structured logging

Phases return structured:

``` text
STATUS
TASK
RESULT
OUTPUT
NEXT ACTION
```

This is easier for an agent and automation layer to parse than
conversational prose.

## State reuse

Prevents repeated manual input and inconsistent values.

------------------------------------------------------------------------

# 12. Execution Mode

The agent is instructed to return structured result blocks rather than
narration.

Example:

``` text
STATUS  SUCCESS
TASK    Compilation
OUTPUT  Build Result: succeeded
NEXT ACTION Run tests
```

Why?

-   machine-readable
-   deterministic
-   easier orchestration
-   easier debugging
-   less unnecessary token usage

------------------------------------------------------------------------

# 13. Security-ticket analysis

For TLSCS-756, the documented example included findings such as:

``` text
interfaces.c / handle_status_request / CWE-131
message_processor.c / process_request / CWE-789
```

The analyzer maps:

``` text
finding → file → function → line
```

and ranks candidate files.

A strong interview explanation:

> "The analysis phase was deliberately read-only. It proposed ranked
> candidates with confidence and a fix strategy, but it did not modify
> source until the engineer approved the change."

------------------------------------------------------------------------

# 14. Build recovery

Why is recovery a separate phase?

Because compilation failure does not necessarily mean the original fix
is conceptually wrong.

Possible cause:

-   missing include
-   declaration mismatch
-   compile-time API issue
-   type mismatch
-   integration effect

The recovery loop:

``` text
compile
 ↓
capture errors
 ↓
analyze
 ↓
approved-file-only modification
 ↓
recompile
```

Bounded retries prevent uncontrolled loops.

------------------------------------------------------------------------

# 15. Git vs Perforce

The system supports both.

Git:

``` text
branch
→ commit
→ push
→ PR
```

Perforce:

``` text
change
→ submit
```

The wrapper takes the SCM choice explicitly.

------------------------------------------------------------------------

# 16. Testing

The README describes an offline suite covering:

-   MCP server
-   wrapper syntax
-   dry-run
-   flags
-   analyzer ranking/read-only behavior
-   Execution Mode contract
-   state reuse
-   allowlist
-   resolution-document generation

It reports an expected local suite of:

``` text
57 passed, 0 failed
```

The important distinction is:

> Offline tests validate the contract and wrapper behavior; live effects
> such as SSH, rsync, builds, Perforce and PR APIs require the real
> environment.

------------------------------------------------------------------------

# 17. Key engineering principles

### Least privilege

Agent only gets explicit capabilities.

### Human in the loop

High-risk actions require approval.

### Deterministic execution

Wrappers handle side effects.

### Observability

Structured logs/results make behavior inspectable.

### Idempotency/reuse

State prevents repeated setup.

### Fail-safe behavior

Unexpected inputs/errors stop the workflow rather than silently
continuing.

------------------------------------------------------------------------

# 18. Strong interview Q&A

### Q1. What is an AI agent?

**Answer:** An agent is a system where a model interprets a goal,
decides which available actions/tools to use, observes results and
continues the workflow toward the goal. Unlike a plain chatbot, it can
interact with external systems and execute multi-step actions.

### Q2. Agent vs LLM?

**Answer:** The LLM provides reasoning/generation capabilities; the
agent adds state, tools, action selection, workflow control and
execution around the model.

### Q3. What is MCP?

**Answer:** MCP is a protocol for exposing tools/context to models in a
structured way. In my project, the MCP server provided controlled access
to Jira, Confluence and Git source.

### Q4. Why MCP instead of REST APIs directly?

**Answer:** The underlying systems still use APIs, but MCP gives the
agent a consistent tool interface. It separates model interaction from
provider-specific API details and makes capabilities explicit and
auditable.

### Q5. Why skills?

**Answer:** Skills encode repeatable workflows and constraints in a
human-readable form. They tell the agent what sequence of operations to
follow.

### Q6. Why wrappers?

**Answer:** Wrappers make side effects deterministic and auditable. They
validate parameters, support dry runs, log structured results and
enforce guardrails.

### Q7. How did you prevent the agent from modifying random files?

**Answer:** The modification wrapper receives an approved file allowlist
and fails if files outside that scope are changed.

### Q8. Why not fully autonomous?

**Answer:** Because code modification and pushing changes are
high-impact actions. Human gates provide control at those boundaries
while still automating repetitive work.

### Q9. What if build fails?

**Answer:** Phase 6 performs bounded build recovery using captured
compiler errors and only approved files, then retries compilation.

### Q10. What happens if Jira findings are outside the MCP-accessible sources?

**Answer:** The documentation notes that some security findings may be
in a linked Google Drive sheet that the current MCP server cannot read.
In that case, the findings are pasted into the analysis prompt. A future
`drive_get_file` tool could remove that manual step.

### Q11. How would you improve the system?

**Answer:** - add more connectors - stronger sandbox isolation - policy
engine - approval UI - automatic test selection - richer static
analysis - security scanning - retry/backoff - distributed state store -
metrics/tracing - agent evaluation suite - audit trail - secret
management integration

### Q12. What is the biggest risk?

**Answer:** An autonomous agent has the potential to perform an
incorrect high-impact action. Therefore tool permissions, isolation,
approval gates, allowlists, validation and auditability are as important
as model quality.

------------------------------------------------------------------------

# 19. 30-second architecture answer

> "The architecture has four layers: Copilot Agent, MCP server, skills
> and wrappers. Copilot handles natural-language orchestration. MCP
> exposes explicit tools for Jira, Confluence and Git. Skills define the
> workflow, while wrappers execute deterministic side effects such as
> sandbox creation, code analysis, compilation and PR creation. A state
> file carries validated values across phases. The workflow has human
> approval gates before modification and commit/push, and wrappers
> provide guardrails such as file allowlists, dry-run support and
> structured logging."

------------------------------------------------------------------------

# 20. Most important terms to know

``` text
AI Agent
LLM
Tool Calling
MCP
MCP Server
Skill
Workflow Orchestration
State Management
Human-in-the-loop
Guardrail
Allowlist
Sandbox
Structured Logging
Dry Run
Idempotency
Git
Perforce
SSH
rsync
PR
Static Analysis
Root Cause Analysis
Regression Testing
CWE
TLS
Certificate
```
