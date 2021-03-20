---
title: 'Vanliga frågor och svar: SRE och kodning | Microsoft Docs'
titleSuffix: Azure
description: 'Vanliga frågor och svar: förstå relationen mellan SRE och kodning'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90089079"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Vanliga frågor och svar: behöver jag veta hur man kan koda för att komma igång med SRE?

När individer överväger att delta i SRE och team tänker på att sätta igång i SRE-praxis, är en vanlig fråga som är "vill du veta hur man kodar?"

Kort svar: Ja. 

Men det fullständiga svaret är lite mer nyanserade. Nu ska vi titta på tre platser där kodningen kommer in på platsens Tillförlitlighets teknik tillsammans med den nivå av kod kunskaper som krävs för var och en. Den här listan är inte fullständig, men de här scenarierna är några vanliga användnings fall.

## <a name="scenario-1-removing-toil-through-automation"></a>Scenario 1: ta bort Toil via Automation

Tekniker för plats tillförlitlighet och andra som använder SRE-praxis när det är möjligt att ta bort Toil. "Toil" syftar på en speciell sak i SRE. Toil syftar på åtgärder som utförs av en mänsklig som har vissa egenskaper. Enahanda arbete har inget långvarigt inlösande värde. Det tar inte tjänsten framåt på något meningsfullt sätt. Det är ofta återkommande och huvudsakligen manuellt (även om det kan automatiseras). När tjänsten eller system blir större över tid ökar förmodligen också antalet begäranden för systemet i antal med en proportionell hastighet och kräva ännu mer manuellt arbete.

Om en tjänst till exempel kräver att SRE-teamet återställer något varje vecka, eller om du vill etablera nya konton och disk utrymme manuellt, eller om du vill starta om det manuellt, så är den operativa belastningen som är Toil. Genomförandet av de här åtgärderna har inte gjort tjänsten bättre på något långsiktigt, beständigt sätt. Dessa åtgärder måste förmodligen upprepas flera gånger.

SRE:er hatar enahanda arbete. De arbetar för att ta bort det när det är möjligt och lämpligt. Det här är en av de platser där automatisering blir aktuellt i SRE. Om dessa begär Anden kan hanteras automatiskt kan det göra det möjligt för teamet att arbeta med mer information och påverka sakernas betydelse.

*Kodnings experter*: Automation kräver viss kod expert, men behöver inte kräva fullständiga kunskaper om program varu teknik. Om du kan skriva små skript (kanske i PowerShell eller Bourne-gränssnitt) eller även om du skapar en [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) med knappt valfri kod kan den här appen fortfarande hjälpa till att eliminera Toil.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Scenario 2: kontrol lera via API: er/domänautentiseringsuppgifter (DSLs)/templates

Även om det inte är absolut nödvändigt för SRE-arbete, kan du kontrol lera miljöer via API: er, DSLs och mallar (särskilt moln miljöer) tillåta SREs att skala upp sitt arbete. Etablering/avetablerings infrastruktur, konfigurering av övervakning och integrering av flera tjänster blir mycket effektivare via kod.

*Kodnings expert*: som det föregående scenariot kräver detta vissa kodnings kunskaper, men det behöver inte kräva fullständiga kunskaper om program varu teknik. Förutom de skript och Logic-appar som nämnts tidigare, [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) kan också användas med minimal kodnings upplevelse.

## <a name="scenario-3-fixing-the-code"></a>Scenario 3: korrigera koden

Platsens Tillförlitlighets tekniker ser till att förbättra tillförlitligheten i ett system. Det här målet kräver ibland utforska till käll koden i ett system, vilket avgör problemet och som ofta bidrar till att återställa till kodbasen. Även om nivån på riktigt ambitiös av det här arbetet kan variera mycket beroende på situationen, är kodnings experten ett visst krav i dessa fall.

*Kodnings expert*: fullständiga kunskaper om program varu teknik krävs ofta i det här scenariot.


## <a name="next-steps"></a>Nästa steg

Är du intresse rad av att lära dig mer om Tillförlitlighets teknik och låg kod på plats? Kolla in vår [webbplats för Tillförlitlighets teknik](../index.yml), den produkt dokumentation som är länkad ovan.
