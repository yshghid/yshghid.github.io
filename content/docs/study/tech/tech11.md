---
date : 2025-05-28
tags: ['2025-05']
categories: ['BI']
bookHidden: true
title: "연구실 bashrc 스크립트"
---

# 연구실 bashrc 스크립트

#2025-05-28

---

#1 local

```bash
  1 #alias cobi2='ssh -p 5290 ysh980101@155.230.28.211'
  2 alias cobi2="ssh -p 3160 ysh980101@155.230.110.91"
  3 alias cobi3="ssh -p 7777 ysh980101@155.230.110.92"
  4 alias cobi4="ssh -p 4712 ysh980101@155.230.110.93"
  5 # >>> conda initialize >>>
  6 # !! Contents within this block are managed by 'conda init' !!
  7 __conda_setup="$('/opt/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null    )"
  8 if [ $? -eq 0 ]; then
  9     eval "$__conda_setup"
 10 else
 11     if [ -f "/opt/anaconda3/etc/profile.d/conda.sh" ]; then
 12         . "/opt/anaconda3/etc/profile.d/conda.sh"
 13     else
 14         export PATH="/opt/anaconda3/bin:$PATH"
 15     fi
 16 fi
 17 unset __conda_setup
 18 # <<< conda initialize <<<
 19 
 20 
 21 
 22 source /opt/homebrew/opt/chruby/share/chruby/chruby.sh
 23 source /opt/homebrew/opt/chruby/share/chruby/auto.sh
 24 chruby ruby-3.1.3
```

#2 cobi2

