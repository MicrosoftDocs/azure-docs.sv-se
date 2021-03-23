---
title: Upptäck program varu inventering på lokala servrar med Azure Migrate
description: Lär dig hur du identifierar program varu inventering på lokala servrar med Azure Migrate identifiering och utvärdering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 47ea06fa2143f9a5dc5808ccb98fc80c87fefd93
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786710"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>Identifiera installerad program varu inventering och SQL Server instanser och databaser

I den här artikeln beskrivs hur du identifierar installerad program varu inventering och SQL Server instanser och databaser på servrar som kör i VMware-miljön med hjälp av Azure Migrate: identifierings-och utvärderings verktyg.

Genom att utföra program varu inventering kan du identifiera och skräddarsy en sökväg för migrering till Azure för dina arbets belastningar. Program varu inventeringen använder Azure Migrate-installationen för att utföra identifieringen med hjälp av autentiseringsuppgifter för servern. Den är helt agent lös – inga agenter är installerade på servrarna för att samla in dessa data.

> [!NOTE]
> Program varu inventering är för närvarande endast i för hands version för servrar som körs i VMware-miljön och är begränsat till enbart identifiering. För närvarande erbjuder vi inte programbaserad utvärdering.<br/> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat ett projekt](./create-manage-projects.md) med verktyget Azure Migrate: identifiering och bedömning har lagts till i det.
- Granska [VMware-kraven](migrate-support-matrix-vmware.md#vmware-requirements) för att utföra program varu inventering.
- Granska installations [kraven](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) innan du konfigurerar installationen.
- Granska [kraven för program identifiering](migrate-support-matrix-vmware.md#application-discovery-requirements) innan du initierar program varu inventering på servrar.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Distribuera och konfigurera Azure Migrate-enheten

1. [Granska](migrate-appliance.md#appliance---vmware) kraven för att distribuera Azure Migrate-enheten.
2. Granska de Azure-URL: er som krävs för att få åtkomst till [molnet](migrate-appliance.md#government-cloud-urls) [offentliga](migrate-appliance.md#public-cloud-urls) och myndigheter.
3. [Granska data](migrate-appliance.md#collected-data---vmware) som samlas in under identifiering och bedömning.
4. [Antecknings](migrate-support-matrix-vmware.md#port-access-requirements) portens åtkomst krav för produkten.
5. [Distribuera Azure Migrate-apparaten](how-to-set-up-appliance-vmware.md) för att starta identifieringen. För att distribuera installationen kan du hämta och importera en ägg-mall till VMware för att skapa en server som körs i din vCenter Server. När du har distribuerat enheten måste du registrera den med projektet och konfigurera den för att initiera identifieringen.
6. När du konfigurerar installationen måste du ange följande i Konfigurations hanteraren för installationen:
    - Information om de vCenter Server som du vill ansluta till.
    - vCenter Server autentiseringsuppgifter som är begränsade för att identifiera servrarna i VMware-miljön.
    - Autentiseringsuppgifter för servern, som kan vara domän-/Windows-eller Linux-autentiseringsuppgifter (inte domän). [Lär dig mer](add-server-credentials.md) om hur du anger autentiseringsuppgifter och hur vi hanterar dem.

## <a name="verify-permissions"></a>Kontrollera behörigheter

- Du måste [skapa ett vCenter Server skrivskyddat konto](./tutorial-discover-vmware.md#prepare-vmware) för identifiering och utvärdering. Det skrivskyddade kontot måste ha behörighet för **Virtual Machines**  >  **gäst åtgärder** för att kunna samverka med servrarna för att utföra program varu inventering.
- Du kan lägga till flera domän-och icke-domänautentiseringsuppgifter (Windows/Linux)-autentiseringsuppgifter i Konfigurations hanteraren för program identifiering. Du behöver ett gäst användar konto för Windows-servrar och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla Linux-servrar. [Lär dig mer](add-server-credentials.md) om hur du anger autentiseringsuppgifter och hur vi hanterar dem.

### <a name="add-credentials-and-initiate-discovery"></a>Lägg till autentiseringsuppgifter och initiera identifiering

1. Öppna Konfigurations hanteraren för installationen, slutför de nödvändiga kontrollerna och registreringen av enheten.
2. Gå till panelen **Hantera autentiseringsuppgifter och identifierings källor** .
1.  I **steg 1: ange vCenter Server autentiseringsuppgifter** klickar du på **Lägg till autentiseringsuppgifter** för att ange autentiseringsuppgifter för det vCenter servers konto som ska användas av enheten för att identifiera servrar som kör på vCenter Server.
1. I **steg 2: ange vCenter Server information** klickar du på **Lägg till identifierings källa** för att välja det egna namnet för autentiseringsuppgifter i list rutan. Ange **IP-adressen/FQDN** för vCenter Server instans :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="panel 3 på installations programmet för vCenter Server information i Configuration Manager":::
1. I **steg 3: ange autentiseringsuppgifter för servern för att utföra program varu inventering, hantering av agenter och identifiering av SQL Server instanser och databaser**, klickar du på **Lägg till autentiseringsuppgifter** för att tillhandahålla flera autentiseringsuppgifter för att initiera program varu inventeringen.
1. Klicka på **Starta identifiering** för att starta vCenter Server identifiering.

 När vCenter Server identifieringen är klar initierar installationen identifieringen av installerade program, roller och funktioner (program varu inventering). Varaktigheten beror på antalet identifierade servrar. För 500-servrar tar det cirka en timme innan den identifierade inventeringen visas i Azure Migrate portalen.

## <a name="review-and-export-the-inventory"></a>Granska och exportera inventeringen

När program varu inventeringen har slutförts kan du granska och exportera inventeringen i Azure Portal.

1. I **Azure Migrate-Windows-, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och utvärdering**, klicka på det visade antalet för att öppna sidan **identifierade servrar** .

    > [!NOTE]
    > I det här skedet kan du också aktivera beroende analys för de identifierade servrarna, så att du kan visualisera beroenden mellan servrar som du vill utvärdera. [Läs mer](concepts-dependency-visualization.md) om beroende analys.

2. I kolumnen **program varu inventering** klickar du på det visade antalet för att granska de identifierade programmen, rollerna och funktionerna.
4. Exportera inventeringen genom att klicka på **Exportera app Inventory** i **identifierade servrar**.

Program varu inventeringen exporteras och hämtas i Excel-format. Inventerings bladet för **program vara** visar alla appar som identifierats på alla servrar.

## <a name="discover-sql-server-instances-and-databases"></a>Identifiera SQL Server instanser och databaser

- Program varu inventeringen identifierar också SQL Server instanser som körs i VMware-miljön.
- Om du inte har angett Windows-autentisering eller SQL Server autentiseringsuppgifter för Konfigurations hanteraren för installationen lägger du till autentiseringsuppgifterna så att enheten kan använda dem för att ansluta till respektive SQL Server-instanser.

När enheten är ansluten samlar den in konfigurations-och prestanda data för SQL Server instanser och databaser. SQL Server konfigurations data uppdateras en gång var 24: e timme och prestanda data samlas in var 30: e sekund. Därför kan alla ändringar i egenskaperna för SQL Server-instansen och databaser, till exempel databas status, kompatibilitetsnivå osv. ta upp till 24 timmar att uppdatera på portalen.

## <a name="next-steps"></a>Nästa steg

- [Skapa en utvärdering](how-to-create-assessment.md) för identifierade servrar.
- [Utvärdera SQL-servrar](./tutorial-assess-sql.md) för migrering till Azure SQL.
