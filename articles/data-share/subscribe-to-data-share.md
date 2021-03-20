---
title: 'Självstudie: Godkänn & ta emot data – Azure Data Share'
description: Självstudie – acceptera och ta emot data med Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017057"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Självstudier: Acceptera och ta emot data med Azure Data Share  

I den här självstudien får du lära dig hur du godkänner en data delnings-inbjudan med Azure Data Share. Du får lära dig hur du tar emot data som delas med dig, samt hur du aktiverar ett vanligt uppdaterings intervall för att säkerställa att du alltid har den senaste ögonblicks bilden av de data som delas med dig. 

> [!div class="checklist"]
> * Så här godkänner du en Azure Data Share-inbjudan
> * Skapa ett Azure Data Share-konto
> * Ange ett mål för dina data
> * Skapa en prenumeration på din data resurs för schemalagd uppdatering

## <a name="prerequisites"></a>Förutsättningar
Innan du kan acceptera en inbjudan till en data resurs måste du etablera ett antal Azure-resurser som visas nedan. 

Se till att alla krav är uppfyllda innan du accepterar en inbjudan om data delning. 

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Inbjudan till en data resurs: en inbjudan från Microsoft Azure med ett ämne med rubriken "Azure Data Share-inbjudan från **<yourdataprovider@domain.com>** ".
* Registrera [resurs leverantören Microsoft. DataShare](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du ska skapa en data resurs resurs och Azure-prenumerationen där dina Azure-datalager finns.

### <a name="receive-data-into-a-storage-account"></a>Ta emot data till ett lagrings konto

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage-konto](../storage/common/storage-account-create.md). 
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i deltagarrollen. 
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägarrollen.  

### <a name="receive-data-into-a-sql-based-target"></a>Ta emot data i ett SQL-baserat mål
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

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ta emot data till ett Azure Datautforskaren-kluster: 

