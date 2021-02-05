---
title: AADCloudSyncTools PowerShell-modul för Azure AD Connect Cloud Sync
description: Den här artikeln beskriver hur du installerar Azure AD Connect Cloud Provisioning-agenten.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593188"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>AADCloudSyncTools PowerShell-modul för Azure AD Connect Cloud Sync

Modulen AADCloudSyncTools innehåller en uppsättning användbara verktyg som du kan använda för att hantera dina Azure AD Connect distributioner av moln synkronisering.

## <a name="pre-requisites"></a>Förutsättningar
Följande krav måste vara uppfyllda:

- Alla krav för den här modulen kan installeras automatiskt med hjälp av `Install-AADCloudSyncToolsPrerequisites`
- I den här modulen används MSAL-autentisering, så den kräver MSAL.PS-modulen installerad. Verifiera genom att köra i ett PowerShell-fönster `Get-module MSAL.PS -ListAvailable` . Om modulen har installerats som den ska får du ett svar. Du kan använda `Install-AADCloudSyncToolsPrerequisites` för att installera den senaste versionen av MSAL.PS
- Även om AzureAD PowerShell-modulen inte är ett krav för någon funktion i den här modulen, är det bra att ha det, så att den också installeras automatiskt med hjälp av `Install-AADCloudSyncToolsPrerequisites` .
- För att installera moduler från PowerShell manuellt krävs TLS 1,2-tvång. För att säkerställa att du kan installera moduler, ställer du in följande i PowerShell-sessionen innan du använder
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Installera AADCloudSyncTools PowerShell-modulen
Använd följande steg för att installera och använda AADCloudSyncTools-modulen:

1. Öppna Windows PowerShell med administratörs behörighet
2. Skriv eller kopiera och klistra in följande: `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Tryck på RETUR.
4. Verifiera att modulen har importer ATS genom att ange eller kopiera och klistra in följande: `Get-module AADCloudSyncTools`
5. Nu bör du se information om modulen.
6. För att installera AADCloudSyncTools-modulen måste följande krav köras: `Install-AADCloudSyncToolsPrerequisites`
7. Vid den första körningen installeras modulen PoweShellGet om den inte är tillgänglig. Om du vill läsa in den nya PowershellGet-modulen stänger du PowerShell-fönstret och öppnar en ny PowerShell-session med administratörs behörighet. 
8. Importera modulen igen med steg 3.
9. Kör `Install-AADCloudSyncToolsPrerequisites` för att installera MSAL-och AzureAD-modulerna
11. Alla pre-reqs ![ bör installeras](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools-cmdletar
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Använder modulen MSAL.PS för att begära en token för Azure AD-administratören att komma åt Microsoft Graph 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exporterar och paketerar alla fel söknings data i en komprimerad fil, enligt följande:
 1. Startar en utförlig spårning med start-AADCloudSyncToolsVerboseLogs.  Spårningsloggarna finns i mappen C:\ProgramData\Microsoft\Azure AD Connect Agent\Trace.
 2. Samlar in en spårnings logg i 3 minuter.
   Du kan ange en annan tid med-TracingDurationMins eller hoppa över utförlig spårning med-SkipVerboseTrace
 3. Stoppar utförlig spårning med Stop-AADCloudSyncToolsVerboseLogs
 4. Samlar in Loggboken loggar under de senaste 24 timmarna
 5. Komprimerar alla agent loggar, utförliga loggar och logg boken loggar till en komprimerad ZIP-fil under användarens dokument-mapp. 
 </br>Du kan ange en annan mapp för utdata med-OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Visar information om Azure AD-klient och interna variabler

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Använder Graph för att hämta AD2AAD-tjänstens huvud namn och returnerar information om synkroniseringsjobb.
Kan också anropas med hjälp av det speciella Sync-jobbets ID som en parameter.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Använder Graph för att hämta AD2AAD-tjänstens huvud namn och returnerar synkroniseringsschemat.
Kan också anropas med hjälp av det speciella Sync-jobbets ID som en parameter.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Använder Graph för att hämta AD2AAD för tjänstens huvud namn och returnerar synkroniseringsschemat för jobb.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Använder Graph för att hämta synkroniseringsschemat för det angivna synkroniseringsjobb-ID: t och utvärderar alla filter grupps omfång.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Använder Graph för att hämta AD2AAD-tjänstens huvud namn och returnerar inställningarna för synkronisering.
Kan också anropas med hjälp av det speciella Sync-jobbets ID som en parameter.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Använder Graph för att hämta AD2AAD-tjänstens huvud namn och returnerar synkroniseringsstatus för jobbet.
Kan också anropas med hjälp av det speciella Sync-jobbets ID som en parameter.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Använder Graph för att hämta tjänstens huvud namn för AD2AAD och/eller SyncFabric.
Utan parametrar returnerar endast AD2AAD-tjänstens huvud namn.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Söker efter förekomst av PowerShellGet v-2.2.4.1 eller senare och Azure AD-och MSAL.PS-moduler och installerar dessa om de saknas.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Anropar en webbegäran för URI: n, metoden och texten som anges som parametrar

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Använder Azure AD PowerShell för att ta bort det aktuella kontot (om det finns) och återställer synkroniseringen av synkroniseringsschemat med ett nytt synkroniseringsschema i Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Startar om en fullständig synkronisering.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Fortsätter synkronisering från föregående vattenstämpel.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Ändrar AADConnectProvisioningAgent.exe.config för att aktivera utförlig spårning och startar om AADConnectProvisioningAgent-tjänsten. du kan använda-SkipServiceRestart för att förhindra att tjänsten startas om, men alla konfigurations ändringar börjar gälla.  Spårningsloggarna finns i mappen C:\ProgramData\Microsoft\Azure AD Connect Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Ändrar AADConnectProvisioningAgent.exe.config för att inaktivera utförlig spårning och startar om AADConnectProvisioningAgent-tjänsten. Du kan använda-SkipServiceRestart för att förhindra att tjänsten startas om, men alla konfigurations ändringar börjar gälla.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Pausar synkroniseringen.

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

