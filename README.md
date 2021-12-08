# nsProcess

`nsProcess` is a NSIS plugin that allows to handle processes (checking if a process is running, killing it, etc.). It is very useful to check if your the application you're trying to install is not currently running at the same time of the installer.

This repository is just a convenient way to get up-to-date and pre-compiled files for the `nsProcess`.

## Credits

Its original source files were downloaded from <https://nsis.sourceforge.io/NsProcess_plugin>.

- Its original author is [Shengalts Aleksander](Shengalts@mail.ru).
- Source function `FIND_PROC_BY_NAME` based upon [Ravi Kochhar](kochhar@physiology.wisc.edu) code.
- `FindProcDLL` plugin by iceman_k.
- `KillProcDLL` plugin by DITMan.
- NSIS UNICODE-compatible version (1.6) by brainsucker.

## Features

- Find a process by name.
- Kill all processes with specified name (not only one).
- Close all processes with specified name (first tries to close all process windows, waits for 3 seconds for process to exit, terminates if still alive, use `_CloseProcess` function).
- The process name is case-insensitive.
- Win95/98/ME/NT/2000/XP/Win7/Win8/Win10/Win11 support.
- Finds processes of other user(s) when running 'as Administrator' or when having switched to another user.
- Small plugin size (4 Kb).
- NSIS UNICODE support.

## Installation

- `nsProcess.nsh` goes to `C:\Program Files (x86)\NSIS\Include`
- `nsProcess.dll` goes to:
  - ANSI: `C:\Program Files (x86)\NSIS\Plugins\x86-ansi`
  - Unicode: `C:\Program Files (x86)\NSIS\Plugins\x86-unicode`

## Usage

Include the file in your `.nsi` script with:

```nsis
!include "nsProcess.nsh"
```

### Find a Process

```nsis
${nsProcess::FindProcess} "[file.exe]" $var
```

Result of the call goes into `$var`.

| Code  | Meaning |
|------:|:--------|
|   `0` | Success. |
| `603` | Process was not currently running. |
| `604` | Unable to identify system type. |
| `605` | Unsupported OS. |
| `606` | Unable to load `NTDLL.DLL`. |
| `607` | Unable to get procedure address from `NTDLL.DLL`. |
| `608` | `NtQuerySystemInformation` failed. |
| `609` | Unable to load `KERNEL32.DLL`. |
| `610` | Unable to get procedure address from `KERNEL32.DLL`. |
| `611` | `CreateToolhelp32Snapshot` failed. |

Example:

```nsis
${nsProcess::FindProcess} "notepad.exe" $R0
```

### Kill/Close a Process

```nsis
${nsProcess::KillProcess} "[file.exe]" $var
${nsProcess::CloseProcess} "[file.exe]" $var
```

| Code  | Meaning |
|------:|:--------|
|   `0` |   Success. |
| `601` | No permission to terminate process. |
| `602` | Not all processes terminated successfully. |
| `603` | Process was not currently running. |
| `604` | Unable to identify system type. |
| `605` | Unsupported OS. |
| `606` | Unable to load `NTDLL.DLL`. |
| `607` | Unable to get procedure address from `NTDLL.DLL`. |
| `608` | `NtQuerySystemInformation` failed. |
| `609` | Unable to load `KERNEL32.DLL`. |
| `610` | Unable to get procedure address from `KERNEL32.DLL`. |
| `611` | `CreateToolhelp32Snapshot` failed. |

Example:

```nsis
nsProcess:_CloseProcess "notepad.exe"
Pop $R0
```

### Unload Plugin

```nsis
${nsProcess::Unload}
```

## Example

```nsis
!include "LogicLib.nsh"

Var /GLOBAL noMoreAppsAreRunning

!macro CheckIfAppIsRunning
  StrCpy $noMoreAppsAreRunning False

  ${Do}
    ${nsProcess::FindProcess} "${APP_EXECUTABLE}" $R0
    ${If} $R0 == 0 # The process is currently running.
      MessageBox MB_OKCANCEL|MB_ICONEXCLAMATION "$(CheckIfAppIsRunning_Message)" IDOK msgbox_ok IDCANCEL msgbox_quit

      msgbox_ok:
      ${nsProcess::CloseProcess} "${APP_EXECUTABLE}" $R0
      ${If} $R0 == 0
        Goto msgbox_end
      ${Else}
        Goto msgbox_quit
      ${Endif}

      msgbox_quit:
      Quit

      msgbox_end:
    ${ElseIf} $R0 == 603 # No more processes are running.
      StrCpy $noMoreAppsAreRunning True
    ${Else} # Error
      MessageBox MB_OK|MB_ICONSTOP "$(CheckIfAppIsRunning_Error_Find)"
      Quit
    ${EndIf}
  ${LoopUntil} $noMoreAppsAreRunning == True

  ${nsProcess::Unload}
!macroend

!insertmacro CheckIfAppIsRunning
```
