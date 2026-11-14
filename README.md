# Enterprise Risk Management (ERM) AI Agent Skill

**License**: Apache-2.0\
**Specification**: agentskills.io framework compatible\
**Author**: wai-kwan (https://github.com/wai-kwan)

An AI agent skill optimized for conducting structured enterprise risk assessment and profiling. Built using the open [agentskills.io specification](https://agentskills.io), this skill enables coding assistants and workspace LLM models to analyze corporate text assets and output an analytical, Excel-ready `risk_register.csv` file.

It works in two modes:
- **Generation Mode**: identifies `N` new risks from a project, scenario or company profile and builds a complete register.
- **Completion Mode**: takes a risk register you have only partly filled in, in any layout, or a plain list of risks, and completes it, keeping your own values.

The AI agent can also use two companion skills alongside it: [risk-discovery](https://github.com/wai-kwan/risk-discovery) to discover risks through a facilitated workshop or interview, and [kri](https://github.com/wai-kwan/key-risk-indicator) to create Key Risk Indicators for the risks in the register (see [Companion Skills](#companion-skills)).

---

## Key Framework Capabilities

- **Context-Aware Scoping**: Dynamically identifies `N` unique risks based on a project, scenario, or provided company profile context.
- **Granular Multi-Scope Sorting**: Supports isolated or blended evaluation runs across `Strategic`, `Operational`, `Financial`, and `Enterprise` categories. The 'Enterprise' option will encompass a balanced mix of top Strategic, Operational, and Financial risks.
- **Focused Cause-and-Effect Analysis**: Identifies **2 to 3 distinct, material Causes and 2 to 3 Effects** for each risk, choosing the most important ones without padding. Each Cause is a root driver specific enough to design a preventive control for.
- **Strict Analytical Score-Coupling**: Likelihood is rated strictly from the Causes and Impact strictly from the Effects, which prevents arbitrary scores and model logic drift.
- **Dual-Action Controls Engine**: Generates specific **Preventive** actions to break risk drivers, and **Reductive** actions to soften actual business impacts.
- **Register Completion**: Fills the gaps in a partial or non-standard register, so you can start from the risks you already know.
- **Spreadsheet-Safe Data Generation**: Formats the risk register into a comprehensive 14-column schema in a standard CSV file layout featuring the following explicit headers:

  | Column Name | Description / Values |
  | :--- | :--- |
  | **Risk ID** | Sequential identifier (R1, R2, ..., R`N`), or your own IDs in Completion Mode |
  | **Category** | Strategic, Operational, or Financial |
  | **Risk Title** | Concise, risk-perspective name for the risk |
  | **Description** | Single-sentence description of the risk |
  | **Causes (Drivers)** | 2 to 3 specific root catalysts |
  | **Effects (Consequences)** | 2 to 3 specific downstream impacts |
  | **Likelihood Scale (1-5)** | Numerical probability rating integer 1-5 |
  | **Likelihood** | Qualitative text definition (Rare to Almost-Certain) |
  | **Impact Scale (1-5)** | Numerical severity rating integer 1-5 |
  | **Impact** | Qualitative text definition (Insignificant to Severe) |
  | **Risk Score** | Mathematical product of Likelihood Scale × Impact Scale |
  | **Risk Criticality** | Risk threshold text tag (CRITICAL, HIGH, MODERATE, LOW) |
  | **Preventive Controls** | Risk control measures breaking root drivers, at least one per Cause |
  | **Reductive Controls** | Risk control measures minimizing consequences, at least one per Effect |

---

## Risk Assessment Framework

Each identified risk is evaluated dynamically against its explicit qualitative drivers and consequence outcomes using standard, structured Enterprise Risk Management methodology.

### Causes and Effects
Each risk gets **2 to 3 Causes and 2 to 3 Effects**, never more than 3. The skill picks the most important ones and stops when further items would overlap or be minor. A risk that genuinely has a single cause or effect keeps just one rather than being padded with near duplicates.

### Likelihood Scale
Likelihood is assessed based on the mathematical probability of the underlying **Causes** materializing:
- **1** = Rare
- **2** = Unlikely
- **3** = Possible
- **4** = Likely
- **5** = Almost certain

### Impact Scale
Impact is assessed based on the direct severity of the downstream **Effects** if the risk event occurs:
- **1** = Insignificant
- **2** = Low
- **3** = Moderate
- **4** = High
- **5** = Severe

### Risk Criticality Matrix
Calculated automatically via the formula: \(\text{Risk Score} = \text{Likelihood (1-5)} \times \text{Impact (1-5)}\). 

The tool maps boundaries into four clear text-only risk classification tags optimized for seamless spreadsheet sorting and data filtration:
- **CRITICAL** (Risk Scores 20 to 25)
- **HIGH** (Risk Scores 10 to 19)
- **MODERATE** (Risk Scores 5 to 9)
- **LOW** (Risk Scores 1 to 4)

The register is sorted from the highest Risk Score to the lowest (ties: higher Impact first).

*Tip: Open `risk_register.csv` in Microsoft Excel, select your Risk Criticality column, and navigate to **Home -> Conditional Formatting -> Highlight Cells Rules -> Equal To...** to instantly apply custom corporate background highlight fills to the tags.*

### Risk Controls
To ensure comprehensive governance coverage, a dual-action set of Preventive and Reductive risk control measures are generated for every single risk item: at least one Preventive Control for each Cause, and at least one Reductive Control for each Effect.

---

## Required Configuration Parameters (Generation Mode)

Before processing, the AI agent evaluates the user prompt for three core parameters:
1. **Number of Risks (`N`)**: Defaults to **10 risks** if unspecified.
2. **Risk Category Filter**: Filters strictly by `Strategic`, `Operational`, `Financial`, or `Enterprise` (default). The 'Enterprise' option will encompass a balanced mix of top Strategic, Operational, and Financial risks.
3. **Risk Context Validation**: Expects a clear operational context (project brief, timeline, scenario, or company profile). **If the context is ambiguous, the agent will pause and prompt the user for clarification before executing.**

The output is saved as `risk_register.csv` in the workspace directory. If a file of that name already exists, the skill asks before replacing it, and otherwise saves the new register as `risk_register_2.csv` (or the next unused number).

---

## Completion Mode: Completing Your Own Register

Give the skill a register you have started, whether it is a CSV or spreadsheet, a pasted table, or just a list of risks, and it fills in the rest in the standard 14-column format.

**Your values always win.**
- Your words are never reworded, shortened, reordered or removed. Only empty fields are filled.
- If you gave only one Cause or Effect, the skill keeps yours first and adds one more to reach the minimum of 2 (up to 3 only if you ask it to enrich the register). Each added Cause or Effect gets a matching control.
- Formatting is made spreadsheet safe without changing any words: list markers such as `-`, `•` or `1.` become `*`.

**Flexible input.**
- Columns are matched by meaning, not exact header: "Risk Name" is read as the Risk Title, "Root cause" as Causes, "Probability" as Likelihood, and so on.
- Text ratings are converted to numbers ("Likely" becomes 4, "Medium" becomes 3).
- Columns that do not belong in the register (such as Owner or Status) are left out, and the skill tells you which.
- The only requirement is a Risk Title or a Description for each risk.

**Consistent scoring.** Risk Score and Risk Criticality are always recalculated from the final Likelihood and Impact. If this changes a score you supplied, the skill flags it.

**Your IDs are kept.** Rows are sorted by score, but your own Risk IDs stay as they are so your references remain valid. IDs are assigned only where none were given.

**Your file is never changed.** The completed register is saved next to your input with `_completed` added to the name, for example `Q3_risks.xlsx` becomes `Q3_risks_completed.csv`. Pasted input is saved as `risk_register_completed.csv`.

**A clear report.** A CSV cannot show which values were yours and which were generated, so the skill ends with a short report: for each risk, which fields it generated or topped up, plus every conversion, recalculation, formatting fix and dropped column. Generated ratings and controls are proposals for the risk owner to review.

### Example
Input:
```csv
Risk,Root cause,Likelihood,Owner
Loss of our sole supplier disrupting production,Single source contract,Likely,Head of Procurement
Key engineers leaving during the product launch,,,
```

The skill keeps "Single source contract" as the first Cause of the first risk and adds a second, converts "Likely" to 4, generates every other field with 2 to 3 Causes and Effects per risk, sorts the risks by score, assigns R1 and R2, and reports that the Owner column was not carried over.

---

## Companion Skills

ERM is the foundation of a family of risk management skills that share the same 14-column register format. When they are installed, the AI agent can use them alongside ERM to **discover risks** and **create KRIs**:

| Skill | What the AI agent uses it for |
|---|---|
| [risk-discovery](https://github.com/wai-kwan/risk-discovery) | **Discover risks** through a facilitated, time-boxed workshop or interview, drawing out the participants' own top risks into an ERM format register. A session can be handed over to ERM to complete the register |
| [kri](https://github.com/wai-kwan/key-risk-indicator) | **Create Key Risk Indicators** with Green, Amber and Red thresholds for the risks in the register, calling ERM to fill in a register that is missing information |

A typical flow: discover risks with risk-discovery, complete and rate them with ERM, then create KRIs with kri to monitor them. ERM suggests risk-discovery when you want to find risks with your team, and offers kri after producing or completing a register. If a companion is not installed, it gives you the link.

---

## Installation & Setup

Place the `erm` skill folder into your active local workspace or global agent configurations directory. As the [agentskills.io specification](https://agentskills.io/specification) requires, `SKILL.md` sits at the top of the folder and the folder name (`erm`) matches the skill `name`.

### Workspace Setup (Local Repo)
To activate this skill automatically inside your specific project workspace, copy the folder footprint like so:
```text
your-project-repository/
└── .github/
    └── skills/
        └── erm/
            └── SKILL.md
```

Other agents load skills from their own folders (for example `.claude/skills/erm/` for Claude Code), so use the location your agent expects.

### Direct CLI Installation (When using tools like agentskills CLI)
```bash
npx skills add wai-kwan/enterprise-risk-management
```

### Claude (claude.ai)
Download the packaged `erm.skill` file from this repository and upload it under Settings, Capabilities, Skills, or use the Save skill button on the file card.

### Recommended companions
```bash
npx skills add wai-kwan/risk-discovery
npx skills add wai-kwan/key-risk-indicator
```

---

## Usage Guide & Prompt Triggers

Once the skill folder is added to your environment workspace, ask in plain language or reference the slash command `/erm`.

### Generation Mode

#### 1. Default Blended Evaluation
> "Run /erm across our recent project architecture documentation to find 10 risks."

#### 2. Tailored Operational Run
> "Analyze our business plan using the erm skill. Identify 5 operational risks to audit our security and business continuity vectors."

#### 3. Strategic Fiscal Run
> "Using /erm, evaluate our compliance files to extract 8 financial risks."

### Completion Mode

#### 4. Complete a Partial Register
> "Use /erm to complete the missing fields in Q3_risks.xlsx."

#### 5. Rate and Add Controls to a List of Risks
> "Here are our top risks from the board meeting. Rate them and add preventive and reductive controls using the erm skill."

#### 6. Complete and Extend
> "Complete my risk_register.csv and add 3 new financial risks."

---

## Authorship & Credits

- **Author**: [wai-kwan](https://github.com/wai-kwan)
- **Specification Standard**: Built to align with the [agentskills.io](https://agentskills.io) open ecosystem.
- **Companion skills**: [risk-discovery](https://github.com/wai-kwan/risk-discovery) and [kri](https://github.com/wai-kwan/key-risk-indicator)

Feedback, corporate framework contributions, and issue logs are welcome via GitHub Issues.

---

## 📄 License and Liability Protection

This project is licensed under the **Apache License 2.0**. It offers premium open-source permissiveness combined with explicit **patent, trademark, and contributor liability protections** for enterprise developer teams. See the `SKILL.md` frontmatter metadata for more information.
