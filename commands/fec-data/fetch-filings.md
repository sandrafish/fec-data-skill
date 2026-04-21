# Fetch State Campaign Finance Filings from FEC

Export FEC filings for state House, Senate, and Leadership PAC committees into separate SQLite databases using libfec.

## What this does

- Reads committee IDs from `[state]_house_comm_id.csv`, `[state]_sen_comm_id.csv`, and `[state]_leadership_pac_id.csv`
- Exports **F3** filings (2026 cycle) for House and Senate committees
- Exports **F3X** filings (2026 cycle) for Leadership PAC committees
- Outputs: `[state]_house.db`, `[state]_sen.db`, `[state]_leadership_pac.db`

Replace `[state]` with your two-letter state abbreviation (e.g. `co`, `tx`, `ny`).

## Steps

1. Ask the user for their state abbreviation if not already known, then strip headers and BOM from each CSV to create plain ID list files:

```bash
STATE=co   # replace with your state abbreviation
tail -n +2 ${STATE}_house_comm_id.csv | sed 's/\r//' | grep -v '^$' > house_ids.txt
tail -n +2 ${STATE}_sen_comm_id.csv | sed 's/\r//' | grep -v '^$' > sen_ids.txt
tail -n +2 ${STATE}_leadership_pac_id.csv | sed 's/\r//' | grep -v '^$' > leadership_pac_ids.txt
```

2. Export House committees (F3, 2026 cycle):

```bash
libfec export house_ids.txt --cycle 2026 --form-type F3 --clobber -o ${STATE}_house.db
```

3. Export Senate committees (F3, 2026 cycle):

```bash
libfec export sen_ids.txt --cycle 2026 --form-type F3 --clobber -o ${STATE}_sen.db
```

4. Export Leadership PAC committees (F3X, 2026 cycle — leadership PACs are non-connected committees and file F3X, not F3):

```bash
libfec export leadership_pac_ids.txt --cycle 2026 --form-type F3X --clobber -o ${STATE}_leadership_pac.db
```

5. Report the filing counts from each database:

```bash
sqlite3 ${STATE}_house.db "SELECT COUNT(*) || ' filings in ${STATE}_house.db' FROM f3_cover;"
sqlite3 ${STATE}_sen.db "SELECT COUNT(*) || ' filings in ${STATE}_sen.db' FROM f3_cover;"
sqlite3 ${STATE}_leadership_pac.db "SELECT COUNT(*) || ' filings in ${STATE}_leadership_pac.db' FROM f3x_cover;"
```

## Notes

- House and Senate candidate committees file **F3**
- Leadership PACs (non-connected committees) file **F3X**
- Use `LIBFEC_API_KEY` env var for higher FEC API rate limits
- You'll need to build your own committee ID CSVs — see the README for how to find committee IDs on the FEC website
