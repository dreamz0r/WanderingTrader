# Wandering Trader

**Author: Griffin** | **Source version: 1.4.10**

Configuration reference and examples. Requires CF, Expansion Market and Expansion AI; marker settings also require Expansion Navigation.

## Editing the configuration

1. Start the server. At mission startup it immediately creates any missing `WanderingTrader/Settings.json` and `WanderingTrader/Traders.json` inside the directory selected by the server's `-profiles` argument.
2. First-run configs include four enabled traders: clothing, weapons, food and medicine. Each offers 2–4 distinct item types, waits 60–120 minutes and stays for 60 minutes.
3. To edit, stop the server, change the profile JSON files, then restart. The `Traders.json` beside this README is an example export; the server reads the profile copy. Existing files are retained. Invalid configs are logged instead of silently replaced.
4. Add your Steam64 ID as a string to Expansion AI's `Admins` setting. With `EnableDebugCommands` enabled, use `!wt spawn` to spawn all available enabled traders immediately or `!wt despawn` to remove all active camps. `!wt` shows usage. Ordinary players cannot use these commands.

JSON requires quoted field names, lowercase `true`/`false`, and no comments or trailing commas. Keep complete generated entries when editing; omitted fields in DayZ JSON do not reliably inherit all source defaults. Config changes are read at startup, with no live reload.

## Settings.json: shared server settings

| Setting | Default | Instructions |
| --- | --- | --- |
| `ConfigVersion` | `1` | Settings schema version. Leave at 1. This is separate from the mod version and Traders schema. |
| `Enabled` | `true` | Enable automatic visits. Set false for admin-triggered visits only; debug commands remain available. |
| `EnableDebugCommands` | `true` | Allow authenticated Expansion AI admins to use the `!wt` commands. False disables spawning/despawning through chat. |
| `FailedSpawnRetrySeconds` | `60` | Wait after an unsuccessful automatic spawn before searching again. Integer seconds, 1–604800. |
| `Show2DMarker` | `true` | Display each active camp on the map. Requires Expansion Navigation and server markers enabled. |
| `Show3DMarker` | `true` | Display each active camp's marker in the world. Independent of the map marker switch; same Navigation requirements. |
| `ShowGlobalNotification` | `true` | Send a global arrival notification when a merchant successfully spawns. Independent of markers. |
| `MarkerIcon` | `"Trader"` | Expansion icon name used for camp markers and notifications. Use an icon available in your installed Expansion version. |
| `MarkerColor` | `-256` | Signed ARGB integer for markers and notifications. Default is opaque yellow. |
| `NotificationTitle` | `"Wandering Merchant"` | Legacy compatibility field. Currently unused: each arrival notification uses that trader's `Name` as its title. |
| `NotificationText` | `"The wandering merchant has set up camp for %1 minutes."` | Arrival message. `%1` is replaced with that trader's stay duration, rounded up to whole minutes. |
| `NotificationDurationSeconds` | `10` | Display duration for arrival notifications, 1–120 seconds. |
| `MarketZoneRadius` | `30` | Expansion market zone radius around the trader, 10–100 metres. |
| `BuyPricePercent` | `100` | Positive percentage applied to the market zone's purchase prices. 100 uses the normal category price. |
| `SellPricePercent` | `-1` | Expansion market zone sale price percentage; must be at least -1. -1 uses Expansion's default behavior. This mod's standalone offers are purchase-only, so this does not enable sell-back. |
| `SpawnPositions` | `[]` | Shared candidate sites, each `[x,y,z]` in world coordinates. Empty means random sites across the map. Per-trader positions take precedence. Ground height is projected from terrain. |
| `ExclusionAreas` | `[]` | Blocked areas, each `{"Position":[x,y,z],"Radius":100}`. Radius must be positive, in metres. |
| `MapEdgeMargin` | `200` | Minimum map-boundary margin in metres; at least 10. If the margin leaves no usable map area, spawning fails. |
| `MaxSlopeDegrees` | `10` | Maximum permitted terrain slope, 0–30 degrees. Lower values restrict camps to flatter terrain. Uneven-ground checks also apply. |
| `CampClearanceRadius` | `8` | Camp clear-space check radius, 6–50 metres. Larger values make suitable campsites harder to find. |
| `MinDistanceFromPlayers` | `250` | Minimum campsite distance from players in metres; at least 0. Also applies to debug spawning. |
| `MinDistanceFromPreviousCamp` | `500` | Minimum distance from the same trader's previous camp in metres; at least 0. Debug spawning skips this check. |
| `PositionSearchAttempts` | `250` | Candidate sites tested per spawn attempt, 1–1000. After failure, automatic spawning waits `FailedSpawnRetrySeconds`. |

## Traders.json: root settings

