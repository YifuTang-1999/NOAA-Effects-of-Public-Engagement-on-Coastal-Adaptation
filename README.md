# NOAA-Effects-of-Public-Engagement-on-Coastal-Adaptation
# README: Connecticut Environmental Action Similarity Analysis

## 1. Purpose of the File

This workbook summarizes an analysis of municipal environmental and hazard-mitigation actions in Connecticut.

The main purpose of the analysis is to identify when different municipalities report substantively similar actions, even when the wording is not exactly the same.

For example, the following actions may describe the same general type of intervention:

- Install a generator at the High School
- Acquire an emergency generator for a school shelter
- Install backup power at a municipal shelter

Although the wording differs, these actions are conceptually similar. The analysis groups such actions together and assigns them a common action code, such as `C0A0005`.


## 2. Unit of Analysis

The primary unit of analysis is an individual municipal action.

Each action is associated with:

- a municipality
- an original action number
- a category
- the original action text
- a newly assigned similarity-based action code

The analysis is conducted within category. Actions from different categories are not clustered together.

## 3. Category Definitions

The original dataset assigns each action to a category. The analysis preserves these categories.

| Category | Category Name |
|---|---|
| 0 | No Category / Nothing |
| 1 | New Policy |
| 2 | Capacity Building |
| 3 | Outreach & Engagement |
| 4 | Resilience Planning |
| 5 | Resilience Projects |

Similarity is evaluated only among actions within the same category.

For example, a category 0 action is not compared with a category 4 action for the purpose of assigning an action code.

## 4. Action Codes

Each cluster of substantively similar actions receives an action code.

The code format is:

`C{category}A{four-digit cluster number}`

Examples:

- `C0A0003` means the third action cluster within category 0.
- `C4A0003` means the third action cluster within category 4.

If two actions receive the same action code, they are treated as belonging to the same conceptual action group.

## 5. Distinct Codes versus Unique-to-Municipality Codes

It is important to distinguish between two concepts: distinct action codes and unique-to-municipality action codes.

### Distinct Action Codes

Distinct action codes refer to the number of different action codes observed within a given municipality and category.

For example, if East Hartford has the following category 4 action codes:

`C4A0003; C4A0006; C4A0001; C4A0006`

Then East Hartford has four action records in category 4, but only three distinct action codes:

`C4A0001; C4A0003; C4A0006`

### Unique-to-Municipality Action Codes

An action code is unique to a municipality only if that code appears in exactly one municipality statewide.

For example, `C4A0003` appears in 15 municipalities, including East Hartford, East Granby, Andover, and Avon. Therefore, `C4A0003` is not unique to East Hartford.

This means:

- East Hartford did report an action coded as `C4A0003`.
- However, `C4A0003` is not an East Hartford-specific action.

## 6. Description of Workbook Sheets

### README

This sheet provides a brief description of the input file, coding method, code format, and uniqueness rules.

### OverallSummary

This sheet summarizes the results by category across all municipalities.

It can be used to determine:

- the total number of action records in each category
- the number of distinct action codes in each category
- the number of action codes that are unique to a municipality
- the number of municipalities with valid actions in each category

### MuniCategorySummary

This is the primary summary sheet for municipality-level analysis.

Each row represents one municipality-category combination.

Key variables include:

| Column | Description |
|---|---|
| `municipality` | Municipality name |
| `category` | Original category number |
| `n_action_records` | Number of action records for the municipality-category pair |
| `n_distinct_action_codes_in_municipality_category` | Number of different action codes within the municipality-category pair |
| `action_codes_with_repetition` | Action codes listed in action-record order; repeated codes are retained |
| `distinct_action_codes` | De-duplicated list of action codes |
| `n_unique_to_municipality_action_records` | Number of action records whose codes appear in only one municipality statewide |
| `n_unique_to_municipality_action_codes` | Number of distinct action codes unique to that municipality |
| `unique_to_municipality_distinct_codes` | Codes that appear only in that municipality statewide |

### MuniWideSummary

This sheet presents the same municipality-level information in a wide format.

Each municipality occupies one row, and category-specific measures are shown across columns.

This format is useful for comparing municipalities side by side.

### AllActionsWithCodes

This is the action-level detail sheet.

Each row represents one original action record and includes its assigned action code.

Key variables include:

| Column | Description |
|---|---|
| `municipality` | Municipality associated with the action |
| `action_no` | Original action number |
| `category` | Original category |
| `action_code` | Newly assigned similarity-based action code |
| `cluster_size_records` | Number of action records in the same action-code cluster |
| `cluster_size_municipalities` | Number of municipalities in which the action code appears |
| `similarity_to_representative` | Similarity between the action and the representative action for its code |
| `action_text` | Original action text |
| `action_clean` | Cleaned text used for similarity comparison |

### ActionCodebook

This sheet functions as a codebook for the generated action codes.

Each row represents one action code and provides:

- the category
- the number of action records assigned to the code
- the number of municipalities in which the code appears
- a representative action
- example action texts
- the list of municipalities associated with the code

This sheet is useful for interpreting what each action code substantively represents.



## 7. Similarity Method

The current version uses a reproducible text-similarity procedure rather than an online large-language-model embedding service.

The procedure follows these steps:

1. Clean the action text.
2. Remove or standardize punctuation, numbers, measurement units, URLs, and other non-substantive text elements.
3. Normalize selected domain-specific terms and common synonyms.
4. Compare actions only within the same category.
5. Group actions into the same action code when their cleaned texts are sufficiently similar.

The similarity threshold used in the current version is:

`similarity_threshold = 0.58`

A lower threshold would merge more actions together, increasing the risk of over-grouping. A higher threshold would be more conservative, increasing the risk of splitting substantively similar actions into separate codes.

## 8. Meaning of `similarity_to_representative`

The variable `similarity_to_representative` measures the similarity between a given action and the representative action for its assigned action code.

For example, if an action is assigned to `C4A0003`, then its similarity score is calculated as:

Current action vs. representative action for `C4A0003`

A value of `1.0000` usually indicates that the action is the representative action itself or is textually identical after cleaning.

A lower value, such as `0.383`, indicates lower textual similarity to the representative action. Such cases may still be grouped together if the algorithm identifies strong overlap in core terms or a containment relationship between the cleaned texts.

These lower-similarity cases are good candidates for manual review.
