---
title: Dela och ta emot data från Azure SQL Database och Azure Synapse Analytics
description: Lär dig hur du delar och tar emot data från Azure SQL Database och Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: f87ad76e9bb1db4d71716bf860d5fee2d413e8e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740383"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Dela och ta emot data från Azure SQL Database och Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share stöder ögonblicks bilds-baserad delning Azure SQL Database och Azure Synapse Analytics. Den här artikeln förklarar hur du delar och tar emot data från dessa källor.

Azure Data Share stöder delning av både tabeller och vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL DW) och delning av tabeller från en dedikerad SQL-pool för Azure Synapse Analytics (arbets yta). Det finns för närvarande inte stöd för att dela från Azure Synapse Analytics (arbets yta) SQL-poolen. Data konsumenter kan välja att acceptera data till Azure Data Lake Storage Gen2 eller Azure-Blob Storage som CSV-eller Parquet-fil, samt till Azure SQL Database och Azure Synapse Analytics som tabeller.

När du accepterar data i Azure Data Lake Store Gen2 eller Azure Blob Storage skrivs innehållet i mål filen över om det redan finns en fullständig ögonblicks bild.
När data tas emot i SQL-tabellen och om mål tabellen inte redan finns skapar Azure Data Share SQL-tabellen med käll schemat. Om det redan finns en mål tabell med samma namn kommer den att tas bort och skrivas över med den senaste fullständiga ögonblicks bilden. Stegvisa ögonblicks bilder stöds inte för närvarande.

## <a name="share-data"></a>Dela data

