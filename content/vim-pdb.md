Title: Create a Python Breakpoint in Vim
Category: Tips
Date: 2011-08-18

[Pdb](http://docs.python.org/2/library/pdb.html) is Python's built-in debugger.
To make it easier to start pdb, I added this line to my .vimrc:

    :::vim
    noremap <D-d> Oimport pdb; pdb.set_trace()<Esc>:w<Return>

When I type Command-d in normal mode, vim will insert the line
`import pdb; pdb.set_trace()` and save the current file. When I run the python
program, and that line is excuted, the Python program will pause and start pdb.

