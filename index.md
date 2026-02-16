---
layout: default
title: Solar + Storage Project Formula Guide
---

# ☀️ Solar + Storage: Financial Logic & Formulas
This documentation defines the calculation steps for PPA Rates, Site Leases, and Build Costs for integrated Solar + Battery projects.

---

## 📋 1. Core Inputs & Assumptions

### User Inputs Required
* **PV:** System size ($kW$)
* **Build Cost:** Total solar cost ($)
* **PPA Rate Esc:** Annual escalator (%)
* **t:** PPA Term (years)
* **Battery Power:** $kW$ (AC)
* **Battery Energy:** $kWh$
* **Battery Cost:** $/kWh$

### Standard Assumptions
* **IRR:** $9\%$
* **Degradation ($dg$):** $0.5\%$
* **ITC Value:** $30\% \times \text{Solar Capex} \times 97\% \times 90\%$ (Note: $90\%$ represents ITC transfer loss)

---

## 🧮 2. Step-by-Step PPA Calculation

### Step 1: Solar Cost
$$\text{Solar Cost} = \text{Build Cost ($/W)} \times PV(kW) \times 1000$$

### Step 2: Operating Expenses (OpEx)
**Insurance Cost:**
* If State = California: $1.1\times$ multiplier.
* If Coastal = Yes: $1.25\times$ multiplier.
$$\text{Insurance} = \text{Solar Cost} \times 0.003 \times \text{StateVar} \times \text{CoastalVar}$$

**Asset Management:**
$$\text{Asset Mgmt} = \min(25000, 7\times PV, 6000 + (2\times PV))$$

### Step 3: O&M Rate Interpolation (Logic)
The system uses a linear interpolation between size brackets ($100kW$ to $7000kW$).

| Size Header ($V1$) | 100 | 250 | 500 | 1000 | 3000 | 5000 | 7000 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Solar Rate** ($V2$) | 30 | 25 | 18 | 14 | 8.5 | 8.5 | 8 |
| **Storage Rate** ($V3$) | 32 | 32 | 25 | 23 | 17.5 | 17.5 | 17 |

**Interpolation Formula:**
$$y = y_1 + \frac{(x - x_1)(y_2 - y_1)}{(x_2 - x_1)}$$

---

## 🔋 3. Battery & Total Capex
### Solar Capex Calculation
$$\text{Solar Capex} = \text{Solar Cost} + \$50k + 3\%(\text{Solar Cost}) + 20\%(\text{Subtotal})$$

### Battery System Cost
$$\text{Battery Cost} = \text{Energy Capacity (kWh)} \times \text{Cost per kWh}$$

### Total System Cost
$$\text{Total Cost} = \text{Solar Capex} + \text{Battery Capex}$$

---

## 📝 4. Final PPA Rate Formula
To solve for the PPA Rate, the model adjusts for battery efficiency loss ($10\%$).

$$\text{PPA Rate} = - \text{Total Cost} + (1+IRR) \times ITC + (\text{Prod}_{\text{adj}} \times PPA_1) - \text{OPEX}$$
$$\sum_{t=2}^{T} (1+IRR)^t [(\text{Prod}_{\text{adj}} \times (1-dg)^{t-1} \times (PPA_1 \times (1+Esc)^{t-1}))] - [OPEX_1^{t-1}]$$

---

## 🏗️ 5. Build Cost per Watt (Solar + Storage)
To find the required build cost for a target PPA:

$$\text{Build Cost/W} = \frac{\text{Total Capex} - 60,000}{1.236 \times PV \times 1000}$$
