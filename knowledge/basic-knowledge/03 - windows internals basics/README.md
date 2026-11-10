
# 03 - Windows Internals Basics

## 1. Overview

This lesson introduces the fundamental concepts of Windows Internals and explains how the Windows operating system manages processes, services, system configuration, file systems, and event logs.

Understanding Windows Internals is essential for cybersecurity professionals, especially those working in Security Operations Centers (SOC), Digital Forensics, Incident Response, and Malware Analysis.

### Learning Objectives

By the end of this lesson, you should be able to:

- Understand Windows processes, threads, and process identifiers.
- Explain the relationship between parent and child processes.
- Understand Windows Services and their startup configurations.
- Use Windows Event Viewer to examine system and security logs.
- Identify important Windows Event IDs.
- Understand the structure and purpose of the Windows Registry.
- Understand the NTFS file system and important forensic artifacts.
- Use basic Windows commands and PowerShell to investigate system activity.

---

## 2. Windows Operating System Architecture

Windows is an operating system responsible for managing hardware resources, executing applications, controlling access to system resources, and providing services to users.

### 2.1. User Mode and Kernel Mode

Windows separates execution into two primary processor access modes.

| Mode | Description | Examples |
|---|---|---|
| User Mode | Runs applications with restricted access to system resources | Browsers, Notepad, PowerShell |
| Kernel Mode | Runs operating system components with privileged access | Windows Kernel, device drivers |

**User Mode**

Applications normally execute in User Mode.

A User Mode application cannot directly access arbitrary kernel memory or hardware resources.

Instead, it requests operating system services through supported interfaces.

**Kernel Mode**

Kernel Mode provides privileged access to system resources.

Important responsibilities include:

- Memory management.
- Process and thread scheduling.
- Hardware interaction through drivers.
- System resource management.
- Enforcement of operating system security mechanisms.

### 2.2. Simplified Windows Architecture

```text
+----------------------------------+
|          User Applications       |
|  Browser | Notepad | PowerShell  |
+----------------------------------+
|        Windows API / DLLs        |
+----------------------------------+
|         System Call Layer        |
+----------------------------------+
|          Windows Kernel          |
| Process | Memory | I/O | Drivers |
+----------------------------------+
|             Hardware             |
|       CPU | RAM | Storage        |
+----------------------------------+
```

This diagram is a simplified conceptual representation rather than a complete Windows architecture diagram.

### 2.3. Why Windows Architecture Matters

Understanding the separation between User Mode and Kernel Mode helps security analysts investigate:

- Suspicious process activity.
- Unauthorized privilege escalation.
- Malicious device drivers.
- Unexpected system behavior.
- Malware attempting to access protected resources.

---

## 3. Windows Processes

### 3.1. What Is a Process?

A **Process** is an instance of a program being executed by the operating system.

When a user opens an application, Windows creates or uses one or more processes to execute its instructions.

Examples:

| Application | Common Process Name |
|---|---|
| File Explorer | `explorer.exe` |
| Notepad | `notepad.exe` |
| Command Prompt | `cmd.exe` |
| PowerShell | `powershell.exe` |
| Task Manager | `Taskmgr.exe` |

A process contains or references several important resources.

| Component | Description |
|---|---|
| PID | Unique process identifier while the process exists |
| Parent PID | Identifier associated with the process that created it |
| Virtual Address Space | Memory address space used by the process |
| Threads | Units of execution within the process |
| Handles | References to operating system objects |
| Access Token | Security context associated with the process |

### 3.2. Process ID (PID)

A **Process ID (PID)** is a numerical identifier assigned to a process.

Example:

```text
Process Name       PID
----------------------
explorer.exe       4120
notepad.exe        5864
cmd.exe            7208
```

Windows uses PIDs to identify and manage processes.

Important characteristics:

- Each existing process has a PID.
- PIDs are not permanent identifiers.
- Windows may reuse a PID after the previous process terminates.
- A PID should be considered together with other information, such as process creation time.

### 3.3. Parent Process and Child Process

A process can create another process.

The creating process is commonly called the **Parent Process**, while the newly created process is called the **Child Process**.

Example:

```text
explorer.exe
    |
    +--- notepad.exe
    |
    +--- cmd.exe
           |
           +--- whoami.exe
```

In this example:

- `explorer.exe` creates `notepad.exe` and `cmd.exe`.
- `cmd.exe` creates `whoami.exe`.
- `cmd.exe` is the parent of `whoami.exe`.

The parent-child relationship is useful when reconstructing system activity.

