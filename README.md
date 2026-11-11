<div align="center">
  <img src="https://raw.githubusercontent.com/Danissemo/ForeverFlip/main/.github/assets/logo.svg" width="96" alt="ForeverFlip" />

  <h1>ForeverFlip community data</h1>

  <p>Full auction house scans of WoW Forever, shared by players through the
  <a href="https://github.com/Danissemo/ForeverFlip">ForeverFlip</a> addon.</p>

  <a href="https://github.com/Danissemo/ForeverFlip-Data/actions/workflows/aggregate.yml"><img src="https://img.shields.io/github/actions/workflow/status/Danissemo/ForeverFlip-Data/aggregate.yml?label=aggregate" alt="Aggregate" /></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/data-CC0%201.0-f5b52e" alt="CC0 1.0" /></a>
</div>

## 📦 What is here

| Where | What | Use |
|---|---|---|
| Releases `raw-YYYY-MM-DD` | **Full scans, unchanged**: every auction of a full scan, in scan order, one `.ffr.gz` file per scan | Any analysis: prices, sales, sellers' behaviour, history |
| [`shared/`](shared) | Summaries of the 12 newest scans of each auction house (`<realm>-<faction>.txt`) and an index | Downloaded by the ForeverFlip Sync helper for the addon |
| [`registry/scans.jsonl`](registry) | One line per archived scan: file, auction house, time, uploader, SHA-256, seal, status (`trusted`, `pending`, `confirmed`, `unconfirmed`) and the confirming scans | Choosing scans for an analysis |
| [`registry/uploaders.json`](registry/uploaders.json) | Trusted and banned accounts | Maintained by hand |

Players upload with issues titled **Scan upload: ...** that point to a secret gist. [A
workflow](.github/workflows/aggregate.yml) runs on each such issue and every 30 minutes: it checks the
scan, archives it, answers in the issue, closes it, and publishes confirmed scans in the summaries.

## 🧾 Full scan format (`.ffr.gz`, FFR1)

A gzip-compressed UTF-8 text. The first line is a header, then one line per auction, in the order the
server listed them (the order the auctions were created):

```
FFR1|<realm>-<faction>|<unix time>|<realm>|<faction>|<addon version>|<auctions>|<seal>
<itemID>,<count>,<buyout>,<minBid>,<bid>,<timeLeftBand>,<own>,<kind>,<name>
```

| Field | Meaning |
|---|---|
| `itemID` | Item ID |
| `count` | Units in the auction (stack size) |
| `buyout` | Buyout of the **whole** auction in copper, `0` for bid-only |
| `minBid`, `bid` | Minimum bid and current bid in copper |
| `timeLeftBand` | `0` under 30 minutes, `1` up to 2 hours, `2` up to 12 hours, `3` longer |
| `own` | `1` for the uploader's own auction, when the server tells |
| `kind` | `c` commodity, `i` item, `g` gear; empty while the game had not loaded the item |
| `name` | Full item name, for gear only (random endings such as "of the Bear" are told apart by name) |

An empty line is an auction the game could not read. The seal is the checksum the addon computed
while reading the scan (`compute_seal` in `ffscan.py`). Read the files with the
[`sync/ffscan.py`](https://github.com/Danissemo/ForeverFlip/blob/main/sync/ffscan.py) module of the
addon repository (`decode_raw`).

## 📊 Summary format (FF1)

One scan per line: `FF1|<scope>|<time>|<key>,<usual>,<cheapest>,<units>;...` with numbers in base 36 and
`%`, `,`, `;`, `|` in item keys written as `%XX`. Keys are `c:<id>`, `i:<id>` or
`g:<id>:<lower-case name>`. The usual price is the cheapest price of the 2% price bucket that reaches
30% of the listed units.

## 🤝 Contributing scans

1. Install [ForeverFlip](https://github.com/Danissemo/ForeverFlip) and turn on **Settings → Sharing →
   Keep my full scans for the ForeverFlip Sync helper**.
2. Run the **ForeverFlip Sync** helper next to the game with a token of any GitHub account at least
   7 days old (`gh auth login`, or a classic token with the scopes `gist` and `public_repo`).
   Downloading needs nothing.

The helper and this repository check every scan: its seal, format, age and values. A scan is
published in the summaries only after a scan of another player of the same auction house, taken
within 3 hours, confirms it, and only with the items whose prices agree. See
[Safeguards](https://github.com/Danissemo/ForeverFlip/blob/main/docs/community.md#safeguards).

## 📄 License

The data is published under [CC0 1.0](LICENSE): use it for
anything. World of Warcraft is a trademark of Blizzard Entertainment; this project is not affiliated
with Blizzard.