```bash
  1 # ~/.bashrc: executed by bash(1) for non-login shells.
  2 # see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
  3 # for examples
  4 
  5 # If not running interactively, don't do anything
  6 case $- in
  7     *i*) ;;
  8       *) return;;
  9 esac
 10 
 11 # don't put duplicate lines or lines starting with space in the history.
 12 # See bash(1) for more options
 13 HISTCONTROL=ignoreboth
 14 
 15 # append to the history file, don't overwrite it
 16 shopt -s histappend
 17 
 18 # for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
 19 HISTSIZE=1000
 20 HISTFILESIZE=2000
 21 
 22 # check the window size after each command and, if necessary,
 23 # update the values of LINES and COLUMNS.
 24 shopt -s checkwinsize
 25 
 26 # If set, the pattern "**" used in a pathname expansion context will
 27 # match all files and zero or more directories and subdirectories.
 28 #shopt -s globstar
 29 
 30 # make less more friendly for non-text input files, see lesspipe(1)
 31 [ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"
 32 
 33 # set variable identifying the chroot you work in (used in the prompt below)
 34 if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
 35     debian_chroot=$(cat /etc/debian_chroot)
 36 fi
 37 
 38 # set a fancy prompt (non-color, unless we know we "want" color)
 39 case "$TERM" in
 40     xterm-color|*-256color) color_prompt=yes;;
 41 esac
 42 
 43 # uncomment for a colored prompt, if the terminal has the capability; turned
 44 # off by default to not distract the user: the focus in a terminal window
 45 # should be on the output of commands, not on the prompt
 46 #force_color_prompt=yes
 47 
 48 if [ -n "$force_color_prompt" ]; then
 49     if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
 50     # We have color support; assume it's compliant with Ecma-48
 51     # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
 52     # a case would tend to support setf rather than setaf.)
 53     color_prompt=yes
 54     else
 55     color_prompt=
 56     fi
 57 fi
 58 
 59 if [ "$color_prompt" = yes ]; then
 60     PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
 61 else
 62     PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
 63 fi
 64 unset color_prompt force_color_prompt
 65 
 66 # If this is an xterm set the title to user@host:dir
 67 case "$TERM" in
 68 xterm*|rxvt*)
 69     PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
 70     ;;
 71 *)
 72     ;;
 73 esac
 74 
 75 # enable color support of ls and also add handy aliases
 76 if [ -x /usr/bin/dircolors ]; then
 77     test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
 78     alias ls='ls --color=auto'
 79     #alias dir='dir --color=auto'
 80     #alias vdir='vdir --color=auto'
 81 
 82     alias grep='grep --color=auto'
 83     alias fgrep='fgrep --color=auto'
 84     alias egrep='egrep --color=auto'
 85 fi
 86 
 87 # colored GCC warnings and errors
 88 #export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'
 89 
 90 # some more ls aliases
 91 alias ll='ls -alF'
 92 alias la='ls -A'
 93 alias l='ls -CF'
 94 
 95 # Add an "alert" alias for long running commands.  Use like so:
 96 #   sleep 10; alert
 97 alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
 98 
 99 # Alias definitions.
100 # You may want to put all your additions into a separate file like
101 # ~/.bash_aliases, instead of adding them here directly.
102 # See /usr/share/doc/bash-doc/examples in the bash-doc package.
103 
104 if [ -f ~/.bash_aliases ]; then
105     . ~/.bash_aliases
106 fi
107 
108 # enable programmable completion features (you don't need to enable
109 # this, if it's already enabled in /etc/bash.bashrc and /etc/profile
110 # sources /etc/bash.bashrc).
111 if ! shopt -oq posix; then
112   if [ -f /usr/share/bash-completion/bash_completion ]; then
113     . /usr/share/bash-completion/bash_completion
114   elif [ -f /etc/bash_completion ]; then
115     . /etc/bash_completion
116   fi
117 fi
118 
119 export LANG=ko_KR.UTF-8
120 
121 PS1='[\u@\h \W]\n $ '
122 
123 EDITOR=vim; export EDITOR
124 
125 # User specific aliases and functions
126 alias rm='rm -i'
127 alias cp='cp -i'
128 alias mv='mv -i'
129 alias l.='ls -dl .[a-zA-Z]*'
130 alias ll='ls -lht --color=tty'
131 alias ls='ls -F --color=auto --show-control-char'
132 alias grep='grep --color=auto'
133 alias vi='vim'
134 alias sudo='sudo '
135 
136 alias tophatpy='/usr/local/src/tophat-2.0.13/src/tophat.py'
137 
138 KALLISTO=/data1/packages/kallisto
139 BOWTIE2=/data1/packages/bowtie2
140 BWA=/data1/packages/bwa/bin
141 BISMARK=/data3/workshop/2023_methylation_analysis/tool/Bismark-0.22.3/
142 TOPHAT=/usr/local/src/tophat-2.0.13/src/
143 SRA=/data/home/ysh980101/2310/sratoolkit/sratoolkit.3.0.7-centos_linux64/bin
144 
145 TOOLS=$TOOLS:$BOWTIE2:$BISMARK:$TOPHAT:$KALLISTO:$BWA:$SRA
146 PATH=$PATH:$TOOLS
147 
148 export PATH
149 
150 
151 # >>> conda initialize >>>
152 # !! Contents within this block are managed by 'conda init' !!
153 __conda_setup="$('/data1/home/ysh980101/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
154 if [ $? -eq 0 ]; then
155     eval "$__conda_setup"
156 else
157     if [ -f "/data1/home/ysh980101/miniconda3/etc/profile.d/conda.sh" ]; then
158         . "/data1/home/ysh980101/miniconda3/etc/profile.d/conda.sh"
159     else
160         export PATH="/data1/home/ysh980101/miniconda3/bin:$PATH"
161     fi
162 fi
163 unset __conda_setup
164 # <<< conda initialize <<<
165 
166 alias jupyter='nohup jupyter lab --config .jupyter/jupyter_lab_config.py & >/dev/null'
167 
168 
169 
170 
171 
172 
173 PATH="/data1/home/ysh980101/perl5/bin${PATH:+:${PATH}}"; export PATH;
174 PERL5LIB="/data1/home/ysh980101/perl5/lib/perl5${PERL5LIB:+:${PERL5LIB}}"; export PERL5LIB;
175 PERL_LOCAL_LIB_ROOT="/data1/home/ysh980101/perl5${PERL_LOCAL_LIB_ROOT:+:${PERL_LOCAL_LIB_ROOT}}"; export PERL_LOCAL_LIB_ROOT;
176 PERL_MB_OPT="--install_base \"/data1/home/ysh980101/perl5\""; export PERL_MB_OPT;
177 PERL_MM_OPT="INSTALL_BASE=/data1/home/ysh980101/perl5"; export PERL_MM_OPT;
```

#3 cobi3

```bash
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
        # We have color support; assume it's compliant with Ecma-48
        # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
        # a case would tend to support setf rather than setaf.)
        color_prompt=yes
    else
        color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# colored GCC warnings and errors
#export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
# Add an "alert" alias for long running commands.  Use like so:
#   sleep 10; alert
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/ysh980101/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/ysh980101/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/home/ysh980101/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/ysh980101/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

#4 cobi4

```bash
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
        # We have color support; assume it's compliant with Ecma-48
        # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
        # a case would tend to support setf rather than setaf.)
        color_prompt=yes
    else
        color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# colored GCC warnings and errors
#export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# Add an "alert" alias for long running commands.  Use like so:
#   sleep 10; alert
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi

# >>> user specific aliases and functions

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/ysh980101/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/ysh980101/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/home/ysh980101/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/ysh980101/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```
