# nsProcess

`nsProcess` is a NSIS plugin that allows to handle processes (checking if a process is running, killing it, etc.). Its original source files were downloaded from <https://nsis.sourceforge.io/NsProcess_plugin>.

Its features, taken from NSIS website:

- Find a process by name.
- Kill all processes with specified name (not only one).
- Close all processes with specified name (first tries to close all process windows, waits for 3 seconds for process to exit, terminates if still alive, use `_CloseProcess` function).
- The process name is case-insensitive.
- Win95/98/ME/NT/2000/XP/Win7/Win8/Win10/Win11 support.
- Finds processes of other user(s) when running 'as Administrator' or when having switched to another user.
- Small plugin size (4 Kb).
- NSIS UNICODE support.

This repository is just a convenient way to get up-to-date and pre-compiled files for the `nsProcess`.
