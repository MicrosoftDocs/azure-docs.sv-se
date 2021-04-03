---
title: Azure frontend – säkerhet på program nivå | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azures front dörr gör det möjligt att skydda och skydda dina Programserver delar
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89399947"
---
# <a name="application-layer-security-with-front-door"></a>Säkerhet på program nivå med front dörr
Azures front dörr tillhandahåller funktioner för webb program skydd för att skydda dina webb program mot nätverks attacker och vanliga webb sårbarheter utnyttjar som SQL-inmatning eller Cross Site Scripting (XSS). Aktive rad för http (s)-frontend, den främre dörrens program nivå säkerhet, är globalt distribuerat och alltid aktiverat, och stoppar skadliga attacker på Azures nätverks gräns, långt bort från dina Server delar. Med extra säkerhet och prestanda optimering ger front dörren snabba och säkra webb upplevelser för dina slutanvändare.

## <a name="application-protection"></a>Program skydd
Den främre dörrens program skydd konfigureras på varje gräns miljö över hela världen, i linje med program, och blockerar automatiskt icke-HTTP-trafik från att nå dina webb program. Vår distribuerade arkitektur för flera innehavare möjliggör globalt skydd i skala utan att offra prestanda. För http (s)-arbetsbelastningar tillhandahåller front dörrs webb program skydds tjänst en omfattande regel motor för anpassade regler, förkonfigurerade ruleset mot vanliga attacker och detaljerad loggning för alla begär Anden som matchar en regel. Flexibla åtgärder, inklusive Allow, block eller log, stöds bara.

## <a name="custom-access-control-rules"></a>Anpassade regler för åtkomst kontroll
- Lista **över tillåtna IP-listor och blockerade listor:** Du kan konfigurera anpassade regler för att styra åtkomsten till dina webb program baserat på listan över klient-IP-adresser. Både IP v4 och IP V6 stöds
- **Geografisk baserad åtkomst kontroll:** Du kan konfigurera anpassade regler för att styra åtkomsten till dina webb program baserat på landskod som en klient-IP är från
- **Filtrering av http-parametrar:** Du kan konfigurera anpassade åtkomst regler baserat på matchande http (s) parametrar för begäran, inklusive rubriker, URL-adresser och frågesträngar

## <a name="azure-managed-rules"></a>Azure-hanterade regler
- En förkonfigurerad uppsättning regler mot vanliga OWASP säkerhets risker är aktiverat som standard. I för hands versionen inkluderar regel uppsättningen SQLi och XSS-begäranden. Ytterligare regler kommer att läggas till. Du kan välja att starta med åtgärden logga endast för att verifiera att förkonfigurerade regler fungerar som förväntat för dina program 

## <a name="rate-limiting"></a>Frekvensbegränsning
- En hastighets kontroll regel är att begränsa onormal hög trafik från alla klient-IP-adresser.  Du kan ange ett tröskelvärde för antalet webb förfrågningar som tillåts av en klient-IP under en minuts varaktighet.

## <a name="centralized-protection-policy"></a>Centraliserad skydds princip
- Du kan definiera flera skydds regler och lägga till dem i en princip i prioritetsordning. Anpassade regler har högre prioritet än hanterade ruleset för att tillåta undantag. En enda princip är kopplad till ditt webb program.  Samma skydds princip för webb program replikeras till alla gräns servrar på alla platser, och säkerställer konsekvent säkerhets policy i alla regioner

## <a name="configuration"></a>Konfiguration
- Under för hands versionen kan du använda REST-API: er, PowerShell eller CLI för att skapa och distribuera frontend-programmets skydds regler och principer. Portal åtkomst stöds innan tjänsten är allmänt tillgänglig. 


## <a name="monitoring"></a>Övervakning
Med front dörren kan du övervaka webb program mot attacker med real tids mått som är integrerade med Azure Monitor för att spåra aviseringar och enkelt övervaka trender.

## <a name="pricing"></a>Priser
Den främre dörrens säkerhet på program nivå är kostnads fri under för hands versionen.


## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
