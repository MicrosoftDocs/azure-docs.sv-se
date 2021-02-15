---
title: Windows Virtual Desktop PowerShell – Azure
description: Så här felsöker du problem med PowerShell när du konfigurerar en Windows Virtual Desktop-miljö.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367630"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

Använd den här artikeln för att lösa fel och problem när du använder PowerShell med Windows Virtual Desktop. Mer information om Fjärrskrivbordstjänster PowerShell finns i [Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>PowerShell-kommandon som används vid installation av virtuella Windows-datorer

Det här avsnittet innehåller PowerShell-kommandon som vanligt vis används när du konfigurerar Windows Virtual Desktop och ger möjlighet att lösa problem som kan uppstå när du använder dem.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Fel: New-AzRoleAssignment: den tillhandahållna informationen mappas inte till ett AD-objekt-ID

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Orsak:** Användaren som anges av parametern *-SignInName* går inte att hitta i den Azure Active Directory som är kopplad till den virtuella Windows-miljön.

**KORRIGERA:** Kontrol lera följande saker.

- Användaren ska synkroniseras med Azure Active Directory.
- Användaren bör inte vara knuten till B2C (Business-to-Consumer) eller B2B-handel (Business-to-Business).
- Windows Virtual Desktop-miljön bör vara knuten till rätt Azure Active Directory.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Fel: New-AzRoleAssignment: klienten med objekt-ID har inte behörighet att utföra åtgärden över omfattning (kod: AuthorizationFailed)

**Orsak 1:** Kontot som används saknar ägar behörighet för prenumerationen.

**Korrigering 1:** En användare med ägar behörighet måste köra roll tilldelningen. Alternativt måste användaren tilldelas rollen administratör för användar åtkomst för att tilldela en användare till en program grupp.

**Orsak 2:** Kontot som används har ägar behörigheter, men är inte en del av miljöns Azure Active Directory eller har inte behörighet att fråga den Azure Active Directory där användaren finns.

**Korrigera 2:** En användare med Active Directory behörigheter måste köra roll tilldelningen.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Fel: New-AzWvdHostPool--platsen är inte tillgänglig för resurs typen

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Orsak: Windows Virtual Desktop stöder val av plats för värdar, program grupper och arbets ytor som lagrar metadata för tjänsten på vissa platser. Dina alternativ är begränsade till där den här funktionen är tillgänglig. Det här felet innebär att funktionen inte är tillgänglig på den plats som du har valt.

KORRIGERA: en lista över regioner som stöds kommer att publiceras i fel meddelandet. Använd en av de regioner som stöds i stället.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Fel: New-AzWvdApplicationGroup måste finnas på samma plats som poolen för värdar

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Orsak:** Det finns ett matchnings fel för platsen. Alla värdar, program grupper och arbets ytor har en plats där du kan lagra tjänstens metadata. Alla objekt som du skapar som är associerade med varandra måste finnas på samma plats. Om en adresspool till exempel finns i `eastus` måste du också skapa program grupperna i `eastus` . Om du skapar en arbets yta för att registrera dessa program grupper i, måste även den arbets ytan finnas med `eastus` .

**KORRIGERA:** Hämta platsen som värd poolen skapades i och tilldela sedan den program grupp som du skapar till samma plats.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du konfigurerar Windows-miljön för virtuella skriv bord och värdar finns i [skapa miljö-och värddatorer](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md)
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/templates/deployment-history.md).
