---
title: Självstudie för SaaS för flera innehavare
description: Etablera och katalogisera nya klienter med det fristående program mönstret
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793253"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Etablera och katalogisera nya klienter med hjälp av mönstret program per klient SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln beskriver etablering och katalogering av nya klienter med hjälp av SaaS-mönstret för fristående app per klient.
Den här artikeln innehåller två huvud delar:
* Konceptuell diskussion om etablering och katalogisera nya klienter
* En själv studie kurs som visar exempel på PowerShell-kod som utför etableringen och katalogiserar
    * Självstudien använder Wingtip ticks-exemplet SaaS-program, som är anpassat till den fristående appen per klient mönster.

## <a name="standalone-application-per-tenant-pattern"></a>Mönster för fristående program per klient organisation

Det fristående programmet per klient mönster är ett av flera mönster för SaaS-program för flera innehavare.  I det här mönstret tillhandahålls en fristående app för varje klient. Programmet omfattar komponenter på program nivå och en Azure SQL Database.  Varje klient program kan distribueras i leverantörens prenumeration.  Azure erbjuder också ett [program för hanterade program](../../azure-resource-manager/managed-applications/overview.md) där en app kan distribueras i en klients prenumeration och hanteras av leverantören på klient organisationens vägnar.

   ![app-per-klient mönster](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

När du distribuerar ett program för en klient, tillhandahålls appen och databasen i en ny resurs grupp som skapats för klienten.  Genom att använda separata resurs grupper isoleras varje klients program resurser och kan hanteras separat. I varje resurs grupp är varje program instans konfigurerad för att få åtkomst till motsvarande databas direkt.  Den här anslutnings modellen skiljer sig från andra mönster som använder en katalog för att hantera anslutningar mellan appen och databasen.  Och eftersom det inte finns någon resurs delning måste varje klient databas tillhandahållas med tillräckligt med resurser för att hantera den högsta belastningen. Det här mönstret används ofta för SaaS-program med färre klienter, där det finns en stark tonvikt på klient isoleringen och mindre tonvikt på resurs kostnader.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Använda en klient katalog med ett program per klient mönster

Även om varje klients app och databas är helt isolerade kan olika hanterings-och analys scenarier samverka mellan klienter.  Att använda en schema ändring för en ny version av programmet kräver till exempel ändringar i schemat för varje klient databas. Rapporter och analys scenarier kan också kräva åtkomst till alla klient databaser oavsett var de distribueras.

   ![Diagram som visar hur du använder en klient katalog med ett program per klient mönster.](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Klient katalogen innehåller en mappning mellan en klient-ID och en klient databas, vilket gör att en identifierare kan matchas mot en server och ett databas namn.  I Wingtip SaaS-appen beräknas klient-ID: t som en hash för klient namnet, även om andra scheman kan användas.  Även om fristående program inte behöver katalogen för att hantera anslutningar, kan katalogen användas för att begränsa andra åtgärder till en uppsättning klient databaser. Elastisk fråga kan till exempel använda katalogen för att fastställa den uppsättning databaser över vilka frågor distribueras för rapportering mellan klienter.

## <a name="elastic-database-client-library"></a>Elastic Database klient bibliotek

I Wingtip-exempelprogrammet implementeras katalogen av Shard-hanterings funktionerna i [Elastic Database klient bibliotek](elastic-database-client-library.md) (EDCL).  Biblioteket gör det möjligt för ett program att skapa, hantera och använda en Shard-karta som lagras i en databas. I exempel på Wingtip-biljetter lagras katalogen i *klient katalog* databasen.  Shard mappar en klient nyckel till Shard (databasen) där klientens data lagras.  EDCL Functions hanterar en *Global Shard-mappning* som lagras i tabeller i *klient katalog* databasen och en *lokal Shard-mappning* som lagras i varje Shard.

EDCL-funktioner kan anropas från program eller PowerShell-skript för att skapa och hantera posterna i Shard-kartan. Andra EDCL-funktioner kan användas för att hämta en uppsättning Shards eller ansluta till rätt databas för den angivna klient nyckeln.

> [!IMPORTANT]
> Redigera inte data i katalog databasen eller den lokala Shard-kartan i klient databaserna direkt. Direkta uppdateringar stöds inte på grund av hög risk för skadade data. Redigera i stället mappnings data med EDCL-API: er.

## <a name="tenant-provisioning"></a>Etablering av klient organisation

Varje klient kräver en ny Azure-resurs grupp som måste skapas innan resurser kan tillhandahållas i den. När resurs gruppen finns kan du använda en Azure Resource Management-mall för att distribuera program komponenterna och-databasen och sedan konfigurera databas anslutningen. För att initiera databasschemat kan mallen importera en BACPAC-fil.  Du kan också skapa databasen som en kopia av en mall-databas.  Databasen uppdateras sedan ytterligare med inledande plats data och registreras i katalogen.

## <a name="tutorial"></a>Självstudie

I den här guiden får du lära du dig hur man:

* Etablera en katalog
* Registrera de exempel på klient databaser som du distribuerade tidigare i katalogen
* Etablera en ytterligare klient och registrera den i katalogen

En Azure Resource Manager mall används för att distribuera och konfigurera programmet, skapa klient databasen och sedan importera en BACPAC-fil för att initiera den. Import förfrågan kan placeras i kö i flera minuter innan den åtgärdas.

I slutet av den här självstudien har du en uppsättning fristående klient program, där varje databas är registrerad i katalogen.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)
* De tre exempel klient programmen distribueras. Om du vill distribuera dessa appar på mindre än fem minuter kan du läsa [distribuera och utforska Wingtip Ticket SaaS fristående program mönster](./saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Etablera katalogen

I den här uppgiften får du lära dig hur du etablerar katalogen som används för att registrera alla klient databaser. Du kommer att:

* **Etablera katalog databasen** med en Azure-resurs hanterings mall. Databasen initieras genom att importera en BACPAC-fil.
* **Registrera de exempel på klient program** som du distribuerade tidigare.  Varje klient har registrerats med hjälp av en nyckel som skapats från en hash av klientens namn.  Klient namnet lagras också i en tilläggs tabell i katalogen.

1. I PowerShell ISE öppnar du *. ..\Learning Modules\UserConfig.PSM* och uppdaterar **\<user\>** värdet till det värde som du använde när du distribuerade de tre exempel programmen.  **Spara filen**.
1. I PowerShell ISE öppnar du *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och anger **$Scenario = 1**. Distribuera klient katalogen och registrera de fördefinierade klient organisationerna.

1. Lägg till en Bryt punkt genom att placera markören var som helst på den rad som står, `& $PSScriptRoot\New-Catalog.ps1` och tryck sedan på **F9**.

    ![Ange en Bryt punkt för spårning](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Kör skriptet genom att trycka på **F5**.
1.  När skript körningen stoppas vid Bryt punkten trycker du på **F11** för att stega in i New-Catalog.ps1-skriptet.
1.  Spåra skript körningen med hjälp av meny alternativen för fel sökning, F10 och F11, för att gå över eller till anropade funktioner.
    *   Mer information om hur du felsöker PowerShell-skript finns i [tips om att arbeta med och felsöka PowerShell-skript](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

När skriptet har slutförts kommer katalogen att finnas och alla exempel klienter kommer att registreras.

Titta nu på de resurser som du har skapat.

1. Öppna [Azure Portal](https://portal.azure.com/) och bläddra bland resurs grupperna.  Öppna resurs gruppen **Wingtip-sa-catalog \<user\>** och anteckna katalog servern och databasen.
1. Öppna databasen i portalen och välj *Data Explorer* på menyn till vänster.  Klicka på inloggnings kommandot och ange sedan lösen ordet = **P \@ ssword1**.


1. Utforska schemat för *tenantcatalog* -databasen.
   * Alla objekt i `__ShardManagement` schemat tillhandahålls av Elastic Database klient biblioteket.
   * `Tenants`Tabellen och `TenantsExtended` vyn är tillägg som läggs till i exemplet som visar hur du kan utöka katalogen för att ge ytterligare värde.
1. Kör frågan, `SELECT * FROM dbo.TenantsExtended` .

   ![data Utforskaren](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Som ett alternativ till att använda Datautforskaren kan du ansluta till databasen från SQL Server Management Studio. Det gör du genom att ansluta till servern Wingtip-


    Observera att du inte bör redigera data direkt i katalogen – Använd alltid Shard Management-API: er.

## <a name="provision-a-new-tenant-application"></a>Etablera ett nytt klient program

I den här uppgiften får du lära dig hur du etablerar ett enda klient program. Du kommer att:

* **Skapa en ny resurs grupp** för klient organisationen.
* **Etablera programmet och databasen** i den nya resurs gruppen med hjälp av en Azure-mall för resurs hantering.  Den här åtgärden inkluderar att initiera databasen med vanliga schema-och referens data genom att importera en BACPAC-fil.
* **Initiera databasen med grundläggande klient information**. Den här åtgärden inkluderar att ange platstyp, som avgör vilket fotografi som används som bakgrund på dess händelse webbplats.
* **Registrera databasen i katalog databasen**.

1. I PowerShell ISE öppnar du *. ..\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och anger **$Scenario = 2**. Distribuera klient katalogen och registrera de fördefinierade klient organisationerna

1. Lägg till en Bryt punkt i skriptet genom att placera markören var som helst på rad 49 som står, `& $PSScriptRoot\New-TenantApp.ps1` och tryck sedan på **F9**.
1. Kör skriptet genom att trycka på **F5**.
1.  När skript körningen stoppas vid Bryt punkten trycker du på **F11** för att stega in i New-Catalog.ps1-skriptet.
1.  Spåra skript körningen med hjälp av meny alternativen för fel sökning, F10 och F11, för att gå över eller till anropade funktioner.

När klienten har etablerats öppnas den nya klientens händelse webbplats.

   ![röd och en tävling](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Du kan sedan granska de nya resurserna som skapats i Azure Portal.

   ![röda, tävlings resurser](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Om du vill stoppa faktureringen tar du bort resurs grupper

När du är färdig med att utforska exemplet tar du bort alla resurs grupper som du har skapat för att stoppa den associerade faktureringen.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om SaaS databas program för flera innehavare finns i [design mönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip Ticket SaaS standalone-programmet.
> * Om de servrar och databaser som utgör appen.
> * Så här tar du bort exempel resurser för att stoppa relaterad fakturering.

Du kan utforska hur katalogen används för att stödja olika scenarier för flera innehavare med hjälp av en databas per klient version av [Wingtip Ticket SaaS-program](./saas-dbpertenant-wingtip-app-overview.md).