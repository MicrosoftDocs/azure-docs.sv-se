---
title: Vad är Azure Service Health-meddelanden?
description: Aviseringar för tjänst hälsa gör att du kan visa meddelanden om tjänst hälsa som publicerats av Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529020"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>Använd Azure Portal för att visa meddelanden om tjänst hälsa

Meddelanden om tjänst hälsa publiceras av Azure och innehåller information om resurserna i din prenumeration. Dessa meddelanden är en underordnad klass med aktivitets logg händelser och finns också i aktivitets loggen. Meddelanden om tjänst hälsa kan vara informations-eller åtgärds bara, beroende på klassen.

Det finns olika klasser av service Health-meddelanden:  

- **Åtgärd krävs:** Azure kan märka att något händer på ditt konto och arbeta med dig för att åtgärda detta. Azure skickar ett meddelande till dig, antingen information om de åtgärder som du behöver vidta eller hur du kontaktar Azure-teknik eller support.  
- **Incident:** En händelse som påverkar tjänsten påverkar för närvarande en eller flera av resurserna i din prenumeration.  
- **Underhåll:** En planerad underhålls aktivitet som kan påverka en eller flera av resurserna under din prenumeration.  
- **Information:** Möjliga optimeringar som kan hjälpa till att förbättra resurs användningen. 
- **Säkerhet:** Brådskande säkerhetsrelaterad information om dina lösningar som körs på Azure.

Varje tjänst hälso avisering innehåller information om omfattningen och konsekvenserna för dina resurser. Informationen omfattar:

Egenskapsnamn | Beskrivning
-------- | -----------
kanal | Ett av följande värden: **admin** eller **åtgärd**.
correlationId | Vanligt vis ett GUID i sträng formatet. Händelser som tillhör samma åtgärd delar vanligt vis samma correlationId.
eventDataId | Den unika identifieraren för en händelse.
eventName | Rubriken för en händelse.
nivå | Händelse nivån
resourceProviderName | Namnet på resurs leverantören för den påverkade resursen.
resourceType| Resurs typen för den påverkade resursen.
subStatus | Vanligt vis är HTTP-statuskoden för motsvarande REST-anrop, men kan även innehålla andra strängar som beskriver en under status. Till exempel: OK (HTTP-status kod: 200), skapad (HTTP-status kod: 201), godkänd (HTTP-status kod: 202), ingen innehålls (HTTP-status kod: 204), felaktig begäran (HTTP-status kod: 400), hittades inte (HTTP-status kod: 404), konflikt (HTTP-status kod: 409), internt Server fel (HTTP-status kod: 500), tjänsten är inte tillgänglig (HTTP-status kod: 503) och gateway-timeout (HTTP-statuskod
eventTimestamp | Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen.
submissionTimestamp | Tidsstämpel när händelsen blev tillgänglig för frågor.
subscriptionId | Azure-prenumerationen som den här händelsen loggades i.
status | Sträng som beskriver status för åtgärden. Några vanliga värden är: **startade**, **pågår**, **lyckades**, **misslyckades**, **aktiva** och **löst**.
operationName | Åtgärdens namn.
category | Den här egenskapen är alltid **ServiceHealth**.
resourceId | Resurs-ID för den påverkade resursen.
Egenskaper. title | Den lokaliserade rubriken för den här kommunikationen. Engelska är standard.
Egenskaper. kommunikation | Lokaliserad information om kommunikationen med HTML-kod. Engelska är standard.
Egenskaper. incidentType | Ett av följande värden: **åtgärd krävs**, **information**, **incident**, **Underhåll** eller **säkerhet**.
Egenskaper. trackingId | Incidenten som den här händelsen är associerad med. Använd detta för att korrelera händelser som rör en incident.
Egenskaper. impactedServices | En Escaped JSON-blob som beskriver de tjänster och regioner som påverkas av incidenten. Egenskapen innehåller en lista över tjänster, som var och en har en **ServiceName**, och en lista över påverkade regioner, som var och en har en **RegionName**.
Egenskaper. defaultLanguageTitle | Kommunikationen på engelska.
Egenskaper. defaultLanguageContent | Kommunikationen på engelska antingen som HTML-kod eller oformaterad text.
Egenskaper. Stage | Möjliga värden för **incident** och **säkerhet** är **aktiva,** **löst** eller **RCA**. För **åtgärd krävs** eller **information** är det enda värdet **aktivt.** För **Underhåll** är de: aktiva, **planerade**,  **inaktuella**, **avbrutna**, **omplanerade**, **stängda** eller **fullständiga**.
Egenskaper. communicationId | Den kommunikation som den här händelsen är associerad med.

### <a name="details-on-service-health-level-information"></a>Information om information om tjänste hälso nivå

**Åtgärd krävs** (Properties. incidentType = = åtgärd krävs)
- Information – administratörs åtgärder krävs för att förhindra att befintliga tjänster påverkas.
    
**Underhåll** (Properties. incidentType = = underhåll)
- Varning-katastrof underhåll
- Planerat underhåll av information – standard

**Information** (Properties. incidentType = = information)
- Information-administratören kan krävas för att förhindra att befintliga tjänster påverkas.

**Säkerhet** (egenskaper. incidentType = = säkerhet)
- Varning – säkerhets rekommendation som påverkar befintliga tjänster och som kan kräva administratörs åtgärder.
- Informations-och säkerhets rekommendation som påverkar befintliga tjänster.

**Tjänst problem** (Properties. incidentType = = incident)
- Fel som är omfattande vid åtkomst till flera tjänster i flera regioner påverkar en rad olika kunder.
- Varning – problem med åtkomst till specifika tjänster och/eller specifika regioner påverkar en delmängd av kunderna.
- Information-problem som påverkar hanterings åtgärder och/eller latens, påverkar inte tjänstens tillgänglighet.
