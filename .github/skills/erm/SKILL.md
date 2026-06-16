---
name: erm
description: Executes a targeted enterprise risk assessment following a structured workflow of risks identification, cause/effect analysis, risk rating, risk scoring, risk criticality assessment, and generating prevention & reduction risk treatment control measures, and saving to a standalone risk register CSV file.
license: Apache-2.0
compatibility: Any agentskills.io compatible workspace
tags: [enterprise-risk-management, erm, risk-management, risk-assessment, risk, ai-agents, skills, ai-agents-skill]
metadata:
  written_by: waikwan
  version: 1.1.0
  domain: Enterprise Risk Management, ERM, Risk Management, Risk Assessment, AI Agents Skill
  standard: Enterprise Risk Management Workflow Specification
---

# Enterprise Risk Management (ERM) Skill

This skill guides the agent through an end-to-end enterprise risk assessment workflow based on explicit context, customizable scoping, rigorous cause-and-effect controls, and automated sorted CSV data outputs.

## When to Use This Skill
- When conducting a strategic, financial, operational, or enterprise (encompassing strategic, financial, and operational) risk assessment.
- When generating a comprehensive, spreadsheet-compatible risk register featuring cause-effect analysis, risk rating, risk ranking, and preventive and reductive risk treatment control measures.

## Required User Configuration Parameters
Before running, check the user prompt for these parameters. If not provided, apply the default fallback values:
1. **Number of Risks (`N`)**: Default to **10 risks** if not explicitly specified.
2. **Risk Category Filter**: Filter strictly by the chosen scope:
   - `Strategic`: Core macro-level business strategy threats.
   - `Operational`: Any threat affecting day-to-day operations, including **business operations, IT, HR, legal, compliance, security, business continuity**.
   - `Financial`: Fiscal exposure, liquidity, market pricing, credit, and financial sustainability.
   - `Enterprise`: A balanced mix of the top risks spanning the Strategic, Operational, and Financial categories. This is the default scope if none is specified.
3. **Risk Context**: The context to conduct the risk assessment on, which can be a project, a scenario, a company profile etc. If the context is not clear, prompt the user for details of the context.

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

## Automated File Persistence & Output Formatting
Upon processing, the agent **MUST** automatically create (or overwrite) a clean, standard comma-separated values file named **`risk_register.csv`** in the active workspace directory, injecting the exact sorted 14-column tabular content layout specified in the Standard Template below.

- **Zero-Whitespace Commas (CRITICAL)**: Separate fields strictly with a single comma character (`,`). Do NOT output a space after any comma delimiter. The next character or opening quote must snap directly to the comma (use `,"* Text` and NEVER `, "* Text`).
- **Strict Cell Encapsulation**: Wrap any text field containing physical line breaks, asterisks, or commas entirely in absolute double quotes (`"..."`) from the very first character to the very last character of that field.
- **Physical Multi-line Records**: Use actual, physical newlines (carriage returns) inside the double quotes to represent multi-line records. Never output an unquoted line break, as it will break the spreadsheet row integrity.

### Standard Template Layout for `risk_register.csv`

```csv
Risk ID,Category,Risk Title,Description,Causes (Drivers),Effects (Consequences),Likelihood Scale (1-5),Likelihood,Impact Scale (1-5),Impact,Risk Score,Risk Criticality,Preventive Controls,Reductive Controls
R1,Strategic,Inability to capitalise on the advancement of AI,Unable to harness the advancement of AI to maintain the lead position of the company.,"* Lack of AI talent.
* Pace of AI advancement too rapid to keep abreast.",* Loss of business to competitors.,4,Likely,5,Severe,20,CRITICAL,"* Increase headcount budget for AI talents.
* Create an AI task force to oversee the deployment of AI in the company.",* Incorporate AI capabilities in the company's star products.
R2,Operational,Cyber attack on the crown jewel system,Cyber attack on the company's crown jewel system causing operational downtime and data breach.,"* Outdated firewall software.
* Weak employee password policies.","* Business standstill if the crown jewel system is down.
* Leakage of customers data.
* Regulatory non-compliance penalties.
* Public reputational damage.",3,Possible,5,Severe,15,HIGH,"* Deploy automated weekly patch cycles.
* Enforce mandatory multi-factor authentication.","* Develop a Business Continuity Plan for the crown jewel system.
* Encrypt all customer data on the crown jewel system.
* Maintain a standalone cyber insurance policy.
* Prepare a pre-approved PR response playbook."
R3,Financial,Unfavourable FX Exchange Volatility,Unfavourable FX exchange affecting the global revenue.,* Macroeconomic shift in fiscal policy.,* Revenue losses.,2,Unlikely,4,High,8,MODERATE,* Monitor treasury forex indices weekly.,* Secure fixed forward-hedging capital contracts.
R4,Operational,Major fire at warehouse,Major fire at the main warehouse during the peak season with heavy damage.,"* Lack of fire control discipline.
* Staff negligence.",* Revenue losses.,2,Unlikely,4,High,8,MODERATE,"* Conduct annual fire risk assessment.
* Conduct annual staff refresher training on fire prevention and conduct fire drill.
* Review BCP annually to ensure up-to-date.",* Ensure fire insurance is inforce.
```