* Ett Azure Datautforskaren-kluster i samma Azure-datacenter som data leverantörens Datautforskaren kluster: om du inte redan har ett kan du skapa ett [Azure datautforskaren-kluster](/azure/data-explorer/create-cluster-database-portal). Om du inte känner till Azure-datacenter i data leverantörens kluster kan du skapa klustret senare i processen.
* Behörighet att skriva till Azure Datautforskaren-klustret, som finns i *Microsoft. Kusto/kluster/Write*. Den här behörigheten finns i deltagarrollen. 
* Behörighet att lägga till roll tilldelning till Azure Datautforskaren-klustret, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i ägarrollen. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Du kan öppna inbjudan från e-post eller direkt från Azure Portal. 

   Om du vill öppna inbjudan från e-post, kontrollerar du Inkorgen för en inbjudan från din data leverantör. Inbjudan är från Microsoft Azure, med titeln **Azure Data Share-inbjudan från <yourdataprovider@domain.com>**. Klicka på **Visa inbjudan** för att se din inbjudan i Azure. 

   Du öppnar inbjudan från Azure Portal direkt genom att söka efter **inbjudningar för data delning** i Azure Portal. Den här åtgärden tar dig till listan över data resurs inbjudningar.

   ![Lista över inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Förbered din Azure CLI-miljö och Visa sedan dina inbjudningar.

Börja med att förbereda din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Kör kommandot [AZ datashare Consumer inbjudan List](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) för att se dina aktuella inbjudningar:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Kopiera ditt Inbjudnings-ID så att det används i nästa avsnitt.

---

## <a name="accept-invitation"></a>Acceptera inbjudan

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Se till att alla fält har granskats, inklusive **användnings villkoren**. Om du samtycker till användnings villkoren måste du markera kryss rutan för att ange att du godkänner. 

   ![Villkor för användning](./media/terms-of-use.png "Villkor för användning") 

1. Under *konto för mål data resurs* väljer du den prenumeration och resurs grupp som du ska distribuera data resursen till. 

   För fältet **data resurs konto** väljer du **Skapa nytt** om du inte har ett befintligt data resurs konto. Annars väljer du ett befintligt data resurs konto som du vill acceptera data resursen till. 

   I fältet **mottaget resurs namn** kan du lämna standardvärdet som anges av data ange eller ange ett nytt namn för den mottagna resursen. 

   När du har samtyckt till användnings villkoren och angett ett data delnings konto för att hantera din mottagna resurs väljer du **Godkänn och konfigurera**. En resurs prenumeration kommer att skapas. 

   ![Godkänn alternativ](./media/accept-options.png "Godkänn alternativ") 

   Den här åtgärden tar dig till den mottagna resursen i ditt data resurs konto. 

   Om du inte vill acceptera inbjudan väljer du *avvisa*. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [AZ datashare Consumer Share-Subscription Create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) för att skapa data resursen.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Konfigurera mottagen resurs

### <a name="portal"></a>[Portal](#tab/azure-portal)

Följ stegen nedan för att konfigurera var du vill ta emot data.

1. Fliken Välj **data uppsättningar** . Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål. Välj **+ Mappa till mål** för att välja ett mål data lager. 

   ![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

1. Välj en mål data lager typ som du vill att data ska hamna i. Alla datafiler eller tabeller i mål data lagret med samma sökväg och namn kommer att skrivas över. 

   För delning på plats väljer du ett data lager på den angivna platsen. Platsen är Azure Data Center där dataproviderns käll data lager finns. När data uppsättningen har mappats kan du följa länken i mål Sök vägen för att komma åt data.

   ![Mål lagrings konto](./media/dataset-map-target-sql.png "Mål lagring") 

1. För ögonblicks bilds-baserad delning, om dataprovidern har skapat ett ögonblicks bild schema för att tillhandahålla regelbunden uppdatering av data, kan du också aktivera schema för ögonblicks bild genom att välja fliken **ögonblicks bild schema** . Markera kryss rutan bredvid schemat för ögonblicks bilder och välj **+ Aktivera**.

   ![Aktivera schema för ögonblicks bild](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicks bild")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd dessa kommandon för att konfigurera var du vill ta emot data.

1. Kör kommandot [AZ datashare Consumer Share-Subscription List-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) för att hämta data UPPSÄTTNINGS-ID:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Kör kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) för att skapa ett lagrings konto för den här data resursen:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Använd kommandot [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show) för att hämta lagrings kontots ID:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Använd följande kommando för att hämta kontots huvud-ID:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Använd kommandot [AZ Role tilldelning Create](/cli/azure/role/assignment#az_role_assignment_create) för att skapa en roll tilldelning för kontots huvud namn:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Skapa en variabel för mappningen baserat på data uppsättnings-ID:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Använd kommandot [AZ datashare Consumer data uppsättning-kommandot mappnings avbildning](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) för att skapa data uppsättnings mappningen:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Kör kommandot [AZ datashare Consumer Share-Subscription Synchronize start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) för att starta data uppsättningens synkronisering.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Kör kommandot [AZ datashare konsument Share-Subscription Synchronization List](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) för att se en lista över dina synkroniseringar:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Använd kommandot [AZ datashare konsument resurs-prenumerations lista-källa-Share-Synchronize-Setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) för att se synkroniseringsinställningar som angetts i din resurs.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Utlös en ögonblicks bild

### <a name="portal"></a>[Portal](#tab/azure-portal)

De här stegen gäller endast för Snapshot-baserad delning.

1. Du kan utlösa en ögonblicks bild genom att välja fliken **information** följt av **Utlös ögonblicks bild**. Här kan du utlösa en fullständig eller stegvis ögonblicks bild av dina data. Om det är första gången du tar emot data från din dataprovider väljer du fullständig kopia. 

   ![Utlös ögonblicks bild](./media/trigger-snapshot.png "Utlös ögonblicks bild") 

1. När den senaste körnings statusen har *slutförts* går du till mål data lagret för att Visa mottagna data. Välj **data uppsättningar** och klicka på länken i mål Sök vägen. 

   ![Konsument data uppsättningar](./media/consumer-datasets.png "Mappning av konsument data uppsättning") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör kommandot [AZ datashare Consumer trigger Create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) för att utlösa en ögonblicks bild:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Använd endast det här kommandot för Snapshot-baserad delning.

---

## <a name="view-history"></a>Visa historik
Det här steget gäller endast för Snapshot-baserad delning. Välj fliken **Historik** för att visa historiken för dina ögonblicks bilder. Här hittar du historiken för alla ögonblicks bilder som har genererats under de senaste 30 dagarna.

## <a name="clean-up-resources"></a>Rensa resurser

När resursen inte längre behövs går du till sidan **Översikt över data resurs** och väljer **ta bort** för att ta bort den.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du godkänner och tar emot en Azure-Dataresurs. Om du vill veta mer om Azure Data Share-koncept kan du fortsätta till Azure Data Share-terminologi.

> [!div class="nextstepaction"]
> [Azure Data Share-koncept](terminology.md)