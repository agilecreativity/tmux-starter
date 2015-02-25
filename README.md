## tmux-starter

The simple shell script that automate the tmux sessions the way I like it.
See [this blog post](http://choomnuan.com/blog/2015/02/21/how-to-automate-your-tmux-sessions-using-simple-shell-script/)
for more detail.

### Why do I need this stuff

- I am sick of adding tabs in iTerm e.g. Cmd-Tab is for chump!
- When using the small monitor it makes sense to use one window per pane e.g. Macbook Air
- When you have a bigger screen then you want ot have multiple panes per windows e.g. Macbook Air with external monitor
- So first start with all one pane per window
- Then use the magic of `:join-pane` command to stitch windows together
- Or if you back to your small screen use `:break-pane -t :` as needed!
- It is just a shell script, you may not have ruby installed in the server! but tmux is available!!
  e.g. this is the case when you are on the client's site, tmux is installed, but hey may not be ruby! (thus tmuxinator is not an option!)
- use of `$tmux source-file ~/.tmux.conf` to reload the config on the fly!

### Usage

Please read the script and adjust the folowing information for your use.
The script is very small.

You will need to at least change the following code (at the end of the script) to your specific project.

```sh
# Note: main program {{{
g_session_name=$1
# Use the default value if not specified
: ${g_session_name:="rails-dev"}
g_session_count=0

kill_process 'sidekiq'
kill_process 'bin/rails'
kill_process 'redis-server'
set_base_environment $g_session_name

kill-session $g_session_name

PROJECT_DIR='~/codes/exec-io/envisage'

create-session 'redis-server' 'cd $PROJECT_DIR; redis-server'
add-window 'sidekiq'          'cd $PROJECT_DIR; bundle exec sidekiq'
add-window 'rails-console'    'cd $PROJECT_DIR; rails console'
add-window 'rails-server'     'cd $PROJECT_DIR; rails server'
add-window 'guard'            'cd $PROJECT_DIR; bundle exec guard'
add-window 'editor'           'cd $PROJECT_DIR; vim'

session-info
attach-session
## }}}
```

### Links and resources that I found/used while implementing this script!

- http://unix.stackexchange.com/questions/14300/moving-tmux-window-to-pane
- http://tldp.org/LDP/Bash-Beginners-Guide/html/
- http://askubuntu.com/questions/385528/how-to-increment-a-variable-in-bash
- http://stackoverflow.com/questions/5014632/how-can-i-parse-a-yaml-file-from-a-linux-shell-script
- http://www.cyberciti.biz/tips/howto-setting-default-values-for-shell-variables.html
- [tmuxinator][] - Ruby gem that make provision tmux easier (require ruby)
- [Good introduction to Bash](http://www.tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html)
- [http://stackoverflow.com/questions/3432536/create-session-if-none-exists](http://stackoverflow.com/questions/3432536/create-session-if-none-exists)
- http://robots.thoughtbot.com/a-tmux-crash-course
- http://code.tutsplus.com/tutorials/test-driving-shell-scripts--net-31487
- https://code.google.com/p/shunit2/downloads/list
- https://code.google.com/p/shunit2/wiki/GeneralFaq
- [Tmux Crash Course](http://robots.thoughtbot.com/a-tmux-crash-course)
- http://superuser.com/questions/492266/run-or-send-a-command-to-a-tmux-pane-in-a-running-tmux-session

### Useful tmux commands

```
# Sessions
tmux switch -t session_name
tmux attach -t session_name
tmux list-sessions (tmux ls)
tmux detach (prefix + d)

# Windows
tmux new-window (prefix + c)
tmux select-window -t :0-9 (prefix + 0-9) [note: depend on your pane-base-index]
tmux rename-window (prefix + ,)

# Panes
tmux split-window (prefix + ")
tmux split-window -h (prefix + %)
tmux split-pane -[UDLR] (prefix + { or })
tmux select-pane -[UDLR]
tmux select-pane -t :.+

# Misc commands
tmux list-keys
tmux list-commands
tmux info
tmux source-file ~/.tmux.conf

# Must haves
## remap prefix to Control + a
set -g prefix C-a
unbind C-b
bind C-a send-prefix

## force a reload of a config file
unbind r
bind r source-file ~/.tmux.conf

## Quick pane cycling (i use
unbind ^A
bind ^A select-pand -t :.+
```

### Misc command that you may like to know about tmux

```
tmux start-server\; has-sesstion -t {{name}} 2>/dev/null
tmux new-session -d -s {{name}} -n {{windows.first.name}}

# if tmux version < 1.7
tmux set-option -t {{name}} default-path {{root}} 1>/dev/null

# if tmux version >= 1.7
tmux set-option -t {{name}} -c           {{root}} 1>/dev/null
```
