---
title: Installera Azure AD Connect Cloud Provisioning agent med PowerShell
description: Lär dig hur du installerar Azure AD Connect Cloud Provisioning-agenten med hjälp av PowerShell-cmdletar.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614186"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Installera Azure AD Connect etablerings agenten med hjälp av PowerShell-cmdletar 
I följande dokument visas hur du installerar Azure AD Connect etablerings agenten med hjälp av PowerShell-cmdletar.
 

## <a name="prerequisite"></a>Förutsättning: 


>[!IMPORTANT]
>Följande installations anvisningar förutsätter att alla [krav](how-to-prerequisites.md) är uppfyllda.
>
> Windows Server måste ha TLS 1,2 aktiverat innan du installerar Azure AD Connect etablerings agenten med hjälp av PowerShell-cmdletar. Om du vill aktivera TLS 1,2 kan du använda stegen som finns [här](how-to-prerequisites.md#tls-requirements).

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Installera Azure AD Connect etablerings agenten med hjälp av PowerShell-cmdletar 


 1. Logga in på Azure Portal och gå sedan till **Azure Active Directory**.
 2. På den vänstra menyn väljer du **Azure AD Connect**.
 3. Välj **Hantera etablering (för hands version)**  >  **Granska alla agenter**.
 4. Hämta Azure AD Connect etablerings agenten från Azure Portal till en lokal.  

   ![Hämta lokal agent](media/how-to-install/install-9.png)</br>
 5. I dessa instruktioner har agenten laddats ned till följande mapp: "C:\ProvisioningSetup"-mappen. 
 6. Installera ProvisioningAgent i tyst läge

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Importera etablerings agentens PS-modul 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Anslut till AzureAD med autentiseringsuppgifter för global administratör kan du anpassa det här avsnittet för att hämta lösen ord från ett säkert arkiv. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD-autentiseringsuppgift $globalAdminCreds 

 9. Lägg till gMSA-kontot, ange autentiseringsuppgifter för domän administratören för att skapa standard gMSA-kontot 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Eller Använd cmdleten ovan enligt nedan för att ange ett gMSA-konto som skapats i förväg 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Lägg till domän 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Eller Använd cmdleten ovan enligt nedan för att konfigurera önskade domänkontrollanter 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Upprepa föregående steg för att lägga till fler domäner, ange konto namn och domän namn för respektive domän 
 14. Starta om tjänsten 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Gå till Azure Portal för att skapa konfigurationen för moln synkronisering.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>GMSA PowerShell-cmdletar för etablerings agent
Nu när du har installerat agenten kan du använda mer detaljerade behörigheter för gMSA.  Mer information och stegvisa anvisningar om hur du konfigurerar behörigheter finns i [Azure AD Connect PowerShell-cmdlets för Cloud Provisioning-agenten gMSA](how-to-gmsa-cmdlets.md) .

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Azure AD Connect PowerShell-cmdletar för Cloud Provisioning-agenten gMSA](how-to-gmsa-cmdlets.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)