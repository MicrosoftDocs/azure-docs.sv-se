---
title: Data Lake Storage-och WANdisco LiveData-plattform för Azure (för hands version)
description: Migrera lokala Hadoop-data till Azure Data Lake Storage Gen2 med hjälp av WANdisco LiveData-plattformen för Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508937"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Uppfylla kraven för krav på migrering med WANdisco LiveData-plattformen för Azure (för hands version)

Migrera lokala Hadoop-data till Azure Data Lake Storage Gen2 med hjälp av [WANdisco livedata-plattformen för Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Den här plattformen eliminerar behovet av avbrott i programmet, tar bort risken för data förlust och säkerställer data konsekvens även om åtgärder fortsätter lokalt.  

> [!NOTE]
> WANdisco LiveData-plattformen för Azure finns i en offentlig för hands version. För regional tillgänglighet, se [regioner som stöds](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

Plattformen består av två tjänster: [livedata Migrator för Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) för att migrera aktivt använda data från lokala miljöer till Azure Storage och [livedata plan för Azure](https://www.wandisco.com/products/livedata-plane-for-azure) som säkerställer att alla ändrade data eller inmatnings data replikeras konsekvent. 

> [!div class="mx-imgBorder"]
> ![Bild av Live Data Platform-översikt](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Du kan hantera båda tjänsterna med hjälp av Azure Portal och Azure CLI, och båda följa samma fakturerings modell som du betalar per användning som alla andra Azure-tjänster. LiveData-plattformen för Azure-förbrukning visas på samma månatliga Azure-faktura och ger ett konsekvent och bekvämt sätt att spåra och övervaka din användning.

Till skillnad från migrering av data _offline_ genom [att kopiera statisk information till Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md), eller genom att använda Hadoop-verktyg som [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), kan du behålla hela driften av dina affärs system under en _online_ -migrering med WANdisco livedata för Azure. Håll dina stora data miljöer operativa även när du flyttar sina data till Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Viktiga funktioner i WANdisco LiveData-plattformen för Azure

[WANdisco livedata-plattformen för Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) använder sig av en unik lösnings motor för Wide Area Network-kapacitet för att uppnå data konsekvens och för att utföra datareplikering i stor skala medan program kan fortsätta att ändra data under replikeringen. <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

Viktiga funktioner i plattformen är följande:

- **Data konsekvens**: lös utmaningarna vid migrering av stora data volymer mellan miljöer och hålla dessa data konsekventa över lagrings system genom strömning, även om de befinner sig under kontinuerlig ändring. Använd WANdiscos unika, Wide Area Network-kapabla lösnings motor direkt i Azure för att uppnå data konsekvens och migrera data med konsekvens garantier i alla data ändringar.

- **Underhålls åtgärder**: eftersom program kan fortsätta att skapa, ändra, läsa och ta bort data under migreringen behöver du inte störa verksamheten eller använda ett avbrott-fönster bara för att migrera Big data till Azure. Fortsätt att använda program, analys infrastruktur, intag av jobb och annan bearbetning.

- **Validera resultat**: verifiering från slut punkt till slut punkt som dina data kan användas effektivt när du har migrerat till Azure kräver att du kör arbets belastningar för produktions program. Endast en LiveData-tjänst ger detta utan att vi introducerar risken för data avvikelser genom att upprätthålla data konsekvens oavsett om ändringen sker vid källan eller målet för migreringen. Testa och verifiera programmets beteende utan risk eller ändra till dina processer och system.

- **Minska komplexiteten**: eliminera behovet av att skapa och hantera schemalagda jobb för att kopiera data genom att migrera data via automatisering. Använd djupgående integrering med Azure som ett kontroll plan för att hantera och övervaka migreringens förlopp, inklusive selektiv datareplikering, Hive-metadata, data säkerhet och konfidentialitet.

- **Effektivitet**: underhåll högt data flöde och prestanda och skala enkelt till stora data volymer. Med kontroll över bandbredds förbrukningen kan du se till att du kan uppfylla dina mål för migrering utan att påverka andra system åtgärder.

## <a name="migrate-big-data-faster-without-risk"></a>Migrera Big data snabbare utan risk

Den första tjänsten av WANdisco LiveData-plattformen för Azure är [livedata Migrator för Azure](https://www.wandisco.com/products/livedata-migrator-for-azure). en lösning för att migrera aktivt använda data från lokala miljöer till Azure Storage. LiveData Migrator för Azure har tillhandahållits och hanteras helt från Azure Portal eller Azure CLI, och fungerar tillsammans med ditt Hadoop-kluster utan någon konfigurations ändring, program ändringar eller tjänsten startar om för att börja migrera data direkt.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator för Azure-arkitektur](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Stora data migreringar kan vara komplexa och utmanande. Att flytta petabyte av information utan att störa affärs åtgärder har varit omöjligt att uppnå med data kopierings teknik offline. [Livedata Migrator för Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) erbjuder enkel distribution och kan upprätta en livedata-tjänst med kontinuerlig datamigrering och replikering samtidigt som program läser, skriver och ändrar data som migreras.

Att utföra en migrering är lika enkelt som de här tre stegen:

1. Etablera LiveData Migrator-instansen från Azure Portal till ditt lokala Hadoop-kluster. Ingen kluster ändring eller nedtid krävs, och program kan fortsätta att köras.

   > [!div class="mx-imgBorder"]
   >![Skapa en LiveData Migrator-instans](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Definiera målets Azure Data Lake Storage Gen2-aktiverat lagrings konto.

   > [!div class="mx-imgBorder"]
   >![Skapa ett LiveData Migrator-mål](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Definiera platsen för de data som du vill migrera, till exempel: `/user/hive/warehouse` , och starta migreringen.

   > [!div class="mx-imgBorder"]
   > ![Skapa en LiveData Migrator-migrering](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Övervaka migreringens förlopp via Azures standard verktyg, inklusive Azure CLI och Azure Portal, och fortsätt att använda din lokala miljö i hela. Läs igenom de här [kraven](https://docs.wandisco.com/live-data-platform/docs/prereq/)innan du börjar.

## <a name="replicate-data-under-active-change"></a>Replikera data under aktiv ändring

Storskalig migrering av lokala data från sjöar till Azure behöver program testning och validering. Att kunna göra detta utan risk för att införa data ändringar som kommer att skapa flera källor av sanningen som inte är lätta att stämma av är avgörande för att eliminera risker och minimera kostnaden för att flytta till Azure. [Livedata-planet för Azure](https://www.wandisco.com/products/livedata-plane-for-azure) använder WANdiscos koordinations motor teknik för att undvika dessa problem.

> [!div class="mx-imgBorder"]
> ![LiveData-plan för Azure-arkitektur](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Se till att dina data är konsekventa i lokala Hadoop-kluster och Azure Storage med LiveData-planet för Azure efter den första migreringen:

1. Etablera LiveData-plan för Azure lokalt och i Azure, från Azure Portal. Inga program ändringar krävs.

2. Konfigurera regler för replikering som avser de data platser som du vill ska vara konsekventa, till exempel: `/user/contoso/sales/region/WA` .

3. Kör program som har åtkomst till och ändrar data på valfri plats som ett Hadoop-kompatibelt fil system som du behöver.

LiveData-planet för Azure ser till att dina data är konsekventa utan att det medför avsevärda kostnader för kluster drift eller program prestanda. Ändra eller mata in data medan alla ändringar replikeras konsekvent.

## <a name="next-steps"></a>Nästa steg

- [Livedata-plattformen för Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) för Azure används som alla andra Azure-resurser och är tillgänglig i för hands versionen nu. 

- Förstå [kraven](https://docs.wandisco.com/live-data-platform/docs/prereq/), Planera migreringen och slutför en storskalig migrering snabbt med livedata Migrator för Azure.

- Testa LiveData-Migrator utan att behöva ha ett lokalt Hadoop-kluster med hjälp av [HDFS-sandbox](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Se även

- [LiveData Migrator för Azure på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [LiveData-plan för Azure på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator för Azure-planer och-priser](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData plan för Azure-planer och-priser](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData-plattform för vanliga frågor och svar om Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Kända problem med LiveData-plattformen för Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Introduktion till Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)