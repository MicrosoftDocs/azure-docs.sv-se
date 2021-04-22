---
title: 'Självstudie: Acceptera & ta emot data – Azure Data Share'
description: Självstudie – Acceptera och ta emot data med Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: d063a0870616b5b977df18c56d9d66515b03d0a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870867"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Självstudier: Acceptera och ta emot data med Azure Data Share  

I den här självstudien lär du dig att acceptera en dataresursinbjudan med hjälp av Azure Data Share. Du får lära dig hur du tar emot data som delas med dig, samt hur du aktiverar ett regelbundet uppdateringsintervall för att säkerställa att du alltid har den senaste ögonblicksbilden av de data som delas med dig. 

> [!div class="checklist"]
> * Så här accepterar du en Azure Data Share inbjudan
> * Skapa ett Azure Data Share konto
> * Ange ett mål för dina data
> * Skapa en prenumeration på din dataresurs för schemalagd uppdatering

## <a name="prerequisites"></a>Förutsättningar
Innan du kan acceptera en dataresursinbjudan måste du etablera ett antal Azure-resurser som anges nedan. 

Se till att alla förutsättningar är slutförda innan du godkänner en dataresursinbjudan. 

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En Data Share: En inbjudan från Microsoft Azure med ämnet "Azure Data Share inbjudan från **<yourdataprovider@domain.com>** ".
* Registrera [resursprovidern Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) i Azure-prenumerationen där du skapar en Data Share-resurs och Azure-prenumerationen där dina Azure-måldatalager finns.

### <a name="receive-data-into-a-storage-account"></a>Ta emot data till ett lagringskonto

* Ett Azure Storage-konto: Om du inte redan har ett kan du skapa ett [Azure Storage konto](../storage/common/storage-account-create.md). 
* Behörighet att skriva till lagringskontot, som finns i *Microsoft.Storage/storageAccounts/write*. Den här behörigheten finns i deltagarrollen. 
* Behörighet att lägga till rolltilldelning till lagringskontot, som finns i *Microsoft.Authorization/rolltilldelningar/skriv*. Den här behörigheten finns i ägarrollen.  

### <a name="receive-data-into-a-sql-based-target"></a>Ta emot data till ett SQL-baserat mål
Om du väljer att ta emot data Azure SQL Database Azure Synapse Analytics finns nedan en lista över förhandskrav. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Krav för att ta emot data till Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW)

* En Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW).
* Behörighet att skriva till databaser på SQL-servern, som finns i *Microsoft.Sql/servers/databases/write*. Den här behörigheten finns i **deltagarrollen**. 
* **Azure Active Directory administratör** för SQL-servern
* SQL Server brandväggsåtkomst. Detta kan göras med följande steg: 
    1. I SQL Server i Azure Portal du till *Brandväggar och virtuella nätverk*
    1. Klicka **på Ja** för Tillåt Att *Azure-tjänster och -resurser får åtkomst till den här servern.*
    1. Klicka **på +Lägg till klient-IP.** Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Krav för att ta emot data Azure Synapse Analytics SQL-pool (arbetsyta)

* En Azure Synapse Analytics (arbetsyta) dedikerad SQL-pool. Det finns för närvarande inte stöd för att ta emot data till en serverlös SQL-pool.
* Behörighet att skriva till SQL-poolen i Synapse-arbetsytan, som finns i *Microsoft.Synapse/workspaces/sqlPools/write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet för den Data Share resursens hanterade identitet för åtkomst till Synapse-arbetsytans SQL-pool. Detta kan göras med följande steg: 
    1. I Azure Portal du till Synapse-arbetsytan. Välj SQL Active Directory-administratör i det vänstra navigeringsfönstret och ange dig **själv som Azure Active Directory administratör.**
    1. Öppna Synapse Studio väljer du *Hantera i* det vänstra navigeringsfönstret. Välj *Åtkomstkontroll* under Säkerhet. Tilldela dig själv **rollen som SQL-administratör** **eller arbetsyteadministratör.**
    1. I Synapse Studio väljer du *Utveckla i* det vänstra navigeringsfönstret. Kör följande skript i SQL-poolen för att lägga Data Share hanterad identitet som en "db_datareader, db_datawriter, db_ddladmin". 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Observera att *<share_acc_name>* är namnet på din Data Share resurs. Om du inte har skapat Data Share resurs ännu kan du komma tillbaka till det här kravet senare.  

