---
title: Bygg en migrations plan med Azure Migrate
description: Ger vägledning om hur du skapar en migrations plan med Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ef916e0e8b32c96382a731d4a307e2b2a98ba1ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753866"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Skapa migreringsplan med Azure Migrate

Följ den här artikeln för att bygga din migrerings plan till Azure med [Azure Migrate](migrate-services-overview.md). 

## <a name="define-cloud-migration-goals"></a>Definiera mål för moln migrering

Innan du börjar, kan det vara bra att förstå och utvärdera dina [motivation](/azure/cloud-adoption-framework/strategy/motivations) vad gäller att flytta till molnet. Som förklaras i [moln införande ramverket](/azure/cloud-adoption-framework)finns det ett antal utlösare och resultat.   

**Affärs händelse** | **Migrerings resultat**
--- | ---
Avsluta data Center | Cost 
Sammanslagning, förvärv eller Divestiture | Minskning av leverantör/teknisk komplexitet
Minskning av kapital kostnader | Optimering av interna åtgärder
Slut på support för verksamhets kritiska tekniker | Öka flexibiliteten i verksamheten
Svar på ändringar i regelefterlevnad | Förberedelser för nya tekniska funktioner
Nya krav för data suveränitet | Skalning för att uppfylla kraven på marknaden
Minskning av störningar och förbättringar av IT-stabiliteten | Skalning för att uppfylla geografiska krav

Genom att identifiera dina motivation kan du fästa dina mål för strategisk migrering. Nästa steg är att identifiera och planera en sökväg för migrering som är skräddarsydd för dina arbets belastningar. Verktyget [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool) hjälper dig att utvärdera lokala arbets belastningar och ger vägledning och verktyg som hjälper dig att migrera.

## <a name="understand-your-digital-estate"></a>Förstå din digitala egendom

Börja med att identifiera din lokala infrastruktur, dina program och beroenden. På så sätt kan du identifiera arbets belastningar för migrering till Azure och samla in optimerade kostnads projektioner. Verktyget Server utvärdering hjälper dig att identifiera de arbets belastningar som används, beroenden mellan arbets belastningar och optimering av arbets belastning.

### <a name="workloads-in-use"></a>Arbets belastningar som används

Azure Migrate använder en förenklad Azure Migrate-enhet för att utföra identifiering av virtuella VMware-datorer lokalt, virtuella Hyper-V-datorer, andra virtualiserade datorer och fysiska servrar. Kontinuerlig identifiering samlar in dator konfigurations information och prestanda-metadata, samt program data. Så här samlar du in enheten från lokala datorer: 

- Dator-, disk-och NIC-metadata.

- Installerade program, roller och funktioner.

- Prestanda data, inklusive processor-och minnes användning, disk-IOPS och data flöde.

När du har samlat in data kan du exportera program inventerings listan för att hitta appar och SQL Server instanser som körs på datorerna. Du kan använda verktyget Azure Migrate: databas utvärdering för att förstå SQL Server beredskap.

 ![Program inventering på Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export av program inventering](./media/concepts-migration-planning/application-inventory-export.png)

Tillsammans med data som identifierats med verktyget för Server utvärdering kan du använda dina CMDB-data (Configuration Management Database) för att bygga en vy över din server och databas egendom och för att förstå hur dina servrar distribueras mellan affär senheter, program ägare, geografiska områden osv. Detta hjälper dig att avgöra vilka arbets belastningar som ska prioriteras för migrering. 

### <a name="dependencies-between-workloads"></a>Beroenden mellan arbets belastningar

