---
title: Översikt över åtkomst kontroll för Synapse-arbetsyta
description: I den här artikeln beskrivs de mekanismer som används för att styra åtkomsten till en Synapse-arbetsyta och de resurser och de kod artefakter som den innehåller.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 30cc917e2db3a7c4c6d5d6ebd5a8a47afff5d505
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133148"
---
# <a name="synapse-access-control"></a>Synapse åtkomst kontroll 

Den här artikeln innehåller en översikt över de mekanismer som är tillgängliga för att kontrol lera åtkomsten till Synapse data bearbetnings resurser och data.  

## <a name="overview"></a>Översikt

Synapse tillhandahåller ett omfattande och detaljerade system för åtkomst kontroll som integrerar: 
- **Azure-roller** för resurs hantering och åtkomst till data i lagrings utrymmen, 
- **Synapse-roller** för att hantera Live-åtkomst till kod och körning, 
- **SQL-roller** för data Plans åtkomst till data i SQL-pooler och 
- **Git-behörigheter** för käll kods kontroll, inklusive kontinuerlig integrering och distributions support.  

Synapse-roller tillhandahåller uppsättningar med behörigheter som kan tillämpas på olika omfång. Den här precisionen gör det enkelt att ge rätt åtkomst till administratörer, utvecklare, säkerhets personal och operatörer för att beräkna resurser och data.

Åtkomst kontroll kan förenklas med hjälp av säkerhets grupper som är justerade med personers jobb roller. Du behöver bara lägga till och ta bort användare från lämpliga säkerhets grupper för att hantera åtkomst.

## <a name="access-control-elements"></a>Åtkomst kontroll element

### <a name="creating-and-managing-synapse-compute-resources"></a>Skapa och hantera Synapse-beräknings resurser

Azure-roller används för att styra hanteringen av: 
- Dedikerade SQL-pooler, 
- Apache Spark pooler och 
- Integrerings körningar. 

Om du vill *skapa* dessa resurser måste du vara en Azure-ägare eller deltagare i resurs gruppen. Om du vill *Hantera* dem när du har skapat måste du vara en Azure-ägare eller deltagare i antingen resurs gruppen eller enskilda resurser. 

### <a name="developing-and-executing-code-in-synapse"></a>Utveckla och köra kod i Synapse 

Synapse stöder två utvecklings modeller.

- **Synapse Live-utveckling**. Du kan utveckla och felsöka kod i Synapse Studio och sedan **publicera** den för att spara och köra.  Synapse-tjänsten är källan till sanningen för kod redigering och körning.  Alla opublicerade arbeten försvinner när du stänger Synapse Studio.  
- **Git-aktiverad utveckling**. Du utvecklar och felsöker kod i Synapse Studio och **genomför** ändringar i en fungerande gren av en git-lagrings platsen. Arbetet från en eller flera grenar integreras i en samarbets gren, från var du **publicerar** den till tjänsten. Git-lagrings platsen är källan till sanningen för kod redigering medan tjänsten är källan till sanningen för körning. Ändringar måste göras i git-lagrings platsen eller publiceras till tjänsten innan du stänger Synapse Studio. [Lär dig mer](../cicd/continuous-integration-deployment.md) om hur du använder Synapse Analytics med git.

I båda utvecklings modellerna kan alla användare med åtkomst till Synapse Studio skapa kod artefakter. Du behöver dock ytterligare behörigheter för att publicera artefakter till tjänsten, läsa publicerade artefakter, för att genomföra ändringar i git, för att köra kod och för att få åtkomst till länkade data som skyddas av autentiseringsuppgifter.

### <a name="synapse-roles"></a>Synapse-roller

Synapse-roller används för att styra åtkomsten till Synapse-tjänsten som gör det möjligt att: 
- Visa lista över publicerade kod artefakter, 
- Publicera kod artefakter, länkade tjänster och inloggnings definitioner,
- Köra kod eller pipeliner som använder Synapse-beräknings resurser,
- Kör kod eller pipeliner som har åtkomst till länkade data som skyddas av autentiseringsuppgifter
- Visa utdata som är associerade med publicerade kod artefakter,
- Övervaka status för beräknings resurser och Visa körnings loggar.

Synapse-roller kan tilldelas i området för arbets ytan eller i mer detaljerade omfång för att begränsa de behörigheter som beviljas vissa Synapse-resurser.

### <a name="git-permissions"></a>Git-behörigheter

När du använder git-aktiverad utveckling i git-läge, styr dina git-behörigheter om du kan läsa och bekräfta ändringar av kod artefakter, inklusive länkade tjänst-och inloggnings definitioner.   
   