**Cybersecurity Example**

Consider the following process tree:

```text
winword.exe
    |
    +--- powershell.exe
              |
              +--- suspicious.exe
```

This behavior may require further investigation because a document application is associated with PowerShell execution and an unfamiliar executable.

However, unusual parent-child relationships do not automatically indicate malware.

An analyst should examine:

- Process command lines.
- Executable file paths.
- Digital signatures.
- Process creation times.
- User activity.
- Related security events.

**Important:** On Windows, parent process information can be influenced by process creation mechanisms. A reported parent-child relationship should not be treated as definitive proof of which program initiated an action.

### 3.4. Threads

A **Thread** is a unit of execution within a process.

A single process can contain multiple threads.

For example, a browser may use different threads to:

- Handle user interaction.
- Perform background operations.
- Process network activity.
- Execute application tasks.

### 3.5. Process vs Thread

| Feature | Process | Thread |
|---|---|---|
| Definition | Running program instance | Execution unit within a process |
| Memory | Has its own virtual address space | Shares its process's address space |
| Resources | Owns or references system resources | Uses resources of its process |
| Execution | Contains one or more threads | Executes program instructions |
| Identification | PID | TID (Thread ID) |

### 3.6. Viewing Running Processes

**Using Task Manager**

1. Press `Ctrl + Shift + Esc`.
2. Open the Processes tab.
3. Observe running applications and background processes.
4. Open the Details tab.
5. Examine process names and PIDs.

**Using Command Prompt**

List running processes:

```cmd
tasklist
```

Search for a specific process:

```cmd
tasklist | findstr notepad
```

**Using PowerShell**

List processes:

```powershell
Get-Process
```

Find a specific process:

```powershell
Get-Process -Name notepad
```

Display process names, PIDs, and parent PIDs:

```powershell
Get-CimInstance Win32_Process |
Select-Object Name, ProcessId, ParentProcessId
```

Display executable paths and command lines when available:

```powershell
Get-CimInstance Win32_Process |
Select-Object Name, ProcessId, ExecutablePath, CommandLine
```

Some process details may require elevated permissions.

---

## 4. Windows Services

### 4.1. What Is a Windows Service?

A **Windows Service** is a program or software component designed to provide functionality in the background.

Services are managed by the **Service Control Manager (SCM)**.

Many services can start without requiring a user to open an application.

Examples:

| Service | Description |
|---|---|
| Windows Update (`wuauserv`) | Supports Windows updates |
| DHCP Client (`Dhcp`) | Manages DHCP network configuration |
| DNS Client (`Dnscache`) | Provides DNS client functionality |
| Print Spooler (`Spooler`) | Manages printing operations |
| Windows Event Log (`EventLog`) | Manages Windows event logging |

### 4.2. Process vs Service

| Process | Service |
|---|---|
| An executing program instance | A managed background service |
| Has a PID | Runs within a process when active |
| May have a graphical interface | Usually operates without a user interface |
| Can be started by applications or users | Typically managed through the SCM |

A single process may host multiple services.

For example, `svchost.exe` is used to host many Windows services.

### 4.3. Service Startup Types

Windows Services support several startup configurations.

| Startup Type | Description |
|---|---|
| Automatic | Starts automatically during system startup |
| Automatic (Delayed Start) | Starts automatically after a delay |
| Manual | Starts when requested by a user, application, or system component |
| Disabled | Cannot start until its configuration is changed |

### 4.4. Service States

Common service states include:

- Running.
- Stopped.
- Start Pending.
- Stop Pending.
- Paused.

### 4.5. Managing Windows Services

**Using the Services Console**

1. Press `Win + R`.
2. Enter:

```text
services.msc
```

3. Select a service.
4. Examine its status and startup type.
5. Open Properties to inspect its configuration.

**Using Command Prompt**

Check a service:

```cmd
sc query Spooler
```

View service configuration:

```cmd
sc qc Spooler
```

**Using PowerShell**

List services:

```powershell
Get-Service
```

Find the Windows Update service:

```powershell
Get-Service -Name wuauserv
```

List running services:

```powershell
Get-Service |
Where-Object {$_.Status -eq "Running"}
```

### 4.6. Services in Cybersecurity

Windows Services can be abused as a persistence mechanism.

A malicious program may attempt to install a service that starts automatically.

Indicators requiring investigation include:

- Newly installed services.
- Unexpected executable paths.
- Unusual service names.
- Services running with excessive privileges.
- Services associated with unknown executables.

