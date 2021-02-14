---
title: Skydda användarbaserade tjänst konton | Azure Active Directory
description: En guide för att skydda lokala användar konton.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417956"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Skydda användarbaserade tjänst konton i Active Directory

Lokala användar konton är den traditionella metoden för att skydda tjänster som körs i Windows. Använd de här kontona som en sista utväg när globala hanterade tjänst konton (gMSAs) och fristående hanterade tjänst konton (sMSAs) inte stöds av tjänsten. Se Översikt över lokala tjänst konton för information om hur du väljer den bästa typen av konto som ska användas. Undersök även om du kan flytta din tjänst till att använda ett Azure-tjänstkonto som en hanterad identitet eller tjänst princip. 

Lokala användar konton kan skapas för att tillhandahålla en säkerhets kontext för tjänster och beviljas behörigheter som krävs för att tjänsterna ska kunna komma åt lokala resurser och nätverks resurser. De kräver manuell lösen ords hantering på samma sätt som andra Active Directory (AD)-användar konton. Tjänst-och domän administratörer krävs för att se till att starka lösen ords hanterings processer ser till att dessa konton är säkra.

När du använder ett användar konto som ett tjänst konto ska du endast använda det för en enda tjänst. Namnge det på ett sätt som gör det tydligt att det är ett tjänst konto och för vilken tjänst. 

## <a name="benefits-and-challenges"></a>Fördelar och utmaningar

Fördelar

Lokala användar konton är den mest flexibla konto typen för användning med tjänster. Användar konton som används som tjänst konton kan styras av alla principer styr normala användar konton. Använd dem bara om du inte kan använda en MSA. Utvärdera även om ett dator konto är ett bättre alternativ. 

Utmaningar med lokala användar konton

Följande utmaningar är kopplade till användningen av lokala användar konton.

| Utmaningar| Åtgärder |
| - | - |
| Lösen ords hantering är en manuell process som kan leda till sämre säkerhet och avbrott i tjänsten.| Se till att lösen ords komplexitet och lösen ords ändring styrs av en robust process som säkerställer regelbundna uppdateringar med starkt lösen ord. <br> Koordinera lösen ords ändringar med en lösen ords uppdatering för tjänsten, vilket leder till avbrott i tjänsten. |
| Det kan vara svårt att identifiera lokala användar konton som fungerar som tjänst konton.| Dokumentera och underhålla register över tjänst konton som har distribuerats i din miljö. <br> Spåra konto namnet och de resurser som de har tilldelats åtkomst till. <br> Överväg att lägga till ett prefix för svc_ till alla användar konton som används som tjänst konton. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Hitta lokala användar konton som används som tjänst konton

Lokala användar konton är precis som andra AD-användarkonto. Det kan därför vara svårt att hitta dessa konton eftersom det inte finns något enskilt attribut för ett användar konto som identifierar det som ett tjänst konto. 

Vi rekommenderar att du skapar en lätt identifierbar namngivnings konvention för alla användar konton som används som ett tjänst konto.

Lägg exempelvis till "tjänst-" som ett prefix och ge tjänsten namnet: "service-HRDataConnector".

Du kan använda några av indikatorerna nedan för att hitta dessa tjänst konton, men det kanske inte hittar alla sådana konton.

* Konton som är betrodda för delegering.

* Konton med SPN-namn.

* Konton vars lösen ord är inställt på upphör aldrig att gälla.

Du kan köra följande PowerShell-kommandon för att hitta de lokala användar konton som skapats för tjänster.

### <a name="find-accounts-trusted-for-delegation"></a>Hitta konton som är betrodda för delegering

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Hitta konton med tjänst princip namn

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Hitta konton med angivna lösen ord som aldrig upphör att gälla

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Du kan också granska åtkomst till känsliga resurser och arkivera gransknings loggar till ett SIEM-system (Security information and Event Management). Genom att använda system som Azure Log Analytics eller Azure Sentinel kan du söka efter och analysera och tjänst konton.

## <a name="assess-security-of-on-premises-user-accounts"></a>Utvärdera säkerheten för lokala användar konton

Utvärdera säkerheten för dina lokala användar konton som används som tjänst konton med följande kriterier:

* Vad är principen för lösen ords hantering?

* Är kontot medlem i alla privilegierade grupper?

* Har kontot Läs-/Skriv behörighet till viktiga resurser?

### <a name="mitigate-potential-security-issues"></a>Minska potentiella säkerhets problem

I följande tabell visas potentiella säkerhets problem och motsvarande begränsningar för lokala användar konton.

| Säkerhetsproblem| Åtgärder |
| - | - |
| Lösenordshantering|* Se till att lösen ords komplexitet och lösen ords ändring styrs av en robust process som garanterar regelbundna uppdateringar med starka lösen ords krav. <br> * Koordinera lösen ords ändringar med en lösen ords uppdatering för att minimera avbrott i tjänsten. |
| Kontot är medlem i privilegierade grupper.| Granska grupp medlemskap. Ta bort kontot från privilegierade grupper. Bevilja kontot enbart de rättigheter och behörigheter som krävs för att köra tjänsten (kontakta tjänst leverantören). Till exempel kanske du kan neka inloggning lokalt eller neka interaktiv inloggning. |
| Kontot har Läs-/Skriv behörighet till känsliga resurser.| Granska åtkomst till känsliga resurser. Arkivera gransknings loggar till en SIEM (Azure Log Analytics eller Azure Sentinel) för analys. Åtgärda resurs behörigheter om en oönskad åtkomst nivå upptäcks. |


## <a name="move-to-more-secure-account-types"></a>Flytta till fler säkra konto typer

Microsoft rekommenderar inte att kunder använder lokala användar konton som tjänst konton. För alla tjänster som använder den här typen av konto ska du utvärdera om det kan konfigureras i stället för att använda en gMSA eller en sMSA.

Dessutom kan du utvärdera om själva tjänsten kan flyttas till Azure så att fler säkra tjänst konto typer kan användas. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skyddar tjänst konton

* [Introduktion till lokala tjänst konton](service-accounts-on-premises.md)

* [Skydda grupphanterade tjänst konton](service-accounts-group-managed.md)

* [Skyddade fristående hanterade tjänst konton](service-accounts-standalone-managed.md)

* [Skydda dator konton](service-accounts-computer.md)

* [Säkra användar konton](service-accounts-user-on-premises.md)

* [Styr lokala tjänst konton](service-accounts-govern-on-premises.md)

 
