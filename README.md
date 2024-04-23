# UAC Bypass Techniques (Windows)

This project explores four different User Account Control (UAC) bypass techniques on Windows systems. The goal is to assess which method is most effective and why, considering factors such as reliability, stealthiness, compatibility across different Windows versions, and ease of execution.

## Disclaimer

The tools and scripts provided in this repository are made available for educational purposes only and are intended to be used for testing and protecting systems with the consent of the owners. The author does not take any responsibility for the misuse of these tools. It is the end user's responsibility to obey all applicable local, state, national, and international laws. The developers assume no liability and are not responsible for any misuse or damage caused by this program. Under no circumstances should this tool be used for malicious purposes. The author of this tool advocates for the responsible and ethical use of security tools. Please use this tool responsibly and ethically, ensuring that you have proper authorization before engaging any system with the techniques demonstrated by this project.

## Prerequisites

- **Operating System**: Tested on Windows 10 x64, version 22H2, Kali Linux 2023.4
- **Payload Studio**: Tested with Payload Studio Pro version 1.3.1
- **DuckyScript Version**: DuckScript 3.0 Advanced
- **Hardware**: Hak5 USB Rubber Ducky (2022)

## Technique Analysis

### **TECHNIQUE 1: `Using Keyboard Shortcuts and Context Menu`**

```
CTRL ESC
DELAY 100
STRING cmd
DELAY 2000
SHIFT F10
DELAY 2000
DOWNARROW
ENTER
DELAY 3000
LEFT
ENTER
```

**Steps**:

1. **Open Run Dialog**: `CTRL ESC` initiates the Start menu or search functionality.
2. **Command Prompt**: Types `cmd` to open the command prompt.
3. **Context Menu**: `SHIFT F10` opens the context menu for the selected item, here aiming for the search result.
4. **Navigation**: Uses `DOWNARROW` and `ENTER` to select and execute an option, likely aiming for "Run as administrator."
5. **Confirmation**: The `LEFT` and `ENTER` keys navigate and confirm the UAC prompt.

**Pros**:

- Does not rely on specific Windows features that might be disabled or restricted.
- Purely keyboard-based, which can bypass some forms of monitoring.

**Cons**:

- Highly visible to anyone watching the screen.
- Depends on the specific layout of the context menu, which can vary.
- Slower due to manual delay commands to wait for UI elements.

### **TECHNIQUE 2: `Using PowerShell to Elevate Command Prompt`**

```
GUI r
DELAY 500
STRING powershell Start-Process cmd -Verb runAs
ENTER
DELAY 3000
ALT y
```

**Steps**:

1. **Open Run Dialog**: `GUI r` opens the Run dialog.
2. **Run as Administrator**: Executes `powershell Start-Process cmd -Verb runAs` to start a command prompt as an administrator.
3. **Bypass UAC**: `ALT y` confirms the UAC prompt automatically.

**Pros**:

- Straightforward and quick if PowerShell execution policies allow it.
- Uses PowerShell's ability to start processes with elevated privileges.

**Cons**:

- PowerShell script execution policies may block this method.
- Visibility of PowerShell window may draw attention.

### **TECHNIQUE 3: `Creating and Running a VBScript to Elevate cmd`**

```
GUI r
DELAY 500
STRINGLN cmd
DELAY 500
STRINGLN copy con sudo.cmd
STRINGLN @echo Set objShell = CreateObject("Shell.Application") > %temp%\sudo.tmp.vbs
STRINGLN @echo args = Right("%*", (Len("%*") - Len("%1")))  >> %temp%\sudo.tmp.vbs
STRINGLN @echo objShell.ShellExecute "%1", args, "","runas" >> %temp%\sudo.tmp.vbs
STRINGLN @cscript %temp%\sudo.tmp.vbs
CTRL z
ENTER
STRINGLN sudo.cmd cmd
DELAY 3000
ALT y
```

**Steps**:

1. **Open Run Dialog**: `GUI r` opens the Run dialog.
2. **Open CMD**: Types `cmd` and uses it to start creating a new CMD file.
3. **VBS Script Creation**: Writes lines to create a `.vbs` script named "sudo" that elevates privileges and runs the specified command (`cmd`) with administrative rights.
4. **Execution**: Executes the `.vbs` script to bypass UAC with `CTRL z` to save and close, then runs the script with elevated privileges.
5. **Bypass UAC**: `ALT y` confirms UAC prompt automatically.

**Pros**:

- More stealthy compared to opening PowerShell directly.
- Allows for flexibility in executing various commands.

**Cons**:

- Complex setup involving multiple steps and script writing.
- May be blocked by antivirus software due to the creation of VBScript files.

### **TECHNIQUE 4: `Utilizing Accessibility Features`**

```
GUI x
DELAY 500
STRING a
DELAY 500
ALT y
```

**Steps**:

1. **Power User Menu**: `GUI x` opens the power user menu that includes administrative tools.
2. **Command Prompt Admin**: `STRING a` attempts to select "Command Prompt (Admin)" or "Windows PowerShell (Admin)" or a similarly indexed administrative tool.
3. **Bypass UAC**: `ALT y` is pressed to confirm UAC prompt and proceed.

**Pros**:

- Quick and uses built-in Windows shortcuts.
- Less likely to be blocked by script execution policies.

**Cons**:

- High visibility due to the use of GUI elements.
- Specific to versions of Windows that include the Mobility Center with these options.

### Comparative Evaluation

The best technique depends on the target environment and the specific constraints of the engagement e.g. security settings. For environments where stealth is not a primary concern, **Technique 2** offers a straightforward and reliable method, assuming PowerShell execution policies are permissive. **Technique 3** offers a more stealthy approach at the cost of complexity and potential detection by antivirus solutions. **Technique 1** and **Technique 4** are more dependent on user interface elements and are highly visible but could be effective in scenarios where digital monitoring is less of a concern than physical observation. When choosing a technique, consider the balance between stealth, reliability, compatibility, and the likelihood of detection by security solutions in place on the target system. 

- **Technique 1** relies on navigating context menus, which might be less reliable due to potential changes in UI or menu items over different Windows versions.
- **Technique 2** uses PowerShell to elevate directly to an administrator command prompt, making it straightforward but potentially more detectable due to PowerShell monitoring.
- **Technique 3** is the most complex, using a VBScript for elevation, which could evade some detection mechanisms but requires more steps and could be prone to errors in script creation.
- **Technique 4** is quick and uses built-in Windows shortcuts to access admin tools, but its effectiveness depends on the correct indexing of the "Command Prompt (Admin)" or equivalent in the power user menu, which might vary.
