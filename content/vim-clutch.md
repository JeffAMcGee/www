Title: Using a Vim Clutch
Category: Tips
Date: 2013-12-18

I've wanted to try a [Vim Clutch](https://github.com/alevchuk/vim-clutch) for a
few years now, but once I got it set up, it turned out to be disapointing.

A Vim Clutch is a foot pedal that toggles between insert mode
and normal mode. I set my clutch up a bit differently than the original vim
clutch because I wanted the position of my foot to encode the state of vim: up
in normal mode and down in insert mode. I mapped the pedal to the middle mouse
button and added this to my `.vimrc`:

    :::vim
    nnoremap <MiddleMouse> i
    inoremap <MiddleRelease> <Esc>

Once I had that set up, I realized that I rarely use `i` to get into insert
mode. Instead, I use commands like `a` (append), `o` (open a new line), `cw`
(change word), `ci"` (change inside quotation) to enter insert mode. As a
result, I rarely found myself using the foot pedal.

I now have a gently used foot pedal that is looking for a purpose in life. A
co-worker suggested mapping the foot pedal to Command-Tab to switch between
applications. Maybe I will try that.

