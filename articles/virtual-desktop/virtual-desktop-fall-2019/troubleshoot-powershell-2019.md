---
title: Windows Virtual Desktop (klassisk) PowerShell – Azure
description: Så här felsöker du problem med PowerShell när du konfigurerar en Windows Virtual Desktop (klassisk) klient miljö.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e7db817e2fb355e3a69f7b90a4fc6eaa4adec515
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444232"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>Windows Virtual Desktop (klassisk) PowerShell

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../troubleshoot-powershell.md).

Använd den här artikeln för att lösa fel och problem när du använder PowerShell med Windows Virtual Desktop. Mer information om Fjärrskrivbordstjänster PowerShell finns i [Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>PowerShell-kommandon som används vid installation av virtuella Windows-datorer

Det här avsnittet innehåller PowerShell-kommandon som vanligt vis används när du konfigurerar Windows Virtual Desktop och ger möjlighet att lösa problem som kan uppstå när du använder dem.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Fel: Add-RdsAppGroupUser kommandot--angivet UserPrincipalName har redan tilldelats en RemoteApp-app i den angivna poolen

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Orsak:** Det användar namn som används har redan tilldelats en app-grupp av en annan typ. Användare kan inte tilldelas både till en fjärr skrivbord och en fjärran sluten program grupp under samma sessions värd pool.

**KORRIGERA:** Om användaren behöver både fjärrappar och fjärr skrivbord, kan du skapa olika lagringspooler eller bevilja användar åtkomst till fjärr skrivbordet, vilket innebär att alla program kan användas på den virtuella datorn för sessionen.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Fel: Add-RdsAppGroupUser kommando--det angivna UserPrincipalName finns inte i Azure Active Directory som är kopplat till fjärr skrivbords klienten

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Orsak:** Användaren som anges av-UserPrincipalName går inte att hitta i den Azure Active Directory som är kopplad till den virtuella Windows-klienten för fjärr skrivbord.

**KORRIGERA:** Bekräfta objekten i följande lista.

- Användaren är synkroniserad med Azure Active Directory.
- Användaren är inte knuten till B2C-eller Business-to-Business-handel (B2B).
- Windows-klienten för virtuella skriv bord är kopplad till rätt Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Fel: Get-RdsDiagnosticActivities--användaren har inte behörighet att fråga hanterings tjänsten

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Orsak:** -TenantName-parameter

**KORRIGERA:** Utfärda Get-RdsDiagnosticActivities med-TenantName \<TenantName> .

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Get-RdsDiagnosticActivities – användaren har inte behörighet att fråga hanterings tjänsten

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Orsak:** Använd växeln-Deployment.

**KORRIGERA:** -distributions växeln kan endast användas av distributions administratörer. Dessa administratörer är vanligt vis medlemmar i den virtuella Skriv bords gruppen Fjärrskrivbordstjänster/Windows. Ersätt växeln-Deployment med-TenantName \<TenantName> .

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: New-RdsRoleAssignment – användaren har inte behörighet att fråga hanterings tjänsten

**Orsak 1:** Kontot som används har inte Fjärrskrivbordstjänster ägar behörighet till klienten.

**Korrigering 1:** En användare med Fjärrskrivbordstjänster ägar behörighet måste köra roll tilldelningen.

**Orsak 2:** Kontot som används har Fjärrskrivbordstjänster ägar behörighet men är inte en del av klientens Azure Active Directory eller har inte behörighet att fråga den Azure Active Directory där användaren finns.

**Korrigera 2:** En användare med Active Directory behörigheter måste köra roll tilldelningen.

>[!Note]
>New-RdsRoleAssignment kan inte ge behörighet till en användare som inte finns i Azure Active Directory (AD).

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview-2019.md).
- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues-2019.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration-2019.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection-2019.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](../troubleshoot-client.md)
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup-2019.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](../../azure-resource-manager/templates/deployment-history.md).