A suspicious service should be investigated using additional evidence rather than its name alone.

---

## 5. Windows Event Viewer

### 5.1. What Is Event Viewer?

**Windows Event Viewer** is a built-in tool used to examine event logs generated by Windows components and applications.

Event logs record information about system activity, errors, security events, and other operations.

Event Viewer is important for:

- System troubleshooting.
- Security monitoring.
- Incident response.
- Digital forensics.
- Investigating suspicious activity.

### 5.2. Opening Event Viewer

Press `Win + R` and enter:

```text
eventvwr.msc
```

Alternatively, search for Event Viewer in the Start menu.

### 5.3. Main Windows Event Logs

| Log | Description |
|---|---|
| Application | Events generated by applications |
| Security | Security auditing events |
| System | Events generated by Windows components and services |
| Setup | Installation and configuration events |
| Forwarded Events | Events collected from other computers |

### 5.4. Application Log

The Application Log contains events generated by software applications.

Examples:

- Application crashes.
- Application errors.
- Software warnings.
- Application-related diagnostic information.

### 5.5. System Log

The System Log contains events generated by operating system components.

Examples:

- Driver failures.
- Service installation.
- Service startup problems.
- Hardware-related errors.

### 5.6. Security Log

The Security Log records events related to security auditing.

Examples:

- Successful logons.
- Failed logons.
- Process creation events.
- Account management activity.
- Audit log clearing.

**Important:** Security events depend on the configured audit policies. Not every possible event is recorded automatically.

### 5.7. Event Levels

| Event Level | Description |
|---|---|
| Critical | Severe system problem |
| Error | An operation failed |
| Warning | Potential problem |
| Information | Normal or informational event |
| Verbose | Detailed diagnostic information |

An Error or Warning does not necessarily indicate a security incident.

### 5.8. Important Event Properties

When investigating an event, examine:

| Property | Description |
|---|---|
| Event ID | Identifier of the event |
| Time Created | Time the event was recorded |
| Log Name | Log containing the event |
| Source / Provider | Component generating the event |
| Level | Event severity or classification |
| Computer | Computer associated with the event |
| Event Data | Additional details related to the event |

An Event ID should be interpreted together with its provider and log.

---

## 6. Important Windows Event IDs

### 6.1. Common Security Event IDs

| Event ID | Description | Log |
|---|---|---|
| 4624 | Successful logon | Security |
| 4625 | Failed logon | Security |
| 4634 | Account logged off | Security |
| 4648 | Logon attempted using explicit credentials | Security |
| 4672 | Special privileges assigned to a new logon | Security |
| 4688 | New process created | Security |
| 4720 | User account created | Security |
| 4726 | User account deleted | Security |
| 1102 | Security audit log cleared | Security |

### 6.2. Common System Event ID

| Event ID | Description | Log |
|---|---|---|
| 7045 | A new Windows service was installed | System |

### 6.3. Event ID 4624 – Successful Logon

Event ID `4624` indicates that a logon session was successfully created.

Important fields may include:

- Account Name.
- Account Domain.
- Logon Type.
- Source Network Address.
- Workstation Name.
- Logon ID.

**Common Logon Types**

| Logon Type | Description |
|---|---|
| 2 | Interactive logon |
| 3 | Network logon |
| 4 | Batch logon |
| 5 | Service logon |
| 7 | Unlock |
| 10 | RemoteInteractive logon, commonly associated with RDP |
| 11 | CachedInteractive logon |

### 6.4. Event ID 4625 – Failed Logon

Event ID `4625` indicates a failed logon attempt.

Security analysts can investigate repeated failed logons to identify possible password-guessing activity.

Example:

```text
10:00:01 - Event ID 4625
10:00:03 - Event ID 4625
10:00:05 - Event ID 4625
10:00:07 - Event ID 4625
```

Several failed logons within a short period may indicate suspicious behavior.

However, they can also result from incorrect saved credentials or configuration problems.

### 6.5. Event ID 4688 – Process Creation

Event ID `4688` records the creation of a new process when the corresponding audit policy is enabled.

Useful information may include:

- New Process Name.
- New Process ID.
- Creator Process Name.
- Creator Process ID.
- Subject Account.
- Process Command Line, if command-line auditing is enabled.

This event can help reconstruct process activity during an investigation.

### 6.6. Event ID 7045 – Service Installation

Event ID `7045` is commonly generated when a new Windows service is installed.

It may contain:

- Service Name.
- Service File Name.
- Service Type.
- Service Start Type.
- Service Account.

