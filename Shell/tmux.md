# TMUX

TMUX is a terminal multiplexer which allows run parallel shells, detach/re-attach sessions and keep process running even if your SSH got disconnected. 

## Starting and Exiting 

```bash
tmux
tmux new -s name # Start a new named session
tmux ls # List sessions
tmux attach -t name # Attach to a named session
tmux detach # Detach from a session
tmux kill-session -t name # Kill a session 
```

## Windows and Tabs

```bash
Ctrl-b c # Create a new window
Ctrl-b n # Next Window
Ctrl-b p # Previous Window
Ctrl-b w # List Windows
Ctrl-b , # Rename Current Window
Ctrl-b & # Close Current Window
```

## Splitting Screen

```bash
Ctrl-b % # Split Vertically
Ctrl-b " "# Split Horizontally
Ctrl-b o # Switch between panes
Ctrl-b q # Show pane numbers
Ctrl-b x # Close current pane
```

## Resizing Panes

```bash
Ctrl-b up, down, left, right arrows # Resize pane
Ctrl-b Alt + Arrow # Bigger jumps
```

## Session Management

```bash
tmux switch -t work # Switch session
```

## Copy Mode

`Ctrl-b [` Enter copy mode
Use arrows and PgUp/PgDn to scroll
Space -> Start selection, Enter -> Copy
`Ctrl-b ]` Paste the copied selection

