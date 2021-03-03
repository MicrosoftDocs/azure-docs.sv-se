---
title: Styrande lokala tjänst konton | Azure Active Directory
description: En guide för att skapa och köra en konto livs cykel process för tjänst konton
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
ms.openlocfilehash: 88fdfa1f449a0b65861ee09f2e78055a606c99d3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649214"
---
# <a name="governing-on-premises-service-accounts"></a>Styrande lokala tjänst konton

Det finns fyra typer av lokala tjänst konton i Windows Active Directory:

* [Grupphanterade tjänst konton](service-accounts-group-managed.md) (gMSAs)

* [fristående hanterade tjänst konton](service-accounts-standalone-managed.md) (sMSAs)

* [Dator konton](service-accounts-computer.md)

* [Användar konton som fungerar som tjänst konton](service-accounts-user-on-premises.md)


Det är viktigt att styra tjänst konton nära: 

* Skydda tjänst konton utifrån krav och syfte för användnings fall.

* Hantera livs cykeln för tjänst konton och deras autentiseringsuppgifter.

* Utvärdera tjänst konton utifrån den risk som de kommer att exponeras för och de behörigheter de bär, 

* Se till att Active Directory och Azure Active Directory inte har några föråldrade tjänst konton med eventuellt behörighet som kan ha uppnått.

## <a name="principles-for-creating-a-new-service-account"></a>Principer för att skapa ett nytt tjänst konto

Använd följande kriterier när du skapar ett nytt tjänst konto.

| Principer| Överväganden | 
| - |- | 
| Mappning av tjänst konto| Knyt tjänst kontot till en enskild tjänst, ett program eller skript. |
| Ägarskap| Se till att det finns en ägare som begär det och förutsätter ansvaret för kontot. |
| Omfång| Definiera omfånget klart och förutse användnings tiden för tjänst kontot. |
| Syfte| Skapa tjänst konton för ett enskilt specifikt syfte. |
| Privilege| Använd principen för minsta behörighet av: <br>Tilldela dem aldrig till inbyggda grupper som administratörer.<br> Tar bort behörigheter för lokal dator där det är lämpligt.<br>Skräddarsy åtkomst och använda Active Directory delegering för katalog åtkomst.<br>Använda detaljerad åtkomst behörighet.<br>Inställning av konto förfaller och platsbaserade begränsningar för användarbaserade tjänst konton |
| Övervaka och granska användning| Övervaka inloggnings data och se till att de matchar den avsedda användningen. Ange aviseringar för avvikande användning. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Framtvinga lägsta behörighet för användar konton och begränsa överanvändning av konto

Använd följande inställningar med användar konton som används som tjänst konton:

* [**Kontots förfallo datum**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): ange att tjänst kontot automatiskt ska upphöra att gälla en angiven tid efter dess gransknings period, om det inte fastställs att det ska fortsätta

*  **LogonWorkstations**: begränsa behörigheter för var tjänst kontot kan logga in. Om den körs lokalt på en dator och endast har åtkomst till resurser på datorn, begränsar du den från att logga in någon annan stans.

* [**Det går inte att ändra lösen ord**](/powershell/module/addsadministration/set-aduser?view=win10-ps): förhindra att tjänst kontot ändrar sitt eget lösen ord genom att ange parametern till false.

 
## <a name="build-a-lifecycle-management-process"></a>Bygg en process för livs cykel hantering

För att upprätthålla säkerheten för dina tjänst konton måste du hantera dem från den tidpunkt då du identifierar behovet tills de har inaktiverats. 

Använd följande process för livs cykel hantering av tjänst konton:

1. Samla in användnings information för kontot
1. Publicera tjänst kontot och appen för konfigurations hanterings databasen (CMDB)
1. Utföra riskbedömning eller formell granskning
1. Skapa tjänst kontot och tillämpa begränsningar.
1. Schemalägg och utför återkommande granskningar. Justera behörigheter och omfång efter behov.
1. Avetablera konto vid behov.

### <a name="collect-usage-information-for-the-service-account"></a>Samla in användnings information för tjänst kontot

Samla in relevant företags information för varje tjänst konto. I tabellen nedan visas den minsta information som ska samlas in, men du bör samla allt som krävs för att skapa affärs ärendet för kontots existens.

| Data| Information |
| - | - |
| Ägare| Användare eller grupp som är konto för tjänst kontot |
| Syfte| Syftet med tjänst kontot |
| Behörigheter (omfattningar)| Förväntad uppsättning behörigheter |
| Länkar för konfigurations hanterings databas (CMDB)| Konto för kors länks tjänst med mål skript/program och ägare (er) |
| Risk| Bedömning av risk och affärs påverkan baserat på säkerhets riskbedömning |
| Giltighet| Förväntad maximal livs längd för att aktivera schemaläggning av konto upphör ande eller omcertifiering |


 

Vi rekommenderar att du gör en begäran om självbetjäning för kontot och behöver relevant information. Ägaren, som kan vara ett program eller en företags ägare, en IT-medlem eller en infrastruktur ägare. Genom att använda ett verktyg som Microsoft-formulär för den här begäran och associerad information blir det enkelt att kontakta det med CMDB Inventory Tool om kontot är godkänt.

### <a name="onboard-service-account-to-cmdb"></a>Onboard Service-konto till CMDB

Lagra insamlad information i ett CMDB program. Förutom företags informationen inkluderar alla beroenden till annan infrastruktur, appar och processer.  Med den här centrala lagrings platsen blir det enklare att:

* Bedöm risk.

* Konfigurera tjänst kontot med nödvändiga begränsningar.

* Förstå relevanta funktions-och säkerhets beroenden.

* Utför regelbundna granskningar för säkerhet och fortsatta behov.

* Kontakta ägaren (erna) om du vill granska, ta bort och ändra tjänst kontot.

Överväg ett tjänst konto som används för att köra en webbplats och som har behörighet att ansluta till en eller flera SQL-databaser. Information som lagras i din CMDB för det här tjänst kontot kan vara:

|Data | Information|
| - | - |
| Ägare, vice| John blomma, Anna Mayers |
| Syfte| Kör webb sidan för HR och Anslut till HR-databaser. Kan personifiera slutanvändaren vid åtkomst till databaser. |
| Behörigheter, omfattningar| HR-webserver: Logga in lokalt, kör webb sida<br>HR-SQL1: Logga in lokalt, Läs på alla HR *-databaser<br>HR-SQL2: Logga in lokalt, Läs på lön * databas |
| Cost Center| 883944 |
| Risk som utvärderas| Säker Inverkan på företaget: medium; privat information; Säker |
| Konto begränsningar| Logga in på: endast ovannämnda servrar; Det går inte att ändra lösen ordet. MBI-Password princip; |
| Giltighet| obegränsad |
| Gransknings cykel| Två per år (efter ägare, efter säkerhets team, efter sekretess) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Utföra riskbedömning eller formell granskning av tjänst kontots användning

Med tanke på dess behörigheter och syfte, bedöma risken som kontot kan innebära för det associerade programmet eller tjänsten och till din infrastruktur om den komprometteras. Överväg både direkt och indirekt risk. 

* Vad skulle en angripare få direkt åtkomst till?

* Vilken annan information eller vilka system kan ha åtkomst till tjänst kontot?

* Kan kontot användas för att bevilja ytterligare behörigheter?

* Hur vet du när behörigheter ändras?

Riskbedömningen, när de har genomförts och dokumenterats, kan påverka:

* Konto begränsningar

* Kontots livs längd

* Konto gransknings krav (takt och granskare)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Skapa ett tjänst konto och tillämpa konto begränsningar

Skapa bara tjänst konto när relevant information dokumenteras i din CMDB och du utför en riskbedömning. Konto begränsningar bör justeras mot riskbedömning. Tänk på följande begränsningar när det gäller din bedömning.:

* [Kontots förfallo datum](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * För alla användar konton som används som tjänst konton definierar du ett realistiskt och visst slutdatum för användning. Ange detta med flaggan "konto Expires". Mer information finns i[ set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration?view=win10-ps). 

* Logga in på ([LogonWorkstation](/powershell/module/addsadministration/set-aduser?view=win10-ps))

* Krav för [lösen ords princip](../../active-directory-domain-services/password-policy.md)

* Skapa på en [OU-plats](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) som endast garanterar hantering för privilegierade användare

* Konfigurera och samla in granskning [som identifierar ändringar](/windows/security/threat-protection/auditing/audit-directory-service-changes) av tjänst kontot – och [tjänst kontot använder](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

När du är redo att börja med produktion ger du åtkomst till tjänst kontot på ett säkert sätt. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Schemalägga regelbundna granskningar av tjänst konton

Konfigurera regelbundna granskningar av tjänst konton som klassificeras som medel stora och höga risker. Recensioner ska innehålla: 

* Ägarens attestering till fortsatta behov av kontot och motiveringen av behörigheter och omfång.

* Granska efter sekretess-och säkerhets team, inklusive utvärdering av överordnade och underordnade anslutningar.

* Data från revisioner som säkerställer att de endast används för avsedda ändamål

### <a name="deprovision-service-accounts"></a>Deetablering av tjänst konton

I avetablerings processen tar du först bort behörigheter och övervaka och tar sedan bort kontot om det behövs.

Avetablera tjänst konton när:

* Skriptet eller programmet som tjänst kontot skapades för har dragits tillbaka.

* Funktionen i skriptet eller programmet som tjänst kontot används för (till exempel åtkomst till en specifik resurs) dras tillbaka.

* Tjänst kontot har ersatts med ett annat tjänst konto.

När du har tagit bort alla behörigheter använder du den här processen för att ta bort kontot.

1. När det associerade programmet eller skriptet har avetablerats övervakar du inloggnings-och resurs åtkomsten för de associerade tjänst kontona så att de inte används i någon annan process. Om du är säker på att det inte längre behövs går du till nästa steg.

2. Inaktivera tjänst kontot från att logga in och se till att det inte längre behövs. Skapa en affärs princip för tids kontona måste vara inaktive rad.

3. Ta bort tjänst kontot när den fortfarande inaktiverade principen är uppfylld. 

   * För MSA: er kan du [avinstallera det](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) med PowerShell eller ta bort manuellt från den hanterade tjänst konto behållaren.

   * För dator-eller användar konton kan du manuellt ta bort kontot från i Active Directory.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skyddar tjänst konton

* [Introduktion till lokala tjänst konton](service-accounts-on-premises.md)

* [Skydda grupphanterade tjänst konton](service-accounts-group-managed.md)

* [Skyddade fristående hanterade tjänst konton](service-accounts-standalone-managed.md)

* [Skydda dator konton](service-accounts-computer.md)

* [Säkra användar konton](service-accounts-user-on-premises.md)

* [Styr lokala tjänst konton](service-accounts-govern-on-premises.md)