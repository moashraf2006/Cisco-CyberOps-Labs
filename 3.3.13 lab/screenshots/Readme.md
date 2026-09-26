# 🖥️ Monitor and Manage System Resources in Windows

A Windows administration lab focused on monitoring and managing system resources using built-in Windows administrative tools.

## 📌 Lab Overview

In this lab, I worked with Windows administrative utilities to:

* Start and stop the **Routing and Remote Access** service.
* Monitor system activity using **Performance Monitor**.
* Examine service-related events using **Event Viewer**.
* Create and configure a custom **Data Collector Set**.
* Monitor available system memory and save performance data to a CSV file.

## 🎯 Objectives

The lab consists of three main parts:

1. **Starting and Stopping the Routing and Remote Access Service**
2. **Working in the Computer Management Utility**
3. **Configuring Administrative Tools**

---

## 🔹 Part 1 — Routing and Remote Access

The first part demonstrates how starting and stopping a Windows service affects the system.

The **Routing and Remote Access (RRAS)** service allows the local device to function as a router or remote access server.

### Steps

1. Open **Control Panel → Network and Sharing Center**.
2. Open **Change adapter settings**.
3. Open **Administrative Tools**.
4. Launch **Performance Monitor**.
5. Clear the existing Performance Monitor graph.
6. Open **Services**.
7. Locate **Routing and Remote Access**.
8. Change its **Startup type** to `Manual`.
9. Start the service.
10. Refresh the Network Connections window.

### Observation

After starting the Routing and Remote Access service:

> An **Incoming Connections** icon appears in the Network Connections window.

After stopping the service:

> The **Incoming Connections** icon is no longer displayed.

### Performance Monitor

The counter recorded most prominently in the graph was:

```text
% Processor Time
```

The report view displayed a value for:

```text
Processor Information → % Processor Time
```

The example value in the lab was `2.804`, although values may vary between systems.

After disabling the service:

```text
Status: Blank
Startup Type: Disabled
```

---

## 🔹 Part 2 — Computer Management

The second part uses **Computer Management**, which provides tools grouped into:

* System Tools
* Storage
* Services and Applications

### Event Viewer

Navigate to:

```text
Computer Management
└── System Tools
    └── Event Viewer
        └── Windows Logs
            └── System
```

The lab requires locating events related to the **Routing and Remote Access** service.

Four events should describe the sequence of starting and stopping the service. The exact event descriptions may vary depending on the system.

---

## 🔹 Part 3 — Configuring Performance Monitoring

The final part focuses on creating a custom **Data Collector Set** for monitoring memory.

### Create the Data Collector Set

Navigate to:

```text
Control Panel
→ Administrative Tools
→ Performance Monitor
```

Then:

1. Expand **Data Collector Sets**.
2. Right-click **User Defined**.
3. Select **New → Data Collector Set**.
4. Name it:

```text
Memory Logs
```

5. Select **Create manually (Advanced)**.
6. Select **Performance counter**.
7. Add:

```text
Memory → Available MBytes
```

8. Set the sample interval to:

```text
4 seconds
```

### Storage Location

The collected data is saved under:

```text
C:\PerfLogs
```

The resulting directory contains:

```text
DataCollector01.csv
```

The exact folder name depends on the computer name and timestamp.

### CSV Data

The log format is configured as:

```text
Comma Separated
```

The far-right column in the CSV file represents:

```text
Available memory in MBytes
```

---

## 📝 Lab Questions & Answers

| Question                                                        | Answer                                                                                     |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| What appears after starting RRAS?                               | An **Incoming Connections** icon appears.                                                  |
| What happens after stopping RRAS?                               | The **Incoming Connections** icon disappears.                                              |
| Which counter was recorded most in the graph?                   | **% Processor Time**                                                                       |
| What value is displayed by the counter?                         | Values vary; the lab example shows **2.804** for Processor Information → % Processor Time. |
| What is the status/startup type after disabling RRAS?           | Status is blank and Startup Type is **Disabled**.                                          |
| What happens to the Memory Logs icon after stopping collection? | The green arrow is removed.                                                                |
| What does the far-right CSV column show?                        | **Available memory in MBytes**.                                                            |

## The lab notes that some answers, particularly performance values and event descriptions, may vary depending on the system.

## 🛠️ Tools Used

* Windows **Control Panel**
* **Network and Sharing Center**
* **Network Connections**
* **Services**
* **Performance Monitor**
* **Computer Management**
* **Event Viewer**
* **Data Collector Sets**

---

## 💡 Key Takeaways

This lab demonstrates how Windows administrators can use built-in tools to:

* Manage Windows services.
* Observe changes caused by service state changes.
* Monitor CPU activity.
* Investigate system events.
* Collect performance metrics over time.
* Export performance data for further analysis.

## The lab specifically demonstrates monitoring **CPU usage** and **available memory** using Performance Monitor.

## 📚 Source

Cisco Networking Academy — **Lab: Monitor and Manage System Resources in Windows**.
