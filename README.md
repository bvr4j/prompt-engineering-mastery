# Prompt Engineering Mastery — Practical Experiments

## 1. Overview

This project evaluates three prompt-engineering techniques using Google AI Studio:

1. Zero-shot vs. few-shot prompting
2. Direct prompting vs. step-by-step reasoning instructions
3. Structured JSON output using a defined schema

The experiments were conducted using Gemini 4 Hydrogen (`gemini-4-hydrogen`) with the thinking level set to Medium.

The objective was to observe how prompt design affects classification accuracy, reasoning transparency, and output consistency.

---

## 2. Experiment 1 — Zero-shot vs. Few-shot Prompting

### Objective

Compare sentiment classification without examples (zero-shot) against classification with five labeled examples (few-shot).

### Task

Classify customer reviews into exactly one category:

- Positive
- Negative
- Neutral

### 2.1 Zero-shot Prompt

```text
Classify the sentiment of the following customer review into exactly one category: Positive, Negative, or Neutral.

Review: "The delivery was quick, but the product quality was disappointing."

Return only the category. Do not provide an explanation.
```

### Actual Zero-shot Output

```text
Negative
```

### 2.2 Few-shot Prompt

```text
Classify the sentiment of each customer review into exactly one category: Positive, Negative, or Neutral.

Examples:

Review: "Excellent quality and very fast delivery. Completely satisfied."
Output: Positive

Review: "The product stopped working after two days. Very disappointed."
Output: Negative

Review: "The package arrived yesterday."
Output: Neutral

Review: "Good price, but the quality is not as advertised."
Output: Negative

Review: "Works exactly as expected. I would purchase again."
Output: Positive

Now classify:

Review: "The delivery was quick, but the product quality was disappointing."

Return only one category. Do not provide an explanation.
```

### Actual Few-shot Output

```text
Negative
```

### 2.3 Five-review evaluation dataset

The same five test reviews were evaluated using zero-shot and few-shot prompting.

| Review | Expected | Zero-shot | Few-shot |
|---|---|---|---|
| Absolutely love this product. The battery lasts all day. | Positive | Positive | Positive |
| The item arrived broken and customer support never responded. | Negative | Negative | Negative |
| The package was delivered on Monday. | Neutral | Neutral | Neutral |
| The design is beautiful, but the performance is terrible. | Negative | Negative | Negative |
| Affordable, reliable, and exactly what I needed. | Positive | Positive | Positive |

### Actual Zero-shot Batch Output

```text
1. Positive
2. Negative
3. Neutral
4. Negative
5. Positive
```

### Actual Few-shot Batch Output

```text
1. Positive
2. Negative
3. Neutral
4. Negative
5. Positive
```

### Results

| Technique | Correct | Accuracy |
|---|---:|---:|
| Zero-shot | 5/5 | 100% |
| Few-shot | 5/5 | 100% |

### Analysis

Both approaches achieved identical accuracy on the five-review dataset.

Zero-shot prompting was sufficient for this straightforward classification task. Few-shot examples reinforced the expected categories and response format but did not produce a measurable improvement in accuracy.

This result does not establish that few-shot prompting is unnecessary in general. A larger and more ambiguous dataset would be needed to evaluate that claim.

---

## 3. Experiment 2 — Direct Prompting vs. Step-by-Step Reasoning

### Objective

Compare a direct mathematical prompt against a prompt explicitly requesting intermediate calculations and verification.

### Problem

A retailer purchases 120 headphones at ₹800 each. It sells 75 headphones at a 25% profit and the remaining headphones at a 10% loss.

Calculate:

1. Total purchase cost.
2. Selling price of the first 75 headphones.
3. Selling price of the remaining headphones.
4. Overall profit or loss percentage.

### 3.1 Direct Prompt

```text
A retailer purchases 120 headphones at ₹800 each. It sells 75 headphones at a 25% profit and the remaining headphones at a 10% loss.

Calculate:
1. Total purchase cost.
2. Selling price of the first 75 headphones.
3. Selling price of the remaining headphones.
4. Overall profit or loss percentage.

Give the final numerical answers.
```

### Actual Direct Output

```text
1. Total purchase cost: ₹96,000

2. Selling price of the first 75 headphones:
₹75,000 (₹1,000 per headphone)

3. Selling price of the remaining 45 headphones:
₹32,400 (₹720 per headphone)

4. Overall profit or loss percentage:
11.875% profit (₹11,400 total profit)
```

### 3.2 Step-by-Step Prompt