| Setting | Instructions |
| --- | --- |
| `ConfigVersion` | Leave at `2`. Legacy version 1 files upgrade to version 2; attachment migration backs up the previous file as `.bak`. |
| `Traders` | Array of trader definitions. Add, edit or remove entries here; maximum 64. Each trader has its own camp, timer and stock. |

## Each trader definition

| Setting | Instructions |
| --- | --- |
| `Id` | Required unique stable identifier, 1–48 characters using lowercase letters, digits and underscores. Example: `weapons`. |
| `Name` | Required display name for the camp marker and arrival notification title. |
| `Enabled` | Boolean. True enables this trader; false excludes it from automatic visits and debug spawning. |
| `NPCClassName` | Protected merchant avatar. Use `WT_Merchant` (Mirek), `WT_MerchantFemale` (Eva), or `WT_Merchant_<Name>`. Supported suffixes: Mirek, Denis, Boris, Cyril, Elias, Francis, Guo, Hassan, Indar, Jose, Kaito, Lewis, Manua, Niki, Oliver, Peter, Quinn, Rolf, Seth, Taiki, Linda, Maria, Frida, Gabi, Helga, Irena, Judy, Keiko, Eva, Naomi, Baty. Arbitrary AI classes are rejected. |
| `MinTimeToSpawn` | Minimum random wait in real elapsed minutes. At least 0.016667; fractions supported. First wait is measured from server startup; later waits follow this trader's despawn. Example default: 60. |
| `MaxTimeToSpawn` | Maximum wait in minutes, at least the minimum and at most 10080. Example default: 120. Restart begins fresh waits. |
| `StayDurationMinutes` | Time the trader remains after spawning, 0.016667–10080 minutes. Example default: 60. |
| `MinItemsForSale` | Minimum number of distinct top-level item types offered during a visit. Nonnegative integer; cannot exceed the number of configured `Items`. New examples use 2. |
| `MaxItemsForSale` | Maximum number of distinct offers, at least the minimum and at most 512. Effective maximum is capped to the available item entries. New examples use 4. Equal minimum/maximum gives a fixed count. Both fields 0 keep legacy independent chance rolls. |
| `Currencies` | Nonempty array of Expansion currency class names, for example `["ExpansionBanknoteHryvnia"]`. These must exist in the installed mods. |
| `Gear` | Array of clothing/equipment class names attached to the NPC. Maximum 24. Use compatible NPC attachment slots. Failed attachments are logged. NPC gear is protected. |
| `ShoulderRifle` | Rifle class name placed in the Shoulder slot. Empty string disables it. The decorative rifle remains unloaded and protected. |
| `SpawnPositions` | Optional array of `[x,y,z]` sites, maximum 256. Empty uses the shared settings. Listed sites still undergo terrain, geometry, exclusion and player-distance checks. |
| `Items` | Array of sale item definitions, maximum 512. Each top-level class must be unique within this trader, ignoring case. Only these exact classes are offered; no automatic color variants. |

### Distinct offers versus stock

With `MinItemsForSale: 3` and `MaxItemsForSale: 5`, each visit randomly chooses 3, 4 or 5 different item entries. One Mosin entry counts as **one offer**, whether its stock is 1 rifle or 20 rifles. Its scope attachment does not count as another offer. If a scope is also configured as its own top-level item, that separate entry can count as an offer.

The count is selected first, then item entries are chosen without replacement using `Chance` as their relative selection weight. Higher values are more likely to be chosen; even 100 is not guaranteed when the selected count is smaller than the catalog. Stock rolls separately for each selected item. The selection stays fixed throughout that visit; reopening the market does not reroll it.

Existing definitions missing both new fields load as 0/0 and retain independent percentage rolls. To enable the limits, add both fields to each trader:

```json
{
  "MinItemsForSale": 3,
  "MaxItemsForSale": 5
}
```

These are fields to merge into a trader entry, not a replacement for the complete file.

Minimum 0 with a positive maximum allows an empty visit. Both 0 disable count limits; they do not force an empty catalog. To disable a trader, use its `Enabled` flag.

## Sale items and attachment packages

