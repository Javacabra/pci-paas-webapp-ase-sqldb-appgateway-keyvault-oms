# Payment Processing Blueprint for PCI DSS-compliant environments

## Script Details: `0-Setup-AdministrativeAccountAndPermission.ps1`

This PowerShell script is used to verify pre-deployment requirements for the Payment Card Payment processing solution for PCI DSS enablement.
 
# Description 
 This PowerShell script automates the installation and verification of the PowerShell modules, as well as configuring the administrative user of the solution. 
 > NOTE: This script MUST be run as *Local Administrator* with elevated privileges. For more information, see [Why do I need to run as local administrator?](https://social.technet.microsoft.com/Forums/scriptcenter/en-US/41a4ba3d-93fd-485b-be22-c877afff1bd8/how-to-run-a-powershell-script-in-admin-account?forum=ITCG)  

 Running this script is not required, but installation will fail if the following modules have not been properly configured:
- AzureRM
- AzureAD
- MSOnline
- AzureDiagnosticsAndLogAnalytics
- SqlServer
- Enable-AzureRMDiagnostics (Script)

This script will attempt to install the following versions of these PowerShell modules:
- AzureRM - 5.1.1
- AzureAD - 2.0.0.131
- MSOnline - 1.1.166.0
- AzureDiagnosticsAndLogAnalytics - 0.1
- SqlServer - 21.0.17262

## Example 1: Installing required modules

```powershell
.\0-Setup-AdministrativeAccountAndPermission.ps1 -installModules
```
This command will validate or install any missing PowerShell modules which are required for this foundational architecture.

## Example 2: Configuring your global admin

```powershell
.\0-Setup-AdministrativeAccountAndPermission.ps1 
    -azureADDomainName contosowebstore.com
    -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    -configureGlobalAdmin 
 ```

 This command will deploy installed modules, and setup the solution on a **new subscription**. It will create the user `adminXX@contosowebstore.com` with a randomly generated strong password (15 characters minimum, with uppercase and lowercase letters, and at least one number and one special character.) 
 
## Example 3: Install required modules and Configure your global admin.

```powershell
.\0-Setup-AdministrativeAccountAndPermission.ps1 
    -azureADDomainName contosowebstore.com
    -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    -configureGlobalAdmin 
    -installModules
 ``` 
This command will validate or install any missing PowerShell modules which are required for this foundational architecture. It will create the user `adminXX@contosowebstore.com` with a randomly generated strong password (15 characters minimum, with uppercase and lowercase letters, and at least one number and one special character.) 
 
# Required parameters

> -azureADDomainName <String>

Specifies the ID of the Azure Active Directory Domain, as defined by [Get-ADDomain](https://technet.microsoft.com/en-us/library/ee617224.aspx).

> -tenantId <String>

Specifies the ID of a tenant. If you do not specify this parameter, the account is authenticated with the home tenant.

> -subscriptionId <String>

Specifies the ID of a subscription. If you do not specify this parameter, the account is authenticated with the home tenant.

> -configureGlobalAdmin

Attempt to create an administrator user configured as a subscription administrator. An Active Directory Administrator with global privileges is required to run the installation. The local administrator must be in the `contosowebstore.com` domain namespace to run this solution. This step helps create the correct administrator user.

> -installModules

Installs and verifies all required modules. If any of the commands from the script fail, see the following references below for assistance.

## Troubleshooting your tenant administrator

The following debugging and troubleshooting steps can help identify common issues.

To test your username and passwords with [Azure RM](https://docs.microsoft.com/en-us/powershell/azureps-cmdlets-docs/), run the following commands in PowerShell:
```powershell
$cred = Get-Credential  
Login-AzureRmAccount -Credential $cred
```

To test [Azure AD](https://technet.microsoft.com/en-us/library/dn975125.aspx), run the following commands in PowerShell:  
```powershell
$cred = Get-Credential  
Login-AzureAD -Credential $cred
```

Review the following documentation to test [Enable AzureRM Diagnostics](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/1.3/DisplayScript).                   
Review the following documentation to test [Azure Diagnostics and LogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1).                  

To test [SQL Server PowerShell](https://msdn.microsoft.com/en-us/library/hh231683.aspx?f=255&MSPPError=-2147217396#Installing#SQL#Server#PowerShell#Support), run the following commands in PowerShell:
```powershell
 $Credential = Get-Credential   Connect-AzureAD -Credential $Credential   Get-Module -ListAvailable -Name Sqlps;
```
## Troubleshooting your PowerShell Deployment scripts

Please verify that running the 0-Setup-AdministrativeAccountAndPermission.ps1 results in no error messages. This script configures the open PowerShell session for correctly deploying the ARM templates and performing deployment steps throughout running the 1-DeployAndConfigureAzureResources.ps1 script. 

If module import/installation challenges are experienced when running the 0-Setup-AdministrativeAccountAndPermission.ps1 script, navigate to C:\Program Files\WindowsPowerShell\Modules and remove any directories associated to the following items:
- AzureRM
- AzureAD
- MSOnline
- AzureDiagnosticsAndLogAnalytics
- SqlServer

As the setup script will run through module validation and import, the script can be run again with the -installmodules switch for setting up the PowerShell environment for running the deployment. 
