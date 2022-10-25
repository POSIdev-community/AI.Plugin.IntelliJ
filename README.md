## Overview

The PT Application Inspector plugin allows finding security vulnerabilities and undocumented functionality in your application code as you write it. (PHP, Java, JavaScript, TypeScript are supported) With the built-in analysis modules, the plugin highlights not only source code vulnerabilities and configuration file flaws but also vulnerable third-party components and libraries used in application development.

## How it works

<details>
  <summary><b>Manual installing the plugin</b></summary>

    To install the plugin:
    1. Go to `File` → `Settings` → `Plugins`.
    2. Click the gear icon and select `Install Plugin from Disk`.
    3. Specify the path to the plugin ZIP archive.
    4. Restart IntelliJ IDEA.

    The plugin is now installed.

</details>

**Enabling and disabling the plugin**

You can enable or disable PT Application Inspector in a currently open project by clicking the icon in the lower-right toolbar. If it is not the first time the project is open, the plugin will be enabled automatically. (The scan and action history is stored.) You can also configure enabling the plugin automatically when you open a new project.

After you enable the plugin in your project, the .ai folder with the database, logs, and configuration file will be generated in your workspace. To make Git ignore the .ai folder, create an empty .gitignore file in the project folder.

**Installing the code analyzer**

For the plugin to work properly, you must install the PT Application Inspector code analyzer. There are two ways to do it:
- In a JetBrains IDE.
- In the settings by clicking `Download analyzer`. Unpacking is done automatically in UNIX systems and Windows, while the macOS starts the installer.

The path where the analyzer is installed:
- In Windows: `%LOCALAPPDATA%\Application Inspector Analyzer`
- In Linux: `~/application-inspector-analyzer`
- In macOS: `/Library/Application-Inspector-Analyzer`

![AI-enable](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-enable.gif)

**Scanning a project**

You can start a project scan:

* By clicking `Scan project`
* By clicking `Full scan project`
* By saving changes in a project (if you select `On saving` for the `Trigger scan` setting)

You can monitor the scan progress on a progress bar on the lower panel and on the PT Application Inspector panel on the `Scan log` tab. The first scan can take a while because of the initial load on the database of vulnerable components.

* Scanning is performed based on the default settings. You can change them in the aiproj.json file. To create an .aiproj file, go to the `File` menu, select `New`, and then select `Aiproj file`.

![AI-aiproj](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-aiproj.gif)

* To exclude files and folders from scanning, use the .aiignore file. To create a .aiignore file, go to the `File` menu, select `New`, and then select `Aiignore file`. The file syntax is similar to the .gitignore file. For more information on the syntax, see https://git-scm.com/docs/gitignore.
  You can also use the `SkipGitIgnoreFiles` setting in the configuration file to exclude from scanning files and folders from the .gitignore file. By default, it is enabled.

**Stopping a scan**

You can stop a project scan:

* By clicking `Stop scan`

* By closing the progress bar

![AI-stop](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-stop.gif)

## Exploring scan results

You can view the detected vulnerabilities on the `Detected vulnerabilities` tab in the `PT Application Inspector` window. When you select a vulnerability, a vulnerable piece of code is highlighted in the text editor.

You can see the description of the vulnerability in the  `Description` tool window to the right of `PT Application Inspector`.

**"[PT AI] Vulnerability details" tool window**

The `[PT AI] Vulnerability details` window contains additional vulnerability information (if any):

1. A navigable data flow diagram. It visualizes how each process converts its input into output and how processes interact.

The data flow diagrams consist of the following sections:

- **Entry point**. The starting point of the control flow.

- **Data entry point**. A file and a code line with the coordinates of the input data entry.

- **Data changes**. The description of one or several functions that modify potentially malicious input data.

  > ***Note.** There may be no `Data changes` section in the diagram if input data is not modified.*

- **Exit point**. The execution line of a potentially vulnerable function. This is the exit point associated with the vulnerability in source code. The exit point coordinates are mapped to the vulnerability in the data flow.

- **Best place to fix**. A code line best suited for patching a vulnerability. The section is displayed above the data flow.

You can go to the corresponding place in the text editor from any section of the data flow diagram.

2. An exploit and additional conditions required to exploit a vulnerability.

When you click `Create an exploit request`, an HTTP request is generated automatically. You can use an exploit to test a vulnerability detected in a deployed application.

> ***Note.** To exploit a vulnerability, you must specify a host where your application is deployed in the .aiproj file. Default: localhost.*

> ***Note.** This feature is available in commercial versions of a JetBrains IDE.*

![AI-exploit](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-exploit.gif)

Some vulnerabilities have additional exploitation conditions. You can find them on the `Additional conditions` tab.

