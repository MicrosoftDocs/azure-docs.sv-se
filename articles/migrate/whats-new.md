---
title: Vad är nytt i Azure Migrate
description: Lär dig mer om nya och senaste uppdateringar i Azure Migrate tjänsten.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: 3f49c0acc99f82cc2986ca896d40b3998cf47c5c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835010"
---
# <a name="whats-new-in-azure-migrate"></a>Vad är nytt i Azure Migrate

[Azure Migrate](migrate-services-overview.md) hjälper dig att identifiera, utvärdera och migrera lokala servrar, appar och data till Microsoft Azure molnet. Den här artikeln sammanfattar nya versioner och funktioner i Azure Migrate.

## <a name="update-march-2021"></a>Uppdatering (mars 2021)
- Stöd för att ange flera serverautentiseringsuppgifter på Azure Migrate för att identifiera installerade program (programvaruinventering), agentlös beroendeanalys och identifiera SQL Server instanser och databaser i din VMware-miljö. [Läs mer](tutorial-discover-vmware.md#provide-server-credentials)
- Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i förhandsversion. [Läs mer](concepts-azure-sql-assessment-calculation.md) Gå [självstudierna om identifiering](tutorial-discover-vmware.md) och [utvärdering](tutorial-assess-sql.md) för att komma igång.
- Agentlös VMware-migrering stöder nu samtidig replikering av 500 virtuella datorer per vCenter.

## <a name="update-january-2021"></a>Uppdatering (januari 2021)
-  Azure Migrate: Med servermigreringsverktyget kan du nu migrera virtuella VMware-datorer, fysiska servrar och virtuella datorer från andra moln till virtuella Azure-datorer med diskar krypterade med kryptering på serversidan med kund hanterade nycklar (CMK).

## <a name="update-december-2020"></a>Uppdatering (december 2020)
- Azure Migrate installerar nu automatiskt Azure VM-agenten på de virtuella VMware-datorerna när de migreras till Azure med hjälp av den agentlösa metoden för VMware-migrering. (Windows Server 2008 R2 och senare)
- Migrering av virtuella VMware-datorer till virtuella Azure-datorer med diskar som krypterats med hjälp av kryptering på serversidan (SSE) med kund hanterade nycklar (CMK) med hjälp av Azure Migrate Server Migration (agentlös replikering) är nu tillgänglig via Azure Portal.

## <a name="update-september-2020"></a>Uppdatering (september 2020)
- Migrering av servrar till Tillgänglighetszoner stöds nu.
- Migrering av UEFI-baserade virtuella datorer och fysiska servrar till virtuella Azure-datorer i generation 2 stöds nu. Med den här Azure Migrate: Server Migration-verktyget inte konverteringen från Gen 2 VM till Gen 1 VM under migreringen.
- Det finns en Azure Migrate Power BI instrumentpanel för utvärdering som hjälper dig att jämföra kostnader mellan olika utvärderingsinställningar. Instrumentpanelen levereras med ett PowerShell-verktyg som automatiskt skapar utvärderingar som ansluts till Power BI instrumentpanel. [Läs mer.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- Beroendeanalys (agentlös) kan nu köras samtidigt på 1 000 virtuella datorer.
- Beroendeanalys (agentlös) kan nu aktiveras eller inaktiveras i stor skala med hjälp av PowerShell-skript. [Läs mer.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Visualisera nätverksanslutningar i Power BI data som samlas in med beroendeanalys (agentlös) [Läs mer.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Migrering av virtuella VMware-datorer med en datadiskstorlek på upp till 32 TB stöds nu med hjälp av Azure Migrate: VMware-migreringsmetoden utan servermigrering.

## <a name="update-august-2020"></a>Uppdatering (augusti 2020)

- Förbättrad registrering där en Azure Migrate-projektnyckel genereras från portalen och används för att slutföra installationens registrering.
- Alternativ för att ladda ned antingen OVA-/VHD-filer eller installationsskript från portalen för att konfigurera VMware- respektive Hyper-V-apparaterna.
- Uppdaterad konfigurationshanterare för installationen med förbättrad användarupplevelse.
- Stöd för flera autentiseringsuppgifter för identifiering av virtuella Hyper-V-datorer.

## <a name="update-july-2020"></a>Uppdatering (juli 2020)

- Agentlös VMware-migrering stöder nu samtidig replikering av 300 virtuella datorer per vCenter

## <a name="update-june-2020"></a>Uppdatering (juni 2020)

- Utvärderingar för att migrera lokala virtuella VMware-datorer [till Azure VMware Solution (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) stöds nu. [Läs mer](how-to-create-azure-vmware-solution-assessment.md)
- Stöd för flera autentiseringsuppgifter på installationen för identifiering av fysisk server.
- Stöd för att tillåta Azure-inloggning från installation för klientorganisation där klientbegränsningar har konfigurerats.


## <a name="update-april-2020"></a>Uppdatering (april 2020)

Azure Migrate stöder distributioner i Azure Government. 

- Du kan identifiera och utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar.
- Du kan migrera virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar till Azure.
- För VMware-migrering kan du använda agentlös eller agentbaserad migrering. [Läs mer](server-migrate-overview.md).
- [Granska](migrate-support-matrix.md#supported-geographies-azure-government) de geografiska områden och regioner som stöds för Azure Government.
- [Agentbaserad beroendeanalys](concepts-dependency-visualization.md#agent-based-analysis) stöds inte i Azure Government.
- Funktioner i förhandsversionen stöds i Azure Government, särskilt [agentlös beroendeanalys](concepts-dependency-visualization.md#agentless-analysis)och [programidentifiering.](how-to-discover-applications.md)


## <a name="update-march-2020"></a>Uppdatering (mars 2020)

En skriptbaserad installation är nu tillgänglig för att konfigurera [Azure Migrate installation:](migrate-appliance.md)

- Den skriptbaserade installationen är ett alternativ till . Installation av OVA (VMware)/VHD (Hyper-V) av installationen.
- Den innehåller ett PowerShell-installationsskript som kan användas för att konfigurera installationen för VMware/Hyper-V på en befintlig dator som kör Windows Server 2016.

## <a name="update-november-2019"></a>Uppdatering (november 2019)

Ett antal nya funktioner har lagts till i Azure Migrate:

- **Utvärdering av fysisk server.** Utvärdering av lokala fysiska servrar stöds nu, utöver den fysiska servermigrering som redan stöds.
- **Importbaserad utvärdering**. Utvärdering av datorer med metadata och prestandadata som tillhandahålls i en CSV-fil stöds nu.
- **Programidentifiering:** Azure Migrate stöder nu identifiering på programnivå av appar, roller och funktioner med hjälp Azure Migrate programinstallationen. Detta stöds för närvarande endast för virtuella VMware-datorer och är begränsat till identifiering (utvärdering stöds inte för närvarande). [Läs mer](how-to-discover-applications.md)
- **Visualisering av agentlöst beroende:** Du behöver inte längre uttryckligen installera agenter för beroendevisualisering. Både agentlös och agentbaserad stöds nu.
- **Virtual Desktop:** Använd ISV-verktyg för att utvärdera och migrera lokal infrastruktur för virtuella skrivbord (VDI) till Windows Virtual Desktop i Azure.
- **Webbapp:** Den Azure App Service Migration Assistant, som används för att utvärdera och migrera webbappar, är nu integrerad i Azure Migrate.

Nya verktyg för utvärdering och migrering har lagts till i Azure Migrate:

- **RackWare:** Erbjuder molnmigrering.
- **Movere:** Utvärdering av erbjudanden.

[Läs mer](migrate-services-overview.md) om att använda verktyg och ISV-erbjudanden för utvärdering och migrering i Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrate aktuell version

Den aktuella versionen av Azure Migrate (släpptes i juli 2019) innehåller ett antal nya funktioner:

- **Enhetlig migreringsplattform:** Azure Migrate nu en enda portal för att centralisera, hantera och spåra din migreringsresa till Azure, med ett förbättrat distributionsflöde och en bättre portalupplevelse.
- **Utvärderings- och** migreringsverktyg: Azure Migrate tillhandahåller inbyggda verktyg och integrerar med andra Azure-tjänster, samt med oberoende programvaruleverantörer (ISV). [Läs mer om](migrate-services-overview.md#isv-integration) ISV-integrering.
- **Azure Migrate utvärdering:** Med Azure Migrate Server Assessment-verktyget kan du utvärdera virtuella VMware-datorer och virtuella Hyper-V-datorer för migrering till Azure. Du kan också utvärdera migrering med hjälp av andra Azure-tjänster och ISV-verktyg.
- **Azure Migrate** migrering: Med hjälp av verktyget Azure Migrate Server Migration kan du migrera lokala virtuella VMware-datorer och virtuella Hyper-V-datorer till Azure, samt fysiska servrar, andra virtualiserade servrar och virtuella datorer i privat/offentligt moln. Dessutom kan du migrera till Azure med hjälp av ISV-verktyg.
- **Azure Migrate installation:** Azure Migrate en förenklad installation för identifiering och utvärdering av lokala virtuella VMware-datorer och virtuella Hyper-V-datorer.
    - Den här installationen används av Azure Migrate Server Assessment och Azure Migrate Server Migration för agentlös migrering.
    - Installationen identifierar kontinuerligt servermetadata och prestandadata för utvärdering och migrering.  
- **VMware VM-migrering:** Azure Migrate Server Migration innehåller ett par metoder för att migrera lokala virtuella VMware-datorer till Azure.  En agentlös migrering med Azure Migrate installation och en agentbaserad migrering som använder en replikeringsinstallation och distribuerar en agent på varje virtuell dator som du vill migrera. [Läs mer](server-migrate-overview.md)
 - **Utvärdering och migrering av databaser:** Från Azure Migrate kan du utvärdera lokala databaser för migrering till Azure med hjälp av Azure Database Migration Assistant. Du kan migrera databaser med hjälp av Azure Database Migration Service.
- **Webbappsmigrering:** Du kan utvärdera webbappar med hjälp av en offentlig slutpunkts-URL med Azure App Service. För migrering av interna .NET-appar kan du ladda ned och köra App Service Migration Assistant.
- **Data Box-enhet:** Importera stora mängder offlinedata till Azure med hjälp av Azure Data Box i Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Migrate tidigare version

Om du använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte längre skapa nya Azure Migrate projekt eller utföra nya identifieringar. Du kan fortfarande komma åt befintliga projekt. Om du vill göra detta i Azure Portal > **Alla tjänster** söker du **efter Azure Migrate**. I Azure Migrate finns en länk för att komma åt gamla Azure Migrate projekt.



## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
- Prova våra självstudier för att utvärdera [virtuella VMware-datorer](./tutorial-assess-vmware-azure-vm.md) och [virtuella Hyper-V-datorer.](tutorial-assess-hyper-v.md)
