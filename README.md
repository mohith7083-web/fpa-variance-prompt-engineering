# Case Study: Automating FP&A Variance Analysis via Chain-of-Thought (CoT) Prompting

## 📌 Project Objective
Large Language Models (LLMs) frequently hallucinate financial calculations or provide overly generic summaries when asked to analyze P&L (Profit and Loss) statements. 

The objective of this project is to demonstrate how **Chain-of-Thought (CoT) Prompt Engineering** can be used to force an AI model to mimic the step-by-step reasoning of a human financial analyst. By structuring the AI's internal logic, we can automate Budget vs. Actuals (BvA) variance analysis with high accuracy and strict adherence to business rules.

## 📊 The Input Data (Sample E-Commerce P&L)
I provided the LLM with a highly simplified, raw CSV format of Q3 E-Commerce Performance data:

```csv
Line_Item, Budget_Q3, Actual_Q3, Budget_Volume, Actual_Volume
Gross Revenue, $500000, $460000, 10000 units, 9500 units
Cost of Goods Sold (COGS), $200000, $210000, 10000 units, 9500 units
Shipping Costs, $50000, $62000, 10000 units, 9500 units
Marketing Spend, $80000, $78000, N/A, N/A
❌ Test 1: Zero-Shot Prompting (The Baseline Failure)
First, I tested standard prompting (Zero-Shot) to establish a baseline of how standard AI handles financial data.
The Prompt: "Analyze the variance between the Q3 Budget and Actuals for this e-commerce data. Tell me what went wrong."
The AI's Flawed Output: The AI correctly noticed Revenue was down and COGS was up. However, it jumped straight to a conclusion, providing a generic summary: "Revenue decreased while costs increased, leading to lower profitability. The company should cut shipping costs."
The Problem: It failed to calculate the exact variance percentages, ignored the unit volume data, and provided no deep root-cause decomposition.
✅ Test 2: Chain-of-Thought (CoT) Prompting (The Solution)
To fix this, I engineered a CoT prompt. This prompt does not just ask for an answer; it dictates the exact analytical sequence the AI must follow.
The Engineered CoT Prompt:
"Act as a Senior FP&A Analyst. Analyze the provided Budget vs. Actuals data by strictly following these sequential steps. Do not skip any steps.
Step 1: Calculate the absolute variance and percentage variance for each line item. Mark them as Favorable (F) or Unfavorable (U).
Step 2: Apply a Materiality Threshold. Filter out any line item where the variance is less than 5%; do not analyze these items further.
Step 3: For the remaining material variances, decompose the variance. Separate the Volume Variance (change driven by units sold) from the Price/Rate Variance (change driven by cost per unit).
Step 4: Based on the decomposition, write a concise Root Cause Narrative for business stakeholders.
Execute Step 1 now, show your work, and then proceed to the next steps."
📈 The CoT AI Output (Validated Result)
Because the LLM was forced to "think" step-by-step, it generated a highly accurate, enterprise-grade response:
[Step 1 & 2: Variance Calculation & Materiality Check]
Gross Revenue: -$40,000 (-8.0%) ➔ (U) | Materiality > 5% (Included)
COGS: +$10,000 (+5.0%) ➔ (U) | Materiality >= 5% (Included)
Shipping Costs: +$12,000 (+24.0%) ➔ (U) | Materiality > 5% (Included)
Marketing Spend: -$2,000 (-2.5%) ➔ (F) | Materiality < 5% (Ignored)
[Step 3: Variance Decomposition]
Revenue Volume Variance: 500 fewer units sold at a budgeted price of $50/unit = -$25,000 (U).
Revenue Price Variance: The remaining -$15,000 (U) was caused by a drop in the actual selling price (discounting).
Shipping Rate Variance: Volume decreased by 5%, yet shipping costs increased by 24%, indicating a severe spike in the per-unit shipping rate (from $5.00/unit budgeted to $6.52/unit actual).
[Step 4: Root Cause Narrative]
The Q3 profitability miss was driven by a compounding margin squeeze. The business sold fewer units than budgeted (Volume Variance) but had to discount the selling price to achieve even those 9,500 sales (Price Variance). Simultaneously, supply chain economics deteriorated; despite shipping fewer units, the rate per shipment spiked by $1.52, driving a 24% unfavorable shipping variance.
🎯 Conclusion & Business Value
By utilizing Chain-of-Thought prompting, I constrained the LLM's natural tendency to summarize. The CoT framework forced the model to execute a standard accounting decomposition workflow, resulting in zero mathematical hallucinations and generating insights that are immediately usable by a finance team to identify supply-chain cost spikes.
