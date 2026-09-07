# Credit Card Customer Segmentation

Unsupervised clustering analysis segmenting AllLife Bank's credit card customers by financial capacity and channel behaviour, to support targeted marketing campaigns and a service delivery model upgrade.

📄 **[Full business report (PDF)](CreditCardCustomerSegmentation_Report_Updated.pdf)** — 60 pages, complete methodology and recommendations
📓 **[Analysis notebook](CreditCardCustomerSegmentation.ipynb)**

---

## Business problem

AllLife Bank wanted to improve market penetration and fix a poor customer-support perception. Two stakeholders, two questions:

- **Head of Marketing** — which customer groups exist, and what should each be offered?
- **Head of Delivery** — how does each group prefer to contact the bank, so support can be routed accordingly?

## Data

660 customers, 7 numerical features: average credit limit, total credit cards, and interaction counts across three channels (branch visits, online logins, calls made).

*Dataset provided as part of the UT Austin (McCombs) Post Graduate Program in Data Science. Not redistributed here.*

## Approach

**Exploratory analysis** — univariate distributions, CDF plots, correlation heatmap, hexbin jointplots and a 4D bubble view. The dominant finding: **channel substitution**. A negative correlation of −0.55 between digital and branch engagement, with large zero-engagement cohorts on every channel (21.8% never log in online, 15.2% never visit a branch). Customers commit to one channel and abandon the rest.

**Preprocessing**
- 95th-percentile capping (Winsorization) on credit limit and online visits — preserving affluent customers while stopping them from pulling centroids
- `log1p` transformation to correct right skew
- StandardScaler so a 1-visit difference carries the same weight as a ₹10,000 limit difference
- Dropped administrative IDs that would have introduced noise into distance calculations

**Feature engineering** — `Total_Interactions` and `Digital_Engagement_Ratio` (online share of all interactions). The ratio encodes channel *preference* independently of volume, so a 10-visit and a 5-visit digital-first customer group together rather than splitting by activity level.

**Model selection** — K = 3 chosen on three independent checks: the elbow curve, silhouette analysis (all three clusters clear the average line with almost no negative scores), and the WSS/BSS ratio, which collapses from 1.599 at K = 2 to 0.584 at K = 3 and then only creeps down. K = 3 explains **63.1% of total variance**.

**Cross-validation** — hierarchical clustering run independently across 18 distance × linkage combinations. Euclidean distance with average linkage scored highest on cophenetic correlation (**0.8485**), and its dendrogram cut at distance 3.0 resolved into three branches — the same answer K-Means reached by different mathematics.

## Results

Three personas, reproduced identically by both algorithms:

| Persona | Customers | Avg credit limit | Cards | Branch visits | Online visits | Calls |
|---|---|---|---|---|---|---|
| **Traditional Mass-Market** | 387 (59%) | $33,734 | 5.51 | 3.48 | 0.98 | 2.00 |
| **Support-Reliant Base** | 223 (34%) | $12,161 | 2.41 | 0.94 | 3.56 | 6.90 |
| **Premium Digital High-Rollers** | 50 (8%) | $114,126 | 8.74 | 0.60 | 8.57 | 1.08 |

**Algorithm agreement:** the premium segment was reproduced exactly — the same 50 customers, identical averages. The other two segments drifted by $143 and $56 respectively, under 0.5% on a portfolio spanning $3,000 to $200,000.

## Recommendations

**Marketing**
- *Traditional Mass-Market* — the prime upsell pool (moderate limits, 5.5 products already). Deliver pre-approved limit upgrades **through branch staff**; digital campaigns won't reach them.
- *Premium Digital High-Rollers* — retention, not acquisition. Premium perks delivered **inside the app**; phone and mail are wasted spend and an irritation.
- *Support-Reliant Base* — aggressive upselling here carries credit risk. Lead with foundational products and financial education **via the call centre they already use**.

**Service delivery**
- Prioritise digital self-service (automated disputes, instant limit increases) — it carries the entire relationship with the highest-value segment
- Optimise branches for fast resolution plus onboarding kiosks that migrate routine transactions to self-service
- Automate routine call-centre volume (IVR / AI assistant) to cut hold times for the segment that drives call load

**Scalability note:** K-Means scales roughly linearly and is the viable production engine; hierarchical clustering is quadratic in time and memory and belongs in exploratory research on samples — which is exactly the role it played here.

## Tools

Python · pandas · NumPy · scikit-learn · SciPy · Matplotlib · Seaborn · Yellowbrick

---

**Kartik Rana** — [LinkedIn](https://www.linkedin.com/in/kartik-rana-19b909228)
Post Graduate Program in Data Science with GenAI, McCombs School of Business, The University of Texas at Austin
