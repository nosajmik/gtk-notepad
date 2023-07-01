# gtk-notepad
Basic text editor for Linux written in C/GTK.

### Installation
Use `make all` to build it (no installation yet). You need gtk-2.0 to compile this program.

### Info
This is a basic text editing program, nothing special. You can change font, etc. First project written using GTK.

### More info: nosajmik

If you're on Ubuntu 22.04 or later, i.e., a distro with Canonical's stupid snapcraft stuff, you may need to `unset GTK_PATH` before running, else you'll see an obscure error coming from snap.

To build from source on recent Ubuntu distros, you'll also need to `sudo apt install libgtk2.0-dev`.

Initially, I took the approach of hooking libgtk calls and then using it to track which GTK calls gedit was making. However, even gedit is too complex for tracking how text gets rendered.

Unfortunately, it seems like the text updates in the editor window are managed entirely by libGTK as part of its [TextBuffer](https://docs.gtk.org/gtk3/class.TextBuffer.html) object, and interfacing with the GPU driver seems to happen entirely within libGTK.

It seems to be the case that GTK apps can only connect callback functions to when the TextBuffer has been changed or a text mark has been set using `g_signal_connect`, so that the apps can update their local state accordingly. The callback to `changed` is `gtk_notepad_text_changed` for this app, and that for `mark_set` is `gtk_statusbar_update_lncol`. The former callback changes the `modified` variable to true and just calls the latter callback. `gtk_statusbar_update_lncol` just changes the status bar at the bottom of the application to display the new row and column number in the text box.