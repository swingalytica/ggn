# GGN Specification

**Version 1.0**

Golf Game Notation (GGN) is the open data format used by [RealGolf.Games](https://realgolf.games) for exporting game data. It is inspired by PGN (Portable Game Notation) used in chess.

---

## File Format

- Encoding: **UTF-8**
- Line endings: **LF** (`\n`)
- File extension: **`.ggn`**
- Bulk dumps: **NDJSON-style** — one game per line as a serialized GGN string, compressed with **gzip** (`.ggn.gz`)

---

## Structure

A GGN file consists of three sections in order:

1. **Header** — key/value metadata tags
2. **Players** — one tag per player
3. **Board** _(optional)_ — game board state, only present for `4winning`

---

## Header Tags

Each tag is written on its own line in the format:

```
[Key "Value"]
```

| Tag          | Type     | Required | Description                                    |
| ------------ | -------- | -------- | ---------------------------------------------- |
| `GGN`        | String   | ✅       | Format version, always `"1.0"`                 |
| `ID`         | String   | ✅       | Unique game identifier                         |
| `Mode`       | String   | ✅       | Game mode: `4winning`, `exact`, or `precision` |
| `Date`       | ISO 8601 | ✅       | Game creation timestamp (`created_at`)         |
| `Updated`    | ISO 8601 | ✅       | Last updated timestamp (`updated_at`)          |
| `URL`        | String   | ✅       | Canonical game URL                             |
| `Winner`     | String   | ✅       | See format below                               |
| `TotalShots` | Integer  | ✅       | Total shots in the game                        |
| `ShotsLeft`  | Integer  | ✅       | Shots remaining                                |
| `MovesCount` | Integer  | ✅       | Number of moves made                           |
| `Duration`   | Integer  | ✅       | Game duration in seconds                       |

### Winner Tag Format

```
[Winner "pos|name|player_id|color|points|shots"]
```

- `points` and `shots` are omitted with `-` if not applicable

**Example:**

```
[Winner "1|Alice|user_abc123|red|42|-"]
```

---

## Player Tags

One `[Player ...]` tag per participant, ordered by `pos` ascending.

```
[Player "pos|name|player_id|color|points|shots|data"]
```

- `points` and `shots` are `-` if not applicable
- `data` is a comma-separated list of strings, or `-` if empty

**Example:**

```
[Player "1|Alice|user_abc123|red|42|-|val1,val2"]
[Player "2|Bob|user_def456|blue|-|12|-"]
```

---

## Board Block

Only present when `Mode` is `4winning`. Placed after all Player tags.

```
BOARD
cell,cell,cell
cell,cell,cell
ENDBOARD
```

Each cell is one of:

| Value             | Description                            |
| ----------------- | -------------------------------------- |
| `empty`           | No piece placed                        |
| `color:player_id` | Piece of `color` placed by `player_id` |

**Example:**

```
BOARD
empty,red:user_abc123,empty
blue:user_def456,red:user_abc123,empty
ENDBOARD
```

---

## Complete Example

### 4winning

```
[GGN "1.0"]
[ID "game_abc123"]
[Mode "4winning"]
[Date "2026-04-13T14:32:00Z"]
[Updated "2026-04-13T14:45:00Z"]
[URL "https://realgolf.games/game/game_abc123"]
[Winner "1|Alice|user_abc123|red|-|-"]
[TotalShots "0"]
[ShotsLeft "0"]
[MovesCount "21"]
[Duration "780"]

[Player "1|Alice|user_abc123|red|-|-|-"]
[Player "2|Bob|user_def456|blue|-|-|-"]

BOARD
empty,red:user_abc123,blue:user_def456,empty,empty,empty,empty
red:user_abc123,blue:user_def456,red:user_abc123,empty,empty,empty,empty
ENDBOARD
```

### exact

```
[GGN "1.0"]
[ID "game_def456"]
[Mode "exact"]
[Date "2026-04-13T15:00:00Z"]
[Updated "2026-04-13T15:12:00Z"]
[URL "https://realgolf.games/game/game_def456"]
[Winner "1|Alice|user_abc123|red|15|9"]
[TotalShots "18"]
[ShotsLeft "9"]
[MovesCount "9"]
[Duration "420"]

[Player "1|Alice|user_abc123|red|15|9|shot1,shot2,shot3"]
[Player "2|Bob|user_def456|blue|12|9|shot1,shot2,shot3"]
```

---

## Versioning

The `GGN` header tag contains the format version. Future versions may add new tags or blocks. Parsers should ignore unknown tags gracefully.

---

## License

Data published by RealGolf.Games is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).  
Format specification © RealGolf.Games UG (haftungsbeschränkt), licensed under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) (the spec itself is in the public domain — implement freely).

## License

Data published by RealGolf.Games is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).  
Format specification © RealGolf.Games UG (haftungsbeschränkt), licensed under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) (the spec itself is in the public domain — implement freely).