### <a name="accessing-data-in-sql"></a>Åtkomst till data i SQL

När du arbetar med dedikerade och Server lös SQL-pooler styrs åtkomsten till data planet med hjälp av SQL-behörigheter. 

Skaparen av en arbets yta tilldelas som Active Directory administratör på arbets ytan. När den här rollen har skapats kan den tilldelas en annan användare eller en säkerhets grupp i Azure Portal.

**SQL-pooler utan server**: Synapse-administratörer beviljas `db_owner` ( `DBO` ) behörigheter för SQL-poolen utan server, inbyggd. Synapse-administratörer måste köra SQL-skript på varje pool utan server för att ge andra användare åtkomst till serverbaserade SQL-pooler.  

**Dedikerade SQL-pooler**: Active Directory administratörs behörighet beviljas till skaparen av arbets ytan och MSI-ytan för arbets ytan.  Behörighet att komma åt dedikerade SQL-pooler beviljas inte på annat sätt automatiskt. För att ge andra användare eller grupper åtkomst till dedikerade SQL-pooler måste Active Directory administratören köra SQL-skript mot varje dedikerad SQL-pool.

Se [hur du konfigurerar Synapse Access Control](./how-to-set-up-access-control.md) för SQL-skript för att bevilja SQL-behörigheter i SQL-pooler.  

 ### <a name="accessing-system-managed-data-in-storage"></a>Åtkomst till systemhanterade data i lagring

SQL-pooler utan server och Apache Spark tabeller lagrar sina data i en ADLS Gen2 behållare som är kopplad till arbets ytan. Apache Spark bibliotek för användare som har installerats hanteras också i samma lagrings konto. Om du vill aktivera dessa användnings fall måste användare och MSI-yta beviljas **Storage BLOB data Contributor** -åtkomst till den här arbets ytan ADLS Gen2 lagrings behållare.  

## <a name="using-security-groups-as-a-best-practice"></a>Använd säkerhets grupper som bästa praxis

För att förenkla hanteringen av åtkomst kontroll kan du använda säkerhets grupper för att tilldela roller till personer och grupper. Säkerhets grupper kan skapas för att spegla personer eller jobb funktioner i organisationen som behöver åtkomst till Synapse-resurser eller artefakter.  Dessa person-baserade säkerhets grupper kan sedan tilldelas en eller flera Azure-roller, Synapse-roller, SQL-behörigheter eller git-behörigheter. Med väl valda säkerhets grupper är det enkelt att tilldela en användare de nödvändiga behörigheterna genom att lägga till dem i lämplig säkerhets grupp. 

>[!Note]
>Om du använder säkerhets grupper för att hantera åtkomst sker ytterligare svars tid i Azure Active Directory innan ändringarna börjar gälla. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Åtkomst kontroll tvång i Synapse Studio

Synapse Studio fungerar annorlunda beroende på dina behörigheter och det aktuella läget:
- **Synapse live-läge:** Synapse Studio hindrar dig från att se publicerat innehåll, publicera innehåll eller vidta andra åtgärder om du inte har den behörighet som krävs.  I vissa fall kan du hindras från att skapa kod artefakter som du inte kan använda eller Spara. 
- **Git-läge:** Om du har git-behörigheter som gör att du kan genomföra ändringar i den aktuella grenen tillåts inchecknings åtgärden även om du inte har behörighet att publicera ändringar i Live-tjänsten.  

I vissa fall kan du skapa kod artefakter även utan behörighet att publicera eller bekräfta. På så sätt kan du köra kod (med nödvändiga körnings behörigheter). [Läs mer](./synapse-workspace-understand-what-role-you-need.md) om de roller som krävs för vanliga uppgifter. 

Om en funktion är inaktive rad i Synapse Studio visar en knapp Beskrivning den nödvändiga behörigheten. Använd [roll guiden SYNAPSE RBAC](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) för att se vilken roll som krävs för att ge den behörighet som saknas.


## <a name="next-steps"></a>Nästa steg

- Läs mer om [SYNAPSE RBAC](./synapse-workspace-synapse-rbac.md)
- Lär dig mer om [SYNAPSE RBAC-roller](./synapse-workspace-synapse-rbac-roles.md)
- Lär dig [hur du konfigurerar åtkomst kontroll](./how-to-set-up-access-control.md) för en Synapse-arbetsyta med hjälp av säkerhets grupper.
- Lär dig [hur du granskar SYNAPSE RBAC-roll tilldelningar](./how-to-review-synapse-rbac-role-assignments.md)
- Lär dig [hur du hanterar SYNAPSE RBAC-roll tilldelningar](./how-to-manage-synapse-rbac-role-assignments.md)
