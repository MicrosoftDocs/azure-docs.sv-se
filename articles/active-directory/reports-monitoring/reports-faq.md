---
title: Vanliga frågor och svar Azure Active Directory | Microsoft Docs
description: Vanliga frågor och svar om Azure Active Directory rapporter.
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
ms.openlocfilehash: 4da0083a236900037b388798d825515e94613c20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533706"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Vanliga frågor och svar om Azure Active Directory rapporter

Den här artikeln innehåller svar på vanliga frågor om Azure Active Directory rapportering (Azure AD). Läs mer i informationen om [Azure Active Directory-rapportering](overview-reports.md). 

## <a name="getting-started"></a>Komma igång 

**F: Jag använder för närvarande slutpunkts-API:er för att hämta Azure AD-granskningsrapporter och integrerade programanvändningsrapporter till `https://graph.windows.net/<tenant-name>/reports/` våra rapporteringssystem programmatiskt. Vad ska jag byta till?**

**S:** Leta upp [API-referensen för](https://developer.microsoft.com/graph/) att se hur du kan [använda API:erna för att komma åt aktivitetsrapporter](concept-reporting-api.md). Den här slutpunkten har två rapporter **(granskning** **och inloggningar)** som innehåller alla data som du fick i den gamla API-slutpunkten. Den nya slutpunkten har också en inloggningsrapport med Azure AD Premium-licensen som du kan använda för att hämta information om appanvändning, enhetsanvändning och användar inloggning.

---

**F: Jag använder för närvarande slutpunkts-API:er för att hämta Azure AD-säkerhetsrapporter (specifika typer av identifieringar, till exempel läckta autentiseringsuppgifter eller inloggningar från anonyma IP-adresser) till vårt rapporteringssystem `https://graph.windows.net/<tenant-name>/reports/` programmatiskt. Vad ska jag byta till?**

**S:** Du kan använda [IDENTITY Protection-API:et för riskidentifiering](../identity-protection/howto-identity-protection-graph-api.md) för att få åtkomst till säkerhetsidentifiering via Microsoft Graph. Det här nya formatet ger större flexibilitet i hur du kan fråga efter data, med avancerad filtrering, fältval med mera, och standardiserar riskidentifiering till en typ för enklare integrering i SIEM och andra verktyg för datainsamling. Eftersom data har ett annat format kan du inte ersätta en ny fråga med dina gamla frågor. Det nya [API:et använder Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true), som är Microsofts standard för api:er som Microsoft 365 eller Azure AD. Det arbete som krävs kan antingen utöka dina Microsoft Graph investeringar eller hjälpa dig att påbörja övergången till den nya standardplattformen.

---

**F: Hur gör jag för att skaffa en Premium-licens?**

**S:** Se [Komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) att uppgradera din Azure Active Directory version.

---

**F: Hur snart ska jag se aktivitetsdata när jag har skaffat en Premium-licens?**

**S:** Om du redan har aktivitetsdata som en kostnadsfri licens kan du se dem direkt. Om du inte har några data tar det upp till tre dagar innan data visas i rapporterna.

---

**F: Kan jag se förra månadens data när jag har skaffat en Azure AD Premium-licens?**

**S:** Om du nyligen har växlat till en Premium-version (inklusive en utvärderingsversion) kan du se data i upp till 7 dagar från början. När data ackumuleras kan du se data för de senaste 30 dagarna.

---

**F: Måste jag vara global administratör för att se aktivitetsloggarna till Azure Portal eller hämta data via API:et?**

**S:** Nej, du kan också komma åt rapporteringsdata via portalen eller via API:et om du är säkerhetsläsare **eller** **säkerhetsadministratör** för klientorganisationen. Globala administratörer **kommer naturligtvis också** att ha åtkomst till dessa data.

---


## <a name="activity-logs"></a>Aktivitetsloggar


**F: Vad är databevarande för aktivitetsloggar (granskning och inloggningar) i Azure Portal?** 

**S:** Mer information finns i Principer [för databevarande för Azure AD-rapporter.](reference-reports-data-retention.md)

---

**F: Hur lång tid tar det innan jag kan se aktivitetsdata när jag har slutfört min uppgift?**

**S:** Granskningsloggar har en svarstid på mellan 15 minuter och en timme. Inloggningsaktivitetsloggar kan ta mellan 15 minuter och upp till 2 timmar för vissa poster.

---

**F: Kan jag Microsoft 365 information om aktivitetsloggen via Azure Portal?**

**S:** Även om Microsoft 365 aktivitetsloggar och Azure AD-aktivitetsloggar delar en stor del av katalogresurserna bör du gå till [Administrationscenter för Microsoft 365](https://admin.microsoft.com) för att hämta information om Aktivitetslogg för Office 365 om du vill ha en fullständig vy över Microsoft 365-aktivitetsloggarna.

---

**F: Vilka API:er använder jag för att hämta information om Microsoft 365 aktivitetsloggar?**

**S:** Använd [API:Microsoft 365 Management för att](/office/office-365-management-api/office-365-management-apis-overview) komma åt Microsoft 365 aktivitetsloggar via ett API.

---

**F: Hur många poster kan jag ladda ned från Azure Portal?**

**S:** Du kan ladda ned upp till 5 000 poster från Azure Portal. Posterna sorteras *efter de senaste* och som standard får du de senaste 5 000 posterna.

---

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

**F: Det finns en riskidentifiering i Identity Protection, men jag ser inte motsvarande inloggning i inloggningsrapporten. Är detta förväntat?**

**S:** Ja, Identity Protection utvärderar risken för alla autentiseringsflöden oavsett om de är interaktiva eller icke-interaktiva. Alla inloggningar visar dock bara interaktiva inloggningar.

---

**F: Hur gör jag för att du varför en inloggning eller en användare flaggades som riskabel i Azure Portal?**

**S:** Om du har en **Azure AD Premium-prenumeration** kan du lära dig mer om de underliggande riskidentifieringarna genom att välja användaren i Användare som **har flaggats** för risk eller genom att välja en post i rapporten för riskfyllda inloggningar.  Om du har en **kostnadsfri** eller **basic-prenumeration** kan du visa rapporter om användare i riskzonen och riskfyllda inloggningar, men du kan inte se den underliggande riskidentifieringsinformationen.

---

**F: Hur beräknas IP-adresser i rapporten om inloggningar och riskfyllda inloggningar?**

**S:** IP-adresser utfärdas på ett sådant sätt att det inte finns någon slutgiltig anslutning mellan en IP-adress och var datorn med den adressen finns fysiskt. Mappningen av IP-adresser är ytterligare komplicerad av faktorer som mobila providers och VPN-nätverk som utfärdar IP-adresser från centrala pooler ofta mycket långt från där klientenhet faktiskt används. För närvarande är det bäst att konvertera IP-adresser till en fysisk plats i Azure AD-rapporter baserat på spårningar, registerdata, omvända upp- och annan information. 

---

**F: Vad innebär riskidentifieringen "Inloggning med ytterligare risk har identifierats"?**

**S:** För att ge dig insikt i alla riskfyllda inloggningar i din miljö fungerar "Logga in med ytterligare risk har identifierats" som platshållare för inloggningar för identifieringar som är exklusiva för Azure AD Identity Protection prenumeranter.

---

## <a name="conditional-access"></a>Villkorlig åtkomst

**F: Vad är nytt med den här funktionen?**

**S:** Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggningar. Kunder kan granska statusen för villkorsstyrd åtkomst och fördjupa sig i informationen om de principer som tillämpas på inloggningen och resultatet för varje princip.

**F: Hur gör jag för att du igång?**

**S:** Så här kommer du igång:

* Gå till inloggningsrapporten i [Azure Portal](https://portal.azure.com).
* Klicka på den inloggning som du vill felsöka.
* Gå till fliken **Villkorsstyrd** åtkomst. Här kan du visa alla principer som påverkade inloggningen och resultatet för varje princip. 
    
**F: Vilka är alla möjliga värden för status för villkorsstyrd åtkomst?**

**S:** Status för villkorsstyrd åtkomst kan ha följande värden:

* **Inte tillämpat:** Det innebär att det inte fanns någon princip för villkorsstyrd åtkomst med användaren och appen i omfånget. 
* **Lyckades:** Det innebär att det fanns en princip för villkorsstyrd åtkomst med användaren och appen i omfånget och att principerna för villkorsstyrd åtkomst har uppfyllts. 
* **Fel:** Inloggningen uppfyllt användar- och programvillkoret för minst en princip för villkorsstyrd åtkomst och beviljandekontroller är antingen inte uppfyllda eller inställda på att blockera åtkomst.
    
**F: Vilka är alla möjliga värden för resultatet av principen för villkorsstyrd åtkomst?**

**S:** En princip för villkorlig åtkomst kan ha följande resultat:

* **Lyckades:** Principen har uppfyllts.
* **Fel:** Principen uppfylls inte.
* **Inte tillämpat:** Det kan vara på grund av att principvillkoren inte uppfyller.
* **Inte aktiverat:** Detta beror på att principen är i inaktiverat tillstånd. 
    
**F: Principnamnet i all inloggningsrapport matchar inte principnamnet i certifikatutfärdaren. Varför?**

**S:** Principnamnet i all inloggningsrapport baseras på namnet på principen för villkorlig åtkomst vid tidpunkten för inloggningen. Detta kan vara inkonsekvent med principnamnet i certifikatutfärdaren om du uppdaterade principnamnet senare, det vill säga efter inloggningen.

**F: Min inloggning blockerades på grund av en princip för villkorlig åtkomst, men inloggningsaktivitetsrapporten visar att inloggningen lyckades. Varför?**

**S:** För närvarande kanske inloggningsrapporten inte visar korrekta resultat för Exchange ActiveSync-scenarier när villkorsstyrd åtkomst tillämpas. Det kan finnas fall när inloggningsresultatet i rapporten visar en lyckad inloggning, men inloggningen misslyckades faktiskt på grund av en princip för villkorlig åtkomst.