When you browse through the diagram sections, the vulnerability information is pinned automatically until you move to another vulnerability. Also, if you want work on your code and view information about a particular vulnerability at the same time, you can pin the vulnerability manually.

**Managing detected vulnerabilities**

PT Application Inspector has a set of tools to manage detected vulnerabilities. You can change their status, suppress or filter them. For convenient vulnerability management, you can fold and unfold the list of vulnerabilities for all files simultaneously or for each file separately. Suspected vulnerabilities are marked with `?` and second-order vulnerabilities are marked with `1▸2`.

Actions on vulnerabilities:

* Confirm or discard a vulnerability using the `Quick fix` menu button or on the `[PT AI] Vulnerability details` tab.

* Suppress a vulnerability using the `Quick fix` menu button.

* Filter detected vulnerabilities by severity, status, and `Suppressed` flag.

* Perform group actions on all vulnerabilities in a file using the `Quick fix` menu button. For example, you can select `Confirm vulnerability` → `Fix all '[PT AI] Scanning' problems` in a file.


Several detected vulnerabilities may have the same exit point. Such vulnerabilities will be grouped if they have the same type. They will be displayed as one issue with different ways to exploit them. To view details on such vulnerabilities, go to `[PT AI] Vulnerability details`.

> ***Note.** If you confirm one vulnerability from a group, the entire issue will be confirmed. To discard the entire issue, you must discard all vulnerabilities in a group.*

![AI-action](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-action.gif)


**Developer mode**

When you enable the developer mode, it activates the following functionality:

* Comparing two scan results within a project. To compare results, on the `Scan history` page, right-click the first result and, on the menu, select `Compare with`, then click the second result.

![AI-compare](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-compare.gif)

* Additional tabs for viewing logs in PT Application Inspector. On the `Log` → `Plugin log` tab, you can find the plugin log records. You can also add custom log tabs in the dev_mode_config.json configuration file. By default, dev_mode_config.json contains a path for the analyzer log output.
  To add additional tabs, in the `panelName` field, specify the tab name and, in the `pathsToLogs` field, specify at least one path to a log file to show on the tab.

Example:

```JSON
{
    "logPanels": [
        {
            "panelName": "analyzer",
            "pathsToLogs": [
                "analyzer.log"
            ]
        },
        {
            "panelName": "test",
            "pathsToLogs": [
                "Configuration/error.log",
                "process.log"
            ]
        }
    ]
}
```

### Settings

The following settings are located at the `File` → `Settings` → `Tools` → `PT Application Inspector` path:

* **Analyzer log level**. An analyzer log level. Default: error.
* **Trigger scan**. Select if a scan is started manually or after you save changes in a project. Default: manually.
* **Automatically enable for any project**. Enable the plugin automatically for all projects without explicit confirmation from a user. Default: disabled.
* **Use an additional tool window to view information**. Show the additional `[PT AI] Vulnerability details` window for a more convenient vulnerability management.
* **Allow telemetry collection**. Collect generalized data about scanned code and send it to our team. To see a sample of collected data, click [here](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/master/media/readme/telemetryExample.json). For details, refer to the privacy statement. Default: enabled.
* **Use all available resources for scanning**. Use all resources for faster scanning. It may negatively impact the performance of your computer. Default: disabled.
* **Number of days to store log files for**. Limit the number of days to store log files for. Default: 30.
* **Maximum number of stored log files**. Limit the number of stored log files. Default: 100.
* **Developer mode**. Enable the extended functionality.
* **Limit scan history to**. Limit the number of stored scan results. Stored scan results are displayed in the scan history. The setting is available only if you enabled the developer mode. If the limit is exceeded, every new scan result deletes the oldest scan result. If you reduce the limit, all extra results will be deleted and cannot be restored. Default: 10.

## Requirements
For the PT AI Application Inspector plugin to work correctly, the following technical requirements must be met:

* JetBrains IDE (PhpStorm, IntelliJ IDEA, WebStorm) version 2022.2.3 or later
* 8 GB of RAM
* 5 GB of free disk space

Supported 64-bit OSs:
* Debian version 11.1 or later
* Fedora Workstation version 34 or later
* OpenSUSE version 15.3 or later
* Ubuntu Desktop version 20.04 or later
* Windows 10

Supported macOSs:
* Big Sur version 11.5 or later
* Monterey version 12.0.0 or later

## Privacy statement

By default, the PT Application Inspector plugin collects anonymous usage data and sends it to our team to help us understand how to improve the product. We do not pass collected information to third parties. Source code or IP addresses are not collected. You can stop data collection by disabling the `Allow telemetry collection` setting.

---
**Enjoy!**
