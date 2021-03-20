---
title: Migrera data till Azure Cosmos DB SQL API-konto med Striims
description: Lär dig hur du använder Striims för att migrera data från en Oracle-databas till ett Azure Cosmos DB SQL API-konto.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 136853182e353ad5cd71981db5935fc3babe162e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339617"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrera data till Azure Cosmos DB SQL API-konto med Striims
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Striims-avbildningen på Azure Marketplace erbjuder kontinuerlig data förflyttning i real tid från informations lager och databaser till Azure. När du flyttar data kan du utföra en rad avnormalisering, data omvandling, aktivera real tids analys och data rapporterings scenarier. Det är enkelt att komma igång med Striims för att kontinuerligt flytta företags data till Azure Cosmos DB SQL API. Azure tillhandahåller ett Marketplace-erbjudande som gör det enkelt att distribuera Striims och migrera data till Azure Cosmos DB. 

Den här artikeln visar hur du använder Striims för att migrera data från en **Oracle-databas** till ett **Azure Cosmos DB SQL API-konto**.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en [Azure-prenumeration](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

* En Oracle-databas som körs lokalt med vissa data i den.

## <a name="deploy-the-striim-marketplace-solution"></a>Distribuera Striims Marketplace-lösningen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **skapa en resurs** och Sök efter **striims** på Azure Marketplace. Välj det första alternativet och **skapa**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Hitta Striims Marketplace-objekt":::

1. Ange sedan konfigurations egenskaperna för Striims-instansen. Striims-miljön har distribuerats på en virtuell dator. I fönstret **grundläggande** anger du **användar namnet för den virtuella datorn**, **lösen ord för virtuell dator** (detta lösen ord används för att använda SSH i den virtuella datorn). Välj din **prenumeration**, **resurs grupp** och **plats information** där du vill distribuera striims. När du är klar väljer du **OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Konfigurera grundläggande inställningar för Striims":::

1. I fönstret **kluster inställningar för striims** väljer du typ av striims-distribution och storlek på virtuell dator.

   |Inställning | Värde | Beskrivning |
   | ---| ---| ---|
   |Distributions typ för striims |Fristående | Striims kan köras i en **fristående** eller **kluster** distributions typ. Fristående läge distribuerar Striims-servern på en enskild virtuell dator och du kan välja storleken på de virtuella datorerna beroende på vilken data volym som används. Kluster läget distribuerar Striims-servern på två eller flera virtuella datorer med den valda storleken. Kluster miljöer med fler än två noder erbjuder automatisk hög tillgänglighet och redundans.</br></br> I den här självstudien kan du välja fristående alternativ. Använd standard storleken "Standard_F4s" för den virtuella datorn.  | 
   | Namnet på Striims-klustret|    <Striim_cluster_Name>|  Namnet på Striims-klustret.|
   | Striims kluster lösen ord|   <Striim_cluster_password>|  Lösen ord för klustret.|

   När du har fyllt formuläret väljer du **OK** för att fortsätta.

1. I fönstret **åtkomst inställningar för striims** konfigurerar du den **offentliga IP-adressen** (Välj standardvärden), **domän namn för striims**, **Administratörs lösen ord** som du vill använda för att logga in på striims-användargränssnittet. Konfigurera ett VNET och undernät (Välj standardvärden). När du har fyllt i informationen väljer du **OK** för att fortsätta.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Åtkomst inställningar för striims":::

1. Azure kommer att validera distributionen och se till att allt ser bra ut. Det tar några minuter att utföra verifieringen. När verifieringen är klar väljer du **OK**.
  
1. Granska slutligen användnings villkoren och välj **skapa** för att skapa din striims-instans. 

## <a name="configure-the-source-database"></a>Konfigurera käll databasen 

I det här avsnittet konfigurerar du Oracle-databasen som källa för data förflyttning.  Du behöver [Oracle JDBC-drivrutinen](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) för att ansluta till Oracle. Om du vill läsa ändringar från din käll-Oracle-databas kan du antingen använda [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) -eller [XStream-API: erna](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Oracle JDBC-drivrutinen måste finnas i Striimss Java-classpath för att läsa, skriva eller spara data från Oracle Database.

Ladda ned [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) -drivrutinen till din lokala dator. Du kommer att installera det i Striims-klustret senare.

## <a name="configure-the-target-database"></a>Konfigurera mål databasen

I det här avsnittet ska du konfigurera Azure Cosmos DB SQL API-konto som mål för data förflyttning.

1. Skapa ett [Azure Cosmos DB SQL API-konto](create-cosmosdb-resources-portal.md) med hjälp av Azure Portal.

1. Navigera till fönstret **datautforskaren** i ditt Azure Cosmos-konto. Välj **ny behållare** för att skapa en ny behållare. Anta att du migrerar *produkter* och *ordnar* data från Oracle Database till Azure Cosmos dB. Skapa en ny databas med namnet **StriimDemo** med en behållare med namnet **Orders**. Etablera behållaren med **1000 ru: er** (det här exemplet använder 1000 ru: er, men du bör använda data flödet som beräknas för din arbets belastning) och **/ORDER_ID** som partitionsnyckel. Dessa värden varierar beroende på dina källdata. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="Skapa ett SQL API-konto":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurera Oracle för att Azure Cosmos DB data flöde

1. Nu ska vi gå tillbaka till Striims. Innan du interagerar med Striims installerar du den Oracle JDBC-drivrutin som du laddade ned tidigare.

1. Navigera till Striims-instansen som du distribuerade i Azure Portal. Välj knappen **Anslut** i den övre meny raden och på fliken **SSH** kopierar du URL: en i **inloggning med fältet lokalt konto för virtuell dator** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Hämta SSH-URL: en":::

1. Öppna ett nytt terminalfönster och kör SSH-kommandot som du kopierade från Azure Portal. I den här artikeln används Terminal i en MacOS. du kan följa liknande instruktioner med hjälp av SparaTillFil eller en annan SSH-klient på en Windows-dator. När du uppmanas till det skriver du **Ja** för att fortsätta och ange **lösen ordet** som du har angett för den virtuella datorn i föregående steg.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Anslut till Striims VM":::

1. Öppna sedan en ny terminal-flik för att kopiera filen **ojdbc8. jar** som du laddade ned tidigare. Använd följande SCP-kommando för att kopiera jar-filen från den lokala datorn till mappen tmp i Striims-instansen som körs i Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Kopiera jar-filen från plats datorn till Striims":::

1. Gå sedan tillbaka till det fönster där du gjorde SSH till Striims-instansen och logga in som sudo. Flytta filen **ojdbc8. jar** från katalogen **katalogen/tmp** till **lib** -katalogen för striims-instansen med följande kommandon:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Flytta jar-filen till mappen lib":::


1. Starta om Striims-servern från samma terminalfönster genom att köra följande kommandon:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striims tar en minut att starta. Om du vill se statusen kör du följande kommando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Nu går du tillbaka till Azure och kopierar den offentliga IP-adressen för den virtuella Striims-datorn. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Kopiera Striims VM IP-adress":::

1. Om du vill navigera till Striims-webbgränssnittet öppnar du en ny flik i en webbläsare och kopierar den offentliga IP-adressen följt av: 9080. Logga in med **Administratörs** användar namnet, tillsammans med administratörs lösen ordet som du angav i Azure Portal.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Logga in på Striims":::

1. Nu kommer du till start sidan för Striims. Det finns tre olika fönster – **instrument paneler**, **appar** och **SourcePreview**. I fönstret instrument paneler kan du flytta data i real tid och visualisera dem. Fönstret appar innehåller pipelines för strömmande data eller data flöden. Till höger på sidan finns SourcePreview där du kan förhandsgranska dina data innan du flyttar dem.

1. Välj fönstret **appar** , vi fokuserar på det här fönstret för tillfället. Det finns en mängd olika exempel på appar som du kan använda för att lära dig mer om Striims, men i den här artikeln skapar du vår egen. Välj knappen **Lägg till app** i det övre högra hörnet.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Lägg till Striims-appen":::

1. Det finns flera olika sätt att skapa Striims-program. Välj **börja** med en mall för att börja med en befintlig mall.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Starta appen med mallen":::

1. I fältet **sökmall** skriver du "Cosmos" och väljer **mål: Azure Cosmos DB** och väljer sedan **Oracle CDC för att Azure Cosmos DB**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Välj Oracle CDC till Cosmos DB":::

1. På nästa sida namnger du ditt program. Du kan ange ett namn som **oraToCosmosDB** och sedan välja **Spara**.

1. Ange sedan käll konfigurationen för din käll-Oracle-instans. Ange ett värde för **källans namn**. Käll namnet är bara en namn konvention för Striims-programmet. du kan använda något som liknar **src_onPremOracle**. Ange värden för resten av käll parametrarna **URL**, **användar namn**, **lösen ord** och välj **LogMiner** som läsare för att läsa data från Oracle. Fortsätt genom att välja **Nästa**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Konfigurera käll parametrar":::

1. Striims kommer att kontrol lera din miljö och kontrol lera att den kan ansluta till din käll-Oracle-instans, har rätt behörigheter och att CDC har kon figurer ATS korrekt. När alla värden har verifierats väljer du **Nästa**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Verifiera käll parametrar":::

1. Välj de tabeller från Oracle-databasen som du vill migrera. Låt oss till exempel välja tabellen Orders och välj **Nästa**. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Välj käll tabeller":::

1. När du har valt käll tabellen kan du göra mer komplicerade åtgärder som mappning och filtrering. I det här fallet behöver du bara skapa en replik av käll tabellen i Azure Cosmos DB. Så väljer du **Nästa** för att konfigurera målet

1. Nu ska vi konfigurera målet:

   * **Målnamn** – ange ett eget namn för målet. 
   * **Mata in från** – från List rutan väljer du den indataströmmen som du skapade i käll-Oracle-konfigurationen. 
   * **Samlingar**– ange konfigurations egenskaperna för mål Azure Cosmos dB. Collection-syntaxen är **SourceSchema. SourceTable, TargetDatabase. TargetContainer**. I det här exemplet är värdet "SYSTEM. ORDER, StriimDemo. Orders ". 
   * **Accesskey** – PrimaryKey för ditt Azure Cosmos-konto.
   * **ServiceEndpoint** – URI för ditt Azure Cosmos-konto finns i avsnittet **nycklar** i Azure Portal. 

   Välj **Spara** och **Nästa**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Konfigurera mål parametrar":::


1. Sedan kommer du till Flow-designern där du kan dra och släppa från Box-kopplingarna för att skapa dina strömmande program. Du kommer inte att göra några ändringar i flödet i det här läget. gå vidare och distribuera programmet genom att välja knappen **Distribuera App** .
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Distribuera appen":::

1. I fönstret distribution kan du ange om du vill köra vissa delar av programmet på specifika delar av distributions miljön. Eftersom vi kör i en enkel distributions topologi via Azure använder vi standard alternativet.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Använd standard alternativet":::

1. När du har distribuerat kan du förhandsgranska data strömmen för att se data som passerar. Välj **våg** ikonen och ögonglobsikonen bredvid den. Välj knappen **distribuerad** i det övre meny fältet och välj **Starta App**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Starta appen":::

1. Genom att använda en CDC-läsare **(Change data Capture)** hämtar striims endast nya ändringar i databasen. Om du har data som flödar genom käll tabellerna visas den. Men eftersom det är en demonstrations tabell är källan inte ansluten till något program. Om du använder en exempel data Generator kan du infoga en händelse kedja i Oracle-databasen.

1. Du ser data som passerar genom Striims-plattformen. Striims hämtar även alla metadata som är kopplade till din tabell, vilket är till hjälp för att övervaka data och se till att data hamnar på rätt mål.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="Konfigurera CDC-pipeline":::

1. Slutligen ska vi logga in på Azure och navigera till ditt Azure Cosmos-konto. Uppdatera Datautforskaren och du kan se att data har anlänt.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Verifiera migrerade data i Azure":::

Genom att använda Striims-lösningen i Azure kan du kontinuerligt migrera data till Azure Cosmos DB från olika källor som Oracle, Cassandra, MongoDB och flera andra att Azure Cosmos DB. Om du vill veta mer går du till [striims-webbplatsen](https://www.striim.com/), [laddar ned en kostnads fri 30-dagars utvärderings version av striims](https://go2.striim.com/download-free-trial)och för eventuella problem när du konfigurerar sökvägen för migrering med striims, så skicka en supportbegäran till en [support förfrågan.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Nästa steg

* Om du migrerar data till Azure Cosmos DB SQL API, se [migrera data till API för Cassandra konto med striims](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Övervaka och felsöka dina data med Azure Cosmos DB mått](use-metrics.md)