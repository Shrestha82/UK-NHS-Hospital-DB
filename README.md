# UK NHS Hospital DB

A machine learning project that predicts whether a UK healthcare organisation belongs to the **NHS Sector** or the **Independent Sector**, using structured data from the NHS Organisation Data Service (ODS).

Predecting the Sector by **"Given a hospital's characteristics, is it NHS or Independent Sector?"**

## Pipeline:

1. Exploratory Data Analysis (distributions, correlation heatmap, pairplots)
2. Data Cleaning (mode imputation for categorical fields, median imputation for Latitude/Longitude)
3. Feature Engineering (dropping identifier/free-text columns, one-hot encoding, feature scaling)
4. Model Training & Evaluation across five classifiers
5. Model Comparison by accuracy

## Independent features (X) used:

- **SubType** (Hospital / Mental Health Hospital / UNKNOWN)
- **IsPimsManaged** (True/False)
- **County** (54 categories — could group into regions)
- **City**
- **ParentName / ParentODSCode** (the managing organisation — likely a strong signal, since NHS trusts vs private groups often go by name)
- **Latitude / Longitude** (Independent hospitals may cluster in wealthier/urban areas)

## Columns to exclude (no predictive value, just identifiers/text):

**OrganisationID, OrganisationCode, OrganisationName, Address1/2/3, Postcode, Phone, Email, Website, Fax,,,** - these are unique IDs or contact info, not features.

## Columns to drop because they have zero variance:

**OrganisationType, OrganisationStatus** - same value for every row, so they add nothing.

## Column-by-column verdict

| Column                         |          Unique |       Verdict       | Why                                                                                                 |
| ------------------------------ | --------------: | :-----------------: | --------------------------------------------------------------------------------------------------- |
| OrganisationCode               |            1211 |       ❌ Drop       | Unique per row — it's an ID, not a feature.                                                         |
| OrganisationType               |               1 |       ❌ Drop       | Only one value ("Hospital") for all 1211 rows — zero variance, no predictive info.                  |
| OrganisationStatus             |               1 |       ❌ Drop       | Always "Visible" — no predictive value.                                                             |
| OrganisationName               |            1189 |       ❌ Drop       | Nearly unique per row — just a name, not a useful pattern.                                          |
| Address1 / Address2 / Address3 | 825 / 662 / 130 |       ❌ Drop       | Free-text street addresses — too granular and unique to generalize.                                 |
| Postcode                       |            1128 |       ❌ Drop       | Near-unique — too granular (postcode prefixes could be engineered separately).                      |
| Phone                          |             845 |       ❌ Drop       | Contact information, not a meaningful feature.                                                      |
| Email                          |             282 |       ❌ Drop       | Contact information.                                                                                |
| Website                        |             650 |       ❌ Drop       | Contact information.                                                                                |
| Fax                            |             317 |       ❌ Drop       | Mostly junk/malformed values (`,,,` appears 803 times).                                             |
| SubType                        |               3 |       ✅ Keep       | Low cardinality (`Hospital`, `UNKNOWN`, `Mental Health Hospital`) — useful categorical feature.     |
| Sector                         |               2 |       ✅ Keep       | **Target (`y`)** — NHS vs Independent (743 vs 468).                                                 |
| County                         |              54 |       ✅ Keep       | Moderate cardinality — geographically meaningful.                                                   |
| City                           |             532 |    ⚠️ Borderline    | High cardinality. Consider target/frequency encoding or dropping in favor of `County`.              |
| ParentODSCode / ParentName     |             307 | ⚠️ Potentially Keep | Strong feature since parent organizations rarely mix sectors, but beware of potential data leakage. |

## Future Improvements

- Address class imbalance (e.g. class_weight='balanced', SMOTE, or resampling)
- Hyperparameter tuning (GridSearchCV / RandomizedSearchCV) across all models
- Feature importance analysis for the Decision Tree / Random Forest models
- Cross-validation instead of a single train/test split for more robust accuracy estimates