* Åtkomst till Synapse-arbetsytans brandvägg. Detta kan göras med följande steg: 
    1. I Azure Portal du till Synapse-arbetsytan. Välj *Brandväggar i* det vänstra navigeringsfönstret.
    1. Klicka **på PÅ** för Tillåt *Azure-tjänster och resurser att komma åt den här arbetsytan.*
    1. Klicka **på +Lägg till klient-IP.** Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ta emot data till ett Azure Data Explorer kluster: 

* Ett Azure Data Explorer-kluster i samma Azure-datacenter som dataleverantörens Datautforskaren-kluster: Om du inte redan har ett kan du skapa ett [Azure Data Explorer kluster](/azure/data-explorer/create-cluster-database-portal). Om du inte känner till Azure-datacentret för dataleverantörens kluster kan du skapa klustret senare i processen.
* Behörighet att skriva till Azure Data Explorer som finns i *Microsoft.Kusto/clusters/write*. Den här behörigheten finns i deltagarrollen. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="open-invitation"></a>Öppna inbjudan

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Du kan öppna inbjudan via e-post eller direkt från Azure Portal. 

   Om du vill öppna en inbjudan via e-post kontrollerar du om det finns en inbjudan från dataleverantören i inkorgen. Inbjudan kommer från Microsoft Azure, med namnet **Azure Data Share inbjudan <yourdataprovider@domain.com> från**. Klicka på **Visa inbjudan** för att se din inbjudan i Azure. 

   Om du vill öppna inbjudan Azure Portal direkt söker du **efter Data Share inbjudningar** i Azure Portal. Den här åtgärden tar dig till listan över Data Share inbjudningar.

   ![Lista över inbjudningar](./media/invitations.png "Lista över inbjudningar") 

1. Välj den resurs som du vill visa. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Förbered Din Azure CLI-miljö och visa sedan dina inbjudningar.

Börja med att förbereda din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Kör kommandot [az datashare consumer invitation list för](/cli/azure/datashare/consumer/invitation#az_datashare_consumer_invitation_list) att se dina aktuella inbjudningar:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Kopiera ditt inbjudnings-ID för användning i nästa avsnitt.

---

## <a name="accept-invitation"></a>Acceptera inbjudan

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Kontrollera att alla fält granskas, inklusive **användningsvillkoren.** Om du godkänner användningsvillkoren måste du markera kryssrutan för att visa att du accepterar. 

   ![Villkor för användning](./media/terms-of-use.png "Villkor för användning") 

1. Under *Målkonto Data Share väljer* du den prenumeration och resursgrupp som du ska distribuera din Data Share till. 

   I fältet **Data Share konto** väljer du **Skapa nytt** om du inte har ett befintligt Data Share konto. Annars väljer du ett Data Share konto som du vill godkänna din dataresurs till. 

   I fältet **Mottaget resursnamn** kan du lämna standardvärdet som anges av den angivna informationen eller ange ett nytt namn för den mottagna resursen. 

   När du har godkänt användningsvillkoren och angett ett Data Share för att hantera din mottagna resurs väljer du **Godkänn och konfigurerar**. En resursprenumeration skapas. 

   ![Acceptera alternativ](./media/accept-options.png "Acceptera alternativ") 

   Den här åtgärden tar dig till den mottagna resursen i ditt Data Share konto. 

   Om du inte vill acceptera inbjudan väljer du *Avvisa.* 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az datashare consumer share-subscription create](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_create) för att skapa Data Share.

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

1. Välj **fliken Datauppsättningar.** Markera kryssrutan bredvid den datauppsättning som du vill tilldela ett mål till. Välj **+ Mappa till mål för** att välja ett måldatalager. 

   ![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

1. Välj en typ av måldatalager som du vill att data ska landa i. Alla datafiler eller tabeller i måldatalagret med samma sökväg och namn skrivs över. Om du tar emot data till Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW) markerar du kryssrutan Tillåt Data Share att köra skriptet **"skapa användare"** ovan för min räkning.

   För delning på plats väljer du ett datalager på den angivna platsen. Platsen är det Azure-datacenter där dataleverantörens källdatalager finns. När datauppsättningen har mappats kan du följa länken i målsökvägen för att komma åt data.

   ![Mållagringskonto](./media/dataset-map-target-sql.png "Mållagring") 

