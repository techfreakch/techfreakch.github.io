---
layout: post
title: "Uninstall old PowerShell Module Versions"
date: 2018-11-12
comments: true
author: michel_zehnder
categories: [powershell]
tags: [powershell]
---

If you're using a Module like AzureRm, and you update it from time to time (Update-Module), you will find that you'll have a couple of versions installed of all the individual modules. Something like this:

<script src="https://gist.github.com/MichelZ/197da5830b6e073183619e302e95145f.js"></script>
<noscript>
<pre>
```powershell
   Directory: C:\Program Files\WindowsPowerShell\Modules


ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Binary     0.9.1.326  ACMESharp                           {Get-ACMECertificate, Get-ACMEChallengeHandlerProfile, Get-ACMEIdentifie...
Script     5.3.0      Azure                               {Get-AzureAutomationCertificate, Get-AzureAutomationConnection, New-Azur...
Script     5.1.2      Azure                               {Get-AzureAutomationCertificate, Get-AzureAutomationConnection, New-Azur...
Script     0.5.4      Azure.AnalysisServices              {Add-AzureAnalysisServicesAccount, Restart-AzureAnalysisServicesInstance...
Script     0.5.3      Azure.AnalysisServices              {Add-AzureAnalysisServicesAccount, Restart-AzureAnalysisServicesInstance...
Script     0.5.2      Azure.AnalysisServices              {Add-AzureAnalysisServicesAccount, Restart-AzureAnalysisServicesInstance...
Script     0.5.1      Azure.AnalysisServices              {Add-AzureAnalysisServicesAccount, Restart-AzureAnalysisServicesInstance...
Manifest   0.5.0      Azure.AnalysisServices              {Add-AzureAnalysisServicesAccount, Restart-AzureAnalysisServicesInstance...
Script     4.6.1      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New-AzureStorageT...
Script     4.4.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New-AzureStorageT...
Script     4.3.2      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New-AzureStorageT...
Script     4.3.1      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New-AzureStorageT...
Script     4.2.1      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New-AzureStorageT...
Script     4.2.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New-AzureStorageT...
Binary     2.0.2.4    AzureAD                             {Add-AzureADApplicationOwner, Get-AzureADApplication, Get-AzureADApplica...
Binary     2.0.1.16   AzureAD                             {Add-AzureADApplicationOwner, Get-AzureADApplication, Get-AzureADApplica...
Binary     2.0.1.3    AzureAD                             {Add-AzureADApplicationOwner, Get-AzureADApplication, Get-AzureADApplica...
Script     6.12.0     AzureRM
Script     0.6.14     AzureRM.AnalysisServices            {Resume-AzureRmAnalysisServicesServer, Suspend-AzureRmAnalysisServicesSe...
Script     0.6.11     AzureRM.AnalysisServices            {Resume-AzureRmAnalysisServicesServer, Suspend-AzureRmAnalysisServicesSe...
Script     0.6.10     AzureRM.AnalysisServices            {Resume-AzureRmAnalysisServicesServer, Suspend-AzureRmAnalysisServicesSe...
Script     0.6.8      AzureRM.AnalysisServices            {Resume-AzureRmAnalysisServicesServer, Suspend-AzureRmAnalysisServicesSe...
Script     0.6.6      AzureRM.AnalysisServices            {Resume-AzureRmAnalysisServicesServer, Suspend-AzureRmAnalysisServicesSe...
Script     0.6.5      AzureRM.AnalysisServices            {Resume-AzureRmAnalysisServicesServer, Suspend-AzureRmAnalysisServicesSe...
Script     6.1.5      AzureRM.ApiManagement               {Add-AzureRmApiManagementRegion, Get-AzureRmApiManagementSsoToken, New-A...
Script     6.1.2      AzureRM.ApiManagement               {Add-AzureRmApiManagementRegion, Get-AzureRmApiManagementSsoToken, New-A...
Script     6.1.1      AzureRM.ApiManagement               {Add-AzureRmApiManagementRegion, Get-AzureRmApiManagementSsoToken, New-A...
Script     6.1.0      AzureRM.ApiManagement               {Add-AzureRmApiManagementRegion, Get-AzureRmApiManagementSsoToken, New-A...
Script     5.1.2      AzureRM.ApiManagement               {Add-AzureRmApiManagementRegion, Get-AzureRmApiManagementSsoToken, New-A...
Script     5.1.1      AzureRM.ApiManagement               {Add-AzureRmApiManagementRegion, Get-AzureRmApiManagementSsoToken, New-A...
```
</pre>
</noscript>

To get rid of the old versions, you can use this easy script:


<script src="https://gist.github.com/MichelZ/e4efbafc6dcc2f8a56583d3053588dc4.js"></script>
<noscript>
<pre>
```powershell
$modules = Get-Module -ListAvailable AzureRm* | Select-Object -ExpandProperty Name -Unique
foreach ($module in $modules)
{
    $Latest = Get-InstalledModule $module; Get-InstalledModule $module -AllVersions | ? {$_.Version -ne $Latest.Version} | Uninstall-Module
}
```
</pre>
</noscript>

It loops through all of the modules, retains the latest version of each module, and uninstalls all of the others.
It's not AzureRm's fault, it's the fault of PowerShellGet, with the "Update-Module" functionality not actually being an "Update", but just a Side-by-Side installation of the new version.
