# 🐧 Working with Text Files in the CLI

A Linux command-line lab focused on text editors, configuration files, file permissions, and modifying service configurations.

## 📌 Lab Overview

In this lab, I worked with Linux text files and configuration files using both graphical and command-line text editors.

The lab covers:

* Graphical text editors with **SciTE**
* Command-line text editing with **nano**
* Linux configuration files
* Hidden configuration files
* User and system-wide configuration
* Editing `.bashrc`
* Configuring an **nginx** web server
* Working with root permissions

---

## 🎯 Objectives

The lab consists of three main parts:

1. **Graphical Text Editors**
2. **Command Line Text Editors**
3. **Working with Configuration Files**

---

## 🔹 Part 1 — Graphical Text Editors

The lab introduces **SciTE**, a simple graphical text editor included in the Cisco CyberOps Workstation VM.

SciTE provides features such as:

* Tabbed environment
* Syntax highlighting
* Basic text editing

### Creating a Text File

A file named `space.txt` was created in the analyst user's home directory.

```bash
scite space.txt
```

The file can also be opened directly from the terminal using:

```bash
scite space.txt
```

### File Visibility in SciTE

When opening `space.txt`, the file may not initially appear because SciTE filters files based on known extensions.

Selecting:

```text
All Files (*)
```

allows the file to be displayed.

Linux itself does not depend on file extensions to identify file types.

### Running SciTE from the Terminal

When SciTE is launched from the terminal:

```bash
scite space.txt
```

the terminal prompt disappears because the terminal is currently running the SciTE process.

The process can be stopped using:

```text
CTRL+C
```

SciTE can also be launched with root privileges using:

```bash
sudo scite
```

---

## 🔹 Part 2 — Command Line Text Editors

Command-line text editors are especially important when working with remote Linux systems through **SSH**, particularly when the remote system does not have a graphical interface.

The lab focuses on **nano**.

### Opening a File with nano

```bash
nano space.txt
```

Because the text in `space.txt` was created without return characters, nano displays it as one long line.

Useful navigation keys include:

```text
Home      → Beginning of line
End       → End of line
Page Up   → Previous page
Page Down → Next page
```

The nano help screen can be opened with:

```text
CTRL+G
```

### Important nano Shortcuts

| Shortcut | Function            |
| -------- | ------------------- |
| `CTRL+O` | Save/write the file |
| `CTRL+W` | Search              |
| `CTRL+X` | Exit                |
| `CTRL+G` | Open help           |

---

## 🔹 Part 3 — Working with Configuration Files

Linux treats many system components as files, including configuration data.

Configuration files are generally text files that store settings used by applications and services.

### Configuration File Locations

User application configuration files are commonly stored in the user's home directory.

System-wide configuration files are conventionally stored under:

```text
/etc
```

---

## 🔸 Hidden Configuration Files

Configuration files in a user's home directory are commonly hidden by beginning their names with a dot (`.`).

For example:

```text
.bashrc
.bash_profile
.bash_logout
```

To display normal files:

```bash
ls -l
```

To include hidden files:

```bash
ls -la
```

---

## 🔸 The `.bashrc` Configuration File

The `.bashrc` file contains configuration for user-specific terminal behavior.

Example:

```bash
cat .bashrc
```

The lab demonstrates configurations such as:

```bash
export EDITOR=vim
PS1='\[\e[1;32m\][\u@\h \W]\$\[\e[0m\] '
alias ls="ls --color"
alias vi="vim"
```

These settings control the shell prompt and command aliases.

---

## 🔸 System-Wide Configuration

System-wide configuration files are conventionally stored under:

```text
/etc
```

Examples include configurations for:

* Web services
* Print services
* FTP services
* Email services

Regular users generally do not have write access to `/etc`, which restricts modification of system-wide service configuration to users with appropriate privileges.

---

## 🔸 User vs System-Wide Configuration

| Configuration        | Typical Location      | Access |
| -------------------- | --------------------- | ------ |
| User application     | User's home directory | User   |
| User shell           | `~/.bashrc`           | User   |
| System-wide services | `/etc`                | Root   |

