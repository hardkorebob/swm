# SWM

A scriptable tabbed manual-tiling window manager for X11. No dependencies beyond Xlib. Inspired by Notion and TinyWM.

![Screenshot](scrot.png)

Hello, everybody. I just wanted to show off the project that I made with Claude and wanted to write a personal note using my voice and the tool called Handy to dictate my text. The following is a technical summary that Claude wrote. I've been playing with LLMs for a minute, and I just wanted to say thank you to everyone for checking the project out and having all the work that everyone's done in the world to make this possible. I use a [Sanskrit](https://github.com/hardkorebob/spte) inspired prompt engineering style for building my tools. I don't use agents/MCP. Patience and caution one step a time. I usually start with a prototype in HTML or Python and then I'll convert it to whatever language seems necessary for the type of tool that I'm making. And of course after a lot of trial and error, I get it to work and I learn along the way.  I really appreciate everyone. Thank you so much. And also it's a fun little window manager. It's a different take on my way of using my computer. So please enjoy. Thank you again. PS: I'm sure there is a bug or ten kicking around but hey, its all just for fun! Plus I fixed all the ASCII tables cuz AI sucks at art. Womp womp...didnt know markdown ascii tables look like real tables in readme... all that time fixing the | to line up ;-)

## Features

- Binary tree tiling with manual horizontal/vertical splits
- Tabbed windows within each tile
- 9 workspaces
- Status bar with workspace indicators, CPU, RAM, IP, Volume (mouse-wheel to adjust)
- Time bar with hex-time (block of 3m 45s * 16 = One hour; Blocks 0-F)
- EWMH fullscreen support
- Scriptable IPC via Unix domain socket (`swmctl`)
- Runtime config reload (SIGHUP or `swmctl reload`)
- Click-to-focus, click on tab bar to switch tabs
- Raise only using Mod4+Arrow (Amiga-style)

## Build & Install

Requires `libX11-dev` library & `-misc-fixed-medium-r-*-*-13-*-*-*-*-*-iso8859-1` bitmap font.

`chmod +x install.sh`

`./install.sh`

## IPC & swmctl

`I said: The real gold of this project is the scripting. You can make a script where the colors change at a certain interval giving the whole desktop a breathing-like, life-like quality. Think about it. It can also make orchestrating custom layouts a breeze. You could even make a small automated "demo" of all the applications of the computer using this socket scripting capability. What do you think? Outline your findings`

**Claude said:** A UNIX socket at /tmp/swm.sock, newline-delimited text commands, with synchronous ok/err: replies. Any language that can open a socket can drive it. Even `socat` or `nc` works one-shot from the shell.

**What's controllable at runtime via set:**

Every color in the entire UI (23 col_* keys), all geometry (tab bar height, border width, border gap, statusbar/timebar height and position), the update interval, all five program slots (terminal, browser, file manager, launcher, reload command), and keybindings. And set calls cfg_apply() immediately — it flushes the color cache, recalculates bar geometry, tears down and rebuilds the bars, repaints the root window background, re-arranges tiles, and re-grabs keys. So every change is live and instantaneous.

**What's controllable via action commands:**

exec, split h/v, split-move h/v, unsplit, close, quit, reload, fullscreen, tab navigation (next/prev/move forward/backward), workspace switching (next/prev/by number), send to workspace, focus/move in four directions.

Query interface: current workspace number, window count in active tile, active window title, and tile count (layout). That's enough for a script to be state-aware.

**What this enables:**

The breathing colors idea works perfectly. A script just loops and interpolates between two hex values on any combination of the 23 color keys. Since set flushes and repaints immediately, you get smooth visual transitions at whatever cadence you want. You could have the border pulse when a long-running build finishes, or shift the whole palette to warm tones at sunset using the system clock.

The layout orchestration is the bigger deal. A script can exec an app, wait for query win-count to increment (confirming it mapped), then split h, focus right, exec the next app, split v, and so on — building an arbitrary tiled layout programmatically. Since send moves windows between workspaces, you can pre-populate all 9 workspaces from a single startup script: workspace 1 gets terminal + editor side-by-side, workspace 2 gets browser + notes, workspace 3 gets htop + logs stacked, etc.

The demo mode idea is viable because of query. A demo script can narrate what it's doing: switch to workspace 1, exec a terminal, split, exec another, move focus around, switch workspaces — all while the queries let it verify each step succeeded before proceeding. Add set color changes between scenes for visual punctuation. You could record the whole thing with a screen recorder and have a fully automated showcase of the WM.

Pipe mode: `echo "next-tab" | swmctl -`

**Check out demo.sh**

Its a weird example but no less fun!

## Config

```
# SWM configuration
# Place at: ~/.config/swm/config
# Reload:   kill -HUP $(pidof swm)
#      or:  swmctl reload

# ===== Keys ======

bind = F1                  prev_tab
bind = F2                  next_tab
bind = F3                  prev_ws
bind = F4                  next_ws
bind = F6                  close
bind = F7                  spawn launcher
bind = F8                  spawn file_manager
bind = F9                  spawn browser
bind = F10                 spawn terminal
bind = Mod4+r              spawn reload
bind = Mod4+h              split_h
bind = Mod4+v              split_v
bind = Mod4+d              remove_split
bind = Mod4+q              quit
bind = Mod4+comma          move_tab_bwd
bind = Mod4+period         move_tab_fwd
bind = Mod4+Left           focus left
bind = Mod4+Right          focus right
bind = Mod4+Up             focus up
bind = Mod4+Down           focus down
bind = Mod4+Shift+h        split_h_move
bind = Mod4+Shift+v        split_v_move
bind = Mod4+Shift+Left     move_win left
bind = Mod4+Shift+Right    move_win right
bind = Mod4+Shift+Up       move_win up
bind = Mod4+Shift+Down     move_win down
bind = Mod4+1              switch_ws 1
bind = Mod4+2              switch_ws 2
bind = Mod4+3              switch_ws 3
bind = Mod4+4              switch_ws 4
bind = Mod4+5              switch_ws 5
bind = Mod4+6              switch_ws 6
bind = Mod4+7              switch_ws 7
bind = Mod4+8              switch_ws 8
bind = Mod4+9              switch_ws 9
bind = Mod4+Shift+1        send_to_ws 1
bind = Mod4+Shift+2        send_to_ws 2
bind = Mod4+Shift+3        send_to_ws 3
bind = Mod4+Shift+4        send_to_ws 4
bind = Mod4+Shift+5        send_to_ws 5
bind = Mod4+Shift+6        send_to_ws 6
bind = Mod4+Shift+7        send_to_ws 7
bind = Mod4+Shift+8        send_to_ws 8
bind = Mod4+Shift+9        send_to_ws 9

# ===== Geometry =====
# 0 = bottom, 1 = top

tab_bar_height      = 18
border_width        = 1
border_gap          = 2
statusbar_pos       = 0        
statusbar_height    = 24
timebar_pos         = 1        
timebar_height      = 16
bar_update_interval = 1.0

# ===== Commands =====

terminal     = xterm
file_manager = thunar
browser      = firefox
launcher     = dmenu_run

# ===== Colours — status bar =====

col_statusbar_bg          = #1E1E1E
col_statusbar_fg          = #FFBF00
col_statusbar_ws_active   = #fbe7ac
col_statusbar_ws_inactive = #555555
col_statusbar_ws_occupied = #888888
col_statusbar_ws_fg_act   = #000000
col_statusbar_ws_fg_inact = #AAAAAA

# ===== Colours — tab bar =====

col_tab_active_bg     = #fbe7ac
col_tab_inactive_bg   = #3C3C3C
col_tab_active_fg     = #000000
col_tab_inactive_fg   = #AAAAAA
col_tab_bar_bg        = #2B2B2B
col_tab_active_bg_dim = #555555    
col_tab_active_fg_dim = #CCCCCC

# ===== Colours — borders / desktop =====

col_border_active   = #696969
col_border_inactive = #1E1E1E
col_desktop_bg      = #000000

# ===== Colours — bottom hex-time bar =====

col_timebar_bg     = #1E1E1E
col_timebar_hour   = #CC3300
col_timebar_hex    = #00CC66
col_timebar_hseg   = #CCAA00
col_timebar_label  = #FFFFFF
col_timebar_track  = #333333
```
