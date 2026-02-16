# Solar + Storage Project Formula Guide

This document defines the step-by-step financial logic for Solar + Storage projects, covering PPA rates, Site Lease calculations, and Build Cost per Watt.

---

## 📥 1. Inputs & Assumptions

### Prompt Inputs Required
* **PV:** System size in $kW$.
* **Build Cost:** Total cost in $.
* **PPA Rate Esc.:** Annual escalator in %.
* **t:** PPA Term (years).
* **Annual Production:** Expected $kWh$.
* **Battery Power:** $kW$ (AC).
* **Battery Energy:** $kWh$.
* **Battery Cost:** $/kWh$.

### Financial Assumptions
* **PPA_1:** $0.10$ (Standard) or $0.13$ (Lease context).
* **IRR:** $9\%$.
* **Degradation ($dg$):** $0.5\%$.
* **ITC Value:** $$30\% \times \text{Solar Capex} \times 97\% \times 90\%$$ *(Note: 90% factor accounts for ITC transfer loss)*.

---

## ⚡ 2. Step-by-Step PPA Formula

### Step 1: Solar Cost
$$\text{Solar Cost} = \text{Build Cost ($/W)} \times PV(kW) \times 1000$$

### Step 2: Insurance & Asset Management
**Insurance:**
* If State = California: $1.1\times$
* If Coastal = Yes: $1.25\times$
$$\text{Insurance} = \text{Solar Cost} \times 0.003 \times \text{State\_Var} \times \text{Coastal\_Var}$$

**Asset Management:**
$$\text{Asset Mgmt} = \min(25000, 7 \times \frac{PV}{1000}, 6000 + (2 \times \frac{PV}{1000}))$$

### Step 3: Battery System Capex
$$\text{Battery Cost} = \text{Battery Energy (kWh)} \times \text{Battery Unit Cost ($/kWh)}$$

---

## 📊 3. OpEx & O&M Interpolation Logic

For systems between $100 kW$ and $6999 kW$, the rate is interpolated.

| Array Name | Description | Values ($V1, V2, V3$) |
| :--- | :--- | :--- |
| **SIZE_HEADERS** | Rate Brackets | `[100, 250, 500, 1000, 3000, 5000, 7000]` |
| **SOLAR_RATES** | O&M for Solar | `[30, 25, 18, 14, 8.5, 8.5, 8]` |
| **STORAGE_RATES** | O&M for S+S | `[32, 32, 25, 23, 17.5, 17.5, 17]` |

**The Interpolation Formula:**
$$y = y_1 + \frac{(x - x_1) \times (y_2 - y_1)}{(x_2 - x_1)}$$

* **$x1$:** The largest value in $V1$ less than or equal to system size.
* **$x2$:** The value in $V1$ immediately larger than $x1$.
* **$y1/y2$:** Corresponding rates from $V2$ (Solar) or $V3$ (Storage).

---

## 🔋 4. Final Capex & PPA Rate

### Total System Cost
1. **Solar Capex:** $$\text{Solar Cost} + \$50k + 3\%(\text{Solar Cost}) + 20\%(\text{Subtotal})$$
2. **Battery Capex:** $$\text{Battery Cost} + 3\%(\text{Battery Cost}) + 20\%(\text{Battery Cost} + 3\% \text{ of Solar})$$
3. **Total Cost:** Solar Capex + Battery Capex.

### Adjusted Production
* **Annual Discharge:** $\text{Battery kWh} \times 365$.
* **Efficiency Loss:** $\text{Annual Discharge} \times 10\%$.
* **Adjusted Production:** $\text{Solar Production} - \text{Efficiency Loss}$.

### Final PPA Rate Formula
$$PPA = -\text{Total Cost} + (1+IRR) \times ITC + (\text{Prod}_{\text{adj}} \times PPA_1) - \text{OPEX}$$
$$+ \sum_{t=2}^{T} (1+IRR)^t [(\text{Prod}_{\text{adj}} \times (1-dg)^{t-1}) \times (PPA_1 \times (1+Esc)^{t-1})] - [OPEX_1^{t-1}]$$

---

## 🏗️ 5. Build Cost per Watt (Solar + Storage)

To reverse-engineer the build cost based on the integrated Capex:

$$\text{Build Cost/W} = \frac{(\text{Total Capex} - 60,000)}{(1.236 \times PV \times 1000)}$$

---
*Last Updated: February 2026*
