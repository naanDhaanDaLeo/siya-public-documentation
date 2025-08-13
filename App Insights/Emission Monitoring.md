# Emission Monitoring Dashboards

Our emission monitoring dashboards provide comprehensive environmental compliance tracking for maritime vessels through two key performance indicators: **Energy Efficiency Operational Indicator (EEOI)** and **Carbon Intensity Indicator (CII)**. These dashboards are specifically designed to help fleet operators monitor, analyze, and improve their vessels' environmental performance while ensuring compliance with international maritime regulations.

## Data Sources

The dashboards integrate data from multiple sources to provide accurate and comprehensive emissions analysis:

- **ERP System**: Primary source for vessel operational data, fuel consumption records, and voyage information
- **Vessel Voyage Data**: Complete voyage tracking including distances, cargo loads, and operational profiles  
- **Fuel Consumption Records**: Detailed consumption data for all fuel types and grades
- **Vessel Events**: Comprehensive activity tracking (Sea passage, Harbor steaming, Anchorage, Port operations)
- **Vessel Particulars**: Technical specifications and deadweight tonnage
- **IMO DCS Data**: International Maritime Organization Data Collection System records

## EEOI Dashboard - Energy Efficiency Operational Indicator

### Purpose
The EEOI dashboard calculates and monitors the energy efficiency of vessel operations by measuring CO₂ emissions per unit of transport work. This metric helps operators understand fuel efficiency performance across different voyages and optimize operational strategies.

### Key Features
- **Voyage-specific EEOI calculations** for loaded voyages only
- **Historical trend analysis** showing EEOI performance over the last six voyages
- **Interactive visualizations** with detailed voyage breakdowns
- **Automated reporting** with methodology explanations

### EEOI Calculation Methodology

#### Eligibility Criteria
- Only **loaded voyages** are considered for EEOI calculation
- A voyage is classified as "loaded" when cargo quantity exceeds **500 MT**

#### Formula
```bash
EEOI = Σᵢⱼ (FCᵢⱼ × CFⱼ) / Σᵢ (m_cargoᵢ × Dᵢ)
```

**Where:**
- **FCᵢⱼ**: Fuel consumption for fuel type j during voyage i
- **CFⱼ**: Fuel mass-to-CO₂ conversion factor for fuel type j  
- **m_cargoᵢ**: Cargo mass (in tonnes) for voyage i
- **Dᵢ**: Distance sailed (in nautical miles) for voyage i

#### CO₂ Conversion Factors

| Fuel Type | Specification | Carbon Content | CF (t CO₂ / t Fuel) |
|-----------|---------------|----------------|---------------------|
| Heavy Fuel Oil (HSFO) | ISO 8217 RME-RMK | 0.85 | 3.114 |
| Ultra Low Sulfur Fuel Oil (ULSFO) | ISO 8217 RMA-RMD | 0.86 | 3.151 |
| Marine Diesel Oil (MDO) | ISO 8217 DMX-DMC | 0.875 | 3.206 |
| Low Sulfur Marine Gas Oil (LSMGO) | ISO 8217 DMX-DMC | 0.875 | 3.206 |
| Very Low Sulfur Fuel Oil (VLSFO) | ISO 8217 RMA-RMD | 0.86 | 3.151 |
| Liquefied Natural Gas (LNG) | Natural Gas | 0.75 | 2.931 |

#### Example Calculation
```python
# For a voyage with mixed fuel consumption:
EEOI = [(HSFO(20t) × 3.114) + (MDO(5t) × 3.206)] / (25,000t × 750NM)
EEOI = [62.28 + 16.03] / 18,750,000 = 4.18 gCO₂/t.nm
```

**Result Unit**: gCO₂/t.nm (grams of CO₂ per tonne-nautical mile)

## CII Dashboard - Carbon Intensity Indicator

### Purpose
The CII dashboard calculates annual carbon intensity ratings (A, B, C, D, E) for vessels in accordance with IMO regulations. This system helps operators track their vessels' environmental performance against international standards and plan for future compliance requirements.

### Key Features
- **Annual CII rating calculations** with A-E classification system
- **Year-over-year projections** through 2027
- **Required vs. Attained CII analysis** 
- **Boundary calculations** for rating thresholds
- **Vessel operating profile analysis**

### CII Calculation Methodology

#### Attained CII Formula
```bash
Attained CII = M / (Capacity × Distance) × 1,000,000
```

**Where:**
- **M**: Total CO₂ emissions (tonnes) = Σ(Fuel Consumption × Conversion Factor)
- **Capacity**: Summer deadweight tonnage
- **Distance**: Total distance traveled (nautical miles)

#### Required CII Formula
```bash
Required CII = ((100 - Z) / 100) × CII_ref
```