| Setting | Instructions |
| --- | --- |
| `ClassName` | Exact item class name. Must exist in the game/mods and the server's Expansion Market categories. |
| `Chance` | Top-level sale items only: integer 1–100. With offer limits enabled this is a relative selection weight. With limits 0/0 it is the independent percentage chance per visit, and 100 always appears. Attachment entries do not use this field. |
| `MinStock` | Minimum number of purchase packages available for this item per visit. Normally at least 1. Paired -1/-1 uses the market category maximum. Does not affect the distinct-offer count. |
| `MaxStock` | Maximum packages stocked, at least the minimum and at most 10000. Category-based stock is also capped at 10000. Attachment stock fields are serialized for compatibility but ignored: attachments are included with their parent package. |
| `MinQuantity` | Minimum contents in each purchased item, in native units: rounds, grams, millilitres, pills or uses. Normally at least 1. Paired -1/-1 means full native capacity. This does not set stock. |
| `MaxQuantity` | Maximum contents per purchased item, at least the minimum and at most 100000. -1 with a positive minimum means up to full. Values are clamped to native item capacity. Magazines/ammunition use whole rounds. |
| `RandomQuantity` | Integer 0 or 1. 0 uses the configured contents and stock ranges. 1 overrides both: contents roll from 1 to native capacity, and top-level stock rolls from 1 to the category maximum, capped at 10000. For attachments, only contents are applicable. |
| `MinState` | Best permitted condition: `Pristine`, `Worn`, `Damaged` or `Badly damaged`. Default Pristine. |
| `MaxState` | Worst permitted condition, same accepted states, no better than `MinState`. Default Pristine. Ruined items are excluded. |
| `RandomState` | Integer 0 or 1. 0 uses the condition bounds. 1 ignores those bounds and randomly chooses among the four permitted conditions. |
| `Attachments` | Array of included attachment definitions; `[]` means none. Each attachment has `ClassName`, contents/condition rules, random switches and its own nested `Attachments`. Maximum depth is three attachment levels. Use compatible slots; no ancestor cycles. |

Contents and condition roll once per visit, giving consistent quoted packages and delivered items. For magazines, configure compatible ammunition in the attachment tree to deliver loaded magazines; a magazine without ammunition attachments remains empty. Attachments have no independent chance or stock roll. If the same class appears in several packages for one trader, its contents/condition rules and attachment list must agree.

Prices come from Expansion Market category settings, not these files. Ammo and loose materials priced per unit are charged according to contents; ordinary containers retain per-item category pricing. Included attachments contribute to package prices. Standalone sell-back is disabled. Missing market classes or conflicting package rules prevent that trader's catalog from starting and produce a server log message.

## Configuration examples

### Settings.json: map marker only, no global notification

This is a complete Settings.json example. Automatic visits and admin commands remain enabled. Change the three visibility switches independently.

```json
{
  "ConfigVersion": 1,
  "Enabled": true,
  "EnableDebugCommands": true,
  "FailedSpawnRetrySeconds": 60,
  "Show2DMarker": true,
  "Show3DMarker": false,
  "ShowGlobalNotification": false,
  "MarkerIcon": "Trader",
  "MarkerColor": -256,
  "NotificationTitle": "Wandering Merchant",
  "NotificationText": "The wandering merchant has set up camp for %1 minutes.",
  "NotificationDurationSeconds": 10,
  "MarketZoneRadius": 30,
  "BuyPricePercent": 100,
  "SellPricePercent": -1,
  "SpawnPositions": [],
  "ExclusionAreas": [],
  "MapEdgeMargin": 200,
  "MaxSlopeDegrees": 10,
  "CampClearanceRadius": 8,
  "MinDistanceFromPlayers": 250,
  "MinDistanceFromPreviousCamp": 500,
  "PositionSearchAttempts": 250
}
```

### Traders.json: food trader with 2–3 distinct offers

This is a complete file containing one trader. To retain your other traders, add or edit this entry inside the existing `Traders` array instead of replacing the whole file.

Each visit offers 2 or 3 of the configured item types. Sardine stock is 5–10 cans, but SardinesCan still counts as only one offer. Each purchased can contains 50–250 native quantity units. The vendor waits 60–120 minutes and stays 60 minutes.

```json
{
  "ConfigVersion": 2,
  "Traders": [
    {
      "Id": "food",
      "MinItemsForSale": 2,
      "MaxItemsForSale": 3,
      "Name": "Food Trader",
      "Enabled": true,
      "NPCClassName": "WT_Merchant_Eva",
      "MinTimeToSpawn": 60,
      "MaxTimeToSpawn": 120,
      "StayDurationMinutes": 60,
      "Currencies": [
        "ExpansionBanknoteHryvnia"
      ],
      "Gear": [
        "BeanieHat_Grey",
        "HikingJacket_Blue",
        "Jeans_Blue",
        "HikingBootsLow_Blue",
        "TaloonBag_Blue"
      ],
      "ShoulderRifle": "Mosin9130",
      "Items": [
        {
          "ClassName": "SardinesCan",
          "Chance": 100,
          "Attachments": [],
          "MinState": "Pristine",
          "MaxState": "Damaged",
          "MinStock": 5,
          "MaxStock": 10,
          "MinQuantity": 50,
          "MaxQuantity": 250,
          "RandomQuantity": 0,
          "RandomState": 0
        },
        {
          "ClassName": "BakedBeansCan",
          "Chance": 90,
          "Attachments": [],
          "MinState": "Pristine",
          "MaxState": "Worn",
          "MinStock": 2,
          "MaxStock": 15,
          "MinQuantity": 50,
          "MaxQuantity": 250,
          "RandomQuantity": 0,
          "RandomState": 0
        },
        {
          "ClassName": "WaterBottle",
          "Chance": 80,
          "Attachments": [],
          "MinState": "Pristine",
          "MaxState": "Damaged",
          "MinStock": 2,
          "MaxStock": 15,
          "MinQuantity": 200,
          "MaxQuantity": 1000,
          "RandomQuantity": 0,
          "RandomState": 0
        }
      ],
      "SpawnPositions": []
    }
  ]
}
```

