# Metanet Setup

## SSH

### SSH in Plesk ativieren

Unter «Webhosting-Zugang» Zugriff auf den Server via SSH  auf `/bin/bash (chrooted)` setzen.

[Metanet Anleitung](https://www.metanet.ch/support/713)

### Erste Verbindung per SSH

    lokal $ ssh <benutzername>@<server> -p2121

### Public Key auf Server ablegen

    remote $ mkdir ~/.ssh && chmod 700 ~/.ssh
    remote $ vim ~/.ssh/authorized_keys

    lokal $ cat ~/.ssh/id_rsa.pub | pbcopy

    # in Vim: paste, save & quit

    remote $ chmod 600 authorized_keys
    remote $ exit

### Lokal SSH konfigurieren 

    lokal $ vim ~/.ssh/config

Neue Verbindung konfigurieren

    Host metanet
     HostName fubar.metanet.ch
     User username
     Port 2121
     # AddKeysToAgent yes
     # UseKeychain yes
     # IdentityFile ~/.ssh/id_rsa

Nur die Zeilen `HostName`, `Port` und `User` scheinen wirklich notwendig zu sein.

### Zweite Verbindung per SSH

    lokal $ ssh metanet

---

## Bash Profile

    remote $ vim ~/.bash_profile    

In Vim `:set paste`.

    # set vim as editor of choice (e.g. for git)
    export EDITOR=vim

    # set commandline to vim-mode
    set -o vi

    # alias for history.
    alias hi='history'

    # some more ls aliases
    alias ll='ls -alF'
    alias la='ls -lah'
    alias l='ls -CF'

    # php version
    alias php='/opt/php72/bin/php'
    export PATH=/opt/php72/bin:$PATH

    # don't put duplicate lines or lines starting with space in the history.
    # See bash(1) for more options
    HISTCONTROL=ignoreboth

    # append to the history file, don't overwrite it
    shopt -s histappend

    # for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
    HISTSIZE=1000
    HISTFILESIZE=2000

    # Ignore redundant stuff in history
    export HISTIGNORE="clear:history:hi"

    # colored output
    export TERM='xterm-256color'
    export GREP_OPTIONS='--color=auto' GREP_COLOR='1;32'
    export PS1=" \[\033[34m\]\u@\h \[\033[33m\]\w\[\033[31m\]\[\033[00m\] $ "

In Vim `:set nopaste`.

---

## Vim

### Vimrc

    remote $ vim ~/.vimrc

In Vim `:set paste`.

    " vim:fdm=marker
    set encoding=utf-8
    set fileencoding=utf-8

    " no underlines for highlighting line numbers
    hi LineNr term=NONE

    call pathogen#infect()

    " Handling of Filetypes, Indentation etc. {{{

    if has("autocmd")
      " Enable file type detection
      filetype on
      syntax on

      " prevent autocommands being rewritten at every sourcing of vimrc
      augroup myAutocommands
        autocmd!

        " Source .vimrc after saving
        autocmd bufwritepost .vimrc source $MYVIMRC

        " Automatically save & restore folds when closing/opening a file.
        autocmd BufWinLeave *.* mkview
        autocmd BufWinEnter *.* silent loadview

        " Detect .md as Markdown (instead of Modula-2)
        autocmd BufNewFile,BufReadPost *.md set filetype=markdown

        " Cleaning Tabs/Spaces:
        " http://vimcasts.org/episodes/whitespace-preferences-and-filetypes/

        " Syntax of these languages is fussy over tabs Vs spaces
        autocmd FileType make setlocal ts=8 sts=8 sw=8 noexpandtab
        autocmd FileType yaml setlocal ts=4 sts=4 sw=4 expandtab

        " Customisations based on house-style (arbitrary).
        autocmd FileType html setlocal ts=3 sts=2 sw=2 expandtab
        autocmd FileType twig setlocal ts=2 sts=2 sw=2 expandtab
        autocmd FileType css setlocal ts=2 sts=2 sw=2 expandtab
        autocmd FileType scss setlocal ts=2 sts=2 sw=2 expandtab
        autocmd FileType javascript setlocal ts=2 sts=2 sw=2 expandtab
        autocmd FileType json setlocal ts=2 sts=2 sw=2 expandtab
        autocmd FileType vim setlocal ts=2 sts=2 sw=2 expandtab
        autocmd FileType php setlocal ts=4 sts=2 sw=2 expandtab
        autocmd FileType markdown setlocal ts=4 sts=4 sw=4 expandtab

        " highlight syntax when changing filetype
        autocmd FileType * :syntax enable

      augroup END

    endif

    " somewhat redundant (see Line 14), should clean this up
    " on the other hand there seem to be plugins eg. pyflakes, which need this.
    filetype plugin indent on

    " }}}

    " Custom Key Mappings {{{

    " Map Leader to Comma
    let mapleader = ","

    " Map Tilde to Paragraph
    nmap § ~

    " Swiss Keyboard Layout
    " Map [ and ] to ö and ä
    nmap ö [
    nmap ä ]
    omap ö [
    omap ä ]
    xmap ö [
    xmap ä ]

    " Swiss Keyboard Layout
    " Map { and } to é and à
    nmap é {
    nmap à }
    omap é {
    omap à }
    xmap é {
    xmap à }

    " Swiss Keyboard Layout
    " <C-]> just doesn't work on this godforsaken Keyboard
    " nmap <Leader>ä <C-]>
    nmap <Leader>ä <C-]>

    " map jk to <ESC>
    imap jk <ESC>
    vmap jk <ESC>

    " show/hide invisibles
    " http://vimcasts.org/episodes/show-invisibles/
    set listchars=tab:▸\ ,eol:¬
    nmap <leader>l :set list!<CR>

    " remap to center view on movements
    " Comment this out, in order to learn Vim's default behaviour
    " nmap G Gzz
    " nmap n nzz
    " nmap N Nzz
    " nmap } }zz
    " nmap { {zz

    " autocomplete brackets etc.
    imap <leader>' ''<ESC>i
    imap <leader>" ""<ESC>i
    imap <leader>( ()<ESC>i
    imap <leader>[ []<ESC>i
    imap <leader>{ {}<ESC>i
    imap <leader>< <><ESC>i
    imap <leader>` ``<ESC>i

    " get out of automatched brackets etc.
    :inoremap <C-j> <Esc>/[)}"'\]>]<CR>:nohl<CR>a

    " jinja, twig, nunjucks
    imap <leader>t {%  %}<ESC>hhi
    imap <leader>f {{  }}<ESC>hhi
    imap <leader>c {#  #}<ESC>hhi

    " add empty Line without going into insert mode
    nmap <leader>o o<ESC>k
    nmap <leader>O O<ESC>j

    " }}}

    " Move Emmet Trigger to ctrl-f {{{
    let g:user_emmet_leader_key='<C-F>'
    " Note to myself: hit ctrl-f then comma. Quickly.
    " }}}

In Vim `:set nopaste`.

### Vim Plugins

    remote $ mkdir -p ~/.vim/autoload ~/.vim/bundle && \
    curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim 

    remote $ cd ~/.vim/bundle && \
    git clone https://github.com/tpope/vim-sensible.git && \ 
    git clone https://tpope.io/vim/surround.git && \ 
    git submodule add https://github.com/flazz/vim-colorschemes.git colorschemes && \ 
    git clone https://github.com/mattn/emmet-vim.git

---

## Mysql

### Verbindung zu Datenbank in Shell

	mysql -h 127.0.0.1 -u username -p1234567890 database