1. För ögonblicksbildbaserad delning kan du även aktivera schemat för ögonblicksbilder genom att välja fliken Schema för ögonblicksbilder om dataleverantören har skapat ett schema för ögonblicksbilder för att tillhandahålla regelbunden uppdatering **av** data. Markera kryssrutan bredvid schemat för ögonblicksbilden och välj **+ Aktivera**. Observera att den första schemalagda ögonblicksbilden startar inom en minut från schematiden och efterföljande ögonblicksbilder startar inom några sekunder från den schemalagda tiden.

   ![Aktivera schema för ögonblicksbilder](./media/enable-snapshot-schedule.png "Aktivera schema för ögonblicksbilder")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd dessa kommandon för att konfigurera var du vill ta emot data.

1. Kör kommandot [az datashare consumer share-subscription list-source-dataset](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_dataset) för att hämta datauppsättnings-ID:t:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Kör kommandot [az storage account create för](/cli/azure/storage/account#az_storage_account_create) att skapa ett lagringskonto för den här Data Share:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Använd kommandot [az storage account show för](/cli/azure/storage/account#az_storage_account_show) att hämta lagringskontots ID:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Använd följande kommando för att hämta kontots huvudnamns-ID:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Använd kommandot [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) för att skapa en rolltilldelning för kontots huvudnamn:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Skapa en variabel för mappningen baserat på datauppsättningens ID:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Använd kommandot [az datashare consumer dataset-mapping create för](/cli/azure/datashare/consumer/dataset-mapping#az_datashare_consumer_dataset_mapping_create) att skapa datauppsättningsmappningen:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Kör [startkommandot az datashare consumer share-subscription synchronization start för](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_start) att starta synkroniseringen av datauppsättningen.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Kör kommandot [az datashare consumer share-subscription synchronization list](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_list) för att visa en lista över dina synkroniseringar:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Använd kommandot [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) för att se synkroniseringsinställningar som angetts på resursen.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Utlösa en ögonblicksbild

### <a name="portal"></a>[Portal](#tab/azure-portal)

De här stegen gäller endast för ögonblicksbildbaserad delning.

1. Du kan utlösa en ögonblicksbild genom att välja **fliken Information** följt av **Utlösarögonblicksbild.** Här kan du utlösa en fullständig eller inkrementell ögonblicksbild av dina data. Om det är första gången du tar emot data från dataleverantören väljer du fullständig kopia. 

   ![Utlösarögonblicksbild](./media/trigger-snapshot.png "Ögonblicksbild av utlösare") 

1. När den senaste körningsstatusen *är lyckad* går du till måldatalagret för att visa mottagna data. Välj **Datauppsättningar** och klicka på länken i Målsökväg. 

   ![Konsumentdatauppsättningar](./media/consumer-datasets.png "Mappning av konsumentdatamängd") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör kommandot [az datashare consumer trigger create för](/cli/azure/datashare/consumer/trigger#az_datashare_consumer_trigger_create) att utlösa en ögonblicksbild:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Använd bara det här kommandot för ögonblicksbildbaserad delning.

---

## <a name="view-history"></a>Visa historik
Det här steget gäller endast för ögonblicksbildbaserad delning. Om du vill visa historiken för dina ögonblicksbilder väljer **du fliken Historik.** Här hittar du historik över alla ögonblicksbilder som har genererats under de senaste 30 dagarna.

## <a name="clean-up-resources"></a>Rensa resurser

När resursen inte längre behövs går du till översiktssidan Data Share **och väljer Ta** **bort** för att ta bort den.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du accepterar och tar emot Azure Data Share. Om du vill veta Azure Data Share om begrepp kan du fortsätta Azure Data Share terminologi.

> [!div class="nextstepaction"]
> [Azure Data Share begrepp](terminology.md)