# WP Baseball Predictor — Public Pick Archive

**Every MLB pick we publish, locked in before first pitch, with the timestamp to prove it.**

This repository is the tamper-evident public record of [WP Baseball Predictor](https://www.wpbaseballpredictor.com)'s automated MLB picks. Before every game, our model's final pick is emailed to subscribers and — in the same minute — committed here. The commit timestamp is applied and attested by GitHub's infrastructure, not by us, which makes this archive something most picks services can't offer: **independent, third-party proof of what we predicted and when**.

No picks are added after the fact. No losses are quietly removed. The graded result of every pick is committed the morning after the game, win or lose.

---

## ⚠️ This archive begins on 2026-08-03

Publishing to this repository started on **3 August 2026**. Picks published before that date are **not** here.

We could have backfilled them. We deliberately did not — and the reason matters more than the missing files.

The entire value of this archive is that a commit timestamp, applied by GitHub rather than by us, proves a pick existed *before* the game was played. A commit created today for a game played in June would carry today's date. It would prove nothing, while looking exactly like evidence. Backfilling would quietly hollow out the one claim this repository exists to support.

So the record starts where the proof starts. It will only ever grow forwards.

Our full results history — including everything before this date — is published and graded at [wpbaseballpredictor.com](https://www.wpbaseballpredictor.com), and in the iOS app. Those are our own systems reporting on themselves; this repository is the part you don't have to take our word for.

*(One exception, noted for completeness: `results/2026/08/2026-08-02.json` covers the slate played the day before publishing began. It was committed on 3 August, on the normal next-morning results cadence, and there is no matching `picks/` file for that date.)*

---

## How the archive works

| When (ET) | What is committed | Where |
|---|---|---|
| ~15 min before each game's first pitch | The locked pick, exactly as emailed to subscribers | `picks/<YYYY>/<MM>/<YYYY-MM-DD>.json` |
| ~3 AM the next morning | Graded results for the completed slate | `results/<YYYY>/<MM>/<YYYY-MM-DD>.json` |

Commit messages follow a fixed pattern — `WP MLB picks for 2026-08-03 (15 games)` / `WP MLB results for 2026-08-03 (15 games)` — so the [commit history](../../commits/main) reads as a chronological ledger.

## Verify a pick yourself

1. Open any file under [`picks/`](../../tree/main/picks) and note the game's `game_date` and `game_time_et`.
2. Click the **History** button on that file and check the commit's timestamp (shown in UTC). It will precede the game's first pitch.
3. Each pick also carries `finalized_at_unix` — the exact second the pick was locked on our servers, which you can compare against the commit time.
4. Open the matching file under [`results/`](../../tree/main/results) to see how the pick graded.

Because git history is content-addressed, editing an old pick would change every subsequent commit hash — any rewrite of history would be visible to anyone who has ever cloned or watched this repository.

## Tally the record yourself

Don't take a marketing page's word for a win rate — compute it from this repo:

```bash
git clone https://github.com/Aiello89/wpbp-pick-archive
cd wpbp-pick-archive
python3 - <<'EOF'
import json, pathlib
wins = losses = 0
for f in pathlib.Path("results").rglob("*.json"):
    for g in json.loads(f.read_text()):
        r = str(g.get("pick_result", "")).lower()
        wins += (r == "win")
        losses += (r == "loss")
total = wins + losses
print(f"{wins}W - {losses}L  ({wins/total*100:.1f}%)  across {total} graded picks"
      if total else "no graded picks yet")
EOF
```

### A note on games that never get graded

Occasionally a pick will appear under `picks/` with no corresponding entry in that day's `results/` file. That is deliberate, and it is not a missing loss.

MLB games get postponed. When a rained-out game is replayed days or weeks later it is a different game — different starting pitchers, different rest, different lineups — so grading the original pick against it would be meaningless. Those picks are treated as void and excluded from the record entirely, exactly as a sportsbook voids a bet on a postponed game. They are never silently converted into wins.

## What's in a pick file

Each `picks/.../<date>.json` is a list of games. Per game:

| Field | Meaning |
|---|---|
| `wp_pick`, `wp_pick_team` | The model's pick (e.g. `"Orioles +1.5"`) |
| `confidence_tier` | Public confidence label (`Lean` / `Confident` / `Strong` / `Lock`) |
| `is_pick_of_day`, `potd_reasoning` | Pick of the Day flag and its one-line rationale |
| `away_ml`, `home_ml`, `pick_odds`, `pick_odds_type` | Market odds at lock time |
| `over_under` | Game-total prediction |
| `away_dark_horse`, `home_dark_horse` | Dark-horse home-run picks |
| `away_pitcher`, `home_pitcher`, `*_pitcher_xfip` | Confirmed starting pitchers with xFIP |
| `away_rest_days`, `home_rest_days` | Rest situation |
| `is_underdog_pick`, `is_strong_favourite` | Situational badges |
| `game_date`, `game_time_et`, `finalized_at_unix` | When the game starts and when the pick locked |

Result files record, per game: the final score and the graded `pick_result` (`WIN`/`LOSS`) alongside the pick it grades.

> **Note on probabilities:** the model's raw win probabilities are intentionally not published — the public record uses confidence tiers. Everything a subscriber sees is here; the model internals are not.

## About the model

Picks are generated by an automated pipeline — no human overrides — combining team analytics (wOBA, FIP, xFIP, BABIP, Pythagorean expectation), confirmed starting-pitcher performance, bullpen strength, recent form, and individual injury impact. Picks lock when starting pitchers are confirmed, roughly 15 minutes before first pitch. Methodology overview: [wpbaseballpredictor.com/faq](https://www.wpbaseballpredictor.com/faq).

The same system powers our [iOS app](https://apps.apple.com/app/id6786511690) and the daily analysis articles on the site.

## Citing this archive

Feel free to reference or analyze this data with a link back to this repository or [wpbaseballpredictor.com](https://www.wpbaseballpredictor.com). If you find a discrepancy between the archive and anything we publish elsewhere, we want to know: **customersupport@wpbaseballpredictor.com**.

## Disclaimer

This archive exists for transparency and entertainment. Nothing here is betting or financial advice; past performance does not guarantee future results. If you choose to bet, bet responsibly and only where it is legal for you — in Ontario, ConnexOntario offers free support at 1-866-531-2600.

---

© 2026 WP North Entertainment Group Ltd. · Canadian and U.S. Patents Pending · [wpbaseballpredictor.com](https://www.wpbaseballpredictor.com)