```text
Solve the following multi-step business mathematics problem.

Work through the calculations step by step. Show the formula and intermediate values for each part. Verify the final profit or loss by comparing total selling price with total purchase cost.

A retailer purchases 120 headphones at ₹800 each. It sells 75 headphones at a 25% profit and the remaining headphones at a 10% loss.

Calculate:
1. Total purchase cost.
2. Selling price of the first 75 headphones.
3. Selling price of the remaining headphones.
4. Overall profit or loss percentage.

Present the final answers clearly.
```

### Actual Step-by-Step Output

The model showed the following calculation results:

```text
Total SP = ₹75,000 + ₹32,400 = ₹107,400

Since Total SP (₹107,400) > Total CP (₹96,000),
the retailer makes an overall profit.

Overall Profit = Total SP − Total CP
               = ₹107,400 − ₹96,000
               = ₹11,400

Overall Profit % = (Overall Profit / Total CP) × 100

                 = (11,400 / 96,000) × 100

                 = 11.875%
```

Final answers displayed by the model:

```text
1. Total purchase cost: ₹96,000
2. Selling price of the first 75 headphones: ₹75,000
3. Selling price of the remaining 45 headphones: ₹32,400
4. Overall profit or loss percentage: 11.875% profit
```

### Independent Verification

Total purchase cost:

120 × 800 = ₹96,000

First 75 headphones:

800 × 1.25 = ₹1,000 per headphone

75 × 1,000 = ₹75,000

Remaining 45 headphones:

800 × 0.90 = ₹720 per headphone

45 × 720 = ₹32,400

Total selling price:

₹75,000 + ₹32,400 = ₹107,400

Profit:

₹107,400 − ₹96,000 = ₹11,400

Profit percentage:

(11,400 / 96,000) × 100 = 11.875%

### Results

| Evaluation | Direct | Step-by-step |
|---|---|---|
| Numerical accuracy | Correct | Correct |
| Intermediate calculations | Limited | Detailed |
| Verification | Not explicit | Explicit |
| Final profit percentage | 11.875% | 11.875% |

### Analysis

Both prompts produced the correct mathematical answer.

The direct prompt was sufficient to obtain the numerical results. The step-by-step instruction improved calculation transparency by requiring formulas, intermediate values, and a final comparison of total selling price with total purchase cost.

In this experiment, step-by-step prompting improved explainability rather than numerical accuracy.

---

## 4. Experiment 3 — Structured JSON Output

### Objective

Evaluate whether a model can consistently extract invoice information from unstructured text and return valid JSON matching a predefined schema.

### Model Configuration

- Model: Gemini 4 Hydrogen
- Thinking level: Medium
- Google AI Studio Structured Outputs: Enabled

### 4.1 Output Schema

The following schema was configured using the Structured Outputs editor:

```json
{
  "type": "object",
  "properties": {
    "customer_name": {
      "type": "string",
      "description": "Customer name, empty string if missing"
    },
    "invoice_date": {
      "type": "string",
      "description": "Date in YYYY-MM-DD format, empty string if missing"
    },
    "amount": {
      "type": "number",
      "description": "Final invoice amount payable, 0 if missing"
    },
    "currency": {
      "type": "string",
      "description": "ISO 4217 currency code, empty string if unknown"
    }
  },
  "required": [
    "customer_name",
    "invoice_date",
    "amount",
    "currency"
  ]
}
```

### 4.2 Extraction Prompt

```text
You are an invoice information extraction system.

Extract these fields:
- customer_name
- invoice_date
- amount
- currency

Rules:
1. Return JSON matching the configured schema.
2. Use an empty string when information is missing.
3. Use 0 when the amount is missing.
4. Never invent information.
5. Convert dates into YYYY-MM-DD format.
6. Amount must be a JSON number.
7. Currency must use an ISO 4217 currency code.
8. Extract the final invoice amount payable, not individual line-item amounts.
```

### 4.3 Test Inputs and Actual Outputs

#### Test 1 — Indian invoice

Input:

```text
Invoice No: INV-2026-101

Bill To: Rahul Sharma

Invoice Date: 12 November 2026

Description: Laptop repair and servicing

Subtotal: ₹8,000
GST: ₹1,440
Total Amount Due: ₹9,440

Payment Status: Pending
```

Actual output:

```json
{
  "customer_name": "Rahul Sharma",
  "invoice_date": "2026-11-12",
  "amount": 9440,
  "currency": "INR"
}
```

Result: PASS

#### Test 2 — Different date and invoice wording

Input:

```text
Invoice No: BILL-204

Customer: Priya Mehta

Date: 5 October 2026

Services: Website maintenance and technical support

Amount payable: ₹15,500

Payment: Completed
```

Actual output:

```json
{
  "customer_name": "Priya Mehta",
  "invoice_date": "2026-10-05",
  "amount": 15500,
  "currency": "INR"
}
```

Result: PASS

#### Test 3 — Missing customer name and date

Input:

```text
Receipt No: RC-7781

A customer purchased office supplies for a total of ₹3,250, including all applicable taxes.

Payment was made using UPI.

No customer name or invoice date is mentioned.
```

Actual output:

```json
{
  "customer_name": "",
  "invoice_date": "",
  "amount": 3250,
  "currency": "INR"
}
```

Result: PASS

#### Test 4 — International invoice

Input:

```text
INVOICE #US-9082

Billed to: Michael Anderson

Issued on: March 18, 2026

Consulting services: $1,200
Additional service fee: $300

Total payable: $1,500 USD

Payment due within 30 days.
```

Actual output:

```json
{
  "customer_name": "Michael Anderson",
  "invoice_date": "2026-03-18",
  "amount": 1500,
  "currency": "USD"
}
```

Result: PASS

#### Test 5 — Conflicting monetary values

Input:

```text
Invoice Reference: TAX-5509

Customer Name: Ananya Rao

Invoice Date: 22 September 2026

Product Charges: ₹12,000
Discount: ₹2,000
Tax: ₹1,800

Final Amount Payable: ₹11,800

Payment Status: Unpaid
```

Actual output:

```json
{
  "customer_name": "Ananya Rao",
  "invoice_date": "2026-09-22",
  "amount": 11800,
  "currency": "INR"
}
```

Result: PASS

### 4.4 Results

| Test | Correct fields | JSON structure | Result |
|---|---|---|---|
| 1 | 4/4 | Valid | PASS |
| 2 | 4/4 | Valid | PASS |
| 3 | 4/4 | Valid | PASS |
| 4 | 4/4 | Valid | PASS |
| 5 | 4/4 | Valid | PASS |

Observed success rate: 5/5 (100%).

### Analysis

Structured Outputs constrained the response to the four configured fields and their required data types.

The model correctly handled date normalization, missing information, currency identification, and conflicting monetary values. Explicit extraction rules combined with schema enforcement produced consistent JSON responses across all five tests.

The evaluation is limited to five test cases and does not establish universal reliability.

---

## 5. Overall Findings

| Technique | Evaluation | Main observation |
|---|---|---|
| Zero-shot prompting | 5/5 correct | Clear instructions were sufficient for this classification dataset. |
| Few-shot prompting | 5/5 correct | Examples reinforced the format but did not improve measured accuracy. |
| Direct prompting | Correct | Produced the correct mathematical result concisely. |
| Step-by-step prompting | Correct | Improved calculation transparency and verification. |
| Structured Outputs | 5/5 successful | Produced schema-constrained JSON across all five inputs. |

### Key Takeaways

1. Prompt quality depends on the task. Simple classification did not require examples in this small evaluation.
2. Few-shot prompting can demonstrate a desired output pattern, but examples do not guarantee higher accuracy.
3. Explicit step-by-step instructions can make mathematical answers easier to inspect and verify.
4. Structured Outputs are useful when downstream systems require predictable data formats.
5. Missing-value rules and explicit field definitions reduce ambiguity during information extraction.

---

## 6. Tools and Resources

- Google AI Studio: https://aistudio.google.com/
- Google Gemini models
- Structured Outputs configuration in Google AI Studio
- OpenAI Prompt Engineering Guide: https://developers.openai.com/api/docs/guides/prompt-engineering?api-mode=responses

---

## 7. Limitations

- The classification evaluation used only five test reviews.
- The mathematical evaluation used one problem.
- The structured-output evaluation used five invoice-like inputs.
- All experiments used one model configuration and one thinking level.
- Results may differ with other models, settings, prompts, or datasets.

The reported accuracy figures describe only the experiments conducted here. They should not be interpreted as general model performance guarantees.

---

## 8. Conclusion

These experiments demonstrate practical applications of zero-shot prompting, few-shot prompting, step-by-step instructions, and schema-constrained structured output.

The results show that prompt design affects not only the answer itself but also its consistency, interpretability, and suitability for downstream applications.

The most significant observation was that structured output constraints produced consistent machine-readable responses across all five invoice tests, while step-by-step instructions improved the transparency of an already-correct mathematical answer.
