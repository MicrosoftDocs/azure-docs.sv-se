---
title: Introduktion till Azure Advisor
description: Använd Azure Advisor för att optimera dina Azure-distributioner.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 12e56bf44a29a32b2149bca14f7c99f319c9c4ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91405215"
---
# <a name="introduction-to-azure-advisor"></a>Introduktion till Azure Advisor

Lär dig mer om viktiga funktioner i Azure Advisor och få svar på vanliga frågor.

## <a name="what-is-advisor"></a>Vad är Advisor?
Advisor är en anpassad moln konsult som hjälper dig att följa bästa praxis för att optimera dina Azure-distributioner. Den analyserar resurs konfigurationen och användningen av telemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnads effektivitet, prestanda, pålitlighet (tidigare kallat hög tillgänglighet) och säkerheten för dina Azure-resurser.

Med Advisor kan du:
* Få proaktiva, användbara och personligt anpassade rekommendationer. 
* Förbättra prestanda, säkerhet och tillförlitlighet för dina resurser när du identifierar möjligheter att minska dina totala Azure-utgifter.
* Få rekommendationer med föreslagna åtgärder infogade.

Du kan komma åt Advisor via [Azure Portal](https://aka.ms/azureadvisordashboard). Logga in på [portalen](https://portal.azure.com), leta upp **Advisor** i navigerings menyn eller Sök efter den på menyn **alla tjänster** .

Advisor-instrumentpanelen visar anpassade rekommendationer för alla dina prenumerationer.  Du kan använda filter för att Visa rekommendationer för vissa prenumerationer och resurs typer.  Rekommendationerna är indelade i fem kategorier: 

* **Tillförlitlighet (tidigare kallat hög tillgänglighet)**: för att säkerställa och förbättra kontinuiteten för affärs kritiska program. Mer information finns i [rekommendationer för Advisor-tillförlitlighet](advisor-high-availability-recommendations.md).
* **Säkerhet**: för att identifiera hot och sårbarheter som kan leda till säkerhets överträdelser. Mer information finns i [rekommendationer för säkerhets rekommendationer](advisor-security-recommendations.md).
* **Prestanda**: för att förbättra program hastigheten. Mer information finns i [rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md).
* **Kostnad**: för att optimera och minska dina totala Azure-utgifter. Mer information finns i [rekommendationer för Advisor-kostnader](advisor-cost-recommendations.md).
* **Drifts kvalitet**: för att hjälpa dig att uppnå effektiv bearbetning och arbets flödes effektivitet, resurs hantering och bästa metoder för distribution. . Mer information finns i [rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md).

  ![Rekommendations typer för rådgivare](./media/advisor-overview/advisor-dashboard.png)

Du kan klicka på en kategori om du vill visa en lista över rekommendationer i den kategorin och välja en rekommendation för att lära dig mer om den.  Du kan också lära dig om åtgärder som du kan utföra för att dra nytta av en affärs möjlighet eller lösa ett problem.

![Rekommendations kategori för rådgivare](./media/advisor-overview/advisor-ha-category-example.png) 

Välj den rekommenderade åtgärden för en rekommendation att implementera rekommendationen.  Ett enkelt gränssnitt öppnas som gör att du kan implementera rekommendationen eller hänvisa till dokumentationen som underlättar implementeringen.  När du implementerar en rekommendation kan det ta upp till en dag för Advisor att känna igen detta.

Om du inte tänker vidta omedelbara åtgärder för en rekommendation kan du skjuta upp den under en angiven tids period eller stänga av den.  Om du inte vill få rekommendationer för en specifik prenumeration eller resurs grupp kan du konfigurera Advisor till att bara skapa rekommendationer för angivna prenumerationer och resurs grupper.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-access-advisor"></a>Hur gör jag för att Access Advisor?
Du kan komma åt Advisor via [Azure Portal](https://aka.ms/azureadvisordashboard). Logga in på [portalen](https://portal.azure.com), leta upp **Advisor** i navigerings menyn eller Sök efter den på menyn **alla tjänster** .

Du kan också Visa Advisor-rekommendationer via den virtuella datorns resurs gränssnitt. Välj en virtuell dator och bläddra sedan till Advisor-rekommendationer i menyn. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Vilka behörigheter behöver jag för att få åtkomst till Advisor?
 
Du kan komma åt Advisor-rekommendationer som *ägare*, *deltagare* eller *läsare* av en prenumeration, resurs grupp eller resurs.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Vilka resurser ger Advisor rekommendationer för?

Advisor ger rekommendationer för Application Gateway, App Services, tillgänglighets uppsättningar, Azure cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, offentliga Azure-IP-adresser, Azure Synapse-analys, SQL-servrar, lagrings konton, Traffic Manager profiler och virtuella datorer.

Azure Advisor innehåller även rekommendationer från [Azure Security Center](../security-center/security-center-recommendations.md) som kan innehålla rekommendationer för ytterligare resurs typer.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kan jag skjuta upp eller stänga av en rekommendation?

Klicka på **Skjut upp** länken om du vill skjuta upp eller stänga av en rekommendation. Du kan ange en skjutning-period eller välja att **aldrig** stänga av rekommendationen.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:

* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor-Poäng](azure-advisor-score.md)
* [Rekommendationer om Advisor-tillförlitlighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
