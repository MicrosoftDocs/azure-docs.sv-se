---
title: Vägledning om haveri beredskap för Azure Data Lake Analytics
description: Lär dig hur du planerar haveri beredskap för dina Azure Data Lake Analytics-konton.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220983"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Vägledning om haveri beredskap för Azure Data Lake Analytics

Azure Data Lake Analytics är en tjänst på begäran som gör det enklare att analysera stordata. I stället för att distribuera, konfigurera och justera maskinvara, kan du skriva frågor genom vilka du kan omvandla dina data och extrahera värdefulla insikter. Med Analytics-tjänsten kan du hantera jobb av alla skalor direkt bara genom att ange hur mycket kraft du behöver. Du betalar endast för jobbet när tjänsten körs vilket gör den kostnadseffektiv. Den här artikeln innehåller vägledning om hur du skyddar dina jobb från ovanliga avbrott i regioner eller oavsiktliga borttagningar.

## <a name="disaster-recovery-guidance"></a>Vägledning om haveriberedskap

När du använder Azure Data Lake Analytics är det viktigt att du förbereder din egen katastrof återställnings plan. Den här artikeln hjälper dig att skapa en katastrof återställnings plan. Det finns ytterligare resurser som kan hjälpa dig att skapa en egen plan:
- [Fel- och haveriberedskap för Azure-program](/azure/architecture/reliability/disaster-recovery)
- [Azure-återhämtning, tekniska riktlinjer](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Bästa praxis och scenario vägledning

Du kan köra ett återkommande U-SQL-jobb i ett ADLA-konto i en region som läser och skriver U-SQL-tabeller samt ostrukturerade data.  Förbered för en katastrof genom att utföra följande steg:

1. Skapa ADLA-och ADLS-konton i den sekundära regionen som ska användas under ett avbrott.

   > [!NOTE]
   > Eftersom konto namn är globalt unika använder du ett konsekvent namngivnings schema som visar vilket konto som är sekundärt.

2. För ostrukturerade data, referens [vägledning för katastrof återställning för data i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. För strukturerade data som lagras i ADLA-tabeller och databaser skapar du kopior av metadata-artefakter, till exempel databaser, tabeller, tabell värdes funktioner och sammansättningar. Du måste regelbundet synkronisera om dessa artefakter när ändringar sker i produktionen. Till exempel måste nyligen infogade data replikeras till den sekundära regionen genom att kopiera data och infoga dem i den sekundära tabellen.

   > [!NOTE]
   > Dessa objekt namn är begränsade till det sekundära kontot och är inte globalt unika, så de kan ha samma namn som i det primära produktions kontot.

Under ett avbrott måste du uppdatera skripten så att de angivna Sök vägarna pekar på den sekundära slut punkten. Användarna skickar sedan sina jobb till ADLA-kontot i den sekundära regionen. Utdata från jobbet kommer sedan att skrivas till ADLA-och ADLS-kontot i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

[Vägledning om haveri beredskap för data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
