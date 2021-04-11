---
title: Vanliga frågor och svar om Windows Virtual Desktop – Azure
description: Vanliga frågor och rekommendationer för virtuella Windows-datorer.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1f5e4cb0d2db30c6b07370be137506f3fe26837f
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505305"
---
# <a name="windows-virtual-desktop-faq"></a>Vanliga frågor och svar om Windows Virtual Desktop

I den här artikeln besvaras vanliga frågor och rekommendationer för virtuella Windows-datorer.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Vilka är de minsta administratörs behörigheter jag behöver för att hantera objekt?

Om du vill skapa värdar för pooler och andra objekt måste du tilldelas deltagar rollen för den prenumeration eller resurs grupp som du arbetar med.

Du måste vara tilldelad rollen administratör för användar åtkomst i en app-grupp för att publicera program grupper till användare eller användar grupper.

För att begränsa en administratör till att endast hantera användarsessioner, till exempel skicka meddelanden till användare, logga ut användare och så vidare, kan du skapa anpassade roller. Exempel:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Stöder Windows Virtual Desktop delade Azure Active Directory modeller?

När en användare tilldelas en app-grupp gör tjänsten en enkel roll tilldelning i Azure. Därför måste användarens Azure Active Directory (AD) och app-gruppens Azure AD finnas på samma plats. Alla tjänst objekt, till exempel värdar, app-grupper och arbets ytor, måste också finnas i samma Azure AD som användaren.

Du kan skapa virtuella datorer i en annan Azure AD så länge som du synkroniserar Active Directory med användarens Azure AD i samma virtuella nätverk (VNET).

## <a name="what-are-location-restrictions"></a>Vad är plats begränsningar?

Alla tjänst resurser har en plats som är kopplad till dem. En värdstat bestämmer vilken geografi som tjänstens metadata för-poolen lagras i. Det kan inte finnas en app-grupp utan en adresspool. Om du lägger till appar i en RemoteApp-app-grupp måste du också ha en värd för sessionen för att fastställa Start-menyns appar. För alla app Group-åtgärder behöver du också en relaterad data åtkomst på den här värddatorn. För att se till att data inte överförs mellan flera platser bör app-gruppens plats vara samma som värd poolens.

Arbets ytor måste också finnas på samma plats som deras app-grupper. När arbets ytan uppdateras uppdateras den relaterade app-gruppen tillsammans med den. Precis som med app-grupper kräver tjänsten att alla arbets ytor är associerade med app-grupper som skapats på samma plats.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Hur utökar du ett objekts egenskaper i PowerShell?

När du kör en PowerShell-cmdlet visas bara resursens namn och plats.

Exempel:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Om du vill se alla egenskaper för en resurs lägger du till antingen `format-list` eller `fl` i slutet av cmdleten.

Exempel:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Om du vill se vissa egenskaper lägger du till de angivna egenskaps namnen efter `format-list` eller `fl` .

Exempel:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Stöder Windows Virtual Desktop gäst användare?

Virtuella Windows-datorer har inte stöd för Azure AD-gäst användar konton. Anta till exempel att en grupp gäst användare har Microsoft 365 E3 per användare, Windows E3 per användare eller WIN VDA-licenser i sitt eget företag, men är gäst användare i ett annat företags Azure AD. Det andra företaget skulle hantera gäst användarens användar objekt i både Azure AD och Active Directory som lokala konton.

Du kan inte använda dina egna licenser till förmån för en tredje part. Windows Virtual Desktop stöder inte heller Microsoft-konto (MSA) för närvarande.

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Varför visas inte klientens IP-adress i WVDConnections-tabellen?

Vi har för närvarande inte ett tillförlitligt sätt att samla in webb klientens IP-adresser, så vi inkluderar inte det värdet i tabellen.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Hur hanterar Windows Virtual Desktop säkerhets kopiering?

Det finns flera alternativ i Azure för att hantera säkerhets kopiering. Du kan använda Azure Backup, Site Recovery och ögonblicks bilder.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Stöder Windows Virtual Desktop samarbets program från tredje part?

Det virtuella Windows-skrivbordet är för närvarande optimerat för Teams. Microsoft stöder för närvarande inte samarbets program från tredje part som zoom. Organisationer från tredje part ansvarar för att ge sina kunder rikt linjer för kompatibilitet. Virtuella Windows-datorer stöder inte heller Skype för företag.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Kan jag ändra från pool till personliga värdar?

När du har skapat en värdbaserad pool kan du inte ändra dess typ. Du kan dock flytta alla virtuella datorer som du registrerar till en värd-pool till en annan typ av värddator.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Vad är den största profil storleken som FSLogix kan hantera?

Begränsningar eller kvoter i FSLogix är beroende av den lagrings infrastruktur som används för att lagra filer med VHD (X) för användar profiler.

Följande tabell innehåller ett exempel på hur många IOPS en FSLogix-profil behöver för att stödja varje användare. Kraven kan variera mycket beroende på användare, program och aktivitet på varje profil.

| Resurs | Krav |
|---|---|
| Stabilt läge IOPS | 10 |
| Logga in/logga ut IOPS | 50 |

