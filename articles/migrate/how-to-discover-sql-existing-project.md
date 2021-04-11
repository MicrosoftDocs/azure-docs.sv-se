---
title: Identifiera SQL Server instanser i ett befintligt Azure Migrate-projekt
description: Lär dig hur du identifierar SQL Server instanser i ett befintligt Azure Migrate-projekt.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567160"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Identifiera SQL Server instanser i ett befintligt projekt 

Den här artikeln beskriver hur du identifierar SQL Server instanser och databaser i ett [Azure Migrate](./migrate-services-overview.md) projekt som skapades innan du förhands granskningen av funktionen Azure SQL-bedömning.

Identifiering av SQL Server instanser och databaser som körs på lokala datorer hjälper till att identifiera och skräddarsy en migrerings Sök väg till Azure SQL. Azure Migrate-installationen utför den här identifieringen med hjälp av domänautentiseringsuppgifter eller SQL Server autentiseringsuppgifter som har åtkomst till de SQL Server instanser och databaser som körs på mål servrarna. Den här identifierings processen är agent lös, inget är installerat på mål servrarna.

## <a name="before-you-start"></a>Innan du börjar

- Kontrollera att du har:
    - Skapat ett [Azure Migrate-projekt](./create-manage-projects.md) före meddelande om SQL-utvärdering för din region
    - Lade till verktyget [Azure Migrate: identifiering och bedömning](./how-to-assess.md) i ett projekt
- Granska [support och krav för app-Discovery](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Kontrol lera att servrarna där du kör app-Discovery har PowerShell version 2,0 eller senare installerat och att VMware-verktyg (senare än 10.2.0) är installerat.
- Kontrol lera [kraven](./migrate-appliance.md) för att distribuera Azure Migrate-enheten.
- Kontrol lera att du har de [roller som krävs](./create-manage-projects.md#verify-permissions) i prenumerationen för att skapa resurser.
- Kontrol lera att enheten har åtkomst till Internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Aktivera identifiering av SQL Server instanser och databaser

1. I ditt Azure Migrate-projekt, antingen
    - Välj **inte aktive rad** på panelen hubb eller   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Azure MIGRATE hubb med SQL-identifiering inte aktive rad":::
    - Välj **inte aktive rad** på någon post på sidan Server identifiering under kolumnen SQL-instanser   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate bladet identifierade servrar med SQL-identifiering inte aktiverat":::
2. I identifiera SQL Server instanser och databaser följer du stegen som beskrivs nedan:
    - Välj **uppgradering** för att skapa den nödvändiga resursen.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Knapp för att uppgradera Azure Migrate-enheten":::
    - Kontrol lera att de tjänster som körs på installationen har uppdaterats till de senaste versionerna. Det gör du genom att starta Konfigurations hanteraren för installationen från din installations Server och välja Visa utrustnings tjänster från panelen installations krav.
        - Enheten och dess komponenter uppdateras automatiskt :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="kontrol lera version":::
    - I panelen Hantera autentiseringsuppgifter och identifierings källor i Konfigurations hanteraren för installationen lägger du till domän-eller SQL Server autentiseringsuppgifter för autentisering som har sysadmin-åtkomst på SQL Server-instansen och databaser som ska identifieras.
    Du kan använda antingen den automatiska funktionen för autentisering av autentiseringsuppgifter i installationen eller mappa autentiseringsuppgifterna manuellt till respektive server som marker ATS [här](./tutorial-discover-vmware.md#start-continuous-discovery).

    Några saker att tänka på:
    - Se till att program varu inventeringen redan är aktive rad eller ange domän-eller icke-domänautentiseringsuppgifter för att aktivera samma. Program varu inventering måste utföras för att identifiera SQL Server instanser.
    - Installations programmet försöker verifiera domänautentiseringsuppgifter med AD, när de läggs till. Se till att enhets servern har nätverks ledningen på den AD-server som är associerad med autentiseringsuppgifterna. Autentiseringsuppgifterna som är associerade med SQL Server autentisering har inte verifierats.

3. När önskade autentiseringsuppgifter har lagts till väljer du starta identifiering för att starta sökningen.

> [!Note]
>Tillåt att SQL-identifiering körs för någon gång innan du skapar utvärderingar för Azure SQL. Om identifieringen av SQL Server instanser och databaser inte kan slutföras markeras respektive instans som **okänd** i utvärderings rapporten.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar en [Azure SQL-utvärdering](./how-to-create-azure-sql-assessment.md)
- Läs mer om [Azure SQL-utvärderingar](./concepts-azure-sql-assessment-calculation.md)