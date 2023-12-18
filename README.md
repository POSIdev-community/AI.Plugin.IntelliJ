## Overview

The PT Application Inspector plugin finds vulnerabilities and undocumented features in application code while it is being written (supported languages: PHP, Python, Java, JavaScript, TypeScript). Built-in analysis modules detect source code vulnerabilities, configuration file errors, and vulnerable third-party components and libraries used in the application development process.

## How it works

### Enabling and disabling the plugin

You can enable or disable the plugin in an open project by clicking the icon in the bottom right toolbar. If it is not the first time you are opening the project, the plugin is enabled automatically (scan and action history is saved). You can also set up the plugin to be automatically enabled when a new project is opened.

When the plugin is enabled, the **.ai** folder is created in the project. This folder contains a database, log files, and a configuration file. For Git to ignore the **.ai** folder, create an empty file `.gitignore` in the project folder.

### Installing the code analyzer

For the plugin to operate correctly, the PT Application Inspector code analyzer is required. There are two ways to install the analyzer:
* In JetBrains IDE.
* In the IntelliJ IDEA interface by clicking **Download analyzer** in the pop-up window. In Linux and Windows, unpacking is performed automatically, whereas in macOS an installer starts.

The path for code analyzer installation:
* In Windows: `%LOCALAPPDATA%\Application Inspector Analyzer`
* In Linux: `~/application-inspector-analyzer`
* In macOS: `/Library/Application-Inspector-Analyzer`

![AI-enable](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-enable.gif)

### Scanning a project

You can start a project scan in the following ways:
* By clicking **Scan project**
* By clicking **Full scan project**
* When saving project changes (if you selected **On saving** for the **Trigger scan**parameter)

You can monitor the scan progress in the bottom panel and on the **Scan log** tab in the **PT Application Inspector** panel. The first scan usually takes longer due to the initial load on the database of vulnerable components.

Scan is performed based on the default settings. You can change these settings in the `aiproj.json` configuration file. To create the `aiproj.json` file, in the **File** menu, select **New** → **Aiproj file**.

To exclude files and folders from scanning, use the `.aiignore` file. To create the `.aiignore` file, in the **File** menu, select **New** → **Aiignore file**. The syntax of this file is similar to the `.gitignore` syntax. For more information, see [git-scm.com/docs/gitignore](git-scm.com/docs/gitignore). You can also use the **SkipGitIgnoreFiles** setting in the `aiproj.json` file to exclude from scanning files and folders from the `.gitignore` file. By default, this setting is enabled.

![AI-aiproj](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-aiproj.gif)

### Stopping a scan

To stop a project scan, click **Stop scan** in the **PT Application Inspector** panel or close the progress bar in the bottom toolbar.

![AI-stop](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-stop.gif)

## Analyzing scan results

You can find the list of all detected vulnerabilities on the **Detected vulnerabilities** tab of the **PT Application Inspector** panel. If you click a vulnerability in the list, the line with its exit point gets highlighted in the code editor. If the system detects vulnerabilities that are not included in the code analyzer database, they are marked with the <span class="doc-icon-pt" style="color:#999999;"></span> tag. The second-order vulnerabilities are marked with the <span class="doc-icon-pt" style="color:#999999;">2</span> tag.

The **Description** tab contains the vulnerability description with example attack scripts, vulnerability elimination recommendations, and links to references.

The **[PT AI] Vulnerability details** panel displays additional information about the vulnerability. The **Data flow** tab contains a data-flow diagram that shows how each process converts its input data to output data and how processes interact. The data-flow diagram consists of the following sections:
* **Entry point**. The starting point of the control flow.
* **Data entry point**. A file and code line with coordinates of data entry.
* **Data changes**. The description of one or several functions that modify potentially harmful input data. This section may not be displayed on the diagram if input data was not modified.
* **Exit point**. The execution line of a potentially vulnerable function. This is an exit point related to the vulnerability in the source code.
* **Best place to fix**. A code line best suited for patching a vulnerability. This section is displayed before the data flow.

You can go to the corresponding place in the code editor from any section of the data-flow diagram.

The **Exploit** tab contains a test HTTP request (exploit) that can be used to exploit the vulnerability in a deployed web application. You can automatically generate an exploit by clicking **Create an exploit request**.

***Note.** To exploit the vulnerability, specify the address of the host where your web application is deployed in the `aiproj.json` file. The default value is "localhost."*

***Note**. This feature is available in commercial versions of JetBrains IDE.*

![AI-exploit](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-exploit.gif)

Some vulnerabilities have additional exploitation conditions displayed on the **Additional conditions** tab.

When you scroll through the sections of the diagram, the vulnerability information is automatically pinned until you move on to another vulnerability. If you want to view information about a certain vulnerability while working on the code, you can pin this vulnerability manually.

Several vulnerabilities can have the same exit point. If these vulnerabilities belong to the same type, they are grouped together and displayed as one problem with different exploitation options. You can view detailed information about such vulnerabilities on the **[PT AI] Vulnerability details** panel.

***Note.** If you confirm one vulnerability from the group, the whole problem will be confirmed automatically. To discard an entire problem, you must discard all the vulnerabilities in the group.*



