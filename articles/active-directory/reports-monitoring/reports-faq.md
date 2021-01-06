---
title: Vanliga frågor och svar om Azure Active Directory rapporter | Microsoft Docs
description: Vanliga frågor och svar kring Azure Active Directory-rapporter.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b6b182d030c267dcec7ed57c0c0dd1901b0cb1
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935095"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Vanliga frågor och svar kring Azure Active Directory rapporter

Den här artikeln innehåller svar på vanliga frågor om Azure Active Directory (Azure AD) Repor ting. Läs mer i informationen om [Azure Active Directory-rapportering](overview-reports.md). 

## <a name="getting-started"></a>Komma igång 

**F: Jag använder för närvarande `https://graph.windows.net/<tenant-name>/reports/` slut punkts-API: er för att hämta Azure AD Audit och integrerade program användnings rapporter i våra rapporterings system program mässigt. Vad ska jag byta till?**

**A:** Leta upp [API-referensen](https://developer.microsoft.com/graph/) för att se hur du kan [använda API: er för att få åtkomst till aktivitets rapporter](concept-reporting-api.md). Den här slut punkten har två rapporter (**gransknings** **-och inloggnings program**) som tillhandahåller alla data som du fick i den gamla API-slutpunkten. Den nya slut punkten har också en inloggnings rapport med Azure AD Premium-licensen som du kan använda för att hämta program användning, enhets användning och användar inloggnings information.

---

**F: Jag använder för närvarande `https://graph.windows.net/<tenant-name>/reports/` slut punkts-API: er för att hämta Azure AD-säkerhetsrapporter (vissa typer av identifieringar, till exempel läckta autentiseringsuppgifter eller inloggningar från anonyma IP-adresser) till våra rapporterings system program mässigt. Vad ska jag byta till?**

**A:** Du kan använda [identitets skyddets identifierings-API](../identity-protection/howto-identity-protection-graph-api.md) för att få åtkomst till säkerhets identifieringar via Microsoft Graph. Det nya formatet ger större flexibilitet i hur du kan fråga data, med avancerad filtrering, val av fält med mera och standardisera risk identifieringar till en typ för enklare integrering i Siem och andra data insamlings verktyg. Eftersom data har ett annat format kan du inte ersätta en ny fråga för dina gamla frågor. [Det nya API: et använder dock Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta), som är Microsoft-standarden för sådana API: er som Microsoft 365 eller Azure AD. Det arbete som krävs kan antingen utöka dina nuvarande Microsoft Graph investeringar eller hjälpa dig att börja över gången till den nya standard plattformen.

---

**F: Hur gör jag för att skaffa en Premium-licens?**

**A:** Se [komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera Azure Active Directory-versionen.

---

**F: hur snart ska jag se aktivitets data när de har skaffat en Premium-licens?**

**A:** Om du redan har aktivitets data som en kostnads fri licens kan du se den direkt. Om du inte har några data tar det upp till tre dagar innan data visas i rapporterna.

---

**F: kan jag se den senaste månadens data efter att ha skaffat en Azure AD Premium-licens?**

**A:** Om du nyligen har bytt till en Premium-version (inklusive en utvärderings version) kan du se data upp till sju dagar från början. När data ackumuleras kan du se data för de senaste 30 dagarna.

---

**F: måste jag vara global administratör för att kunna se aktivitets inloggningarna till Azure Portal eller hämta data via API: et?**

**A:** Nej, du kan också komma åt rapporterings data via portalen eller via API om du är en **säkerhets läsare** eller **säkerhets administratör** för klienten. **Globala administratörer** kommer naturligtvis också att ha åtkomst till dessa data.

---


## <a name="activity-logs"></a>Aktivitetsloggar


**F: Vad är data kvarhållning för aktivitets loggar (granskning och inloggningar) i Azure Portal?** 

**A:** Mer information finns i [principer för data bevarande för Azure AD-rapporter](reference-reports-data-retention.md).

---

**F: hur lång tid tar det innan jag kan se aktivitets data efter att jag har slutfört min uppgift?**

**A:** Gransknings loggar har en fördröjning på mellan 15 minuter och en timme. Inloggnings aktivitets loggar kan ta från 15 minuter till upp till 2 timmar för vissa poster.

---

**F: kan jag få Microsoft 365 aktivitets logg information via Azure Portal?**

**A:** Även om Microsoft 365 aktivitet och Azure AD-aktivitets loggar delar mycket av katalog resurserna, bör du gå till [Microsoft 365 administrations Center](https://admin.microsoft.com) för att hämta information om aktivitets loggen för Office 365 om du vill ha en fullständig vy över de Microsoft 365 aktivitets loggarna.

---

**F: vilka API: er använder jag för att hämta information om Microsoft 365 aktivitets loggar?**

**A:** Använd API: [erna för Microsoft 365 hantering](/office/office-365-management-api/office-365-management-apis-overview) för att få åtkomst till Microsoft 365 aktivitets loggar via ett API.

---

**F: hur många poster jag kan ladda ned från Azure Portal?**

**A:** Du kan hämta upp till 5000 poster från Azure Portal. Posterna sorteras efter senaste *och som* standard får du de senaste 5000 posterna.

---

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

**F: det finns en risk identifiering i identitets skydd, men jag kan inte se motsvarande inloggning i inloggnings rapporten. Förväntas detta?**

**A:** Ja, identitets skyddet utvärderar risker för alla autentiserings flöden oavsett om de är interaktiva eller icke-interaktiva. Alla inloggnings rapporter visar dock bara de interaktiva inloggningarna.

---

**F: Hur gör jag för att veta varför en inloggning eller en användare har flaggats som riskfylld i Azure Portal?**

**A:** Om du har en **Azure AD Premium** prenumeration kan du lära dig mer om de underliggande risk identifieringarna genom att välja användaren i **användare som har flaggats för risk** eller genom att välja en post i rapporten **riskfyllda inloggningar** . Om du har en **kostnads fri** eller **grundläggande** prenumeration kan du visa rapporterna användare i risk-och riskfyllda inloggningar, men du kan inte se information om underliggande risk identifiering.

---

**F: Hur beräknas IP-adresser i inloggnings-och riskfyllda inloggnings rapporten?**

**A:** IP-adresser utfärdas på ett sådant sätt att det inte finns någon slutgiltig anslutning mellan en IP-adress och där datorn med den adressen är fysiskt placerad. Mappning av IP-adresser är ytterligare komplicerad av faktorer som mobil leverantörer och VPN-anslutningar som utfärdar IP-adresser från centrala pooler är ofta mycket från var klient enheten faktiskt används. För närvarande i Azure AD-rapporter är det bästa sättet att konvertera IP-adress till en fysisk plats baserat på spår, register data, omvänd sökning och annan information. 

---

**F: Vad innebär identifieringen av risk identifieringen "inloggning med ytterligare risk upptäckt"?**

**A:** För att ge dig insikter om alla riskfyllda inloggningar i din miljö, "inloggning med ytterligare risk upptäckt" fungerar som plats hållare för inloggningar som är exklusiva för att Azure AD Identity Protection prenumeranter.

---

## <a name="conditional-access"></a>Villkorlig åtkomst

**F: Vad är nytt med den här funktionen?**

**A:** Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggnings rapporter. Kunder kan granska status för villkorlig åtkomst och få information om de principer som tillämpas på inloggningen och resultatet för varje princip.

**F: Hur gör jag för att kom igång?**

**A:** För att komma igång:

* Navigera till inloggnings rapporten i [Azure Portal](https://portal.azure.com).
* Klicka på den inloggning som du vill felsöka.
* Gå till fliken **villkorlig åtkomst** . Här kan du Visa alla principer som påverkade inloggningen och resultatet för varje princip. 
    
**F: vilka är alla möjliga värden för status för villkorlig åtkomst?**

**A:** Status för villkorlig åtkomst kan ha följande värden:

* **Används inte**: det innebär att det inte fanns någon princip för villkorlig åtkomst med användaren och appen i omfånget. 
* **Lyckades**: det innebär att en princip för villkorlig åtkomst med användaren och appen i omfattning och principer för villkorlig åtkomst har uppfyllts. 
* **Fel**: inloggningen uppfyllde användar-och program villkoret för minst en princip för villkorlig åtkomst och beviljade kontroller är antingen inte uppfyllda eller har angetts till blockera åtkomst.
    
**F: Vad är alla möjliga värden för resultatet av principen för villkorlig åtkomst?**

**A:** En princip för villkorlig åtkomst kan ha följande resultat:

* **Lyckades**: principen har uppfyllts.
* **Misslyckades**: principen uppfylldes inte.
* **Används inte**: det kan bero på att princip villkoren inte uppfyllde.
* **Inte aktiverat**: Detta beror på att principen är inaktive rad. 
    
**F: princip namnet i all inloggnings rapport stämmer inte överens med princip namnet i CA. Varför?**

**A:** Princip namnet i rapporten alla inloggningar baseras på namnet på principen för villkorlig åtkomst vid tidpunkten för inloggningen. Detta kan vara inkonsekvent med princip namnet i CA om du uppdaterade princip namnet senare, det vill säga efter inloggningen.

**F: min inloggning blockerades på grund av en princip för villkorlig åtkomst, men rapporten inloggnings aktivitet visar att inloggningen lyckades. Varför?**

**A:** För närvarande kan inloggnings rapporten inte Visa korrekta resultat för Exchange ActiveSync-scenarier när villkorlig åtkomst används. Det kan finnas fall då inloggnings resultatet i rapporten visar en lyckad inloggning, men inloggningen misslyckades på grund av en princip för villkorlig åtkomst.