This event is useful when investigating suspicious persistence mechanisms.

### 6.7. Event ID 1102 – Audit Log Cleared

Event ID `1102` indicates that the Security audit log was cleared.

This event may be important because attackers sometimes attempt to remove evidence of their activities.

However, administrators may also clear logs for legitimate maintenance purposes.

### 6.8. Filtering Events

**Using Event Viewer**

1. Open Event Viewer.
2. Navigate to Windows Logs.
3. Select Security.
4. Click Filter Current Log.
5. Enter the required Event IDs.

Example:

```text
4624,4625,4688,1102
```

**Using PowerShell**

Find successful logons:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4624
} -MaxEvents 10
```

Find failed logons:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
} -MaxEvents 10
```

Find newly installed services:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'System'
    Id = 7045
} -MaxEvents 10
```

Some queries may require administrator privileges.

---

## 7. Windows Registry

### 7.1. What Is Windows Registry?

The **Windows Registry** is a hierarchical database used to store configuration settings for Windows, hardware, user profiles, and applications.

The Registry contains information such as:

- Operating system configuration.
- Installed software settings.
- User preferences.
- Service configuration.
- Startup configuration.

### 7.2. Opening Registry Editor

Press `Win + R` and enter:

```text
regedit
```

**Warning:** Incorrect Registry modifications may cause system instability. Back up relevant settings before making changes.

### 7.3. Main Registry Hives

| Registry Hive | Description |
|---|---|
| HKEY_LOCAL_MACHINE (HKLM) | System-wide configuration |
| HKEY_CURRENT_USER (HKCU) | Current user's configuration |
| HKEY_CLASSES_ROOT (HKCR) | File associations and COM registration information |
| HKEY_USERS (HKU) | Loaded user profile configurations |
| HKEY_CURRENT_CONFIG (HKCC) | Current hardware profile configuration |

### 7.4. Registry Structure

Example:

```text
HKEY_LOCAL_MACHINE
    |
    +--- SOFTWARE
           |
           +--- Microsoft
                  |
                  +--- Windows
```

Registry components include:

- **Key:** A container that can contain subkeys and values.
- **Subkey:** A key located under another key.
- **Value:** A named entry containing data.
- **Value Data:** The information stored in a value.

### 7.5. Common Registry Value Types

| Type | Description |
|---|---|
| REG_SZ | Text string |
| REG_DWORD | 32-bit integer |
| REG_QWORD | 64-bit integer |
| REG_BINARY | Binary data |
| REG_MULTI_SZ | Multiple strings |
| REG_EXPAND_SZ | Expandable string |

### 7.6. Registry Startup Locations

Some Registry keys are used to configure programs that run when a user signs in.

Examples:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

```text
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
```

These locations are important when investigating persistence mechanisms.

However, many legitimate applications also use these keys.

### 7.7. Viewing Registry Information

Using Command Prompt:

```cmd
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

Using PowerShell:

```powershell
Get-ItemProperty `
"HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
```

These commands read Registry information without modifying it.

---

## 8. Windows File System

### 8.1. What Is a File System?

A **File System** defines how data is organized, stored, accessed, and managed on storage devices.

Windows supports several file systems.

| File System | Description |
|---|---|
| NTFS | Common Windows file system |
| FAT32 | Widely compatible file system with a maximum individual file size below 4 GiB |
| exFAT | Commonly used on removable storage devices |
| ReFS | File system designed for resilience and scalability in supported environments |

### 8.2. NTFS – New Technology File System

NTFS is widely used on Windows system drives.

Important features include:

- File and directory permissions.
- Access Control Lists.
- File system journaling.
- File compression.
- Encrypting File System (EFS).
- Alternate Data Streams (ADS).
- Support for large files and volumes.

### 8.3. NTFS Permissions

NTFS permissions control access to files and directories.

| Permission | Description |
|---|---|
| Read | Read file contents |
| Write | Write data |
| Read & Execute | Read and execute files |
| Modify | Read, write, modify, and delete |
| Full Control | Includes permission management and ownership-related capabilities |

Permissions are represented using Access Control Lists (ACLs).

### 8.4. Master File Table (MFT)

The **Master File Table (MFT)** is a fundamental NTFS structure.

It contains records describing files and directories on an NTFS volume.

MFT records may contain:

- File names.
- File attributes.
- File size information.
- Timestamps.
- References to file data.
- References to related directory records.

**Digital Forensics Example**

Investigators may analyze MFT records to identify:

- Files that existed on a volume.
- File metadata.
- Potentially deleted file records.
- File system activity relevant to a timeline.

The availability of information depends on the current state of the file system and its records.

### 8.5. File Metadata

File metadata describes properties of a file.

Common examples:

| Metadata | Description |
|---|---|
| File Name | Name of the file |
| File Size | Size of the file |
| Creation Time | Recorded creation timestamp |
| Modified Time | Recorded last modification timestamp |
| Access Time | Recorded access timestamp |
| File Attributes | File properties |

Metadata is useful for constructing forensic timelines.

However, timestamps can be modified, affected by system behavior, or manipulated intentionally.

### 8.6. Viewing File Metadata

Using PowerShell:

```powershell
Get-Item "C:\Windows\notepad.exe" |
Select-Object Name, Length, CreationTime, LastWriteTime, LastAccessTime
```

Example output:

```text
Name          : notepad.exe
Length        : ...
CreationTime  : ...
LastWriteTime : ...
LastAccessTime: ...
```

Actual values depend on the Windows installation.

---

## 9. Windows Internals in Cybersecurity

Understanding Windows Internals helps security analysts investigate suspicious behavior.

### 9.1. Process Investigation

Example:

```text
explorer.exe
    |
    +--- powershell.exe
              |
              +--- unknown.exe