Efter server identifiering kan du [analysera beroenden](concepts-dependency-visualization.md), visualisera och identifiera relationer mellan servrar och optimerings strategier för att flytta beroende servrar till Azure. Visualiseringen hjälper till att förstå om vissa datorer används eller om de kan tas ur bruk, i stället för att migreras.  Att analysera beroenden hjälper till att se till att ingenting ligger kvar och att oväntade avbrott under migreringen. Med program inventeringen och beroende analysen kan du skapa grupper med hög exakthet och börja utvärdera dem.

 ![Beroendemappning](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Optimering och storleks ändring

Azure ger flexibilitet att ändra storlek på moln kapaciteten med tiden och migreringen ger möjlighet att optimera de processor-och minnes resurser som allokeras till dina servrar. Att skapa en utvärdering på servrar som du har identiteten hjälper dig att förstå din arbets belastnings prestanda historik. Detta är viktigt för rätt storlek på Azure VM SKU: er och disk rekommendationer i Azure.

## <a name="assess-migration-readiness"></a>Utvärdera beredskap för migreringen


### <a name="readinesssuitability-analysis"></a>Beredskap/lämplighet analys

Du kan exportera utvärderings rapporten och filtrera på dessa kategorier för att förstå Azure readiness:

- **Redo för Azure**: datorer kan migreras som de är till Azure, utan några ändringar. 
- **Villkorligt redo för Azure**: datorer kan migreras till Azure, men behöver mindre ändringar, i enlighet med den reparations vägledning som finns i utvärderingen.
- **Inte redo för Azure**: datorer kan inte migreras till Azure som de är. Problem måste åtgärdas i enlighet med reparations vägledningen innan migreringen. 
- **Beredskap okänd**: Azure Migrate kan inte fastställa maskin beredskap på grund av otillräckliga metadata.

Med hjälp av databas utvärderingar kan du utvärdera beredskap för din SQL Server datafastighet för migrering till Azure SQL Database eller Azure SQL-hanterade instanser. Utvärderingen visar status procent för migrations beredskap för var och en av SQL Server-instanserna. För varje instans kan du dessutom se det rekommenderade målet i Azure, potentiella migreringsåtgärder, ett antal avbrytande ändringar, beredskap för Azure SQL DB eller Azure SQL VM och en kompatibilitetsnivå. Du kan gå djupare för att förstå effekterna av migrations-Blocker och rekommendationer för att åtgärda dem.

 ![Databas utvärderingar](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Storleks rekommendationer

När en dator har marker ATS som klar för Azure, gör Server utvärderingen en storleks rekommendation som identifierar den virtuella Azure-SKU: n och disk typen för dina datorer. Du kan få storleks rekommendationer baserat på prestanda historik (för att optimera resurser när du migrerar) eller baserat på lokala dator inställningar utan prestanda historik. I en databas utvärdering kan du se rekommendationer för databas-SKU, pris nivå och beräknings nivå.  

### <a name="get-compute-costs"></a>Hämta beräknings kostnader

Med prestandabaserade storleks alternativ i Azure Migrate utvärderingar får du till gång till virtuella datorer med rätt storlek och bör användas som bästa praxis för att optimera arbets belastningar i Azure. Förutom höger storlek finns det några andra alternativ som gör det enklare att spara Azure-kostnader: 

- **Reserverade instanser**: med [reserverade instanser (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/)kan du avsevärt minska kostnaderna jämfört med [priset för betala per](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)användning.
- **Azure Hybrid-förmån**: med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)kan du hämta lokala Windows Server-licenser med aktiva Software Assurance-eller Linux-prenumerationer, till Azure och kombinera med alternativ för reserverade instanser.
- **Enterprise-avtal**: Azure [Enterprise Agreements (EA)](../cost-management-billing/manage/ea-portal-agreements.md) kan ge besparingar för Azure-prenumerationer och-tjänster.
- **Erbjudanden**: det finns flera [Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/). Till exempel [dev/test – betala per användning](https://azure.microsoft.com/pricing/dev-test/)eller [Enterprise dev/test erbjudande](https://azure.microsoft.com/offers/ms-azr-0148p/)för att tillhandahålla lägre priser för virtuella datorer med utveckling/testning
- **VM-drift tid**: du kan granska dagar per månad och timmar per dag då virtuella Azure-datorer körs. Om du stänger av datorer när de inte används kan du minska kostnaderna (gäller inte för RIs).
- **Mål region**: du kan skapa utvärderingar i olika regioner för att avgöra om migreringen till en särskild region kan vara mer kostnads effektiv. 

### <a name="visualize-data"></a>Visualisera data

Du kan visa Server utvärderings rapporter (med Azure readiness information och kostnad för månatlig distribution) i portalen. Du kan också exportera utvärderingen och utöka migrerings planen med ytterligare visualiseringar. Du kan skapa flera utvärderingar med olika kombinationer av egenskaper och välja de egenskaper som passar bäst för ditt företag.  

 ![Översikt över utvärderingar](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Utvärdera luckor/Blocker

När du tar reda på vilka appar och arbets belastningar du vill migrera, identifierar du nedtidnas begränsningar för dem och letar efter eventuella drift beroenden mellan dina appar och den underliggande infrastrukturen. Med den här analysen kan du planera migreringar som uppfyller ditt återställnings tids mål (RTO) och se till att det är minimalt att förlora data. Innan du migrerar rekommenderar vi att du granskar och minimerar kompatibilitetsproblem, eller funktioner som inte stöds, som kan blockera server/SQL Database-migrering. Rapporten Azure Migrate Server utvärderings rapport och utvärdering av Azure Migrate-databasen kan hjälpa dig med detta. 

### <a name="prioritize-workloads"></a>Prioritera arbets belastningar

När du har samlat in information om inventeringen kan du identifiera vilka appar och arbets belastningar som ska migreras först. Utveckla en metod för att använda "tillämpa och lär" för att migrera appar på ett systematiskt och kontrollerbart sätt, så att du kan stryka ut eventuella fel innan du påbörjar en fullständig migrering.

Om du vill prioritera migreringsprocessen kan du använda strategiska faktorer som komplexitet, tid till migrering, affärs angelägenhets grad, produktions-och icke-produktionsled, efterlevnad, säkerhets krav, program kunskaper osv. 

Några rekommendationer:

- **Prioritera snabb WINS**: Använd utvärderings rapporterna för att identifiera låg hängande frukt, inklusive servrar och databaser som är helt klara och som kräver minimal ansträngning för att migrera till Azure. I tabellen sammanfattas några sätt att göra detta.

    **Tillstånd** | **Åtgärd**
    --- | ---
    **Azure-klara virtuella datorer** | Exportera utvärderings rapporten och filtrera alla datorer med tillstånd *redo för Azure*. Det kan vara den första gruppen av datorer som du lyfter och flyttar till Azure med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **Operativ system som stöder support** | Exportera utvärderings rapporten och filtrera alla datorer som kör Windows Server 2008 R2/Windows Server 2008. Dessa operativ system är i slutet av supporten och endast Azure tillhandahåller ett kostnads fritt tre års säkerhets uppdateringar när du migrerar dem till Azure. Om du kombinerar Azure Hybrid-förmån och använder Fjärrinstallationstjänster kan besparingarna vara mycket högre.
    **SQL Server migrering** | Använd rekommendationer för databas bedömning för att migrera databaser som är klara för Azure SQL Database med hjälp av Azure Migrate: Database Migration Tool. Migrera databaserna som är klara för virtuell Azure SQL-dator med hjälp av Azure Migrate: Migreringsverktyg för Server.
    **Support program från slut punkt** | Exportera program inventeringen och filtrera efter program vara/tillägg som kan nå support. Prioritera dessa program för migrering.
    **Under etablerade datorer** | Exportera utvärderings rapporten och filtrera efter datorer med låg processor användning (%) och minnes användning (%).  Migrera till en virtuell Azure-dator med rätt storlek och Spara på kostnader för underutnyttjade resurser.
    **Överetablerade datorer** | Exportera utvärderings rapporten och filtrera för datorer med hög processor användning (%) och minnes användning (%).  Lös kapacitets begränsningar, förhindra att de överbelastade datorerna bryts och öka prestanda genom att migrera datorerna till Azure. I Azure använder du funktioner för automatisk skalning för att möta efter frågan.<br/><br/> Analysera utvärderings rapporter för att undersöka lagrings begränsningar. Analysera disk-IOPS och data flöde och den rekommenderade disk typen.

- **Starta liten och gå sedan till stor**: börja med att flytta appar och arbets belastningar som presenterar minimal risk och komplexitet, för att skapa förtroende i din migrerings strategi. Analysera Azure Migrate bedömnings rekommendationer tillsammans med din CMDB-lagringsplats för att hitta och migrera arbets belastningar för utveckling/testning som kan vara kandidater för pilot-migreringar. Feedback och information från pilot-migreringar kan vara till hjälp när du börjar migrera produktions arbets belastningar.  
- I **enlighet** med Azure upprätthålls den största portföljen för efterlevnad i branschen, med avseende på bredd och djup för erbjudanden. Använd krav för efterlevnad för att prioritera migreringar, så att appar och arbets belastningar följer dina nationella, regionala och branschspecifika standarder och lagar. Detta gäller särskilt för organisationer som hanterar affärs kritiska processer, innehar känslig information eller som är i kraftigt reglerade branscher. I den här typen av organisationer, standarder och regler Abound, och kan ändras ofta, vara svåra att hålla sig uppdaterad.  

## <a name="finalize-the-migration-plan"></a>Slutför migrations planen

Innan du Slutför migreringen bör du kontrol lera att du tar hänsyn till och minimerar andra potentiella block, enligt följande: 

- **Nätverks krav**: utvärdera begränsningar för nätverkets bandbredd och latens, vilket kan orsaka oförutsedda fördröjningar och avbrott i hastigheten för migrering av replikering.
- **Testning/publicering efter migrering**: Tillåt att en tidsbuffert kan utföra testning av prestanda och användar godkännande för migrerade appar, eller konfigurera/anpassa appar efter migrering, till exempel uppdatering av databas anslutnings strängar, konfigurera webb servrar, utföra klipp över-och rensning osv.
- **Behörigheter**: granska rekommenderade Azure-behörigheter och åtkomst roller för Server/databas och behörigheter som krävs för migrering.
- **Utbildning**: Förbered din organisation för den digitala omvandlingen. En solid Training Foundation är viktig för lyckad organisations ändring. Ta en titt på kostnads fri utbildning på [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), inklusive kurser i Azure fundament ALS, lösnings arkitekturer och säkerhet. Uppmuntra ditt team att utforska [Azure-certifieringar](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF).  
- **Implementerings support**: få support för din implementering om du behöver det. Många organisationer väljer utanför hjälpen för att stödja sin migrering av molnet. För att snabbt och säkert flytta till Azure med personligt anpassad hjälp kan du överväga en [Azure expert-hanterad tjänst leverantör](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)eller [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Skapa en effektiv plan för molnbaserad migrering som innehåller detaljerad information om de appar som du vill migrera, program-/databas tillgänglighet, stillestånds gränser och migrerings mil stolpar. Planen förväntar sig hur lång tid data kopieringen tar och innehåller en realistisk buffert för testning av efter migreringen, och stycknings aktiviteter. 

En test plan efter migreringen bör omfatta funktioner för funktions-, integrerings-, säkerhets-och prestanda testning, för att säkerställa att migrerade appar fungerar som förväntat, och att alla databas objekt och data relationer överförs till molnet.  

Bygg en migrerings översikt och deklarera en underhålls period för att migrera dina appar och databaser med minimalt avbrotts tid och begränsa den potentiella driften och verksamheten under migreringen.  

## <a name="migrate"></a>Migrera

Vi rekommenderar att du kör en testmigrering i Azure Migrate innan du påbörjar en fullständig migrering. En testmigrering hjälper dig att uppskatta hur lång tid det tar och hur du kan justera migrerings planen. Det ger en möjlighet att identifiera eventuella problem och åtgärda dem före fullständig migrering.

När du är redo för migrering använder du verktyget Azure Migrate: Migreringsverktyg för Server och Azure Data Migration service (DMS) för en sömlös och integrerad migrering med spårning från slut punkt till slut punkt.

- Med Migreringsverktyg för Server kan du migrera lokala virtuella datorer och servrar, eller virtuella datorer som finns i andra privata eller offentliga moln (inklusive AWS, GCP) med ungefär noll stillestånds tid.
- Azure DMS tillhandahåller en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar, med minimal stillestånds tid.  

## <a name="next-steps"></a>Nästa steg

- Undersök [resan för migrering i molnet](/azure/architecture/cloud-adoption/getting-started/migrate)   i Azure Cloud adoption Framework.
- Få en [snabb översikt](migrate-services-overview.md) över Azure Migrate och titta på en [komma igång-video](https://youtu.be/wFfq3YPxYHE).
- Lär dig mer om att utvärdera virtuella datorer för migrering till [virtuella Azure-datorer](concepts-assessment-calculation.md).
