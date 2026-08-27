## Overview

The PT Application Inspector plugin finds vulnerabilities and undocumented features in application source code. In addition to code analysis, built-in modules detect errors in configuration files and vulnerabilities in third-party components and libraries used in application development. The plugin supports the following languages: C#, Go, Java, JavaScript, Kotlin, PHP, Python, Ruby, Scala, SQL, Solidity, TypeScript, C/C++, Objective-C, and Swift.

The plugin also partially supports 1C and Dart. You cannot start scans for 1C or Dart projects with the plugin, but you can use the plugin to create PT AI Enterprise Server projects in these languages or load scan results for 1C or Dart projects from PT AI Enterprise Server (the plugin will then display vulnerabilities detected in these projects). The "Hardcoded secrets" and "Vulnerable components and their use" modules are also partially supported. You can use the plugin to create PT AI Enterprise Server projects with these modules, or to load scan results for projects with these modules from PT AI Enterprise Server (the plugin will then display vulnerabilities detected by these modules).

***Note.** The scanning of projects in C/C++ and Objective-C is not supported in macOS.*

## How it works

### Enabling and disabling the plugin

You can enable or disable the plugin in an open project by clicking the icon in the bottom right toolbar. If it is not the first time you are opening the project, the plugin is enabled automatically (scan and action history is saved). You can also set up the plugin to be automatically enabled when a new project is opened.

When the plugin is enabled, the **.ai** folder is created in the project. This folder contains a database, log files, and a configuration file. For Git to ignore the **.ai** folder, create an empty file `.gitignore` in the project folder.

### Installing the code analyzer

For the plugin to operate correctly, the PT Application Inspector code analyzer is required. You can install it automatically by clicking **Download Analyzer** in the pop-up notification in the IntelliJ IDEA interface or manually by downloading it from the link in the instructions below.

To manually install the code analyzer:

1. Download the archive with the analyzer using one of the links:

    * For Windows: [download](https://update.ptsecurity.com/api/v6/products/AI.INFRASTRUCTURE.INSTALLATOR.zip/2.9.0.1126/download/AI.INFRASTRUCTURE.INSTALLATOR.2.9.0.1126.zip)

    * For Linux: [download](https://update.ptsecurity.com/api/v6/products/AI.INFRASTRUCTURE.INSTALLATOR.tar.gz/2.9.0.1126/download/AI.INFRASTRUCTURE.INSTALLATOR.2.9.0.1126.tar.gz)

    * For macOS: [download](https://update.ptsecurity.com/api/v6/products/AI.INFRASTRUCTURE.INSTALLATOR.pkg/2.9.0.1126/download/AI.INFRASTRUCTURE.INSTALLATOR.2.9.0.1126.pkg)

1. In macOS, run the following command to remove the `com.apple.quarantine` attribute:

   ```bash
   xattr -d com.apple.quarantine <analyzer_file_path.pkg>
   ```

   Then run the installation file and follow the instructions.

1. In Windows and Linux, unpack the archive to one of the following locations:

    * In Windows: `%LOCALAPPDATA%\Application Inspector Analyzer`

    * In Linux: `~/application-inspector-analyzer`

![AI-enable](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-enable.gif?raw=true)

### Scanning a project

You can start a project scan in the following ways:
* By clicking the **Scan** button.
* By clicking **Scan Options** → **Full Local Scan**.
* By saving project changes (if you selected **On saving** for the **Trigger scan** setting).

The scan progress is displayed in the bottom panel and in the **Log** tab of the **PT Application Inspector** panel. The first scan usually takes longer due to the initial load on the database of vulnerable components.

Scans are performed based on the default settings. You can change these settings in the `.aiproj.json` configuration file. To create the `aiproj.json` file, in the **File** menu, select **New** → **Aiproj file**.

To exclude files or folders from scanning, use the `.aiignore` file. To create the `.aiignore` file, in the **File** menu, select **New** → **Aiignore file**. The syntax of this file is similar to the `.gitignore` syntax. For more information, see [git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore). You can also use the **SkipGitIgnoreFiles** setting in the `.aiproj.json` file to exclude from scanning files and folders from the `.gitignore` file. By default, this setting is enabled.

![Creating the .aiproj.json file](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-aiproj.gif?raw=true)

### Stopping a scan

To stop a project scan, click **Stop scan** in the **PT Application Inspector** panel or close the progress bar in the bottom toolbar.

![Stopping a scan](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-stop.gif?raw=true)

## Analyzing scan results

You can find the list of all detected vulnerabilities on the **Detected Vulnerabilities** tab of the **PT Application Inspector** panel. If you click a vulnerability in the list, the line with its exit point gets highlighted in the code editor. If the system detected vulnerabilities that are not included in the code analyzer database, they are marked with the `?` tag. Second-order vulnerabilities are marked with the `1>2` tag.

The **Description** tab contains the vulnerability description with example attack scripts, fix recommendations, and links to references.

The **[PT AI] Vulnerability Details** panel displays additional information about the vulnerability. The **Data Flow** tab contains a data-flow diagram that shows how each process converts its input data to output data and how processes interact. Data-flow diagrams consist of the following sections:
* **Entry point**. A point where data flow analysis starts.
* **Data entry point**. A file and code line where untrusted data enter the program.
* **Data changes**. The description of one or several functions that modify potentially harmful input data. This section may not be displayed on the diagram if the input data were not modified.
* **Exit point**. The execution line of a potentially vulnerable function. This is the exit point related to the vulnerability in the source code.

You can go to the corresponding place in the code editor from any section of the data-flow diagram.

For vulnerabilities detected in Solidity applications using the Pygrep core, the **Metavariables** tab is displayed in the card instead of the **Data flow** tab. When scanning a project, the Pygrep core uses rules from the PT AI Enterprise Edition knowledge base or custom rules, the path to which is specified in the Solidity language settings. Each rule contains templates describing metavariables and regular expressions for finding these metavariables. A vulnerability is considered to be found if there are lines of code in which a regular expression corresponding to a metavariable is triggered.

The **Exploit** tab contains a test HTTP request that can be used to exploit the vulnerability in a deployed web application. You can automatically generate an exploit by clicking **Generate Exploit**.

***Note.** To exploit a vulnerability, specify the address of the host where your web application is deployed in the `.aiproj.json` file. The default value is "localhost."*

***Note.** This feature is available in commercial versions of JetBrains IDE.*

![Vulnerability exploitation](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-exploit.gif?raw=true)

Some vulnerabilities have additional exploitation conditions displayed in the **Additional Conditions** tab.

When you scroll through the sections of the diagram, the vulnerability information is automatically pinned until you move on to another vulnerability. If you want to view the information about a certain vulnerability while working on the code, you can pin this vulnerability manually.

Several vulnerabilities can have the same exit point. If these vulnerabilities belong to the same type, they are grouped together and displayed as one problem with different exploitation options. You can view detailed information about such vulnerabilities in the **[PT AI] Vulnerability Details** panel.

***Note.** If you confirm one vulnerability from the group, the whole problem will be confirmed automatically. To discard an entire problem, you must discard all the vulnerabilities in the group.*

### Managing detected vulnerabilities

The PT Application Inspector plugin contains a set of tools for managing detected vulnerabilities. With these tools, you can do the following:
* Filter vulnerabilities by severity, status, and suppression from scan results by clicking the eye button.
* Confirm and discard vulnerabilities by clicking **Confirm** or **Discard** on the **[PT AI] Vulnerability Details** panel.
* Confirm, discard, and suppress vulnerabilities in their context menu in the code editor. There you can also perform group actions on all vulnerabilities in the file. For example, click **Confirm vulnerability** → **Fix all code vulnerabilities in the file**.
* Manage the statuses of several vulnerabilities by selecting them in the **Detected Vulnerabilities** tab and changing the status using the corresponding button.

![Confirming vulnerabilities](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-action.gif?raw=true)

### Using the assistant

If a large number of vulnerabilities is detected during project scanning, you can sort them out much faster using the assistant function. The assistant gives recommendations in the following order:
* Confirm vulnerabilities that have an exploit
* Discard vulnerabilities with a detected filtering function
* Confirm or discard a group of vulnerabilities similar in type or vulnerable code
* Review vulnerability statuses assigned manually by the user

![Assistant Overview](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/assistant_overview.gif?raw=true)

You can start the assistant from the pop-up notification that appears when the scan is completed or by clicking the **Assistant** button. You can choose to go through the whole scenario or only certain steps.

![Assistant Action](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/assistant_action.gif?raw=true)

The assistant shows AI-driven recommendations on how to fix detected vulnerabilities.

![Assistant AI Quick Fix](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-assistant-copilot-quick-fix.gif?raw=true)

How to get a recommendation:

1. Select a vulnerability on the **Detected Vulnerabilities** tab or by clicking **Suggest fix** in the code editor context menu.

1. Select the **How to Fix** tab.

1. Click **Create**.

You can apply the suggested fix or generate an alternative option.

![Assistant AI Overview](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-assistant-copilot-overview.gif?raw=true)

### Comparing scan results

You can compare results of two scans within a project. To do this, in the **Scan History** tab, in the context menu of the first scan, select **Compare with**, and then select the second scan.

![Comparing two scan results within a single project](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-compare.gif?raw=true)

## Integration with PT AI Enterprise Edition

The PT Application Inspector plugin can be integrated with PT AI Enterprise Edition. The integration allows all team members to work with the source code from different environments, which makes the development process more secure.

To configure the integration:

1. In the IntelliJ IDEA main menu, click **Tools** → **PT Application Inspector** → **Connect to PT AI Server**.

1. Enter the PT AI Enterprise Server address and click **Connect**.

   ![connect to server](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-connect.gif?raw=true)

1. Sign in to PT AI Enterprise Edition using the SSO system you set up.

1. Perform the required integration scenario:

    * Upload the source code to PT AI Enterprise Server.

   ![create AIE project](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-create-project.gif?raw=true)

    * Send a local project for scanning to PT AI Enterprise Server with or without saving the results on the server.

   ![start remote scan](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-remote-scan.gif?raw=true)

    * Synchronize the results of the local scan and the scan in PT AI Enterprise Server.

   ![map project](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-map-project.gif?raw=true)

For more information about the integration, see the PT AI Enterprise Edition User Guide.

## Managing branches

If a project is a Git repository, the plugin uses a current branch as the local working branch. Local scan results, vulnerability statuses, and data on synchronization from PT AI Enterprise Server are associated with that branch.

When you set up synchronization between a local project and a PT AI Enterprise Server project, you must select the branch on the server that matches a current local branch. You can map a different branch later by selecting **Tools → PT Application Inspector → Change PT AI Server Project Branch** or by clicking a branch name in the scan results panel.

Branch mapping is needed for the following operations:
* Uploading source code to PT AI Enterprise Server
* Sending a local project to PT AI Enterprise Server for scanning
* Synchronization of vulnerability statuses and scan results

  ![manage branches](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/release/2.10.0/media/readme/AI-manage-remote-branch.gif?raw=true)

When you switch branches in Git, the plugin automatically switches to a corresponding local branch. If the new local branch is not yet mapped to a PT AI Enterprise Server branch, a notification with the **Select Branch** button is displayed. Before uploading code, syncing artifacts, or running a remote scan, you must select the required branch in PT AI Enterprise Server.

When the names of a local and remote branch differ, the plugin displays a **warning** before uploading source code or scan artifacts. You can continue the operation if the mapping was intentional, or you can cancel the action and select a different server branch.

If the Git repository is not initialized, the option to select a remote branch is still available. If the mapped branch was deleted in PT AI Enterprise Server, select a different branch when you receive the corresponding notification.

## Plugin settings

To configure the plugin system configuration, select **Tools** → **Options** → **Service** → **PT Application Inspector**.

The plugin configuration page contains the following sections of settings.

**General settings** section:
* **Analyzer log level**. Set the starting severity level from which the code analyzer events are logged. The default value is error.
* **Trigger scan**. Start scan condition: manually on clicking a start button or automatically when a project file is changed. Default: manually.
* **Automatically enable for any project**. Automatically activate the plugin when opening a project. By default, this setting is disabled.
* **Use an additional tool window to view information**. Display the **Data Flow**, **Exploit**, and **Additional Conditions** tabs in a separate **[PT AI] Vulnerability Details** panel. By default, this setting is enabled.
* **Allow telemetry collection**. Collect general scan information to be sent to PT AI Enterprise Edition. By default, this setting is enabled. [Here](https://github.com/POSIdev-community/AI.Plugin.IntelliJ/blob/master/media/readme/telemetryExample.json) you will find an example of the data that we collect. For more information, see the Privacy statement section.
* **Use all available resources**. Use all available RAM and CPU resources to increase the scanning speed. By default, this setting is disabled.
* **Number of scan history results to store**. The maximum number of scan results saved in the history. Unlimited by default. If the limit is exceeded, each new scan result deletes the oldest result.
* **Number of days to store log files for**. The log file storage period. The default value is 30.
* **Maximum number of stored log files**. The number of log files stored. The default value is 100.

**Server settings** section:
* **Server URL**. The address of the connected PT AI Enterprise Server.
* **Notify about new scan results from the PT AI server**. Toggle the display of notifications about receiving new scan results from PT AI Enterprise Server if synchronization with the project is configured. By default, this setting is enabled.
* **Automatically update scan results from the PT AI server**. Update scan results from PT AI Enterprise Server if synchronization with the project is configured. This setting is available if notification of new scan results is enabled.

**Assistant** section:
* **Run the assistant**. Activate the assistant automatically after the first scan or manually by clicking **Assistant**. The default value is "Automatically after the first scan."
* **Show recommendations on the Quick Fix menu**. Displays tips from the assistant. By default, this setting is enabled.
* The number of vulnerabilities to be confirmed or discarded starting from which a notification from the assistant will be displayed. The default value is 5.
* The number of similar vulnerabilities starting from which a notification from the assistant will be displayed. The default value is 5.
* **Suggest vulnerability fixes**. Show the **How to Fix** tab with vulnerability fix recommendations. The section contains settings for the YandexGPT network:
    * Model name
    * OAuth token for Yandex Cloud
    * ID of the Yandex Cloud directory for which your account has the `ai.languageModels.user` role
    * Temperature: a value from 0 to 1, which defines the model response variability (the higher the value, the more unpredictable the query output)
    * Maximum number of tokens in one recommendation (the number of tokens in the same text may vary between models)

## Requirements

For the correct operation of the PT Application Inspector plugin, the following technical requirements must be met:
* JetBrains IDE (PhpStorm, IntelliJ IDEA, WebStorm) 2025.1 or later
* 8 GB RAM
* 5 GB of free hard drive space

Supported 64-bit OS:
* Debian 11 Bullseye or later
* Fedora Workstation 38 or later
* OpenSUSE Leap 15.5 or later
* Ubuntu 22.04 LTS or later
* Ubuntu 23.04 or later
* Windows 11
* ALT Linux OS in the test mode

Supported macOS:
* Big Sur 11.5 or later
* Monterey 12.0.0 or later

## Support and feedback

If you have any questions about the plugin, follow the links in the **Help & Feedback** section on the plugin configuration page to get the necessary information, join our community, or report an issue.

## Privacy statement

By default, the PT Application Inspector plugin collects anonymous telemetry.  This allows our specialists to improve the stability and performance of the product. They can optimize resource consumption and speed up scanning, find and fix errors across different IDE versions more quickly, and understand which features are used most often to improve the user experience.

Only technical metrics (interaction events, environment settings, and the main scanning settings) for the plugin and IDE are sent. Source code, credentials, tokens, and project contents are completely excluded. All data is transmitted over a secure HTTPS channel, processed anonymously, and not shared with third parties. It does not contain any confidential information.

If you do not want to participate in telemetry collection, disable the **Allow telemetry collection** setting. Changes take effect immediately, without restarting the IDE.