```

Questions to investigate:

1. Who launched PowerShell?
2. What command was executed?
3. Where is `unknown.exe` located?
4. Is the executable digitally signed?
5. Are there related Event ID 4688 records?

### 9.2. Suspicious Service Investigation

Example:

A new service appears with an unfamiliar executable path.

Investigation steps:

1. Identify the service name.
2. Examine the executable path.
3. Check the service startup type.
4. Identify the service account.
5. Search System Logs for Event ID 7045.
6. Correlate the installation time with other events.

### 9.3. Failed Logon Investigation

Example:

```text
Event ID 4625
Event ID 4625
Event ID 4625
Event ID 4624
```

Several failed logons followed by a successful logon may require investigation.

An analyst should examine:

- Account Name.
- Logon Type.
- Source IP Address, when available.
- Timestamps.
- Related authentication events.

This sequence alone does not prove a successful attack.

### 9.4. Registry Persistence Investigation

Example:

An unfamiliar executable is referenced by a Registry Run key.

Investigation steps:

1. Identify the Registry path.
2. Record the value name and data.
3. Examine the referenced executable.
4. Check its file metadata and digital signature.
5. Correlate with process and event log activity.

---

## 10. Practical Exercises

### Exercise 1: Processes and Parent Processes

1. What is the difference between a process and a thread?
2. What is the purpose of a PID?
3. What is a Parent Process?
4. Why are parent-child relationships useful in security investigations?
5. Which PowerShell command can display process names and parent PIDs?

### Exercise 2: Windows Services

1. What is a Windows Service?
2. What is the role of the Service Control Manager?
3. What is the difference between Automatic and Manual startup?
4. Which command opens the Services management console?
5. Why might an unknown automatically starting service require investigation?

### Exercise 3: Event Viewer

1. What is the purpose of Event Viewer?
2. What is the difference between System Log and Security Log?
3. Which Event ID indicates a successful logon?
4. Which Event ID indicates a failed logon?
5. Which Event ID records process creation when auditing is enabled?
6. Which Event ID indicates a new service installation?

### Exercise 4: Windows Registry

1. What is the Windows Registry?
2. What is the difference between HKLM and HKCU?
3. What is a Registry Key?
4. What is the purpose of the Registry Run keys?
5. Why should Registry modifications be performed carefully?

### Exercise 5: Windows File System

1. What is NTFS?
2. What is the purpose of the Master File Table?
3. What are NTFS permissions used for?
4. What is file metadata?
5. Why are file timestamps useful in Digital Forensics?

### Exercise 6: Security Investigation Scenario

Consider the following activity:

```text
09:00:01 - Event ID 4625
09:00:03 - Event ID 4625
09:00:05 - Event ID 4625
09:00:10 - Event ID 4624
09:00:15 - Event ID 4688
09:00:20 - Event ID 7045
```

Answer the following questions:

1. What happened at 09:00:01?
2. What happened at 09:00:10?
3. What does Event ID 4688 indicate?
4. What does Event ID 7045 indicate?
5. Why might this sequence require further investigation?
6. What additional evidence would you collect before concluding that an attack occurred?

---

## 11. Key Takeaways

- Windows separates application execution and privileged operating system operations through User Mode and Kernel Mode.
- A Process is a running program instance.
- A Thread is a unit of execution within a process.
- A PID identifies a process while it exists.
- Parent-child relationships help reconstruct process activity.
- Windows Services provide background functionality and are managed by the Service Control Manager.
- Event Viewer is used to examine Windows event logs.
- Security Logs contain security auditing events.
- System Logs contain operating system and service-related events.
- Event IDs help identify specific types of recorded activity.
- The Windows Registry stores system and application configuration.
- NTFS provides file management, permissions, and metadata structures.
- The Master File Table is an important artifact in NTFS forensics.
- Security investigations require correlating multiple sources of evidence rather than relying on a single event.

---

## 12. References

- [Microsoft Learn – Windows Internals](https://learn.microsoft.com/en-us/sysinternals/resources/windows-internals)
- [Microsoft Learn – Windows Processes and Threads](https://learn.microsoft.com/en-us/windows/win32/procthread/processes-and-threads)
- [Microsoft Learn – Windows Services](https://learn.microsoft.com/en-us/windows/win32/services/services)
- [Microsoft Learn – Windows Event Log](https://learn.microsoft.com/en-us/windows/win32/wes/windows-event-log)
- [Microsoft Learn – Windows Security Auditing](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/)
- [Microsoft Learn – Windows Registry](https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry)
- [Microsoft Learn – NTFS Overview](https://learn.microsoft.com/en-us/windows-server/storage/file-server/ntfs-overview)
- [Microsoft Sysinternals](https://learn.microsoft.com/en-us/sysinternals/)

---

## 13. Answers and Explanations

### Exercise 1: Processes and Parent Processes

1. **Process vs Thread:** A process is a running program instance, while a thread is an execution unit within that process.
2. **PID:** A numerical identifier assigned to a process while it exists.
3. **Parent Process:** A process associated with the creation of another process.
4. **Security Investigation:** Parent-child relationships help analysts reconstruct execution chains and identify unusual activity.
5. **PowerShell Command:**

```powershell
Get-CimInstance Win32_Process |
Select-Object Name, ProcessId, ParentProcessId
```

### Exercise 2: Windows Services

1. **Windows Service:** A background service that provides functionality to Windows or applications.
2. **Service Control Manager:** Manages service installation, configuration, startup, and control.
3. **Automatic vs Manual:** Automatic services start during system startup, while Manual services start when requested.
4. **Services Console:** `services.msc`.
5. **Unknown Service:** An unfamiliar service may indicate unauthorized software or a persistence mechanism, although it may also be legitimate.

### Exercise 3: Event Viewer

1. **Event Viewer:** A tool for examining Windows event logs.
2. **System vs Security:** System Logs record operating system events, while Security Logs record security auditing events.
3. **Successful Logon:** Event ID `4624`.
4. **Failed Logon:** Event ID `4625`.
5. **Process Creation:** Event ID `4688`.
6. **Service Installation:** Event ID `7045`, commonly found in the System Log.

### Exercise 4: Windows Registry

1. **Windows Registry:** A hierarchical database containing Windows and application configuration settings.
2. **HKLM vs HKCU:** HKLM stores system-wide configuration, while HKCU represents the current user's configuration.
3. **Registry Key:** A container that may contain subkeys and values.
4. **Run Keys:** Registry locations that can configure applications to launch when users sign in.
5. **Safety:** Incorrect changes may disrupt applications or operating system functionality.

### Exercise 5: Windows File System

1. **NTFS:** A Windows file system supporting permissions, journaling, metadata, and other features.
2. **MFT:** Stores records describing files and directories on an NTFS volume.
3. **NTFS Permissions:** Control which users and groups can access files and directories.
4. **File Metadata:** Information describing a file, including its size, attributes, and timestamps.
5. **Forensic Value:** Timestamps can help reconstruct a timeline, but must be validated against other evidence.

### Exercise 6: Security Investigation Scenario

1. **09:00:01:** A failed logon attempt was recorded (Event ID 4625).
2. **09:00:10:** A successful logon was recorded (Event ID 4624).
3. **Event ID 4688:** A new process was created.
4. **Event ID 7045:** A new Windows service was installed.
5. **Why Investigate:** Multiple failed logons followed by a successful logon, process creation, and service installation may be consistent with suspicious activity.
6. **Additional Evidence:** Examine account names, source addresses, logon types, process command lines, executable paths, service configurations, timestamps, and related logs.

**Important:** These events do not establish that they belong to the same user or attack sequence. Their relationships must be verified before reaching a conclusion.
