---
title: Collect diagnostic logs for Azure Stack HCI, version 22H2 (preview)
description: How to collect diagnostic logs and share them with Microsoft.
author: ManikaDhiman
ms.author: v-mandhiman
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/22/2022
---

# Collect diagnostic logs (preview)

> Applies to: Azure Stack HCI, version 22H2 (preview)

This article describes how to collect diagnostic logs and send them to Microsoft to identify and fix any issues with your Azure Stack HCI solution. The article also provides information on known issue with log collection and the associated workaround.

## Collect logs via PowerShell

Use the `Send-DiagnosticData` cmdlet from any Azure Stack HCI server node to manually collect and send diagnostic logs to Microsoft. When you run this cmdlet, the logs are temporarily copied locally. This copy is parsed, sent to Microsoft, and then deleted from your system. Microsoft retains this diagnostic data for up to 29 days and handles it as per the [standard privacy practices](https://privacy.microsoft.com/).

Here's the syntax of the `Send-DiagnosticData` cmdlet:

```powershell
Send-DiagnosticData [[-FilterByRole] <string[]>] [[-FromDate] <datetime>] [[-ToDate] <datetime>] [[-CollectSddc] <bool>]  [<CommonParameters>]
```

where: 

- `FromDate` and `ToDate` parameters collect logs for a particular time period. If these parameters aren't specified, logs are collected for the past one hour by default. 

- `FilterByRole` parameter collects and sends diagnostic logs for each role. Currently, you can use the `FilterByRole` parameter to filter log collection by the following roles. This list of roles may change in a future release.

  - DeploymentLogs
  - BareMetal
  - ECE
  - ALM
  - MOC_ARB
  - FleetDiagnosticsAgent
  - ObservabilityAgent
  - RemoteSupportAgent
  - OSUpdateLogs
  - AutonomousLogs
  - OEMDiagnostics
  - ObservabilityVolume
  - NC 

- `CollectSddc` parameter is set to `$true` by default, which triggers the `Get-SDDCDiagnosticInfo` cmdlet and includes its logs as part of the log collection.

## Examples and sample outputs

Here are some example commands with sample outputs that show how to use the `Send-DiagnosticData` cmdlet with different parameters.

### Send diagnostics data with date filtering

In this example, you send diagnostics data with date filtering for log files for the past two hours:

   ```powershell
   Send-DiagnosticData -FromDate (Get-Date).AddHours(-2) -ToDate (Get-Date)
   ```

   Here's a sample output of this command:

   ```output
   PS C:\CloudDeployment\logs> Send-DiagnosticData -FromDate (Get-Date).AddHours(-2) -ToDate (Get-Date)
   Successfully submitted on-demand. Operation tracking Id: ec0d1a53-f75b-4df5-afb8-cfbf6d4c8118
   Current log collection status: Running
   Waiting for log collection to complete...
   ==== CUT ==================== CUT =======
   Log collection ended with status: Succeeded
   PS C:\CloudDeployment\logs>
   ```

### Send diagnostic data with role filtering

In this example, you send diagnostic data with role filtering for BareMetal and ECE:

   ```powershell
   Send-DiagnosticData -FilterByRole BareMetal, ECE
   ```

   Here's a sample output of this command:

   ```output
   PS C:\Users\docsuser> Send-DiagnosticData -FilterByRole BareMetal, ECE
   FromDate parameter not specified. Setting to default value 09/27/2022 17:13:38
   ToDate parameter not specified. Setting to default value 09/27/2022 18:13:38
   Successfully submitted on-demand. Operation tracking Id: ea5fcb7a-4e54-4de2-b519-88439e0a8149
   Current log collection status: Running
   Waiting for log collection to complete...
   ==== CUT ==================== CUT =======
   Log collection ended with status: Succeeded
   PS C:\Users\docsuser>
   ```

### Get a history of log collection

To get a history of log collections for the last 90 days, enter:

   ```powershell
   Get-LogCollectionHistory  
   ```

   Here's a sample output of the `Get-LogCollectionHistory` cmdlet:

   ```output
   PS C:\CloudDeployment\logs> Get-LogCollectionHistory
   Name                           Value
   ----                           -----
   TimeCollected                  9/29/2022 5:08:14 PM +00:00
   Status                         Succeeded
   CollectionFromDate             9/29/2022 4:07:57 PM +00:00
   CollectionToDate               9/29/2022 5:07:57 PM +00:00
   CorrelationId                  fdcd94c8-1bd2-4ec6-8612-c92d5abd9a84
   Type                           OnDemand
   LogUploadSizeMb                1598
   UploadNumberOfFiles            1924
   Directory
   Location
   Error
   ----------                     ---------------------------------------------------------
   TimeCollected                  9/27/2022 11:57:25 PM +00:00
   Status                         Succeeded
   CollectionFromDate             9/27/2022 9:57:16 PM +00:00
   CollectionToDate               9/27/2022 11:57:16 PM +00:00
   CorrelationId                  f3d8dcc6-901e-4c72-a3cc-210055e6f198
   Type                           OnDemand
   LogUploadSizeMb                1069
   UploadNumberOfFiles            1941
   Directory
   Location
   Error
   PS C:\CloudDeployment\logs>
   ```

## Additional considerations on diagnostics logs

- The policy mode of Windows Defender Application Control (WDAC) must be set to audit to run the `Send-DiagnosticData` cmdlet successfully. If it's set to enforced, switch the policy mode to audit before running the `Send-DiagnosticData` cmdlet. See [Known issue with log collection](#known-issue-with-log-collection).

- The `Send-DiagnosticData`cmdlet takes some time to complete based on which roles the logs are collecting, time duration specified, and the number of nodes in your Azure Stack HCI environment.

- When you run the `Send-DiagnosticData` cmdlet, the logs are temporarily copied locally. This copy is parsed and sent to Microsoft and then deleted from your system. Microsoft retains this diagnostic data for up to 29 days and handles it as per the [standard privacy practices](https://privacy.microsoft.com/).

- When you run the `Send-DiagnosticData` cmdlet, the logs are temporarily copied locally. This copy is parsed, sent to Microsoft, and then deleted from your system. Microsoft retains this diagnostic data for up to 29 days and handles it as per the [standard privacy practices](https://privacy.microsoft.com/).

## Known issue with log collection

This release comes with WDAC enabled and enforced by default, which limits the applications and the code that you can run on the core platform. As a result, when you execute the `Send-DiagnosticData` cmdlet, the Windows Event logs aren't collected by default.

### Workaround

As a workaround, switch the default policy mode of WDAC from enforced to audit before running the `Send-DiagnosticData` cmdlet. After collecting logs, switch the WDAC policy mode back to enforced. For instructions on how to switch policy modes, see [Switch between WDAC policy modes](#switch-between-wdac-policy-modes).

### Switch between WDAC policy modes

[!INCLUDE [Switch WDAC policy mode](../../includes/hci-switch-wdac-policy-mode.md)]

After switching the policy mode to audit, run these cmdlets:

1. Run `Send-DiagnosticData` to collect logs.

   ```powershell
   Send-DiagnosticData -Verbose
   ```

1. After log collection is complete, run the following cmdlet to switch WDAC policy mode back to the default enforced mode.

    ```powershell
    Switch-AsWdacPolicy -Mode Enforced
    ```

## Next steps

- [Contact Microsoft Support](get-support.md)
- [Review known issues in Azure Stack HCI, version 22H2 (preview)](../../hci/known-issues-22h2.md)