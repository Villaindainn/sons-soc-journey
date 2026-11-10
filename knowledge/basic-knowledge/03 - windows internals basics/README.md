
# 03 - Windows Internals Basics

> Fundamental knowledge of Windows operating system internals for Cybersecurity, SOC Analysis, and Digital Forensics.

## 1. Overview

Windows Internals is the study of how the Windows operating system works internally, including process management, services, registry, file systems, and event logging.

### Learning Objectives

- Understand Processes and Parent Processes.
- Understand Windows Services.
- Learn how to use PowerShell for process analysis.
- Explore Windows Event Viewer.
- Understand System and Security Logs.
- Identify common Windows Event IDs.
- Apply basic Windows Forensics techniques.

---

## 2. Windows Processes

### 2.1. What is a Process?

A Process is an instance of a program that is currently running on an operating system.

Each process contains important information:

| Attribute | Description |
|---|---|
| PID | Process ID - unique process identifier |
| PPID | Parent Process ID |
| Name | Process name |
| Path | Executable file path |
| User | User account running the process |
| Command Line | Command used to launch the process |

### 2.2. Parent Process

A Parent Process is the process that creates or launches another process.

Example:

```text
explorer.exe
    |
    |--- notepad.exe
    |
    |--- chrome.exe
    |
    |--- powershell.exe
```

In Digital Forensics, analyzing parent-child process relationships helps investigators:

- Identify which process launched another process.
- Detect suspicious process execution.
- Investigate potential malware activity.
- Understand program execution chains.

### 2.3. Practical Lab: Process Analysis

**Step 1:** Open Task Manager.

```text
Ctrl + Shift + Esc
```

Navigate to the Details tab to view running processes and their PIDs.

**Step 2:** Open PowerShell and execute:

```powershell
Get-Process | Select-Object Id, ProcessName
```

**Step 3:** Identify Parent Processes:

```powershell
Get-CimInstance Win32_Process |
Select-Object ProcessId, ParentProcessId, Name
```

**Step 4:** Examine additional process information:

```powershell
Get-CimInstance Win32_Process |
Select-Object ProcessId, ParentProcessId, Name, ExecutablePath, CommandLine
```

### Observation

Record the following information:

- Process Name:
- PID:
- Parent PID:
- Executable Path:
- Command Line:

---

## 3. Windows Services

### 3.1. What is a Windows Service?

Windows Services are programs that run in the background to provide functionality for the operating system and applications.

Common examples:

| Service | Function |
|---|---|
| Windows Update | Manages Windows updates |
| Windows Defender | Provides security protection |
| Print Spooler | Manages printing tasks |
| Windows Event Log | Manages Windows event logs |

### 3.2. Practical Lab: Service Analysis

**Step 1:** Press `Win + R`.

**Step 2:** Enter:

```text
services.msc
```

**Step 3:** Examine:

- Service Name
- Display Name
- Status
- Startup Type

**Step 4:** Use PowerShell:

```powershell
Get-Service
```

Retrieve detailed service information:

```powershell
Get-CimInstance Win32_Service |
Select-Object Name, State, StartMode, ProcessId
```

### Digital Forensics Relevance

Windows Services can be investigated to identify:

- Suspicious services.
- Unauthorized service installations.
- Services used by malware for persistence.
- Services running from unusual executable paths.

---

## 4. Windows Event Viewer

### 4.1. What is Event Viewer?

Event Viewer is a built-in Windows utility that records and displays events occurring within the operating system.

It is an important source of evidence in Windows Digital Forensics.

### 4.2. How to Open Event Viewer

Press:

```text
Win + R
```

Enter:

```text
eventvwr.msc
```

Navigate to:

```text
Event Viewer
    |
    |--- Windows Logs
            |
            |--- Application
            |--- Security
            |--- Setup
            |--- System
            |--- Forwarded Events
```

### 4.3. Important Windows Logs

| Log | Description |
|---|---|
| Application | Application-related events |
| Security | Security and auditing events |
| System | Operating system events |
| Setup | Installation and setup events |
| Forwarded Events | Events forwarded from other computers |

### 4.4. Security Logs

Security Logs contain events related to system security and user activities.

Examples:

- Successful logons.
- Failed logon attempts.
- User logoffs.
- Process creation.
- Account management activities.

### 4.5. System Logs

