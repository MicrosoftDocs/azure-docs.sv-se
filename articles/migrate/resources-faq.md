---
title: Azure Migrate vanliga frågor och svar
description: Få svar på vanliga frågor om tjänsten Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a5db000ad76f23be60d279923a590204c2fcf5e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377388"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: vanliga frågor

I den här artikeln besvaras vanliga frågor om Azure Migrate. Om du har frågor efter att du har läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum). Du kan också granska de här artiklarna:

- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [identifiering, utvärdering och beroende visualisering](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Vad är Azure Migrate?

Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata och offentliga virtuella moln till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering av ISV-erbjudanden från tredje part. [Läs mer](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Vad kan jag göra med Azure Migrate?

Använd Azure Migrate för att identifiera, utvärdera och migrera lokal infrastruktur, program och data till Azure. Azure Migrate stöder utvärdering och migrering av lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, andra virtualiserade virtuella datorer, databaser, webb program och virtuella skriv bord. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Vad är skillnaden mellan Azure Migrate och Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) tillhandahåller en centraliserad hubb för utvärdering och migrering till Azure. 

- Användning av Azure Migrate ger möjlighet till samverkan och framtida utöknings barhet med Azure Migrate verktyg, andra Azure-tjänster och verktyg från tredje part.
- Verktyget Azure Migrate: Migreringsverktyg för Server är syftet-byggt för Server migrering till Azure. Den är optimerad för migrering. Du behöver inte lära dig mer om begrepp och scenarier som inte är direkt relevanta för migrering. 
- Det finns inga avgifter för verktygs användning för migrering i 180 dagar, från tidpunkten då replikeringen startades för en virtuell dator. Du får tid att slutföra migreringen. Du betalar bara för de lagrings-och nätverks resurser som används i replikeringen och för de beräknings avgifter som förbrukas under testmigreringar.
- Azure Migrate stöder alla migrerings scenarier som stöds av Site Recovery. För virtuella VMware-datorer tillhandahåller Azure Migrate också ett alternativ för att migrera utan agent.
- Vi prioriterar nya funktioner för migrering för Azure Migrate: Migreringsverktyg för Server. Dessa funktioner är inte riktade för Site Recovery.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) bör endast användas för haveri beredskap.

Verktyget Azure Migrate: Migreringsverktyg använder vissa backend-Site Recovery-funktioner för att lyfta och flytta migreringen av vissa lokala datorer.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Jag har ett projekt med den tidigare klassiska upplevelsen av Azure Migrate. Hur gör jag för att börja använda den nya versionen?

Den klassiska Azure Migrate tas ur bruk i feb 2024. Efter feb 2024 kommer den klassiska versionen av Azure Migrate inte längre att stödjas och lager-metadata i det klassiska projektet tas bort. Du kan inte uppgradera projekt eller komponenter i den tidigare versionen till den nya versionen. Du måste [skapa ett nytt Azure Migrate-projekt](create-manage-projects.md)och [lägga till verktyg för utvärdering och migrering](./create-manage-projects.md) i det. Använd självstudierna för att lära dig hur du använder verktyg för bedömning och migrering som är tillgängliga. Om du har en Log Analytics arbets yta som är kopplad till ett klassiskt projekt kan du koppla den till ett projekt med aktuell version när du har tagit bort det klassiska projektet.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan Azure Migrate: Server utvärdering och kart verktyg?

Server utvärdering ger en utvärdering för att hjälpa till med migreringen och utvärdering av arbets belastningar för migrering till Azure. [Verktyget Microsoft Assessment and Planning (kart)](https://www.microsoft.com/download/details.aspx?id=7826) hjälper till med andra uppgifter, inklusive migrerings planering för nyare versioner av Windows-klient-och serveroperativ system, samt spårning av program varu användning. Fortsätt att använda kart verktyget för dessa scenarier.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Vad är skillnaden mellan server utvärdering och Site Recovery distributions planeraren?

Server utvärdering är ett verktyg för migrerings planering. Site Recovery Deployment Planner är ett planerings verktyg för haveri beredskap.

Välj ditt verktyg baserat på vad du vill göra:

- **Planera lokal migrering till Azure**: om du planerar att migrera dina lokala servrar till Azure använder du Server bedömning för migrering. Server utvärderingen bedömer lokala arbets belastningar och ger vägledning och verktyg som hjälper dig att migrera. När migrerings planen är på plats kan du använda verktyg som Azure Migrate: Server migrering för att migrera datorerna till Azure.
- **Planera haveri beredskap till Azure**: om du planerar att konfigurera haveri beredskap från en lokal plats till Azure med Site Recovery använder du Site Recovery Deployment Planner. Distributions planeraren ger en djup, Site Recovery-bestämd utvärdering av din lokala miljö för katastrof återställning. Det ger rekommendationer relaterade till haveri beredskap, till exempel replikering och redundans.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hur fungerar server migreringen med Site Recovery?

- Om du använder Azure Migrate: Server-migrering för att utföra en *agent* lös migrering av lokala virtuella VMware-datorer är migreringen inbyggd för Azure Migrate och Site Recovery inte används.
- Om du använder Azure Migrate: Server migrering för att utföra en *agent-baserad* migrering av virtuella VMware-datorer, eller om du migrerar virtuella Hyper-V-datorer eller fysiska servrar, Azure Migrate: Server migrering använder Azure Site Recovery-replikeringstopologin.

## <a name="which-geographies-are-supported"></a>Vilka geografiska områden stöds?

Granska de geografiska områden som stöds för [offentliga moln](migrate-support-matrix.md#supported-geographies-public-cloud) och [myndighetsmoln](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Identifiera det verktyg som du behöver och Lägg sedan till verktyget i ett Azure Migrate-projekt. 

Så här lägger du till ett ISV-verktyg eller en driv kraft:

1. Kom igång genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version, i enlighet med verktygs principen. Licensiering för verktyg är i enlighet med ISV-eller-verktygets licensierings modell.
2. I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna och dokumentationen för verktyget för att ansluta verktyget till Azure Migrate.

Du kan spåra migreringens resa inifrån Azure Migrate-projektet, i Azure och i andra verktyg.

## <a name="how-do-i-delete-a-project"></a>Hur gör jag för att ta bort ett projekt?

Lär dig hur du [tar bort ett projekt](how-to-delete-project.md). 

## <a name="next-steps"></a>Nästa steg

Läs [Azure Migrate översikt](migrate-services-overview.md).