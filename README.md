# claude-tmux-notifier

macOS notifications for [Claude Code](https://claude.com/claude-code) that switch you back to the right tmux pane when clicked.

## What it does

- **Stop**: when Claude finishes a turn, you get a `"Done, waiting for input"` notification.
- **Permission prompt**: when Claude needs your approval, you get a notification with the prompt message.
- **Auto-clear**: notifications are dismissed when you interact with Claude again (next prompt, tool use, or session end).
- **Click-to-focus**: clicking a notification opens Ghostty and switches your tmux client to the originating session and pane.

## Requirements

- macOS (uses `terminal-notifier`)
- [`terminal-notifier`](https://github.com/julienXX/terminal-notifier): `brew install terminal-notifier`
- `tmux` (optional — if running outside tmux, click-to-focus falls back to just opening Ghostty)
- `jq` (for parsing hook payloads; usually preinstalled, otherwise `brew install jq`)
- [Ghostty](https://ghostty.org) — the terminal opened on click. To use a different terminal, set `CLAUDE_NOTIFIER_BUNDLE_ID` to your terminal's bundle id (e.g. `com.googlecode.iterm2`, `com.apple.Terminal`).

## Install

Via marketplace (once published):

```
/plugin marketplace add ddzero2c/claude-tmux-notifier
/plugin install claude-tmux-notifier@ddzero2c
```

Or locally for development:

```
git clone https://github.com/ddzero2c/claude-tmux-notifier ~/repo/claude-tmux-notifier
```

Then in Claude Code:

```
/plugin marketplace add ~/repo/claude-tmux-notifier
/plugin install claude-tmux-notifier
```

## Hook events used

| Event | Behavior |
|---|---|
| `Stop` | Notify "Done, waiting for input" |
| `Notification` (matcher: `permission_prompt`) | Notify with the prompt message |
| `PreToolUse` | Clear the session's notification |
| `UserPromptSubmit` | Clear the session's notification |
| `SessionEnd` | Clear the session's notification |

Notifications are grouped by `claude-<session_id>`, so each Claude session has at most one outstanding notification at a time.

## License

MIT
