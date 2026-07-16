# UK NHS Hospital DB

This dataset is the complete UK NHS (National Health Service) Hospital Database including geospatial data.

Predecting the Sector by **"Given a hospital's characteristics, is it NHS or Independent Sector?"**

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