System Logs record events related to Windows system operations.

Examples:

- System startup and shutdown.
- Service startup and termination.
- Driver-related errors.
- Operating system errors.

---

## 5. Important Windows Event IDs

The following five Event IDs are important for basic Windows Forensics.

| Event ID | Description | Log |
|---|---|---|
| 4624 | Successful Logon | Security |
| 4625 | Failed Logon | Security |
| 4634 | Account Logoff | Security |
| 4688 | Process Creation | Security |
| 7045 | New Service Installed | System |

### 5.1. Event ID 4624 - Successful Logon

Records a successful user logon.

Important fields:

- Account Name
- Logon Type
- Source Network Address
- Time Created

### 5.2. Event ID 4625 - Failed Logon

Records a failed logon attempt.

Useful for investigating:

- Repeated incorrect passwords.
- Suspicious authentication attempts.
- Potential brute-force attacks.

### 5.3. Event ID 4634 - Account Logoff

Records when a logon session is terminated.

Useful for reconstructing user activity timelines.

### 5.4. Event ID 4688 - Process Creation

Records when a new process is created.

Useful for:

- Process execution analysis.
- Investigating suspicious applications.
- Reconstructing process execution chains.

Note: The Audit Process Creation policy must be enabled to collect this event. Command-line logging can also be enabled to capture additional execution details.

### 5.5. Event ID 7045 - New Service Installed

Records when a new Windows service is installed.

Useful for investigating:

- Unauthorized service installations.
- Suspicious services.
- Potential malware persistence mechanisms.

Note: Event ID 7045 belongs to the System log, not the Security log.

---

## 6. Practical Lab: Event Log Analysis

### Task 1: Find Event ID 4624

1. Open Event Viewer.
2. Navigate to Windows Logs.
3. Select Security.
4. Click Filter Current Log.
5. Enter Event ID `4624`.
6. Examine successful logon events.

### Task 2: Find Event ID 4625

Repeat the previous steps using:

```text
4625
```

Examine failed logon attempts, usernames, timestamps, and authentication details.

### Task 3: Find Event ID 4688

Search for:

```text
4688
```

Examine the process creation information and parent process details when available.

### Task 4: Find Event ID 7045

Navigate to:

```text
Windows Logs → System
```

Filter using:

```text
7045
```

Examine information about newly installed services.

---

## 7. PowerShell Quick Reference

Useful commands for Windows analysis:

| Command | Function |
|---|---|
| `Get-Process` | Lists running processes |
| `Get-Service` | Lists Windows services |
| `Get-CimInstance Win32_Process` | Retrieves process information |
| `Get-WinEvent -ListLog *` | Lists available event logs |
| `Get-WinEvent -FilterHashtable @{LogName='Security';Id=4624}` | Retrieves successful logon events |

### Example: Find Failed Logon Events

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
} -MaxEvents 10
```

Note: Administrator privileges may be required to access certain Security logs.

---

## 8. Knowledge Check

Test your understanding:

1. What is a Process?
2. What is the difference between PID and Parent PID?
3. What is the purpose of Windows Services?
4. What is Windows Event Viewer?
5. What is the difference between Security Logs and System Logs?
6. What does Event ID 4624 represent?
7. What can Event ID 4625 help investigators identify?
8. What is Event ID 4688 used for?
9. What does Event ID 7045 represent?
10. Why is Process Analysis important in Digital Forensics?

---

## 9. Key Takeaways

After completing this lesson, you should understand:

- Processes and process identifiers.
- Parent-child process relationships.
- Windows Services and background operations.
- Windows Event Viewer.
- Security and System Logs.
- Important Windows Event IDs.
- Basic PowerShell commands for system investigation.

---

## 10. Learning Progress

- [ ] Understand Processes and PIDs.
- [ ] Understand Parent Processes.
- [ ] Explore Windows Services.
- [ ] Open Windows Event Viewer.
- [ ] Analyze System Logs.
- [ ] Analyze Security Logs.
- [ ] Identify five important Event IDs.
- [ ] Practice PowerShell Event Log queries.

**Status:** In Progress

**Category:** Basic Knowledge

**Related Fields:** SOC Analysis, Windows Forensics, Incident Response

**Next Topic:** Windows Registry Fundamentals

---

*Son's SOC Journey - Learning Cybersecurity Step by Step.*
