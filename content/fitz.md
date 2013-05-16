Title: Fitz Window Decoration for KDE 3
Category: Projects
Date: 2007-06-13

[Fitz](https://github.com/jeffamcgee/fitz) is a window decoration for KDE that
allows you to work (or play) faster.

Unlike other decorations, Fitz does not have a titlebar above the window;
instead, it puts the buttons on the top right corner of the window in the
menubar. As a result, when a window is at the top edge of the screen, the
menubar is only a few pixels away from the top. Fitz lets you get to a menu
item by ramming your mouse into the top of the screen. If you click on the
window frame, the mouse will jump down into the middle of the menubar and Fitz
will generate a click. This effectively puts the menubar at the top of the
screen like Mac OS does.

Here's an example from kaboodle:

![example graphic](http://fitz.sourceforge.net/click.png)

If kaboodle is at the top of the screen, and the user clicks as shown in the
first picture, the menu will open as shown in the second picture.

If there are toolbars or scrollbars on any side of the window, fitz will let
you click on the frame beside buttons and Fitz will move the mouse inward and
generate a click. As a result, Fitz works best with maximized windows. On my
desktop, I have a child panel on the left and I put toolbars on the right side.
I generally only use maximized windows.

(Note: This project is now dead. I never finished porting it to KDE 4, and I
bought a Mac laptop, so there's not much point in me developing this further.)