**Where:**
- **Z**: Reduction factor based on year (increases annually from 2020)
- **CII_ref**: Reference CII value based on vessel type and capacity

#### Z-Factor (Reduction Factor)
```python
def get_z_factor(year):
    if year == 2020: return 1
    elif year == 2021: return 2  
    elif year == 2022: return 3
    elif year >= 2023: return min(3 + 2*(year-2022), 100)
```

#### CII Reference Calculation
```bash
CII_ref = a × Capacity^(-c)
```

**Reference Values by Vessel Type:**

| Vessel Type | a | c |
|-------------|---|---|
| Bulk Carrier | 4745 | 0.622 |
| Tanker | 5247 | 0.610 |
| Container | 1984 | 0.489 |
| Gas Carrier | 8104 / 14405×10⁷ | 0.639 / 2.071 |

#### Rating Boundaries
The CII rating is determined by comparing the attained CII against calculated boundaries:

```python
# Boundary calculations using d-vectors
Superior Boundary = Required_CII × d1    # Rating A
Lower Boundary = Required_CII × d2       # Rating B  
Upper Boundary = Required_CII × d3       # Rating C
Inferior Boundary = Required_CII × d4    # Rating D
# Above Inferior Boundary = Rating E
```

#### Rating Classification Logic
```python
def calculate_rating(attained_cii, boundaries):
    if attained_cii <= boundaries['superior']: return 'A'
    elif attained_cii <= boundaries['lower']: return 'B' 
    elif attained_cii <= boundaries['upper']: return 'C'
    elif attained_cii <= boundaries['inferior']: return 'D'
    else: return 'E'
```

## Dashboard Outputs

### EEOI Dashboard Delivers:
- **Latest voyage EEOI values** with trend analysis
- **Six-voyage historical comparison** charts
- **Fuel efficiency insights** by voyage and fuel type
- **Operational recommendations** for efficiency improvements

#### EEOI Dashboard Example

**Voyage EEOI Performance Analysis**
<iframe 
    src="https://github.com/user-attachments/assets/feab15b5-fba9-4991-bbcf-101bdfae0709"
    width="1210"
    height="478"
    title="EEOI per Voyage"
    style="border:none;">
</iframe>
*Interactive bar chart displaying EEOI values for the last six loaded voyages, enabling operators to identify efficiency trends and optimize future voyage planning*

### CII Dashboard Delivers:
- **Current year CII rating** (A through E classification)
- **Multi-year projections** (2024-2027) based on current performance
- **Required vs. Attained CII comparison**
- **Boundary analysis** showing proximity to rating thresholds
- **Vessel operating profile** with efficiency trends

#### CII Dashboard Examples

**Year-to-Date CII Rating Tracking**
<iframe 
    src="https://github.com/user-attachments/assets/d878cff1-4d7a-464d-a4d5-45183e54d150"
    width="1210"
    height="429"
    title="YTD Live CII Rating"
    style="border:none;">
</iframe>

*Real-time CII performance monitoring showing attained CII values against rating boundaries (A-E) throughout the year*

**Multi-Year CII Projections**
<iframe 
    src="https://github.com/user-attachments/assets/73ba4897-d60b-4e7e-a1c5-a14df9b4ad8a"
    width="1210"
    height="429"
    title="CII Projection using IMODCS verified Data"
    style="border:none;">
</iframe>

*Long-term CII forecasting displaying projected performance against tightening regulatory requirements through 2027*

**Vessel Operation Profile Analysis**
<iframe 
    src="https://github.com/user-attachments/assets/c2d528a7-bb30-4766-939c-802d309abeef"
    width="1219"
    height="637"
    title="Vessel Activity Profile"
    style="border:none;">
</iframe>

*Live Year-to-Date comprehensive analysis of vessel operations directly contributing to changes in CII calculations*

## Benefits

### Operational Benefits
- **Real-time environmental performance monitoring**
- **Predictive compliance tracking** for future regulations
- **Fuel efficiency optimization** through data-driven insights
- **Voyage planning support** with emissions forecasting

### Regulatory Compliance
- **IMO CII regulation compliance** with automated rating calculations
- **EEOI reporting** for operational efficiency documentation
- **Historical data retention** for regulatory audits
- **Standardized calculation methodologies** following international guidelines

### Business Value
- **Cost reduction** through fuel efficiency improvements
- **Risk management** with early warning systems for poor ratings
- **Fleet optimization** through comparative vessel performance analysis
- **Sustainability reporting** with comprehensive emissions data

---

*These emission monitoring dashboards provide essential tools for maritime operators to maintain environmental compliance, optimize operational efficiency, and support sustainable shipping practices through data-driven decision making.*
