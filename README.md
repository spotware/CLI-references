<!--
Run `ctrader-cli.exe --help` for the built-in overview and
`ctrader-cli.exe --commands` for the full interactive reference (no auth).

Two invocation modes:

LAUNCH form (preferred for scripts):
  ctrader-cli <command> --ctid=<cTID> --password=<value> --account=<account-id> -q
  Runs one command non-interactively. Credentials are prefilled; -q
  exits after the command finishes. Flags are --name=value form.

INTERACTIVE shell form (at the > prompt):
  ctrader-cli
  Opens a menu-driven shell. Inside, commands use a POSITIONAL template
  with no -- flags. Optional keywords `yes` and `all` may appear as the
  last positional(s). Example: `> position close all yes`.

Both forms accept the same commands. The flag form is canonical for
non-interactive use; the positional form is for the in-shell prompt.
-->

# cTrader CLI command reference

Command reference for the cTrader CLI. Documents every command, flag, option and homonym-routing rule for non-interactive use.

Each command has a heading, a fenced bash block, an **Auth:** marker, a **Required flags:** line and a per-flag Markdown table. The two auth modes (batch with `--pwd-file`, interactive with `--password` and `-q`) are documented per command; the five homonym commands are flagged inline and explained in [Mode routing](#mode-routing).

## Mode routing

Five commands share a name between batch and interactive modes: `accounts`, `symbols`, `metadata`, `run`, `backtest`. The flags you supply pick which one runs — pass `--pwd-file` or `--broker` (or no auth flags at all) for the batch route, and `--password`, `-q`, or any command-specific flag for the interactive route. Mixing them produces an error such as `Missing --ctid in non-interactive mode` or `Missing --pwd-file`. See [Authentication](#authentication) and [Notes → Homonym routing](#homonym-routing) for the full ruleset.

## Global options

These options apply across commands. Run `ctrader-cli.exe --help` for the canonical list.

| Option | Description |
|---|---|
| `--help` or `-h` | List the available commands and their options. |
| `--commands` | Print the full interactive command reference and exit (no auth). |
| `--version` or `-v` | Show the installed cTrader CLI version. |
| `--ctid` or `-c` | cTrader ID username or email. |
| `--password` | cTrader ID password supplied directly (interactive commands, used with `-q`). |
| `--pwd-file` | Path to a file that holds the cTID password (batch commands). |
| `--account` or `-a` | Trading account login number. |
| `--broker` | Broker name, used when accounts on different brokers share a number. |
| `--environment-variables` or `-e` | Read option values from environment variables instead of the command line. |
| `--full-access` | Run a cBot without access-right restrictions. |
| `--exit-on-stop` | Exit the cTrader CLI process when the cBot stops. |
| `-q`, `--quick` or `--quit` | Run a single command, then exit. |
| `--yes` or `-y` | Skip confirmation prompts. |
| `--all` | Target every applicable entity, with `stop`, `order cancel`, `position close` and `alert delete`. |

## Authentication

cTrader CLI uses two conventions for credentials. The two are not interchangeable: batch commands reject `--password`; interactive commands reject `--pwd-file`. Reset compromised credentials from your cTrader ID account settings.

Five commands (`accounts`, `symbols`, `metadata`, `run`, `backtest`) share a name between batch and interactive modes. See [Mode routing](#mode-routing) above and [Notes → Homonym routing](#homonym-routing) for the full ruleset.

## Period tokens

`--period` (most commands) and `--timeframe` (only `optimize`) accept period tokens such as `m1`, `h1`, `D1`, `Month1`. Token parsing is case-insensitive. Run [`ctrader-cli periods`](#periods) for the canonical current list.

| Family | Tokens |
|---|---|
| Minute bars | `m1` to `m45` |
| Hour bars | `h1`, `h2`, `h3`, `h4`, `h6`, `h8`, `h12` |
| Day bars | `d1`, `d2`, `d3` |
| Week / month | `w1`, `month1` |
| Tick / Renko / Range / Heiken Ashi | `t1`–`t1000`, `re1`–`re2000`, `ra1`–`ra10000`, `hm1`–`hmonth1` |

## Accounts

List the accounts linked to a cTrader ID and read account state.

### `accounts`

List every account linked to the cTrader ID, optionally filtered by broker.

**Auth:** batch  
**Required flags:** `--ctid`, `--pwd-file`

```bash
ctrader-cli accounts --ctid=<cTID> --pwd-file=<path-to-pwd-file>
ctrader-cli accounts --ctid=<cTID> --pwd-file=<path-to-pwd-file> --broker=<broker-name>
ctrader-cli accounts --ctid=<cTID> --pwd-file=<path-to-pwd-file> --account=<account-id>
```

Lists every account when no `--account` is supplied; pass `--account` to limit the result to one entry.

### `account`

Show one account's details such as broker, currency and leverage.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli account --ctid=<cTID> --password=<password> --account=<account-id> -q
```

### `account switch`

Switch the active account inside the interactive shell.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `-q`

```bash
ctrader-cli account switch <account-id>
```

### `account-stats`

Show balance, equity, margin and related statistics.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli account-stats --ctid=<cTID> --password=<password> --account=<account-id> -q
```

## Symbols

List tradable symbols on an account and inspect symbol details and trading sessions.

### `symbols`

List every symbol available on the trading account, optionally filtered by broker.

**Auth:** batch  
**Required flags:** `--ctid`, `--pwd-file`, `--account`

```bash
ctrader-cli symbols --ctid=<cTID> --pwd-file=<path-to-pwd-file> --account=<account-id>
ctrader-cli symbols --ctid=<cTID> --pwd-file=<path-to-pwd-file> --account=<account-id> --broker=<broker-name>
```

### `symbol`

Show details for one symbol such as digits, lot size and swap rules.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`

```bash
ctrader-cli symbol --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol>
```

### `sessions`

Show trading sessions for a symbol including open and close times.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`

```bash
ctrader-cli sessions --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol>
```

## Market data

Read current prices and historical candles from the trading server.

### `price`

Show the current bid and ask for one symbol.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`

```bash
ctrader-cli price --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol>
```

### `prices`

Show the current bid and ask for several symbols.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbols`

```bash
ctrader-cli prices --ctid=<cTID> --password=<password> --account=<account-id> -q --symbols=<symbol-1>,<symbol-2>
```

### `candles`

Return historical candles for a symbol. Choose exactly one of `--count`, or the `--from` and `--to` pair.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`, `--period`

```bash
ctrader-cli candles --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --period=<period> --count=<count>
ctrader-cli candles --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --period=<period> --from=<date> --to=<date>
```

## Orders

List pending orders, inspect an order and place, modify or cancel pending orders.

### `orders`

List active pending orders.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli orders --ctid=<cTID> --password=<password> --account=<account-id> -q
```

### `order`

Show details for one pending order.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--order`

```bash
ctrader-cli order --ctid=<cTID> --password=<password> --account=<account-id> -q --order=<order-id>
```

### `order place-market`

Fill at the next available price.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`, `--side`, `--volume`

```bash
ctrader-cli order place-market --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --side=<side> --volume=<volume> --sl=<sl> --tp=<tp>
```

See [Notes → Volume semantics](#volume-semantics) for the `--volume-type` flag.

### `order place-limit`

Place a limit order at a chosen price.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`, `--side`, `--volume`, `--price`

```bash
ctrader-cli order place-limit --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --side=<side> --volume=<volume> --price=<price> --sl=<sl> --tp=<tp>
```

See [Notes → Volume semantics](#volume-semantics) for the `--volume-type` flag.

### `order place-stop`

Place a stop order that triggers at a chosen price.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`, `--side`, `--volume`, `--stop-price`

```bash
ctrader-cli order place-stop --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --side=<side> --volume=<volume> --stop-price=<stop-price> --sl=<sl> --tp=<tp>
```

See [Notes → Volume semantics](#volume-semantics) for the `--volume-type` flag.

### `order place-stop-limit`

Place a stop-limit order with a slippage allowance once the stop fires.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`, `--side`, `--volume`, `--stop-price`, `--limit-range`

```bash
ctrader-cli order place-stop-limit --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --side=<side> --volume=<volume> --stop-price=<stop-price> --limit-range=<limit-range> --sl=<sl> --tp=<tp>
```

See [Notes → Volume semantics](#volume-semantics) for the `--volume-type` flag.

### `order modify`

Change a pending order.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--order`

```bash
ctrader-cli order modify --ctid=<cTID> --password=<password> --account=<account-id> -q --order=<order-id> --price=<price> --sl=<sl> --tp=<tp>
```

| Flag | Description |
|---|---|
| `--stop-price` | New stop price. |
| `--volume` | New order volume (default units; use `--volume-type` to switch). |
| `--volume-type` | `units` (default) or `lots`. Interprets `--volume`. |

### `order cancel`

Cancel a pending order. Pass `--order=<order-id>` to cancel one, or `--all` to cancel every pending order. Pass `--yes` to skip confirmation. Inside the interactive shell, append `all` and `yes` as the last positionals: `> order cancel all yes`.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli order cancel --ctid=<cTID> --password=<password> --account=<account-id> -q --order=<order-id> --yes
ctrader-cli order cancel --ctid=<cTID> --password=<password> --account=<account-id> --all --yes -q
```

## Positions

List open positions, inspect a position, change stop loss or take profit, and close in full or in part.

### `positions`

List open positions.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli positions --ctid=<cTID> --password=<password> --account=<account-id> -q
```

### `position`

Show details for one open position.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--position`

```bash
ctrader-cli position --ctid=<cTID> --password=<password> --account=<account-id> -q --position=<position-id>
```

### `position modify`

Change the stop loss or take profit of an open position.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--position`

```bash
ctrader-cli position modify --ctid=<cTID> --password=<password> --account=<account-id> -q --position=<position-id> --sl=<sl> --tp=<tp>
```

### `position close`

Close an open position in full. Pass `--position=<position-id>` to close one, or `--all` to close every open position. Pass `--yes` to skip confirmation. Inside the interactive shell: `> position close all yes`.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli position close --ctid=<cTID> --password=<password> --account=<account-id> -q --position=<position-id> --yes
ctrader-cli position close --ctid=<cTID> --password=<password> --account=<account-id> --all --yes -q
```

### `position close-partial`

Close part of an open position.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--position`, `--volume`

```bash
ctrader-cli position close-partial --ctid=<cTID> --password=<password> --account=<account-id> -q --position=<position-id> --volume=<volume> --yes
```

## History

Read past trading activity and current exposure.

### `deals`

List every deal in a date range, or the most recent N deals. Choose one of `--from` and `--to`, or `--count`. Optionally scope to a single `--symbol`.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli deals --ctid=<cTID> --password=<password> --account=<account-id> -q --from=<date> --to=<date>
ctrader-cli deals --ctid=<cTID> --password=<password> --account=<account-id> -q --count=<count>
ctrader-cli deals --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --from=<date> --to=<date>
```

### `orders-history`

List every completed order in a date range, or the most recent N completed orders. Choose one of `--from` and `--to`, or `--count`. Completed orders have a status of `filled`, `expired`, `cancelled` or `error`. Pending orders are not included.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli orders-history --ctid=<cTID> --password=<password> --account=<account-id> -q --from=<date> --to=<date>
ctrader-cli orders-history --ctid=<cTID> --password=<password> --account=<account-id> -q --count=<count>
```

### `exposure`

Show current exposure by symbol for one account.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli exposure --ctid=<cTID> --password=<password> --account=<account-id> -q
```

## Indicators

List the indicators available on the trading server, show an indicator's parameters and return current or historical values.

### `indicators`

List the indicators available on the trading server.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli indicators --ctid=<cTID> --password=<password> --account=<account-id> -q
```

### `indicator parameters`

Show the parameters an indicator accepts, with default values.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--indicator`

```bash
ctrader-cli indicator parameters --ctid=<cTID> --password=<password> --account=<account-id> -q --indicator=<indicator-name>
```

### `indicator calculate`

Calculate the current value of an indicator for a symbol and period.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--indicator`, `--symbol`, `--period`

```bash
ctrader-cli indicator calculate --ctid=<cTID> --password=<password> --account=<account-id> -q --indicator=<indicator-name> --symbol=<symbol> --period=<period>
ctrader-cli indicator calculate --ctid=<cTID> --password=<password> --account=<account-id> -q --indicator=<indicator-name> --symbol=<symbol> --period=<period> --ind-params=<ind-params>
```

### `indicator history`

Return historical values for an indicator.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--indicator`, `--symbol`, `--period`, `--count`

```bash
ctrader-cli indicator history --ctid=<cTID> --password=<password> --account=<account-id> -q --indicator=<indicator-name> --symbol=<symbol> --period=<period> --count=<count>
ctrader-cli indicator history --ctid=<cTID> --password=<password> --account=<account-id> -q --indicator=<indicator-name> --symbol=<symbol> --period=<period> --count=<count> --ind-params=<ind-params>
```

## Alerts

List, create and delete price alerts.

### `alerts`

List price alerts on an account.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli alerts --ctid=<cTID> --password=<password> --account=<account-id> -q
```

### `alert create`

Create a price alert.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`, `--symbol`, `--price`, `--condition`

```bash
ctrader-cli alert create --ctid=<cTID> --password=<password> --account=<account-id> -q --symbol=<symbol> --price=<price> --condition=<condition> --message=<message> --yes
```

### `alert delete`

Delete a price alert. Pass `--alert=<alert-id>` to delete one, or `--all` to delete every alert. Pass `--yes` to skip confirmation. Inside the interactive shell: `> alert delete all yes`.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli alert delete --ctid=<cTID> --password=<password> --account=<account-id> -q --alert=<alert-id> --yes
ctrader-cli alert delete --ctid=<cTID> --password=<password> --account=<account-id> --all --yes -q
```

## cBot lifecycle

List cBot instances, show an `.algo` file's metadata, run a cBot on an account, stop it, backtest it against historical data and sweep its parameters.

### `cbots`

List the cBot instances currently running on the account.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli cbots --ctid=<cTID> --password=<password> --account=<account-id> -q
```

### `metadata`

Show metadata for an `.algo` file, including parameters and properties.

**Auth:** batch (no auth required)  
**Required flags:** none

```bash
ctrader-cli metadata <path-to-algo-file>
```

### `run`

Launch a cBot and stream its output to stdout. Blocks until the cBot exits.

**Auth:** routes by flag (batch with `--pwd-file`/`--broker`; interactive with `--password`/`-q`/command flags)

**Required flags:** `<path-to-algo-file>`, `--symbol`, `--period`  
**Conditional:** batch needs `--ctid`, `--pwd-file`, `--account`; interactive uses `-q` and credentials are supplied interactively

```bash
ctrader-cli run --ctid=<cTID> --pwd-file=<path-to-pwd-file> --account=<account-id> <path-to-algo-file> [<path-to-cbotset-file>] --symbol=<symbol> --period=<period> --exit-on-stop
```

See [Notes → cBot parameter overrides](#cbot-parameter-overrides) for passing custom cBot parameters.

### `stop`

Stop a running cBot instance by its identifier. Pass `--instance=<instance-id>` to stop one, or `--all` to stop every running cBot. Pass `--yes` to skip confirmation. Inside the interactive shell: `> stop all yes`.

**Auth:** interactive  
**Required flags:** `--ctid`, `--password`, `--account`, `-q`

```bash
ctrader-cli stop --ctid=<cTID> --password=<password> --account=<account-id> -q --instance=<instance-id> --yes
ctrader-cli stop --ctid=<cTID> --password=<password> --account=<account-id> --all --yes -q
```

### `backtest`

Run a cBot against historical data.

**Auth:** routes by flag (batch with `--pwd-file`/`--broker`; interactive with `--password`/`-q`/command flags)

**Required flags:** `<path-to-algo-file>`, `--symbol`, `--period`, `--start`, `--end`, `--data-mode`  
**Conditional:** batch needs `--ctid`, `--pwd-file`, `--account`

```bash
ctrader-cli backtest --ctid=<cTID> --pwd-file=<path-to-pwd-file> --account=<account-id> <path-to-algo-file> [<path-to-cbotset-file>] --symbol=<symbol> --period=<period> --start=<start> --end=<end> --data-mode=<data-mode>
```

| Flag | Description |
|---|---|
| `--data-file` | Path to a CSV file that provides the historical data. |
| `--balance` | Starting capital. |
| `--commission` | Commission amount. Interpretation depends on `--commission-type`. |
| `--commission-type` | `UsdPerMillionUsdVolume` (default), `UsdPerOneLot`, `PercentageOfTradingVolume`, `QuoteCurrencyPerOneLot`. |
| `--commission-auto` | Use the symbol's real commission and ignore `--commission` and `--commission-type`. |
| `--spread` | Spread override in pips. |
| `--report` | Path to save an HTML report. |
| `--report-json` | Path to save a JSON report. |
| `--precise-conversion` | Download real historical exchange rates for accurate profit/margin conversion. |
| `--CustomParameter=<value>` | Set any cBot parameter by name (repeat per parameter). |

### `optimize`

Sweep cBot parameter values. Uses `--timeframe` instead of `--period`. Local edition only.

**Auth:** batch  
**Required flags:** `--ctid`, `--pwd-file`, `--account`, `<path-to-algo-file>`, `--params`, `--symbol`

```bash
ctrader-cli optimize --ctid=<cTID> --pwd-file=<path-to-pwd-file> --account=<account-id> <path-to-algo-file> --params=<path-to-params-file> --symbol=<symbol> --timeframe=<timeframe>
```

| Flag | Description |
|---|---|
| `--timeframe` or `-t` | One or more comma-separated timeframes. One pins the main timeframe; several sweep it. Takes precedence over the optset file's timeframe. |
| `--start` | Backtest start, in `dd/MM/yyyy [hh:mm]` format (UTC). |
| `--end` | Backtest end, in `dd/MM/yyyy [hh:mm]` format (UTC). |
| `--data-mode` | `ticks`, `m1`, `m1-csv`, `tick-csv` or `open`. |
| `--data-file` | Path to a CSV file that provides the historical data. |
| `--balance` | Starting capital. |
| `--commission` | Commission amount. Interpretation depends on `--commission-type`. |
| `--commission-type` | `UsdPerMillionUsdVolume` (default), `UsdPerOneLot`, `PercentageOfTradingVolume`, `QuoteCurrencyPerOneLot`. |
| `--commission-auto` | Use the symbol's real commission and ignore `--commission` and `--commission-type`. |
| `--spread` | Spread override in pips. |
| `--method` | `genetic` (default) or `grid`. |
| `--cores` | Number of CPU cores to use. |
| `--criteria` | Comma-separated optimization criteria (e.g. `NetProfit:max,MaxEquityDrawdownPercentages:min`). |
| `--fitness` | Use the cBot custom `GetFitness` function instead of criteria. |
| `--auto-select-best` | Auto-select the best pass on completion. |
| `--optres` | Path to save the `.optres` result file (JSON). |
| `--passes-dir` | Directory to save per-pass report files. |
| `--report` | Path to save an HTML report. |
| `--report-json` | Path to save a JSON report. |
| `--precise-conversion` | Download real historical exchange rates for accurate profit/margin conversion. |

## Algo projects

Scaffold a new cBot, indicator or plugin project and compile it into an `.algo` file. No authentication is required.

### `create`

Scaffold a new cBot, indicator or plugin project. The first positional picks the project kind (`cbot`, `indicator`, `plugin`), the second picks the project name and the optional third picks the language (`csharp` (default) or `python`).

**Auth:** none  
**Required flags:** none

```bash
ctrader-cli create cbot MyFirstBot python
ctrader-cli create --kind=cbot --name=MyFirstBot --language=python
```

| Flag | Description |
|---|---|
| `--kind` | `cbot`, `indicator` or `plugin` |
| `--name` | Algorithm identifier (becomes the class name and folder name; must be a valid identifier in the target language) |
| `--language` | `csharp` (default) or `python` |

### `build`

Build an algo project into an `.algo` file. Pass a path to a `.csproj`/`.sln` file or a project folder (the inner `.csproj` is found automatically).

**Auth:** none  
**Required flags:** none

```bash
ctrader-cli build <path-to-project>
ctrader-cli build --project-path=<path-to-project>
```

## Periods

List every period token accepted by `--period` and `--timeframe`. No account or credentials are required.

### `periods`

Print every supported period token.

**Auth:** none  
**Required flags:** none

```bash
ctrader-cli periods
```

## Help

Show the full command reference inside the interactive shell prompt.

### `help`

Print the interactive shell's full command reference. Equivalent to launching the shell without arguments and typing `help` at the `>` prompt.

**Auth:** none  
**Required flags:** none

```bash
ctrader-cli help
```

## Notes

### Auth modes reference

| Pattern | Required options | Used by |
|---|---|---|
| Batch | `--ctid` and `--pwd-file` | `periods`, `accounts`, `symbols`, `metadata`, `run`, `backtest`, `optimize` |
| Interactive | `--ctid`, `--password` and `-q` | every other command |

### Homonym routing

Commands `accounts`, `symbols`, `metadata`, `run`, `backtest` exist in both modes. The flags you supply pick which one runs.

- **Batch route:** args contain `--pwd-file=<path>` or `--broker=<name>`, or no auth flags at all.
- **Interactive route:** args contain any of `--password=<value>`, `-q`/`--quick`/`--quit`, `--commands`, `--yes`/`-y`, `--all`, `--order`, `--position`, `--alert`, `--instance`, `--side`, `--volume`, `--volume-type`, `--sl`, `--tp`, `--stop-price`, `--limit-range`, `--indicator`, `--ind-params`, `--symbols`, `--condition`, `--message`, `--from`, `--to`, `--count`.

### Date formats

- `--from` and `--to` (used by `candles`, `deals`, `orders-history`): `yyyy-MM-dd` or `dd/MM/yyyy`.
- Backtest `--start` and `--end` (used by `backtest`, `optimize`): `dd/MM/yyyy [hh:mm]` in UTC.

### Volume semantics

`--volume` defaults to units of the base currency. Pass `--volume-type=lots` to interpret it as lots.

### Take-profit semantics

`--tp` takes a single price, so a position opened through cTrader CLI has one take-profit level and closes in full when price reaches it. `--tp` on `position modify` replaces that level rather than adding another. Positions opened in the cTrader desktop or web applications can carry several take-profit levels, which cTrader CLI cannot create or reproduce. The effect of `position modify --tp` on a position that already carries several take-profit levels is not documented here.

### Stop-loss semantics

`--sl` takes a single fixed price. A stop loss stays at that price until a later `position modify --sl` moves it, so trailing a stop or bringing it to break-even means issuing each new price explicitly. Trailing stop loss and break-even protection are not part of `--sl` itself.

### Pending order lifetime

A pending order placed with `order place-limit`, `order place-stop` or `order place-stop-limit` stays active until it fills or `order cancel` cancels it.

### Limit-range semantics

`--limit-range` is the slippage allowance applied once the stop price of an `order place-stop-limit` order fires. It belongs to stop-limit orders only; `order place-market` takes no range flag.

### Stop-order trigger semantics

`order place-stop` and `order place-stop-limit` take their trigger level through `--stop-price`. That price is the whole of the trigger definition for both commands.

### Order and position identification

Orders and positions are referenced by the numeric identifiers passed to `--order` and `--position`. No order or position command accepts a comment or label field.

### Confirmation flags

`--yes`/`-y` skips confirmation prompts. `--all` targets every applicable entity (`stop`, `order cancel`, `position close`, `alert delete`). Both also work as the last positional argument inside the interactive shell (`> position close all yes`).

### cBot parameter overrides

In batch mode, pass `--<ParameterName>=<value>` directly. From inside the interactive shell, pass `--robot-params=<key=value,key=value>` to the same `run`/`backtest` commands.

### Indicator parameter aliases

`--indicator` and `--name` are aliases. `--params` and `--ind-params` are aliases.
