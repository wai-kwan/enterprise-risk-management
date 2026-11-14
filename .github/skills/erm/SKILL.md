---
name: erm
description: Executes a targeted enterprise risk assessment following a structured workflow of risk identification, cause/effect analysis, risk rating, risk scoring, risk criticality assessment, and generating prevention & reduction risk treatment control measures, and saving to a standalone risk register CSV file. Can also take a full, partial or non-standard risk register, or a plain list of risks, and complete only the missing fields while keeping the user's own values, producing a register in the same 14 column format. Use whenever the user wants a risk assessment or risk register, wants to identify, rate, score or treat risks, or asks to complete, fill in, rate or add controls to an existing risk register. Works with two companion skills, risk-discovery to discover risks through a facilitated workshop or interview and kri to create key risk indicators from the register.
license: Apache-2.0
compatibility: Any agentskills.io compatible workspace. Works best with the companion skills risk-discovery (https://github.com/wai-kwan/risk-discovery) and kri (https://github.com/wai-kwan/key-risk-indicator), but does not require them.
metadata:
  tags: "enterprise-risk-management, erm, risk-management, risk-assessment, risk, ai-agents, skills, ai-agents-skill"
  written_by: wai-kwan
  github: "https://github.com/wai-kwan"
  version: "1.2.1"
  domain: Enterprise Risk Management, ERM, Risk Management, Risk Assessment, AI Agents Skill
  standard: Enterprise Risk Management Workflow Specification
---

# Enterprise Risk Management (ERM) Skill

This skill guides the agent through an end-to-end enterprise risk assessment workflow based on explicit context, customizable scoping, rigorous cause-and-effect controls, and automated sorted CSV data outputs. It can also complete a risk register that the user has only partly filled in.

## Companion Skills
Two companion skills share this skill's 14 column register format. Use them when available, and if one is not installed, give the user its link instead.
- **risk-discovery** (https://github.com/wai-kwan/risk-discovery): discovers risks through a facilitated, time-boxed workshop or interview, so the risks come from the participants' own concerns rather than from a context document. If the user wants to discover risks with their team or by talking them through, suggest risk-discovery. Its sessions can hand their register to this skill's Completion Mode.
- **kri** (https://github.com/wai-kwan/key-risk-indicator): creates Key Risk Indicators with Green, Amber and Red thresholds from the register. After producing or completing a register, offer kri as the next step for monitoring the risks.

## When to Use This Skill
- When conducting a strategic, financial, operational, or enterprise (encompassing strategic, financial, and operational) risk assessment.
- When generating a comprehensive, spreadsheet-compatible risk register featuring cause-effect analysis, risk rating, risk ranking, and preventive and reductive risk treatment control measures.
- When the user (or another skill) supplies a partially completed risk register, a register in a different layout, or a list of risks, and wants the missing fields completed in the erm format.

## Choose the Mode
- **Generation Mode** (default): the user gives a context and wants risks identified. Follow the "ERM Workflow Execution Steps" below.
- **Completion Mode**: the user supplies risks of their own, as a file (CSV or spreadsheet), a pasted table, or a list of risk titles or descriptions, and asks to complete, fill in, rate, score or add controls to them. Another skill (for example kri or risk-discovery) may also invoke this mode with a register. Follow "Completion Mode" below.

If the user supplies a register and also asks for new risks, complete their register first, then identify the extra risks with the Generation Mode workflow and add them to the same output.

---

## Required User Configuration Parameters
Before running in Generation Mode, check the user prompt for these parameters. If not provided, apply the default fallback values:
1. **Number of Risks (`N`)**: Default to **10 risks** if not explicitly specified.
2. **Risk Category Filter**: Filter strictly by the chosen scope:
   - `Strategic`: Core macro-level business strategy threats.
   - `Operational`: Any threat affecting day-to-day operations, including **business operations, IT, HR, legal, compliance, security, business continuity**.
   - `Financial`: Fiscal exposure, liquidity, market pricing, credit, and financial sustainability.
   - `Enterprise`: A balanced mix of the top risks spanning the Strategic, Operational, and Financial categories. This is the default scope if none is specified.
3. **Risk Context**: The context to conduct the risk assessment on, which can be a project, a scenario, a company profile etc. If the context is not clear, prompt the user for details of the context.

In Completion Mode, `N` and the Risk Category Filter do not apply: the risks to complete are exactly the ones the user supplied. The Risk Context is still used to make the completed fields specific, so use any context the user gives and ask for it only if the risks cannot be understood without it.

---

## ERM Workflow Execution Steps

Follow these steps in exact sequential order:

### 1. Risk Identification
Analyze the provided user context or corporate workspace files. Identify exactly `N` unique, context-specific risks matching the selected or default `Risk Category Filter`. The title of the risk should be concise and worded from a risk perspective, and not just a general heading of the risk. Categorize any operational, technological, safety, legal, compliance, or business continuity exposures strictly under the **Operational** classification.

### 2. Risk Analysis & Context Optimization
For each identified risk, elaborate in detail using concise, high-density text fragments to manage token limits safely.
- **Description**: A short description of the risk in 1 sentence without any commas or line breaks.
- **Causes**: The root drivers, external factors, or internal catalysts that cause the risk event to happen.
- **Effects**: The specific downstream consequences and impacts on the organization if the risk event materializes.
- **Number of Causes and Effects**: Include the distinct, material causes and effects, **2 to 3 each**, and never more than 3. Choose the most important ones and stop when further items would overlap or be minor. Use fewer than 2 only when the risk genuinely has a single cause or effect, and never pad with near duplicates to reach 2. Each Cause should be a root driver specific enough to design a preventive control for, not a symptom or a restatement of the risk.
- **Formatting Constraint**: When listing multiple Causes or Effects, you MUST use asterisks with an actual physical line break (carriage return) inside the quotes to represent a newline. Do NOT use literal text characters like `\n` or `\r`. Do NOT use hyphens (`-`) or minus signs anywhere to start a list item line, as this breaks spreadsheet parsing.

### 3. Risk Rating, Risk Scoring & Risk Criticality Assignment
Rate each risk on a discrete 1 to 5 scale using the following specific assessment guidance:

#### Likelihood Scale
*Assessed based strictly on the probability of the underlying **Causes** materializing. Do not shift scores arbitrarily without explicit driver justification in Step 2.*
- **1** = Rare, **2** = Unlikely, **3** = Possible, **4** = Likely, **5** = Almost certain

#### Impact Scale
*Assessed based strictly on the severity of the downstream **Effects** if the risk occurs. Do not shift scores arbitrarily without explicit consequence justification in Step 2.*
- **1** = Insignificant, **2** = Low, **3** = Moderate, **4** = High, **5** = Severe

#### Risk Score Calculation, Ranking, Risk Criticality Assignment & Sorting
Calculate the **Risk Score** mathematically for each entry:
$$\text{Risk Score} = \text{Likelihood Scale (1-5)} \times \text{Impact Scale (1-5)}$$

Assign a **Risk Criticality** text tag based on your exact risk score thresholds. The agent MUST evaluate the calculated risk score against the following strict, mutually exclusive mathematical boundaries to assign the **Risk Criticality**. Do not use proximity guesswork, intuitive grouping, or emojis:
- **If Risk Score is 20, 21, 22, 23, 24, or 25 (inclusive)**: **Risk Criticality** is **CRITICAL**.
- **If Risk Score is 10, 11, 12, 13, 14, 15, 16, 17, 18, or 19 (inclusive)**: **Risk Criticality** is **HIGH**.
- **If Risk Score is 5, 6, 7, 8, or 9 (inclusive)**: **Risk Criticality** is **MODERATE**.
- **If Risk Score is 1, 2, 3, or 4 (inclusive)**: **Risk Criticality** is **LOW**.

*The final data matrix **MUST** be sorted dynamically in descending order from the highest Risk Score to the lowest Risk Score. Assign a Risk ID to each risk sequentially in the format R1, R2, ..., R`N` based on this sorted order.*

### 4. Risk Treatment
Develop tailored risk control measures using a dual-action framework for every risk item:
- **Preventive Controls**: For *each* identified Cause, generate one or more specific measures designed to prevent that cause from occurring.
- **Reductive Controls**: For *each* identified Effect, generate one or more specific measures designed to minimize or reduce the severity of that effect.
- **Formatting Constraint**: When listing multiple control measures, you MUST use asterisks with an actual physical line break (carriage return) inside the quotes to represent a newline. Do NOT use literal text characters like `\n` or `\r`. Do NOT use hyphens (`-`) or minus signs anywhere to start a list item line, as this breaks spreadsheet parsing.

---

## Completion Mode
Complete a register the user has partly filled in. The guiding rule: **the user's values always win.** Their words are never changed, empty fields are filled, and Causes and Effects below the minimum are topped up after the user's own items.

### 1. Read the Input
- Read files with a real parser (Python's `csv` module, or an Excel reader), never by splitting on commas or newlines, because erm registers hold physical line breaks inside quoted cells.
- Map columns by **meaning**, not exact header: for example "Risk Name", "Threat" or "Risk Event" is the Risk Title, "Root cause" or "Drivers" is Causes, "Consequence" is Effects, "Probability" is Likelihood, "Mitigation" is a control. Ignore columns that cannot be mapped (such as Owner or Status) and tell the user they are not carried into the output.
- A plain list of risks, one per line, is read as Risk Titles or Descriptions.
- The minimum for each risk is a **Risk Title or a Description**. Skip any row that has neither and report it.
- **Never rename, move, overwrite or edit the user's input file.**

### 2. Complete Each Risk
Work through the 14 columns for every risk and fill only what is empty:

| Field | If empty |
|---|---|
| Risk Title | Write a concise title from the Description, worded from a risk perspective |
| Description | Write one sentence without commas or line breaks from the Title and any other known fields |
| Category | Assign Strategic, Operational or Financial using the definitions in "Required User Configuration Parameters" |
| Causes (Drivers) | Identify 2 to 3 causes as in Workflow Step 2. If the user gave only 1, add causes after theirs (see the rules below) |
| Effects (Consequences) | Identify 2 to 3 effects as in Workflow Step 2. If the user gave only 1, add effects after theirs (see the rules below) |
| Likelihood Scale and Impact Scale | Rate as in Workflow Step 3, using the Causes and Effects (supplied or completed) |
| Likelihood and Impact (text labels) | Derive from the scales (for example 4 is Likely and High) |
| Risk Score and Risk Criticality | Calculate as in Workflow Step 3 |
| Preventive Controls | Generate one or more per Cause as in Workflow Step 4 |
| Reductive Controls | Generate one or more per Effect as in Workflow Step 4 |

Rules for partly filled fields:
- **The user's words are kept as written.** Never reword, shorten, reorder or remove anything the user supplied.
- **Formatting-only fixes are allowed** so the file stays spreadsheet safe: turn list markers such as `-`, `•` or `1.` into `* `, put each item on its own line inside the cell, and remove a hyphen or minus sign that starts a cell. A single item without a marker gets `* ` in front. Change no words, and report the fixes.
- **Top up Causes and Effects to the minimum of 2.** If the user gave only 1 Cause or 1 Effect, keep it first and add one more after it, following the exception in "Number of Causes and Effects" (Workflow Step 2) for risks that genuinely have only one. Go up to 3 only if the user asks you to enrich the register. If the user gave more than 3, keep all of them.
- **Cover added Causes and Effects with controls.** When you add a Cause, add a Preventive Control for it, and when you add an Effect, add a Reductive Control for it, after any controls the user already wrote. Otherwise leave a controls cell the user filled as it is.
- **Text label without a number** (for example Likelihood "Likely" but no scale): convert it to the matching number. Accept common equivalents such as "Medium" (3) or "Very high" (5), and say which conversions you made.
- **Only one of Likelihood or Impact given**: keep it and rate the other.
- **Risk Score given without scales**: choose a Likelihood and Impact pair whose product equals that score, picking the pair that best fits the Causes and Effects. If no pair on the 1 to 5 scales gives that score, rate both scales freely and flag the difference.
- **Risk Score and Risk Criticality are calculations.** Always derive them from the final Likelihood and Impact. If this changes a score or criticality the user supplied, use the calculated value and flag the change.
- **Risk Criticality given but nothing else**: rate Likelihood and Impact so the score falls in that band where the Causes and Effects allow it, otherwise flag the difference.

### 3. IDs and Order
- Sort the completed rows by Risk Score, highest first, as in Workflow Step 3 (ties: higher Impact first, then the user's original order).
- **Keep the user's Risk IDs** so their references stay valid, even if the sorted order is no longer sequential. Assign R1, R2, ... in sorted order only when the user supplied no IDs, and give any risk missing an ID the next unused number.
- Never add risks the user did not supply, unless they asked for new risks as well (see "Choose the Mode").

### 4. Save the Completed Register
- Write the result in the exact 14 column layout and formatting rules below, with no extra columns.
- Save it **next to the input file**. For pasted text or a list with no file, save it in the active workspace directory. If the user or a calling skill gives a location, use that instead.
- Name it after the input file with `_completed` added: `Q3_risks.xlsx` becomes `Q3_risks_completed.csv`, and `risk_register.csv` becomes `risk_register_completed.csv`. For pasted text or a list with no file, use `risk_register_completed.csv`. If the user or a calling skill names the output file, use that name instead.
- Never overwrite the user's input file or an earlier completed file without asking.

### 5. Report What Was Completed
The CSV cannot show which values were supplied and which were generated, so the closing message must. Keep it short:
- the input used and the output file name;
- for each risk, which fields were generated and which were topped up (for example "R2: ratings and controls generated, 1 Cause and 1 Effect added to the user's; Title and Description kept");
- every flag: converted labels, recalculated scores or criticality, formatting fixes, skipped rows, and columns not carried over;
- a reminder that generated ratings and controls are proposals for the risk owner to review;
- an offer to create Key Risk Indicators with the kri skill (see "Companion Skills").

When another skill invoked Completion Mode, return the same information to it so it can mark the generated values in its own outputs.

---

## Automated File Persistence & Output Formatting
In **Generation Mode**, upon processing, the agent **MUST** automatically create a clean, standard comma-separated values file named **`risk_register.csv`** in the active workspace directory, injecting the exact sorted 14-column tabular content layout specified in the Standard Template below. If the user or a calling skill names the output file, use that name instead. If a file of that name already exists, never overwrite it silently: ask the user whether to replace it, and if they decline or cannot be asked, save as `risk_register_2.csv` (or the next unused number) and say so. In **Completion Mode**, save the file under the name given in "Save the Completed Register" instead, so the user's input is never overwritten.

- **Zero-Whitespace Commas (CRITICAL)**: Separate fields strictly with a single comma character (`,`). Do NOT output a space after any comma delimiter. The next character or opening quote must snap directly to the comma (use `,"* Text` and NEVER `, "* Text`).
- **Strict Cell Encapsulation**: Wrap any text field containing physical line breaks, asterisks, or commas entirely in absolute double quotes (`"..."`) from the very first character to the very last character of that field.
- **Physical Multi-line Records**: Use actual, physical newlines (carriage returns) inside the double quotes to represent multi-line records. Never output an unquoted line break, as it will break the spreadsheet row integrity.

### Standard Template Layout for `risk_register.csv`

```csv
Risk ID,Category,Risk Title,Description,Causes (Drivers),Effects (Consequences),Likelihood Scale (1-5),Likelihood,Impact Scale (1-5),Impact,Risk Score,Risk Criticality,Preventive Controls,Reductive Controls
R1,Strategic,Inability to capitalise on the advancement of AI,Unable to harness the advancement of AI to maintain the lead position of the company.,"* Lack of AI talent.
* Pace of AI advancement too rapid to keep abreast.","* Loss of business to competitors.
* Higher operating costs than AI enabled competitors.",4,Likely,5,Severe,20,CRITICAL,"* Increase headcount budget for AI talents.
* Create an AI task force to oversee the deployment of AI in the company.","* Incorporate AI capabilities in the company's star products.
* Use AI to automate high volume internal processes."
R2,Operational,Cyber attack on the crown jewel system,Cyber attack on the company's crown jewel system causing operational downtime and data breach.,"* Outdated firewall software.
* Weak employee password policies.","* Business standstill if the crown jewel system is down.
* Leakage of customers data leading to regulatory non-compliance penalties.
* Public reputational damage.",3,Possible,5,Severe,15,HIGH,"* Deploy automated weekly patch cycles.
* Enforce mandatory multi-factor authentication.","* Develop a Business Continuity Plan for the crown jewel system.
* Encrypt all customer data on the crown jewel system.
* Maintain a standalone cyber insurance policy.
* Prepare a pre-approved PR response playbook."
R3,Financial,Unfavourable FX Exchange Volatility,Unfavourable FX exchange affecting the global revenue.,"* Macroeconomic shift in fiscal policy.
* Interest rate changes in major markets.","* Revenue losses.
* Unpredictable earnings for budgeting.",2,Unlikely,4,High,8,MODERATE,"* Monitor treasury forex indices weekly.
* Set a treasury policy with limits on currency exposure.","* Secure fixed forward-hedging capital contracts.
* Budget with conservative exchange rate assumptions."
R4,Operational,Major fire at warehouse,Major fire at the main warehouse during the peak season with heavy damage.,"* Lack of fire control discipline.
* Staff negligence.","* Revenue losses.
* Delayed customer deliveries during the peak season.",2,Unlikely,4,High,8,MODERATE,"* Conduct annual fire risk assessment.
* Conduct annual staff refresher training on fire prevention and conduct fire drill.
* Review BCP annually to ensure up-to-date.","* Ensure fire insurance is inforce.
* Arrange a standby third party warehouse for the peak season."
```

### Completion Mode Example
A partial input, with an owner column that is not carried over:

```csv
Risk,Root cause,Likelihood,Owner
Loss of our sole supplier disrupting production,Single source contract,Likely,Head of Procurement
Key engineers leaving during the product launch,,,
```

The completed output keeps "Single source contract" as the first Cause of the first risk (formatted as `* Single source contract`) and adds a second Cause after it, converts "Likely" to 4, generates every other field with 2 to 3 Causes and 2 to 3 Effects per risk, sorts both rows by score, and reports which fields were generated or topped up, the formatting fix, and that the Owner column was dropped.
