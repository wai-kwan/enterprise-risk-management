# Enterprise Risk Management (ERM) AI Agent Skill

**License**: Apache-2.0
**Specification**: agentskills.io framework compatible
**Author**: wai-kwan

An AI agent skill optimized for conducting structured enterprise risk assessment and profiling. Built using the open [agentskills.io specification](https://agentskills.io), this skill enables coding assistants and workspace LLM models to analyze corporate text assets and output an analytical, Excel-ready `risk_register.csv` file.

---

## Key Framework Capabilities

- **Context-Aware Scoping**: Dynamically identifies `N` unique risks based on a project, scenario, or provided company profile context.
- **Granular Multi-Scope Sorting**: Supports isolated or blended evaluation runs across `Strategic`, `Operational`, `Financial`, and `Enterprise` categories. The 'Enterprise' option will encompass a balanced mix of top Strategic, Operational, and Financial risks.
- **Strict Analytical Score-Coupling**: Guarantees that numerical ratings directly correlate with the quantity and severity of identified causes and effects—preventing model logic drift.
- **Dual-Action Controls Engine**: Generates specific **Preventive** actions to break risk drivers, and **Reductive** actions to soften actual business impacts.
- **Spreadsheet-Safe Data Generation**: Formats the risk register into a comprehensive 14-column schema in a standard CSV file layout featuring the following explicit headers:

  | Column Name | Description / Values |
  | :--- | :--- |
  | **Risk ID** | Sequential identifier (R1, R2, ..., R`N`) |
  | **Category** | Strategic, Operational, or Financial |
  | **Risk Title** | Concise, risk-perspective name for the risk |
  | **Description** | Single-sentence description of the risk |
  | **Causes (Drivers)** | Specific root catalysts |
  | **Effects (Consequences)** | Specific downstream impacts |
  | **Likelihood Scale (1-5)** | Numerical probability rating integer 1-5 |
  | **Likelihood** | Qualitative text definition (Rare to Almost-Certain) |
  | **Impact Scale (1-5)** | Numerical severity rating integer 1-5 |
  | **Impact** | Qualitative text definition (Insignificant to Severe) |
  | **Risk Score** | Mathematical product of Likelihood Scale × Impact Scale |
  | **Risk Criticality** | Risk threshold text tag (CRITICAL, HIGH, MODERATE, LOW) |
  | **Preventive Controls** | Risk control measures breaking root drivers |
  | **Reductive Controls** | Risk control measures minimizing consequences |

---

## Risk Assessment Framework

Each identified risk is evaluated dynamically against its explicit qualitative drivers and consequence outcomes using standard, structured Enterprise Risk Management methodology.

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

*Tip: Open `risk_register.csv` in Microsoft Excel, select your Risk Criticality column, and navigate to **Home -> Conditional Formatting -> Highlight Cells Rules -> Equal To...** to instantly apply custom corporate background highlight fills to the tags.*

### Risk Controls
To ensure comprehensive governance coverage, a dual-action set of Preventive and Reductive risk control measures are generated for every single risk item to break drivers and minimize consequences.

---

## Required Configuration Parameters

Before processing, the AI agent evaluates the user prompt for three core parameters:
1. **Number of Risks (`N`)**: Defaults to **10 risks** if unspecified.
2. **Risk Category Filter**: Filters strictly by `Strategic`, `Operational`, `Financial`, or `Enterprise` (default). The 'Enterprise' option will encompass a balanced mix of top Strategic, Operational, and Financial risks.
3. **Risk Context Validation**: Expects a clear operational context (project brief, timeline, scenario, or company profile). **If the context is ambiguous, the agent will pause and prompt the user for clarification before executing.**

---

## Installation & Setup

Place the `SKILL.md` configuration folder into your active local workspace or global agent configurations directory.

### Workspace Setup (Local Repo)
To activate this skill automatically inside your specific project workspace, copy the folder footprint like so:
```text
your-project-repository/
└── .github/
    └── skills/
        └── enterprise-risk-management/
            └── SKILL.md
```

### Direct CLI Installation (When using tools like agentskills CLI)
```bash
npx skills add wai-kwan/enterprise-risk-management
```

---

## Usage Guide & Prompt Triggers

Once the skill folder is added to your environment workspace, trigger the agent by referencing the slash command.

### Example Prompt Instructions

#### 1. Default Blended Evaluation
> "Run /erm across our recent project architecture documentation to find 10 risks."

#### 2. Tailored Operational Run
> "Analyze our business plan using the erm skill. Identify 5 operational risks to audit our security and business continuity vectors."

#### 3. Strategic Fiscal Run
> "Using /erm, evaluate our compliance files to extract 8 financial risks."

---

## Authorship & Credits

- **Author**: [wai-kwan](https://github.com/wai-kwan)
- **Specification Standard**: Built to align with the [agentskills.io](https://agentskills.io) open ecosystem.

Feedback, corporate framework contributions, and issue logs are welcome via GitHub Issues.

---

## 📄 License and Liability Protection

This project is licensed under the **Apache License 2.0**. It offers premium open-source permissiveness combined with explicit **patent, trademark, and contributor liability protections** for enterprise developer teams. See the `SKILL.md` frontmatter metadata for more information.
