# claude-tmux-notifier

macOS notifications for [Claude Code](https://claude.com/claude-code) that switch you back to the right tmux pane when clicked.

## What it does

- **Stop**: when Claude finishes a turn, you get a `"Done, waiting for input"` notification.
- **Permission prompt**: when Claude needs your approval, you get a notification with the prompt message.
- **Auto-clear**: notifications are dismissed when you interact with Claude again (next prompt, tool use, or session end).
- **Click-to-focus**: clicking a notification activates your terminal app (auto-detected) and switches your tmux client to the originating session and pane.

## Requirements

- macOS (uses `terminal-notifier`)
- [`terminal-notifier`](https://github.com/julienXX/terminal-notifier): `brew install terminal-notifier`
- `tmux` (optional — with tmux, click-to-focus switches the tmux client back to the originating session+pane; without tmux, click just opens the terminal app)
- `jq` (for parsing hook payloads; usually preinstalled, otherwise `brew install jq`)
- A macOS terminal app — the click-to-focus target is auto-detected from `$__CFBundleIdentifier` (set by macOS to the bundle id of the app that launched the process). Works with Ghostty, iTerm2, Terminal.app, WezTerm, etc., out of the box. To override, set `CLAUDE_NOTIFIER_BUNDLE_ID` (e.g. `com.googlecode.iterm2`).

## Install

In Claude Code:

```
/plugin marketplace add ddzero2c/claude-tmux-notifier
/plugin install claude-tmux-notifier@ddzero2c
```

For local development, point `marketplace add` at a clone instead:

```
/plugin marketplace add ~/path/to/claude-tmux-notifier
/plugin install claude-tmux-notifier
```

## Hook events used

| Event | Behavior |
|---|---|
| `Stop` | Notify "Done, waiting for input" |
| `StopFailure` | Notify "Turn failed (`error_type`)" when a turn ends on an API error |
| `Notification` (matcher: `permission_prompt`) | Notify with the prompt message |
| `Notification` (matcher: `idle_prompt`) | Notify when Claude is idle waiting for input |
| `Elicitation` | Notify "Needs your input" when an MCP server requests input |
| `ElicitationResult` | Clear the session's notification once you answer the elicitation |
| `PreToolUse` | Clear the session's notification |
| `PostToolUse` | Clear the session's notification after a tool runs (e.g. after you approve a permission prompt) |
| `UserPromptSubmit` | Clear the session's notification |
| `SessionStart` (matcher: `resume`) | Clear stale notifications when resuming a session |
| `SessionEnd` | Clear the session's notification |

Notifications are grouped by `claude-<session_id>`, so each Claude session has at most one outstanding notification at a time.

## License

MIT
