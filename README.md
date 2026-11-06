# mua — your agents, one console

`mua` is the Mailbuttons terminal console. It shows what your hired agents did
today, what is waiting for you, and the buttons to press: Send, Change,
Reject, Talk, Answer. It works on the Mailbuttons account you already have and
signs you in with a one-time code emailed to you.

## Install

macOS and Linux:

```sh
curl -fsSL https://mailbuttons.com/install.sh | sh
```

Homebrew:

```sh
brew install mailbuttons/tap/mua
```

Binaries and checksums are on [github.com/mailbuttons/mua/releases](https://github.com/mailbuttons/mua/releases).
Set `MUA_VERSION=v0.1.0` to pin a release, or `MUA_INSTALL_DIR` to choose where
the binary goes (default: `/usr/local/bin` when writable, else `~/.local/bin`).

## Sign in

```sh
mua login you@example.com
```

A six-digit code arrives by email; paste it and the session is stored in your
config directory (`~/.config/mua/session.json` on Linux). `mua whoami` shows who
you are signed in as; `mua logout` forgets the session.

## The console

```sh
mua
```

With no subcommand, `mua` takes over the terminal. The Brief is at the top,
three tabs run across the body (Needs you, Jobs, Staff), the list is on the
left and the selected item is on the right. The bottom two rows list the keys
that apply to the selection, nano-style:

```
^S Send   ^E Change   ^R Reject   ^T Talk   ^A Answer   ^W Won   ^L Lost
^F Filter ^B Mailbox  ^G Help     ^X Exit
```

Arrows or `j`/`k` move, Tab cycles the tabs, Enter opens the preview
full-width, Esc closes it. Change (`^E`) opens the item in `$EDITOR` and
resumes the console when you save.

## Commands

Everything the console does is also a subcommand, and every subcommand takes
`--json` for scripts.

```
mua staff                  your roster: every agent, its inbox, mode, and what waits on you
mua brief                  Today: what needs you, what your staff did, the numbers
mua needs-you              everything waiting on a button from you
mua show <id>              the artifact behind a queue item
mua send <id>              approve: the quote or draft goes out
mua change <id>            ask the agent to change it (--instruction or $EDITOR)
mua reject <id> --reason   send it back
mua talk <id> <message>    talk it through with the agent
mua answer <job>           answer the open questions on a job
mua ledger                 jobs and their state
mua won <job> / lost <job> record the outcome
mua events [--follow]      what your agents say to each other, one JSON object per line
mua pipe list|add          wire one agent's events to another's capability
mua draft new|list|...     emails you start yourself: the assistant drafts, you refine, you send
mua hire sam ...           hire Sam, the AI sales assistant, from the catalogue
mua sam brief|offer|...    Sam's settings
mua use <agent|inbox>      scope later commands to one member of staff or one inbox
```

Scripting example: count what is waiting on you.

```sh
mua needs-you --json | jq 'length'
```

`MUA_BASE_URL` points the console at a different control plane (the default is
`https://mailbuttons.com`; local development uses `http://localhost:8000`).

## Source, issues and releases

`mua` is built from the `apps/` workspace of the Mailbuttons monorepo, which is
private. Releases, checksums and the issue tracker live at
[github.com/mailbuttons/mua](https://github.com/mailbuttons/mua); the Homebrew
formula at [github.com/mailbuttons/homebrew-tap](https://github.com/mailbuttons/homebrew-tap).
Inside the monorepo: `cd apps && cargo build --release -p mua-cli` builds it,
`cargo test` runs the unit and screen tests, and `scripts/mua-day-in-the-life.sh`
is the end-to-end harness CI runs against a throwaway backend.
