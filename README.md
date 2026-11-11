<div align="center">
  <img src="https://raw.githubusercontent.com/Danissemo/ForeverFlip/main/.github/assets/logo.svg" width="96" alt="ForeverFlip" />

  <h1>ForeverFlip community data</h1>

  <p>Full auction house scans of WoW Forever, shared by players through the
  <a href="https://github.com/Danissemo/ForeverFlip">ForeverFlip</a> addon.</p>

  <a href="https://github.com/Danissemo/ForeverFlip-Data/actions/workflows/aggregate.yml"><img src="https://img.shields.io/github/actions/workflow/status/Danissemo/ForeverFlip-Data/aggregate.yml?label=aggregate" alt="Aggregate" /></a>
  <a href="https://github.com/Danissemo/ForeverFlip-Data/releases"><img src="https://img.shields.io/github/v/release/Danissemo/ForeverFlip-Data?include_prereleases&label=latest%20archive&color=f5b52e" alt="Latest archive" /></a>
</div>

## 📦 What is here

| Where | What | Use |
|---|---|---|
| Releases `raw-YYYY-MM-DD` | **Full scans, unchanged**: every auction of a full scan, in scan order, one `.ffr.gz` file per scan | Any analysis: prices, sales, sellers' behaviour, history |
| [`shared/`](shared) | Summaries of the 12 newest scans of each auction house (`<realm>-<faction>.txt`) and an index | Downloaded by the ForeverFlip Sync helper for the addon |
| Release `incoming` | New uploads, waiting to be processed | Written by the helper |

Every 10 minutes [a workflow](.github/workflows/aggregate.yml) checks the new uploads, archives them
and updates the summaries.

## 🧾 Full scan format (`.ffr.gz`, FFR1)

A gzip-compressed UTF-8 text. The first line is a header, then one line per auction, in the order the
server listed them (the order the auctions were created):

```
FFR1|<realm>-<faction>|<unix time>|<realm>|<faction>|<addon version>|<auctions>
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

An empty line is an auction the game could not read. Read the files with the
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
2. Run the **ForeverFlip Sync** helper next to the game.
   - Uploading needs a GitHub token with write access to this repository: ask to be added as a
     collaborator.
   - Downloading needs nothing.

Uploads are checked before they are archived. Scans with a wrong format, more than 7 days old, from
the future or with implausible values are refused.

## 📄 License

The data is published under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/): use it for
anything. World of Warcraft is a trademark of Blizzard Entertainment; this project is not affiliated
with Blizzard.