### Managing detected vulnerabilities

The PT Application Inspector plugin contains a set of tools for managing detected vulnerabilities. With these tools, you can do the following:
* Filter vulnerabilities by severity, status, and exclusion from scan results by clicking the eye button.
* Confirm, discard, and exclude vulnerabilities from scan results in their context menu in the code editor.
* Confirm and discard vulnerabilities by clicking the X and checkmark button on the **[PT AI]****Vulnerability details** panel.
* Perform group actions on all vulnerabilities in the file. For example, in the context menu of a vulnerability, select **Confirm vulnerability** → **Fix all '[PT AI] Scanning' problems**.

![AI-action](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-action.gif)

### Developer mode

With the developer mode enabled, the **PT Application Inspector** panel displays additional tabs:
* **Scan history**. Compares two scan results within a single project. To compare the results, in the context menu of the first scan, select **Compare with**, and then select the second scan.
* **Log**. Displays log entries of the code analyzer **(Log** → **Analyzer**) and of the plugin (**Log** → **Plugin log**). In the `dev_mode_config.json` configuration file, you can configure user log tab display. By default, the file contains the path to output the analyzer log. To add extra tabs, in the **panelName** field, enter the tab name, and in the **pathsToLogs** field, enter at least one path to the log file that will be displayed on the tab.

![AI-compare](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-compare.gif)

Example configuration file `dev_mode_config.json`:

```
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

## Integration with PT AI Enterprise Edition

The PT Application Inspector plugin can be integrated with PT AI Enterprise Edition. The integration allows all team members to work with the source code from different environments, which makes the development process more secure.

To configure the integration:

1. In the main menu of IntelliJ IDEA, click **Tools** → **PT Application Inspector** → **Connect to AI Server**.

1. In the **Server address** field, specify the PT AI Enterprise Server address and click **Connect**.

    ![Connecting to PT AI Enterprise Server](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-connect.gif)
1. Sign in using the SSO system you set up.

1. Synchronize a local project in IntelliJ IDEA and a project in PT AI Enterprise Server in one of the following ways:

   Upload a local project to PT AI Enterprise Server

   ![Upload a local project](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-upload-to-server.gif)
   Download a project from PT AI Enterprise Server to a local file system

   ![Download a project from PT AI Enterprise Server](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-download-from-server.gif)
   Connect a local project to an existing project in PT AI Enterprise Server

![Synchronizing projects](https://raw.githubusercontent.com/POSIdev-community/AI.Plugin.IntelliJ/master/media/readme/AI-map-project.gif)

The statuses of detected vulnerabilities are synchronized automatically, and all the team members can assess the current threat level.

For more information about the integration, see the PT AI Enterprise Edition User Guide.

## Plugin settings

To configure the plugin settings, select **File** → **Settings** → **Tools** → **PT Application Inspector**.

The plugin configuration page contains the following settings:
* **Analyzer log level**. The severity level starting from which the code analyzer events will be logged. The default value is "error."
* **Trigger scan**. The start scan condition: manually on clicking Start or automatically when a project file is changed The default value is "manually."
* **Automatically enable for any project**. Silent activation of the plugin when opening a project. By default, the setting is disabled.
* **Use an additional tool window to view information**. Displays the **Data flow**,**Exploit**, and **Additional condition** tabs in the separate panel **[PT AI] Vulnerability details**. By default, the setting is disabled.
* **Allow telemetry collection**. Collection of general scan information to be sent to PT AI Enterprise Edition. By default, the setting is enabled. An example of the collected data can be viewed [here](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/master/media/readme/telemetryExample.json). For more information, see the privacy statement.
* **Use all available resources for scanning**. The use of all available RAM and CPU resources to increase scanning speed. By default, the setting is disabled.
* **Number of days to store log files**. The number of days log files are stored. The default value is "30."
* **Maximum number of stored log files**. The number of stored log files. The default value is "100."
* **Developer mode**. Advanced plugin features. By default, the setting is disabled.
* **Limit scan history to**. The maximum number of scan results saved in the history. The default value is "10." If the limit is exceeded, each new scan result deletes the oldest result. This setting is available only in the developer mode.

## Requirements

For the correct operation of the PT Application Inspector plugin, the following technical requirements must be met:
* JetBrains IDE (PhpStorm, IntelliJ IDEA, WebStorm) 2022.2.3 or later
* 8 GB RAM
* 5 GB of free hard drive space

Supported 64-bit OS:
* Debian 11 Bullseye or later
* Fedora Workstation 38 or later
* OpenSUSE Leap 15.5 or later
* Ubuntu 22.04 LTS or later
* Ubuntu 23.04 or later
* Windows 10
* ALT Linux OS in the test mode

Supported macOS:
* Big Sur 11.5 or later
* Monterey 12.0.0 or later

## Privacy statement

By default, the PT Application Inspector plugin collects anonymous usage data and sends it to our experts so that they can better understand how to improve the product. We do not share the collected information with third parties. We do not collect source code or IP addresses. To stop the data collection, disable the **Allow telemetry collection** setting.
