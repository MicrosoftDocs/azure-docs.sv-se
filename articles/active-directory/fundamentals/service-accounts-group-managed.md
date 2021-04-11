---
title: Skydda grupphanterade tjänst konton | Azure Active Directory
description: En guide för att skydda grupphanterade tjänst konton dator konton.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6f91adf499c62fef56a30c5f2945ba7a90a0b29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640065"
---
# <a name="securing-group-managed-service-accounts"></a>Skydda grupphanterade tjänst konton

Grupphanterade tjänst konton (gMSAs) är hanterade domän konton som används för att skydda tjänster. gMSAs kan köras på en enskild server eller i en Server grupp, till exempel system bakom en nätverks Load Balancer (NLB) eller en Internet Information Services-server (IIS). När du har konfigurerat tjänsterna så att de använder ett gMSA-huvudobjekt hanteras lösen ords hantering för det kontot av Windows.

## <a name="benefits-of-using-gmsas"></a>Fördelar med att använda gMSAs

gMSAs erbjuder en enda identitets lösning med större säkerhet och minskar den administrativa omkostnaderna genom att:

* **Ange starka lösen ord**. gMSAs använder 240 byte slumpmässigt genererade komplexa lösen ord. Komplexiteten och längden på gMSA lösen ord minimerar sannolikheten för att en tjänst ska bli komprometterad genom Brute Force eller ordlisteattacker.

* **Lösen ords återkallning regelbundet**. gMSAs Shift Password Management till Windows, som ändrar lösen ordet var 30: e dag. Tjänst-och domän administratörer behöver inte längre schemalägga lösen ords ändringar eller hantera drift avbrott för att skydda tjänst konton. 

* **Stöd för distribution till Server grupper**. Möjligheten att distribuera gMSAs till flera servrar gör det möjligt att använda belastnings Utjämnings lösningar där flera värdar kör samma tjänst. 

* **Stöd för förenklad hantering av Server huvud namn (SPN)**. Du kan ställa in SPN med PowerShell vid tidpunkten för skapandet av kontot. Dessutom kan tjänster som stöder automatiska SPN-registreringar göra det mot gMSA, förutsatt att gMSA-behörigheterna har angetts korrekt. 

## <a name="when-to-use-gmsas"></a>När du ska använda gMSAs

Använd gMSAs som önskad kontotyp för lokala tjänster om inte en tjänst, till exempel redundanskluster, inte stöder den.

> [!IMPORTANT]
> Du måste testa tjänsten med gMSAs innan du distribuerar till produktion. Det gör du genom att konfigurera en test miljö och se till att programmet kan använda gMSA och få åtkomst till de resurser som krävs för åtkomst. Mer information finns i [stöd för grupphanterade tjänst konton](/system-center/scom/support-group-managed-service-accounts).


Om en tjänst inte stöder användningen av gMSAs är ditt nästa bästa alternativ att använda ett fristående hanterat tjänst konto (sMSA). sMSAs tillhandahåller samma funktioner som en gMSA, men är endast avsedda för distribution på en enda server.

Om du inte kan använda en gMSA eller sMSA stöds av tjänsten måste tjänsten konfigureras att köras som ett standard användar konto. Tjänst-och domän administratörer krävs för att se till att starka lösen ords hanterings processer ser till att kontot skyddas.

## <a name="assess-the-security-posture-of-gmsas"></a>Utvärdera säkerhets position för gMSAs

gMSAs är mycket säkrare än standard användar konton, vilket kräver en pågående lösen ords hantering. Det är dock viktigt att överväga gMSAs-omfattningen av åtkomst när du tittar på den övergripande säkerhets position.

I följande tabell visas potentiella säkerhets problem och begränsningar för att använda gMSAs.

| Säkerhetsproblem| Åtgärder |
| - | - |
| gMSA är medlem i privilegierade grupper. | Granska dina grupp medlemskap. Om du vill göra det kan du skapa ett PowerShell-skript för att räkna upp alla grupp medlemskap och sedan filtrera en resulterande CSV-fil med namnen på dina gMSA-filer. <br>Ta bort gMSA från privilegierade grupper.<br> Bevilja gMSA enbart de rättigheter och behörigheter som krävs för att köra tjänsten (kontakta tjänst leverantören). 
| gMSA har Läs-/Skriv behörighet till känsliga resurser. | Granska åtkomst till känsliga resurser. Arkivera gransknings loggar till en SIEM, till exempel Azure Log Analytics eller Azure Sentinel, för analys. Ta bort onödiga resurs behörigheter om en oönskad åtkomst nivå identifieras. |


## <a name="find-gmsas"></a>Hitta gMSAs

Din organisation kanske redan har skapat gMSAs. Kör följande PowerShell-cmdlet för att hämta dessa konton:

GMSAs måste vara i organisationsenheten för hanterade tjänst konton (OU) för att fungera effektivt.

  
![Skärm bild av ORGANISATIONSENHET för hanterade tjänst konton.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Se följande kommandon för att hitta en tjänst MSA: er som kanske inte finns där.

**Så här hittar du alla tjänst konton, inklusive gMSAs och sMSAs:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>Hantera gMSAs

Du kan använda följande Active Directory PowerShell-cmdletar för att hantera gMSAs:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Från och med Windows Server 2012 fungerar *-ADServiceAccount-cmdlets med gMSAs som standard. Mer information om användningen av ovanstående cmdlets finns i [**komma igång med grupphanterade tjänst konton**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Flytta till en gMSA
gMSAs är den säkraste typen av tjänst konto för lokala behov. Om du kan gå över till en, bör du. Överväg också att flytta dina tjänster till Azure och dina tjänst konton till Azure Active Directory.

1.  Se till att [rot nyckeln för KDs har distribuerats i skogen](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Åtgärden behöver bara genomföras en gång.

2. [Skapa en ny gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Installera den nya gMSA på varje värd som kör tjänsten.
   > [!NOTE] 
   > För ytterligare information om hur du skapar och installerar gMSA på en värd, innan du konfigurerar tjänsten att använda gMSA, se [komma igång med grupphanterade tjänst konton](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. Ändra din tjänst identitet till gMSA och ange ett tomt lösen ord.

5. Kontrol lera att tjänsten fungerar under den nya gMSA-identiteten.

6. Ta bort den gamla tjänst konto identiteten.

 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skyddar tjänst konton

* [Introduktion till lokala tjänst konton](service-accounts-on-premises.md)

* [Skydda grupphanterade tjänst konton](service-accounts-group-managed.md)

* [Skyddade fristående hanterade tjänst konton](service-accounts-standalone-managed.md)

* [Skydda dator konton](service-accounts-computer.md)

* [Säkra användar konton](service-accounts-user-on-premises.md)

* [Styr lokala tjänst konton](service-accounts-govern-on-premises.md)