### <a name="prerequisites-to-share-data"></a>Krav för att dela data

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).
* Om käll Azure-datalagret finns i en annan Azure-prenumeration än den som du ska använda för att skapa en data resurs resurs registrerar du [Microsoft. DataShare Resource Provider](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure Data Store finns. 

### <a name="prerequisites-for-sql-source"></a>Krav för SQL-källa
Nedan visas en lista över förutsättningar för att dela data från SQL-källan. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Krav för delning från Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW)
Du kan följa stegen i steg [för steg-demonstrationen](https://youtu.be/hIE-TjJD8Dc) för att konfigurera krav.

* En Azure SQL Database-eller Azure Synapse-analys (tidigare Azure SQL DW) med tabeller och vyer som du vill dela.
* Behörighet att skriva till databaserna på SQL Server, som finns i *Microsoft. SQL/Servers/databaser/skriva*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet för data resurs resursens hanterade identitet för att komma åt databasen. Detta kan göras genom följande steg: 
    1. I Azure Portal går du till SQL-servern och anger dig själv som **Azure Active Directorys administratör**.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av [Frågeredigeraren](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) eller SQL Server Management Studio med Azure Active Directory autentisering. 
    1. Kör följande skript för att lägga till den hanterade identiteten för data resursen som en db_datareader. Du måste ansluta med Active Directory och inte SQL Server autentisering. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.  

* En Azure SQL Database användare med **"db_datareader"** -åtkomst för att navigera och välja de tabeller och/eller vyer som du vill dela. 

* SQL Server brand Väggs åtkomst. Detta kan göras genom följande steg: 
    1. I Azure Portal går du till SQL Server. Välj *brand väggar och virtuella nätverk* från vänster navigering.
    1. Klicka på **Ja** om *du vill tillåta Azure-tjänster och-resurser åtkomst till den här servern*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Krav för delning från SQL-poolen för Azure Synapse Analytics (arbets yta)

* En dedikerad SQL-pool för Azure Synapse Analytics (arbets yta) med tabeller som du vill dela. Det finns för närvarande inte stöd för att dela vyn. Det finns för närvarande inte stöd för att dela från SQL-poolen utan server.
* Behörighet att skriva till SQL-poolen i Synapse-arbetsytan, som finns i *Microsoft. Synapse/arbetsytes/sqlPools/Write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet för data resurs resursens hanterade identitet för åtkomst till Synapse-arbetsyta SQL-pool. Detta kan göras genom följande steg: 
    1. I Azure Portal navigerar du till arbets ytan Synapse. Välj SQL Active Directory admin från vänster navigering och ange dig själv som **Azure Active Directorys administratör**.
    1. Öppna Synapse Studio och välj *Hantera* från det vänstra navigerings fältet. Välj *åtkomst kontroll* under säkerhet. Tilldela rollen som administratör för **SQL-administratören** eller **arbets ytan** .
    1. I Synapse Studio väljer du *utveckla* från det vänstra navigerings fältet. Kör följande skript i SQL-poolen för att lägga till den hanterade identiteten för data resurs som en db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.  

* Åtkomst till brand vägg för Synapse-arbetsyta. Detta kan göras genom följande steg: 
    1. I Azure Portal navigerar du till arbets ytan Synapse. Välj *brand väggar* från vänster navigering.
    1. Klicka **på på** för *att tillåta Azure-tjänster och-resurser åtkomst till den här arbets ytan*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Välj Meny knappen i det övre vänstra hörnet i portalen och välj sedan **skapa en resurs** (+).

1. Sök efter *data resurs*.

1. Välj data resurs och välj **skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt data resurs konto.|
    | Resursgrupp | *test-resurs-grupp* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | Namn | *datashareaccount* | Ange ett namn för ditt data resurs konto. |
    | | |

1. Välj **Granska + skapa** och sedan **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

### <a name="create-a-share"></a>Skapa en resurs

1. Gå till översikts sidan för data delning.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din resurs. Ange ett namn, resurs typ, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**.

1. Om du vill lägga till data uppsättningar i din resurs väljer du **Lägg till data uppsättningar**. 

    ![Lägg till data uppsättningar till din resurs](./media/datasets.png "Datauppsättningar")

1. Välj den data uppsättnings typ som du vill lägga till. Du ser en annan lista över data uppsättnings typer beroende på vilken resurs typ (ögonblicks bild eller på plats) som du har valt i föregående steg. 

    ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Välj din SQL Server-eller Synapse-arbetsyta, ange autentiseringsuppgifter om du uppmanas att göra det och välj **Nästa** för att navigera till objektet som du vill dela och välja Lägg till data uppsättningar. Du kan välja tabeller och vyer från Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL DW) eller tabeller från en dedikerad SQL-pool för Azure Synapse Analytics (arbets yta). 

    ![SelectDatasets](./media/select-datasets-sql.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare". E-postadressen måste vara mottagares e-postadress för Azure-inloggning.

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**.

1. Om du har valt typ av ögonblicks bilds resurs kan du konfigurera ögonblicks bilds schema för att tillhandahålla uppdateringar av dina data till din data konsument. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**.

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**.

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan. 

## <a name="receive-data"></a>Ta emot data

### <a name="prerequisites-to-receive-data"></a>Krav för att ta emot data
Innan du kan acceptera en inbjudan till en data resurs måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla krav är uppfyllda innan du accepterar en inbjudan om data delning. 

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Inbjudan till en data resurs: en inbjudan från Microsoft Azure med ett ämne med rubriken "Azure Data Share-inbjudan från **<yourdataprovider@domain.com>** ".
* Registrera [resurs leverantören Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du ska skapa en data resurs resurs och Azure-prenumerationen där dina Azure-datalager finns.

### <a name="prerequisites-for-target-storage-account"></a>Krav för mål lagrings konto
Om du väljer att ta emot data i Azure Storage nedan visas en lista över krav.

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage-konto](../storage/common/storage-account-create.md). 
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i **deltagarrollen**. 
* Behörighet att lägga till roll tilldelning för data resurs resursens hanterade identitet till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i **ägarrollen**.  

### <a name="prerequisites-for-sql-target"></a>Krav för SQL-mål
Om du väljer att ta emot data i Azure SQL Database, är Azure Synapse Analytics nedan listan över krav. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Krav för att ta emot data till Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW)
Du kan följa stegen i steg [för steg-demonstrationen](https://youtu.be/aeGISgK1xro) för att konfigurera krav.

* En Azure SQL Database-eller Azure Synapse-analys (tidigare Azure SQL DW).
* Behörighet att skriva till databaser på SQL-servern, som finns i *Microsoft. SQL/Servers/databaser/skriva*. Den här behörigheten finns i **deltagarrollen**. 
* Behörighet för data resurs resursens hanterade identitet för att få åtkomst till Azure SQL Database-eller Azure Synapse-analys. Detta kan göras genom följande steg: 
    1. I Azure Portal går du till SQL-servern och anger dig själv som **Azure Active Directorys administratör**.
    1. Anslut till Azure SQL Database/informations lagret med hjälp av [Frågeredigeraren](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) eller SQL Server Management Studio med Azure Active Directory autentisering. 
    1. Kör följande skript för att lägga till den hanterade identiteten för data resursen som db_datareader, db_datawriter db_ddladmin. Du måste ansluta med Active Directory och inte SQL Server autentisering. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.         

* SQL Server brand Väggs åtkomst. Detta kan göras genom följande steg: 
    1. I SQL Server i Azure Portal navigerar du till *brand väggar och virtuella nätverk*
    1. Klicka på **Ja** om *du vill tillåta Azure-tjänster och-resurser åtkomst till den här servern*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Krav för att ta emot data i Azure Synapse Analytics (arbets yta) SQL-pool

* En dedikerad SQL-pool för Azure Synapse Analytics (arbets yta). Det finns för närvarande inte stöd för att ta emot data i SQL-poolen utan server.
* Behörighet att skriva till SQL-poolen i Synapse-arbetsytan, som finns i *Microsoft. Synapse/arbetsytes/sqlPools/Write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet för data resurs resursens hanterade identitet för åtkomst till SQL-poolen Synapse-arbetsyta. Detta kan göras genom följande steg: 
    1. I Azure Portal navigerar du till arbets ytan Synapse. Välj SQL Active Directory admin från vänster navigering och ange dig själv som **Azure Active Directorys administratör**.
    1. Öppna Synapse Studio och välj *Hantera* från det vänstra navigerings fältet. Välj *åtkomst kontroll* under säkerhet. Tilldela rollen som administratör för **SQL-administratören** eller **arbets ytan** .
    1. I Synapse Studio väljer du *utveckla* från det vänstra navigerings fältet. Kör följande skript i SQL-poolen för att lägga till den hanterade identiteten för data resursen som en db_datareader, db_datawriter db_ddladmin. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Observera att *<share_acc_name>* är namnet på din data resurs resurs. Om du inte har skapat någon data resurs resurs ännu kan du gå tillbaka till det här kravet senare.  

* Åtkomst till brand vägg för Synapse-arbetsyta. Detta kan göras genom följande steg: 
    1. I Azure Portal navigerar du till arbets ytan Synapse. Välj *brand väggar* från vänster navigering.
    1. Klicka **på på** för *att tillåta Azure-tjänster och-resurser åtkomst till den här arbets ytan*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="open-invitation"></a>Öppna inbjudan

1. Du kan öppna inbjudan från e-post eller direkt från Azure Portal. 

   Om du vill öppna inbjudan från e-post, kontrollerar du Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com>**. Klicka på **Visa inbjudan** för att se din inbjudan i Azure. 

   Du öppnar inbjudan från Azure Portal direkt genom att söka efter **inbjudningar för data delning** i Azure Portal. Då går du till listan med data resurs inbjudningar.

   ![Lista över inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

### <a name="accept-invitation"></a>Acceptera inbjudan
1. Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

   ![Villkor för användning](./media/terms-of-use.png "Villkor för användning") 

1. Under *konto för mål data resurs* väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

   För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

   I fältet **mottaget resurs namn** kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

   När du har samtyckt till användnings villkoren och angett ett data delnings konto för att hantera din mottagna resurs väljer du **Godkänn och konfigurera**. En resurs prenumeration kommer att skapas. 

   ![Godkänn alternativ](./media/accept-options.png "Godkänn alternativ") 

   Detta tar dig till den mottagna resursen i ditt data resurs konto. 

   Om du inte vill acceptera inbjudan väljer du *avvisa*. 

### <a name="configure-received-share"></a>Konfigurera mottagen resurs
Följ stegen nedan för att konfigurera var du vill ta emot data.

1. Fliken Välj **data uppsättningar** . Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål. Välj **+ Mappa till mål** för att välja ett mål data lager. 

   ![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

1. Välj ett mål data lager som du vill att data ska hamna i. Alla datafiler eller tabeller i mål data lagret med samma sökväg och namn kommer att skrivas över. 

   ![Mål lagrings konto](./media/dataset-map-target-sql.png "Mål data lager") 

1. För ögonblicks bilds-baserad delning, om dataprovidern har skapat ett ögonblicks bild schema för att tillhandahålla regelbunden uppdatering av data, kan du också aktivera schema för ögonblicks bild genom att välja fliken **ögonblicks bild schema** . Markera kryss rutan bredvid schemat för ögonblicks bilder och välj **+ Aktivera**.

   ![Aktivera schema för ögonblicks bild](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicks bild")

### <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild
De här stegen gäller endast för Snapshot-baserad delning.

1. Du kan utlösa en ögonblicks bild genom att välja fliken **information** följt av **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. Endast fullständig ögonblicks bild stöds för SQL-källor. När en ögonblicks bild körs kommer efterföljande ögonblicks bilder inte att starta förrän den tidigare slutförts.

   ![Utlös ögonblicks bild](./media/trigger-snapshot.png "Utlös ögonblicks bild") 

1. När den senaste körnings statusen har *slutförts* går du till mål data lagret för att Visa mottagna data. Välj **data uppsättningar** och klicka på länken i mål Sök vägen. 

   ![Konsument data uppsättningar](./media/consumer-datasets.png "Mappning av konsument data uppsättning") 

### <a name="view-history"></a>Visa historik
Det här steget gäller endast för Snapshot-baserad delning. Välj fliken **Historik** för att visa historiken för dina ögonblicks bilder. Här hittar du historiken för alla ögonblicks bilder som har genererats under de senaste 30 dagarna. 

## <a name="supported-data-types"></a>Datatyper som stöds
När du delar data från SQL-källa används följande mappning från SQL Server data typer till data typer för Azure Data resurs under ögonblicks bilds processen. 

| SQL Server data typ | Interimistisk data typ för Azure Data resurs |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte [] |
| bit |Boolesk |
| char |Sträng, char [] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary (max)) |Byte [] |
| Float |Double |
| image |Byte [] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, char [] |
| ntext |Sträng, char [] |
| numeric |Decimal |
| nvarchar |Sträng, char [] |
| real |Enkel |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |Sträng, char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Sträng, char [] |
| xml |Sträng |

>[!NOTE]
> 1. För data typer som mappar till typen decimal, stöder ögonblicks bilder precisionen upp till 28. Om du har data som kräver precision som är större än 28, bör du överväga att konvertera till en sträng. 
> 1.  Om du delar data från Azure SQL Database till Azure Synapse Analytics stöds inte alla typer av data. Mer information finns [i tabell data typer i dedikerad SQL-pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) . 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL-Always Encrypted eller dynamisk data maskning
För närvarande stöder inte Azure Data Share Azure SQL-databaser med Always Encrypted konfigurerade. 

För käll-SQL-tabeller med dynamisk datamaskering visas data maskerade på mottagar sidan.

## <a name="sql-snapshot-performance"></a>Prestanda för SQL-ögonblicksbild
SQL-ögonblicksbildens prestanda påverkas av ett antal faktorer. Vi rekommenderar alltid att du utför dina egna prestanda testningar. Nedan visas några exempel faktorer som påverkar prestanda.

* Maskin varu konfiguration (t. ex. virtuella kärnor, minne, DWU) för käll-och mål-SQL data lager. 
* Samtidig åtkomst till käll-och mål data lager. Om du delar flera tabeller och vyer från samma SQL-datalager, eller tar emot flera tabeller och vyer i samma SQL data lager, påverkas prestandan.   
* Plats för käll-och mål data lager. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Felsöka fel vid SQL-ögonblicksbild
Den vanligaste orsaken till ögonblicks bild felet är att data resursen inte har behörighet till käll-eller mål data lagret. För att bevilja data resurs behörighet till käll-eller mål Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW) måste du köra det angivna SQL-skriptet när du ansluter till SQL-databasen med Azure Active Directory autentisering. Fel sökning av ytterligare fel vid SQL-ögonblicksbild finns i [Felsöka ögonblicks bilds fel](data-share-troubleshoot.md#snapshots).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du delar och tar emot data från SQL-källor med Azure Data Share-tjänsten. Om du vill veta mer om delning från andra data källor fortsätter du till [data lager som stöds](supported-data-stores.md).