Exemplet i den här tabellen är en enskild användare, men kan användas för att uppskatta kraven för det totala antalet användare i din miljö. Du behöver till exempel cirka 1 000 IOPS för 100-användare och runt 5 000 IOPS vid inloggning och utloggning.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Finns det en skalnings gräns för lagringspooler som skapats i Azure Portal?

Dessa faktorer kan påverka skalnings gränsen för värdbaserade pooler:

- Azure-mallen är begränsad till 800 objekt. Läs mer i Azure- [prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Varje virtuell dator skapar även sex objekt, vilket innebär att du kan skapa cirka 132 virtuella datorer varje gången du kör mallen.

- Det finns begränsningar för hur många kärnor du kan skapa per region och per prenumeration. Om du till exempel har en Enterprise-avtal-prenumeration kan du skapa 350 kärnor. Du måste dividera 350 antingen med standard antalet kärnor per virtuell dator eller din egen kärn gräns för att avgöra hur många virtuella datorer du kan skapa varje gången du kör mallen. Läs mer på [Virtual Machines gränser – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- Namnet på den virtuella datorns prefix och antalet virtuella datorer är färre än 15 tecken. Mer information finns i [namngivnings regler och begränsningar för Azure-resurser](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Kan jag hantera Windows Virtual Desktop-miljöer med Azure Lighthouse?

Azure Lighthouse stöder inte fullt hantering av Windows-miljöer med virtuella skriv bord. Eftersom Lighthouse för närvarande inte stöder hantering av användar hantering för flera Azure AD-användare måste Lighthouse-kunder fortfarande logga in på den Azure AD som kunderna använder för att hantera användare.

Du kan inte heller använda CSP sandbox-prenumerationer med Windows Virtual Desktop-tjänsten. Mer information finns i [konto för integration sandbox](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Slutligen, om du har aktiverat resurs leverantören från kontot för CSP-ägare, kan inte CSP-kundens kund konton ändra resurs leverantören.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Hur ofta ska jag aktivera mina virtuella datorer för att förhindra registrerings problem?

När du registrerar en virtuell dator till en adresspool i Windows Virtual Desktop-tjänsten uppdaterar agenten regelbundet den virtuella datorns token när den virtuella datorn är aktiv. Certifikatet för registrerings-token är giltigt i 90 dagar. På grund av den här 90-dagars gränsen rekommenderar vi att virtuella datorer är online i 20 minuter var 90 dag, så att datorn kan uppdatera sina tokens och uppdatera agenten och stack-vid-sida-stack-komponenter. Om du aktiverar den virtuella datorn inom den här tids gränsen kommer dess registrerings token att upphöra att gälla eller bli ogiltig. Om du har startat den virtuella datorn efter 90 dagar och har problem med registreringen, följer du anvisningarna i [fel söknings guiden för Windows Virtual Desktop agent](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) för att ta bort den virtuella datorn från poolen, installera om agenten och registrera den på poolen igen.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>Kan jag ange tillgänglighets alternativ när du skapar värdar för pooler?

Ja. Windows-pooler för virtuella skriv bord kan välja antingen tillgänglighets uppsättning eller tillgänglighets zoner när du skapar en virtuell dator. Dessa tillgänglighets alternativ är desamma som de som Azure Compute använder. Om du väljer en zon för den virtuella dator som du skapar i en adresspool, tillämpas inställningen automatiskt på alla virtuella datorer som du skapar i den zonen. Om du föredrar att sprida dina virtuella datorers värdar i flera zoner måste du följa anvisningarna i [lägga till virtuella datorer med Azure Portal](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) för att manuellt välja en ny zon för varje ny virtuell dator som du skapar.

## <a name="which-availability-option-is-best-for-me"></a>Vilket tillgänglighets alternativ passar mig bäst för mig?

Vilket tillgänglighets alternativ som ska användas för dina virtuella datorer beror på din avbildnings plats och dess hanterade disk fält. I följande tabell förklaras förhållandet som varje inställning har med dessa variabler för att hjälpa dig att avgöra vilket alternativ som är bäst för din distribution. 

| Tillgänglighets alternativ | Bild plats | Alternativ knappen Använd hanterad disk (alternativ knapp) |
|---|---|---|
| Inget | Galleri | Inaktiverat med alternativet Ja som standard |
| Inget | Blob Storage | Aktive rad med "nej" som standard |
| Tillgänglighetszon | Galleri (Blob Storage-alternativet inaktive rad) | Inaktiverat med alternativet Ja som standard |
| Tillgänglighets uppsättning med hanterad SKU (hanterad disk) | Galleri | Inaktiverat med alternativet Ja som standard |
| Tillgänglighets uppsättning med hanterad SKU (hanterad disk) | Blob Storage | Aktive rad med "nej" som standard |
| Tillgänglighets uppsättning med hanterad SKU (hanterad disk) | Blob Storage (alternativ för Galleri är inaktiverat) | Inaktiverat med "nej" som standard |
| Tillgänglighets uppsättning (nyligen skapad av användare) | Galleri | Inaktiverat med alternativet Ja som standard |
| Tillgänglighets uppsättning (nyligen skapad av användare) | Blob Storage | Aktive rad med "nej" som standard |
