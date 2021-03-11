---
title: Skydda fristående hanterade tjänst konton | Azure Active Directory
description: En guide för att skydda fristående hanterade tjänst konton.
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
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548860"
---
# <a name="securing-standalone-managed-service-accounts"></a>Skydda fristående hanterade tjänst konton

Fristående hanterade tjänst konton (sMSAs) är hanterade domän konton som används för att skydda en eller flera tjänster som körs på en server. De kan inte återanvändas på flera servrar. sMSAs tillhandahåller automatisk lösen ords hantering, förenklad hantering av tjänst huvud namn (SPN) och möjligheten att delegera hantering till andra administratörer. 

I Active Directory är sMSAs knutna till en speciell server som kör en tjänst. Du hittar dessa konton som anges i snapin-modulen Active Directory användare och datorer i Microsoft Management Console.

![En skärm bild av snapin-modulen Active Directory användare och datorer som visar ORGANISATIONSENHETen för hanterade tjänst konton.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Hanterade tjänst konton introducerades med Windows Server 2008R2 Active Directory schema och kräver en lägsta operativ system nivå för Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>Fördelar med att använda sMSAs

sMSAs erbjuder större säkerhet än användar konton som används som tjänst konton, samtidigt som administrativa kostnader minskas genom att:

* Ange starka lösen ord. sMSAs använder 240 byte slumpmässigt genererade komplexa lösen ord. Komplexiteten och längden på sMSA lösen ord minimerar sannolikheten för att en tjänst ska bli komprometterad genom Brute Force eller ordlisteattacker.

* Lösen ords återkallning regelbundet. Windows ändrar automatiskt sMSA-lösenordet var 30: e dag. Tjänst-och domän administratörer behöver inte schemalägga lösen ords ändringar eller hantera de associerade avbrotts tiderna.

* Förenkla SPN-hanteringen. Tjänstens huvud namn uppdateras automatiskt om domän funktions nivån (DFL) är Windows Server 2008 R2. Till exempel uppdateras tjänstens huvud namn automatiskt i följande scenarier:

   * Namnet på värd datorn har bytt namn. 

   * DNS-namnet på värddatorn ändras.

   * När du lägger till eller tar bort ytterligare en Sam-AccountName-eller DNS-hostname-parameter med [PowerShell](/powershell/module/addsadministration/set-adserviceaccount)

## <a name="when-to-use-smsas"></a>När du ska använda sMSAs

sMSAs kan förenkla hanterings-och säkerhets uppgifter. Använd sMSAs när du har en eller flera tjänster som har distribuerats till en enskild server och du inte kan använda en gMSA. 

> [!NOTE] 
> Även om du kan använda sMSAs för mer än en tjänst, rekommenderar vi att varje tjänst har sin egen identitet för gransknings syfte. 

Om skaparen av program varan inte kan meddela dig om den kan använda en MSA måste du testa programmet. Det gör du genom att skapa en test miljö och se till att den har åtkomst till alla nödvändiga resurser. Se [skapa och installera en SMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) för steg-för-steg-anvisningar.

### <a name="assess-security-posture-of-smsas"></a>Utvärdera säkerhets position för sMSAs

sMSAs är mycket säkrare än standard användar konton, vilket kräver en pågående lösen ords hantering. Det är dock viktigt att överväga sMSAs "räckvidd för åtkomst som en del av deras övergripande säkerhets position.

I följande tabell visas hur du kan minska potentiella säkerhets problem som sMSAs.

| Säkerhetsproblem| Åtgärder |
| - | - |
| sMSA är medlem i privilegierade grupper|Ta bort sMSA från utökade privilegierade grupper (till exempel domän administratörer). <br> Använd den minst privilegierade modellen och bevilja sMSA enbart de rättigheter och behörigheter som krävs för att köra dess tjänster. <br> Om du är osäker på vilka behörigheter som krävs kan du läsa tjänstens skapare. |
| sMSA har Läs-/Skriv behörighet till känsliga resurser.|Granska åtkomst till känsliga resurser. Arkivera gransknings loggar till en SIEM (Azure Log Analytics eller Azure Sentinel) för analys. <br> Åtgärda resurs behörigheter om en oönskad åtkomst nivå upptäcks. |
| Som standard är frekvensen för förnyelse av sMSA-lösenord 30 dagar| Grup principer kan användas för att justera varaktigheten beroende på företagets säkerhets krav. <br> * Du kan ange lösen ordets giltighets tid med följande sökväg. <br>Datorns konfiguration \ säkerhets inställningar \ säkerhets inställningar Options\Domain medlem: högsta ålder för dator konto lösen ord |



### <a name="challenges-with-smsas"></a>Utmaningar med sMSAs

De utmaningar som är kopplade till sMSAs är följande:

| Utmaningar| Åtgärder |
| - | - |
| De kan användas på en enskild server.| Använd gMSAs om du behöver använda kontot för flera servrar. |
| De kan inte användas i flera domäner.| Använd gMSAs om du behöver använda kontot i flera domäner. |
| Alla program har inte stöd för sMSAs.| Använd gMSAs om möjligt. Om du inte använder ett standard användar konto eller ett dator konto som det rekommenderas av programmets skapare. |


## <a name="find-smsas"></a>Hitta sMSAs

Kör DSA. msc på valfri domänkontrollant och expandera behållaren för hanterade tjänst konton för att visa alla sMSAs. 

Följande PowerShell-kommando returnerar alla sMSAs och gMSAs i Active Directory-domänen. 

`Get-ADServiceAccount -Filter *`

Följande kommando returnerar bara sMSAs i Active Directory-domänen.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Hantera sMSAs

Du kan använda följande Active Directory PowerShell-cmdletar för att hantera sMSAs:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Flytta till sMSAs

Om en program tjänst stöder sMSA men inte gMSAs, och för närvarande använder ett användar konto eller ett dator konto för säkerhets kontexten, kan du [skapa och installera en SMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) på servern. 

Vi rekommenderar att du flyttar resurser till Azure och använder Azure Managed Identities eller tjänstens huvud namn.

 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skyddar tjänst konton

* [Introduktion till lokala tjänst konton](service-accounts-on-premises.md)

* [Skydda grupphanterade tjänst konton](service-accounts-group-managed.md)

* [Skyddade fristående hanterade tjänst konton](service-accounts-standalone-managed.md)

* [Skydda dator konton](service-accounts-computer.md)

* [Säkra användar konton](service-accounts-user-on-premises.md)

* [Styr lokala tjänst konton](service-accounts-govern-on-premises.md)

