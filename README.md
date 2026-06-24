# Phigros RKS Tracker

A local-first Phigros Ranking Score tracker for players who want to record ACC by date, watch RKS progress with more precision, and quickly match charts by song artwork.

一个本地优先的 Phigros RKS 记录器。你可以按日期保存多份 ACC 快照，自动计算单谱面 RKS、B27、3phi 和总 RKS，并用曲绘辅助匹配谱面。

> Not affiliated with Pigeon Games or Phigros. Song metadata and optional artwork are loaded from community resources at runtime; this repository does not bundle official game assets.

## Why This Exists

Phigros progress is not always obvious from the in-game number alone. A tiny ACC improvement on one chart may change your B27 floor, while a new phi can affect the extra P slots. This tracker gives you a calm, inspectable workspace for that process:

- Record scores manually without logging into any third-party service.
- Keep multiple dated snapshots instead of overwriting your history.
- See RKS with 4 decimal places in the UI and 6 decimal places in CSV exports.
- Search by song, difficulty, composer, charter, or internal song id.
- Use artwork thumbnails to recognize charts faster.
- Export everything as CSV so your data remains yours.

## Live Usage

Open `index.html` in a browser, or publish this repository with GitHub Pages and visit the Pages URL.

No backend is required. The app stores your ACC data in browser `localStorage` and exports/imports CSV files for backups.

## Main Features

### Date Versions

Create one version per date, such as:

```text
2026-06-21
2026-06-24
2026-07-01
```

Each date has its own independent ACC table. Use **Copy current** when you want a new date to start from the previous snapshot, then edit only the charts that improved.

### RKS Calculation

For each chart:

```text
if ACC < 70:
  chart RKS = 0

if ACC >= 70:
  chart RKS = ((ACC - 55) / 45)^2 * chart constant
```

The total RKS uses the current Phigros-style B27 + 3phi structure:

```text
RKS = (sum(B27) + sum(top 3 phi constants)) / 30
```

Where:

- `B27` is the sum of the 27 highest chart RKS values.
- `phi` means `ACC = 100.00`.
- The top 3 phi slots are selected by chart constant.
- Phi charts can also appear in B27.

### Precision

The UI intentionally shows more digits than the game display:

- Total RKS: 4 decimals
- Chart RKS: 4 decimals
- CSV `rks`: 6 decimals

Internal sorting and summing use full JavaScript floating-point values before display formatting.

### CSV Backup

Use **Export CSV** to save all date versions. The exported columns include:

```text
version, chartId, song, level, constant, acc, rks, isPhi, b27Rank, phiRank, composer, illustrator, charter
```

Use **Import CSV** to restore a backup. If the CSV has a `version` column, the app restores each row to that date. Older CSV files without `version` import into the currently selected date.

## Data Sources

The app loads chart metadata from:

- `Catrong/phi-plugin` for song names, constants, composers, illustrators, and charters.
- `Catrong/phi-plugin-ill` for optional artwork thumbnails displayed by URL at runtime.

Relevant upstream projects:

- https://github.com/Catrong/phi-plugin
- https://github.com/Catrong/phi-plugin-ill

This repository does not redistribute the artwork files. If an image fails to load, the tracker still works and simply hides that thumbnail.

## Privacy

Your ACC records are not sent anywhere by this app.

The only network requests made by the static page are for public metadata/artwork resources from GitHub raw URLs. Your personal score data stays in your browser unless you manually export and share the CSV.

## GitHub Pages

To publish the app:

1. Open the repository settings on GitHub.
2. Go to **Pages**.
3. Choose the default branch and `/root` as the source.
4. Save, then open the generated Pages URL.

Because the project is a single static HTML app, no build step is needed.

## Local Development

Clone the repository and open `index.html` directly:

```bash
git clone https://github.com/thomasWts/phigros_rks_tracker.git
cd phigros_rks_tracker
```

Then double-click `index.html`, or serve it with any static file server:

```bash
python -m http.server 8000
```

## Suggested Workflow

1. Create a date version for today.
2. Fill ACC values for the charts you care about.
3. Sort by chart RKS or filter to B27.
4. Export CSV after each serious update.
5. On a later date, copy the previous version and update only changed ACC values.

## License

The tracker code in this repository is released under the MIT License.

Phigros, song artwork, song titles, and related game content belong to their respective rights holders. Community data sources have their own licenses and terms; please review the upstream repositories before reusing their data in another project.

## Acknowledgements

Thanks to the Phigros community for maintaining chart metadata and resources, and to the open-source projects that made it easier to understand and verify the RKS formula.
