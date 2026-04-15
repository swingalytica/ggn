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

A GGN file consists of the following sections in order:

1. **Header** — key/value metadata tags
2. **Players Block** — wrapped by `PLAYERS` and `ENDPLAYERS`
3. **Gameboard Block** — wrapped by `GAMEBOARD` and `ENDGAMEBOARD`

---

## Header Tags

Each tag is written on its own line in the format:

```
[Key "Value"]
```

| Tag | Type | Required | Description |
| --- | --- | --- | --- |
| `GGN` | String | ✅ | Format version |
| `ID` | String | ✅ | Unique game identifier |
| `Mode` | String | ✅ | Game mode |
| `Date` | ISO 8601 | ✅ | Game creation timestamp |
| `Updated` | ISO 8601 | ✅ | Last updated timestamp |
| `URL` | String | ✅ | Canonical game URL |
| `WinnerPos` | Integer | ✅ | Winner position |
| `WinnerName` | String | ✅ | Winner display name |
| `WinnerID` | String | ✅ | Winner player identifier |
| `WinnerColor` | String | ✅ | Winner color |
| `WinnerScore` | Integer | ✅ | Winner score |
| `WinnerData` | Data | ✅ | Winner-specific data payload |
| `TotalShots` | Integer | ✅ | Total shots in the game |
| `ShotsLeft` | Integer | ✅ | Shots remaining |
| `MovesCount` | Integer | ✅ | Number of moves made |
| `Duration` | Integer | ✅ | Game duration in seconds |

**Header Example:**

```
[GGN "1.0"]
[ID "game_abc123"]
[Mode "4winning"]
[Date "2026-04-13T14:32:00Z"]
[Updated "2026-04-13T14:45:00Z"]
[URL "https://realgolf.games/game/game_abc123"]
[WinnerPos "1"]
[WinnerName "Alice"]
[WinnerID "user_abc123"]
[WinnerColor "red"]
[WinnerScore "0"]
[WinnerData "-"]
[TotalShots "0"]
[ShotsLeft "0"]
[MovesCount "21"]
[Duration "780"]
```

---

## Players Block

The players section is wrapped by `PLAYERS` and `ENDPLAYERS`.

One `[Player ...]` tag is written per participant, ordered by `POS` ascending.

```
PLAYERS
[Player "POS|NAME|PLAYER_ID|COLOR|DATA"]
ENDPLAYERS
```

- `DATA` is a game-specific payload for that player

**Example:**

```
PLAYERS
[Player "1|Alice|user_abc123|red|-"]
[Player "2|Bob|user_def456|blue|-"]
ENDPLAYERS
```

---

## Gameboard Block

The gameboard section is wrapped by `GAMEBOARD` and `ENDGAMEBOARD`.

Each row is written as:

```
[Row "TEXT|COLOR|PLAYER_ID"]
```

- `PLAYER_ID` is optional

**Example:**

```
GAMEBOARD
[Row ".......|-"]
[Row "..R....|red|user_abc123"]
[Row "..B....|blue|user_def456"]
ENDGAMEBOARD
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
[WinnerPos "1"]
[WinnerName "Alice"]
[WinnerID "user_abc123"]
[WinnerColor "red"]
[WinnerScore "0"]
[WinnerData "-"]
[TotalShots "0"]
[ShotsLeft "0"]
[MovesCount "21"]
[Duration "780"]

PLAYERS
[Player "1|Alice|user_abc123|red|-"]
[Player "2|Bob|user_def456|blue|-"]
ENDPLAYERS

GAMEBOARD
[Row ".......|-"]
[Row "..R....|red|user_abc123"]
[Row "..B....|blue|user_def456"]
ENDGAMEBOARD
```

### exact

```
[GGN "1.0"]
[ID "game_def456"]
[Mode "exact"]
[Date "2026-04-13T15:00:00Z"]
[Updated "2026-04-13T15:12:00Z"]
[URL "https://realgolf.games/game/game_def456"]
[WinnerPos "1"]
[WinnerName "Alice"]
[WinnerID "user_abc123"]
[WinnerColor "red"]
[WinnerScore "15"]
[WinnerData "shot1,shot2,shot3"]
[TotalShots "18"]
[ShotsLeft "9"]
[MovesCount "9"]
[Duration "420"]

PLAYERS
[Player "1|Alice|user_abc123|red|shot1,shot2,shot3"]
[Player "2|Bob|user_def456|blue|shot1,shot2,shot3"]
ENDPLAYERS
```

---

## Versioning

The `GGN` header tag contains the format version. Future versions may add new tags or blocks. Parsers should ignore unknown tags gracefully.

---

## License

Data published by RealGolf.Games is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).  
Format specification © RealGolf.Games UG (haftungsbeschränkt), licensed under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) (the spec itself is in the public domain — implement freely).