### Item package: pistol with a light and battery

Add this complete item entry to a trader's `Items` array. The light and its battery are included with each pistol; together the package counts as one offer. Stock is 1–5 packages. Chance 50 is a selection weight when count limits are enabled, or a 50% independent chance when both limits are 0.

```json
{
  "ClassName": "Glock19",
  "Chance": 50,
  "Attachments": [
    {
      "ClassName": "TLRLight",
      "Attachments": [
        {
          "ClassName": "Battery9V",
          "Attachments": [],
          "MinState": "Pristine",
          "MinQuantity": -1,
          "MaxState": "Pristine",
          "MaxStock": -1,
          "MaxQuantity": -1,
          "MinStock": -1,
          "RandomState": 0,
          "RandomQuantity": 0
        }
      ],
      "MinState": "Pristine",
      "MinQuantity": -1,
      "MaxState": "Pristine",
      "MaxStock": -1,
      "MaxQuantity": -1,
      "MinStock": -1,
      "RandomState": 0,
      "RandomQuantity": 0
    }
  ],
  "MinState": "Pristine",
  "MaxState": "Worn",
  "MinStock": 1,
  "MaxStock": 5,
  "MinQuantity": -1,
  "MaxQuantity": -1,
  "RandomQuantity": 0,
  "RandomState": 0
}
```

### Fixed offer count and independent chance mode

Merge these fields into a trader with at least four configured Items to offer exactly four distinct types per visit:

```json
{
  "MinItemsForSale": 4,
  "MaxItemsForSale": 4
}
```

Use this instead for independent item Chance percentages with no total offer limit:

```json
{
  "MinItemsForSale": 0,
  "MaxItemsForSale": 0
}
```

### Three fixed spawn positions for all traders

In the generated **Settings.json**, replace `"SpawnPositions": []` with three coordinates. This limits traders to those sites instead of sampling the whole map, provided their own `SpawnPositions` arrays in Traders.json are empty.

```json
{
  "SpawnPositions": [
    [4500, 0, 6500],
    [7500, 0, 7500],
    [9500, 0, 10500]
  ]
}
```

Merge this field into your existing Settings.json; do not replace the complete file with this fragment. The coordinates are examples, not verified campsites: replace them with three clear, usable locations on your server's map.

Each coordinate is `[x, y, z]`, in metres. The server calculates terrain height, so `y` can be `0`. On each spawn attempt it randomly chooses **one of the three listed sites**. It does not choose a random location elsewhere on the map or spawn at all three at once. Camp orientation still varies.

### Three fixed spawn positions for one trader

To give only the food trader those sites, edit its existing entry inside the **Traders.json** `Traders` array and replace that entry's `SpawnPositions` field:

```json
{
  "Id": "food",
  "SpawnPositions": [
    [4500, 0, 6500],
    [7500, 0, 7500],
    [9500, 0, 10500]
  ]
}
```

This is an edit fragment, not a complete trader definition. Keep the trader's other fields and Items. A nonempty per-trader list overrides the shared Settings.json list; other traders keep using their own lists or the shared list.

Site checks still apply to fixed positions: terrain, objects, exclusions, map margins, player distance and previous-camp distance can reject them. If all listed sites are unsuitable, spawning fails and automatic visits retry after `FailedSpawnRetrySeconds`; there is no map-wide fallback. Stop the server before editing, restart, then use `!wt spawn` to test.

To return to random map-wide spawning, set `SpawnPositions` to `[]` in **both** Settings.json and the affected trader entry. Emptying only the trader's list makes it use the shared list.

### Spawn exclusions

Merge this into Settings.json to block sites within 300 metres of the example coordinates. Replace the coordinates with your own map location. An exclusion does not select spawn sites; use shared or per-trader SpawnPositions to restrict candidate sites.

```json
{
  "ExclusionAreas": [
    {
      "Position": [
        7500,
        0,
        7500
      ],
      "Radius": 300
    }
  ]
}
```

## Checking your changes

Restart after editing. Use `!wt spawn` to try the configured traders and `!wt despawn` to remove them. If configuration fails, check server log lines starting `[WanderingTrader]`. Sale items and attachments must be present in Expansion Market categories, and Expansion `MarketSystemEnabled` must be enabled.

