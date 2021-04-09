---
title: Azure AD Connect PowerShell-cmdletar för Cloud Provisioning-agenten gMSA
description: Lär dig hur du använder Azure AD Connect PowerShell-cmdlets för gMSA Cloud Provisioning agent.
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
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653804"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect PowerShell-cmdletar för Cloud Provisioning-agenten gMSA

Syftet med det här dokumentet är att beskriva Azure AD Connect PowerShell-cmdletar för gMSA-molnbaserad etablerings agent. Med dessa cmdletar kan du ha mer detaljerad information om de behörigheter som tillämpas på tjänst kontot (gMSA). Som standard tillämpar Azure AD Connect Cloud Sync alla behörigheter som liknar Azure AD Connect på standard-gMSA eller en anpassad gMSA. 

Det här dokumentet kommer att avse följande cmdletar:  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Så här använder du cmdletarna:  

Följande förutsättningar måste vara uppfyllda för att dessa cmdletar ska kunna användas.

1. Installera etablerings agenten. 
2. Importera etablerings agentens PS-modul till en PowerShell-session. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Ta bort befintliga behörigheter.  Om du vill ta bort alla befintliga behörigheter för tjänst kontot, förutom själv användning: `Set-AADCloudSyncRestrictedPermission` .  

    Den här cmdleten kräver en parameter `Credential` som kan skickas, eller så kommer den att uppmanas att ange om den anropas utan den.

    Så här skapar du en variabel användning  

   `$credential = Get-Credential` 

   Detta kommer att uppmana användaren att ange användar namn och lösen ord. Autentiseringsuppgifterna måste vara minst en domän administratör (av domänen där agenten är installerad), och kan även vara företags administratör. 

4.  Sedan kan du anropa cmdlet: en för att ta bort extra behörigheter: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Eller så kan du bara anropa 

   `Set-AADCloudSyncRestrictedPermissions` vilket kommer att fråga efter autentiseringsuppgifter. 

 6.  Lägg till en speciell behörighets typ.  De tillagda behörigheterna är samma som Azure AD Connect.  Se [använda set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) nedan för exempel på hur du anger behörigheter.

## <a name="using-set-aadcloudsyncpermissions"></a>Använda Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` stöder följande behörighets typer som är identiska med de behörigheter som används av Azure AD Connect. Följande behörighets typer stöds: 

|Behörighets typ|Beskrivning|
|-----|-----|
|BasicRead| Se [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) -behörigheter för Azure AD Connect|
|PasswordHashSync|Se [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) -behörigheter för Azure AD Connect|
|PasswordWriteBack|Se [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) -behörigheter för Azure AD Connect|
|HybridExchangePermissions|Se [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) -behörigheter för Azure AD Connect| 
|ExchangeMailPublicFolderPermissions| Se [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) -behörigheter för Azure AD Connect| 
|CloudHR| Använder fullständig behörighet för underordnade användar objekt och skapa/ta bort användar objekt för det här objektet och alla underordnade objekt| 
|Alla|lägger till alla ovanstående behörigheter.| 



Du kan använda AADCloudSyncPermissions på ett av två sätt:
- [Bevilja en viss behörighet till alla konfigurerade domäner](#grant-a-certain-permission-to-all-configured-domains) 
- [Bevilja en viss behörighet till en viss domän](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Bevilja en viss behörighet till alla konfigurerade domäner 
Att bevilja vissa behörigheter till alla konfigurerade domäner kräver att ett företags administratörs konto används.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Bevilja en viss behörighet till en viss domän 
Att bevilja vissa behörigheter till en viss domän kräver att minst ett domän administratörs konto för domänen som du försöker lägga till används.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Obs: för 1. Autentiseringsuppgifterna måste vara minst företags administratör. 

För 2. Autentiseringsuppgifterna kan vara antingen domän administratör eller företags administratör. 

  