User application configuration files are stored in the user's home directory so regular users can customize their applications without requiring access to `/etc`.

---

## 🔹 Editing `.bashrc`

The lab modifies `.bashrc` to change the terminal prompt color.

The original configuration uses:

```text
32
```

which represents green.

It is changed to:

```text
31
```

which represents red.

After saving the file and opening a new terminal, the prompt appears in red.

The same configuration can be edited using nano:

```bash
nano .bashrc
```

The color can then be changed from:

```text
31
```

to:

```text
33
```

where `33` represents yellow.

After saving, the shell can be reloaded with:

```bash
bash
```

### Important Observation

Changes to `.bashrc` do not automatically affect terminals that were already open. The configuration is applied when the terminal starts or the shell is reloaded.

---

## 🔹 Configuring nginx

The lab then demonstrates editing a system-wide nginx configuration file.

The configuration file is located under:

```text
/etc/nginx
```

The file is opened with nano using root privileges:

```bash
sudo nano -l /etc/nginx/custom_server.conf
```

The `-l` option enables line numbering.

### Changing the nginx Port

The nginx configuration is modified so that the server listens on:

```text
TCP 8080
```

The configuration changes the port from:

```text
81
```

to:

```text
8080
```

### Changing the Web Root

The document root is changed from:

```text
/usr/share/nginx/html/
```

to:

```text
/usr/share/nginx/html/text_ed_lab/
```

The semicolon at the end of the configuration line must remain in place.

### Starting nginx

The modified configuration is used to start nginx:

```bash
sudo nginx -c custom_server.conf
```

The local web server can then be accessed through:

```text
127.0.0.1:8080
```

### Stopping nginx

The nginx processes can be stopped with:

```bash
sudo pkill nginx
```

After stopping nginx, the webpage at `127.0.0.1:8080` no longer appears.

---

## 📝 Lab Questions & Answers

| Question                                                                                            | Answer                                                                                                                                       |
| --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Could you immediately find `space.txt` in SciTE?                                                    | No.                                                                                                                                          |
| Why is the terminal prompt not shown when SciTE is launched from the terminal?                      | The terminal is running SciTE and therefore cannot receive commands.                                                                         |
| What character does nano use when a line continues beyond the screen?                               | `>` or `$`, depending on the nano version.                                                                                                   |
| Why are user application configuration files stored in the user's home directory instead of `/etc`? | Regular users do not have permission to write to `/etc`, so storing them in the home directory allows users to customize their applications. |
| Did an already-open terminal change color after modifying `.bashrc`?                                | No. The `.bashrc` configuration is applied when a terminal is first opened.                                                                  |
| What does the nginx error message refer to?                                                         | The lab indicates it appears to be caused by a missing `favicon.ico` file.                                                                   |
| Does the webpage appear after nginx is stopped?                                                     | No.                                                                                                                                          |
| Can the nginx configuration be edited with SciTE?                                                   | Yes, by launching SciTE with root privileges.                                                                                                |

---

## 🛠️ Commands Used

```bash
ls
ls -l
ls -la
cat .bashrc

scite space.txt
sudo scite

nano space.txt
nano .bashrc

sudo nano -l /etc/nginx/custom_server.conf

sudo nginx -c custom_server.conf
sudo pkill nginx
```

---

## 💡 Key Takeaways

* Linux provides both graphical and command-line text editors.
* **SciTE** can be used as a graphical text editor.
* **nano** provides command-line text editing, which is useful when working through remote shells.
* Hidden files commonly begin with `.`.
* User-specific configuration files are commonly stored in the user's home directory.
* System-wide configuration files are conventionally stored under `/etc`.
* Root privileges are required to modify many system-wide configuration files.
* Changes to configuration files may require restarting or reloading the relevant service before they take effect.
* Configuration file locations, syntax, and available parameters vary between services, so documentation should be consulted.

---

## 📚 Source

Cisco Networking Academy — **Lab: Working with Text Files in the CLI**.
