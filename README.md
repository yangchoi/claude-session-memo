# claude-session-memo

Share memos between Claude Code sessions for parallel work handoff.

## Installation

```bash
# Clone to your plugins directory
cd ~/.claude/plugins
git clone https://github.com/yangchoi/claude-session-memo.git
```

Or add to your `~/.claude/plugins/` directory manually.

## Usage

### Save a memo
```
/memo save Backend API done: POST /api/users/verify
```

### List memos
```
/memo list
/memo
```

### Clear all memos
```
/memo clear
```

### Delete specific memo
```
/memo delete 1
```

## Use Case

When working with multiple Claude Code sessions in parallel:

**Session A (Backend work):**
```
/memo save API complete: POST /api/users/verify - uses OAuth
```

**Session B (Frontend work):**
```
/memo list
# Shows: [1] 2024-02-11 - API complete: POST /api/users/verify - uses OAuth

# Now you know the API is ready to integrate
```

## Storage

Memos are stored in `~/.claude/session-memos.json`

## License

MIT
