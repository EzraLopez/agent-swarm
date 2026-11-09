# agent-swarm

A minimal, secure agent orchestrator for macOS. Launch, monitor, and manage multiple AI coding agents in parallel — each isolated in its own git worktree and tmux session.

**Design principles:**
- **Zero dependencies** — only needs `bash`, `tmux`, `git`, and `jq` (all pre-installed or one `brew install` away)
- **No telemetry** — everything is local, nothing phones home
- **Dedicated tmux socket** — agent sessions are isolated from your own tmux server (`tmux -L agent-swarm`)
- **Worktree isolation** — each agent works on its own branch in its own checkout; no clobbering
- **Auditable** — one bash script you can read end-to-end
- **All 11 agents supported** — Claude Code, Codex, Antigravity, Pocket, Kiro, Grok, OpenCode, Cursor, cursor-agent, Copilot, Muse

## Install

```zsh
cd ~/Development
git clone https://github.com/EzraLopez/agent-swarm.git
./agent-swarm/swarm install   # symlinks `swarm` to ~/.local/bin
```

## Usage

```zsh
swarm agents                  # show all known agents + availability
swarm launch claude "fix bug"  # launch claude in a new worktree
swarm list                     # show all sessions
swarm attach <id>              # attach to a running session
swarm pause <id>               # pause (commit work, free disk, keep branch)
swarm resume <id>              # resume a paused session
swarm diff <id>                # show git diff for a session
swarm stop <id>                # kill + cleanup
swarm merge <id>               # merge branch back to main
```

## Architecture

```
swarm <command> ──► ~/.agent-swarm/state.json   (session registry)
                ──► tmux -L agent-swarm          (isolated tmux server)
                ──► <repo>/.swarm/worktrees/     (git worktrees)
```

Each session = one agent + one git worktree + one tmux session. Sessions survive terminal closes and machine reboots (parked as `paused` if the tmux server dies).
