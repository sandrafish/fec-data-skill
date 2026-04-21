# fec-data: Claude Code skill for FEC campaign finance filings

A Claude Code slash command skill that exports FEC filings for state House, Senate, and Leadership PAC committees into separate SQLite databases using [libfec](https://github.com/asg017/libfec).

## What it does

Given CSV files of FEC committee IDs for your state, exports:

| CSV file | Form type | Output database |
|---------- | ----------- | ----------------- | 
| `[state]_house_comm_id.csv` | F3 | `[state]_house.db` |
| `[state]_sen_comm_id.csv` | F3 | `[state]_sen.db` |
| `[state]_leadership_pac_id.csv` | F3X | `[state]_leadership_pac.db` |

Only 2026 cycle filings are included. Leadership PACs file F3X (non-connected committees), not F3.

## Requirements

- [Claude Code](https://claude.ai/code)
- [libfec](https://github.com/asg017/libfec) installed and on your PATH
- `sqlite3` available on your PATH

## Preparing your committee ID CSVs

You'll need to build your own CSV files with the FEC committee IDs for your state. Each file should have a single column with the header `committee_number`, followed by FEC committee IDs (one per line):

```
committee_number
C00311639
C00909481
```

**Finding committee IDs:**
- Search the [FEC committee search](https://www.fec.gov/data/committees/) by state and committee type
- House and Senate candidate committees: filter by office (H or S) and state
- Leadership PACs: search by committee type "leadership PAC" and state
- Note: finding complete lists is easier for smaller states (like Colorado) than larger ones (like California)

Name your files with your two-letter state abbreviation: `co_house_comm_id.csv`, `tx_sen_comm_id.csv`, etc.

## Installation

Copy the `commands/fec-data/` folder into your project's `.claude/commands/` directory:

```bash
cp -r commands/fec-data /your-project/.claude/commands/
```

Or clone this repo and symlink it:

```bash
git clone https://github.com/yourusername/fec-data-skill
ln -s $(pwd)/fec-data-skill/commands/fec-data /your-project/.claude/commands/fec-data
```

## Usage

In Claude Code, run:

```
/fec-data:fetch-filings
```

Claude will ask for your state abbreviation and then run the exports.

## Next...

Run in command line:
```
libfec datasette [state]_house.db
```
Or one of the other databases. This will give you a datasette instance that you can explore.

## Tips

- Set `LIBFEC_API_KEY` in your environment for higher FEC API rate limits
- Re-run the skill any time to refresh data — existing databases will be overwritten
- [Libfec](https://github.com/asg017/libfec/tree/main) is developed by software engineer [Alex Garcia](https://alexgarcia.xyz/) 
- [Datasette](https://docs.datasette.io/en/stable/) is developed by [Simon Willison](https://simonwillison.net/)
