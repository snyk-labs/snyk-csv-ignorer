# Snyk CSV Ignorer

A command-line tool to bulk ignore Snyk issues using a CSV file input. This tool helps automate the process of ignoring multiple Snyk issues across projects and organizations using Snyk's API. The CSV file must contain a column named `ISSUE_URL` with the full Snyk issue URLs as it is returned by the Snyk CSV or Snowflake export.

## Features

- Bulk ignore Snyk issues from a CSV file
- Support for different ignore types (not-vulnerable, wont-fix, temporary-ignore)
- Configurable ignore expiration
- Path-specific ignores
- Rate limiting handling
- Support for disregarding issues only if they're not fixable
- Flexible ignore reason text from command line and/or CSV columns
- Flexible ignore reason type from command line and/or CSV columns

## Prerequisites

- Python 3.x
- Snyk API token
- CSV file containing Snyk issue URLs

## Installation

1. Clone this repository:
```bash
git clone https://github.com/yourusername/snyk-csv-ignorer.git
cd snyk-csv-ignorer
```

2. Install the required dependencies:
```bash
pip install requests
```

3. Set up your Snyk API token as an environment variable:
```bash
export SNYK_TOKEN='your-snyk-api-token'
```

## Usage

```bash
./snyk_ignore_csv.py --file <path-to-csv> [--text <reason-text>] [--ignore-text-column <column-name>] [--type <ignore-type>] [--type-column <column-name>] [options]
```

### Required Arguments

- `--file`: Path to the CSV file containing Snyk issue URLs
- At least one of:
  - `--text`: The reason text for ignoring the issues
  - `--ignore-text-column`: Name of the CSV column containing ignore reason text
- At least one of:
  - `--type`: The classification of the ignore reason (valid options: 'not-vulnerable', 'wont-fix', 'temporary-ignore')
  - `--type-column`: Name of the CSV column containing ignore reason type

### Optional Arguments

- `--text`: The reason text for ignoring the issues (required if --ignore-text-column is not provided)
- `--ignore-text-column`: Name of the CSV column containing ignore reason text (required if --text is not provided)
- `--type`: The classification of the ignore reason (valid options: 'not-vulnerable', 'wont-fix', 'temporary-ignore') (required if --type-column is not provided)
- `--type-column`: Name of the CSV column containing ignore reason type (required if --type is not provided)
- `--disregard-if-fixable`: Only ignore issues if no upgrade or patch is available
- `--expires`: Timestamp (ISO 8601) when the ignore expires (e.g., 2025-12-31)
- `--ignore-path`: Path to ignore (default: '*')

### CSV File Format

The CSV file must contain a column named `ISSUE_URL` with the full Snyk issue URLs. If using `--ignore-text-column`, the CSV should also include the specified column with ignore reason text. If using `--type-column`, the CSV should include the specified column with ignore reason type. Example:

```csv
ISSUE_URL,IGNORE_REASON,IGNORE_TYPE
https://app.snyk.io/org/my-org/project/1234abcd-5678efgh#issue-snyk%3Alic%3Apip%3Acommon-lib%3AUnknown,"Not applicable for our use case",wont-fix
```

## Examples

1. Using only command line text:
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --text "False positive confirmed by security team" \
  --type temporary-ignore \
  --expires 2024-12-31
```

2. Using only column text:
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --ignore-text-column "IGNORE_REASON" \
  --type wont-fix
```

3. Combining command line and column text:
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --text "Security team approved:" \
  --ignore-text-column "IGNORE_REASON" \
  --type wont-fix
```

4. Using type from CSV column:
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --text "Reviewed by security team" \
  --type-column "IGNORE_TYPE"
```

5. Using both text and type from CSV columns:
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --ignore-text-column "IGNORE_REASON" \
  --type-column "IGNORE_TYPE"
```

6. Combining command line type with column text:
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --ignore-text-column "IGNORE_REASON" \
  --type temporary-ignore \
  --expires 2024-12-31
```

7. Using type column with fallback (column takes precedence):
```bash
./snyk_ignore_csv.py \
  --file issues.csv \
  --text "Default reason" \
  --type wont-fix \
  --type-column "IGNORE_TYPE"
```

