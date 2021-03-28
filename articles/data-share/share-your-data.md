---
title: 'Självstudie: dela utanför din organisation – Azure-Dataresurs'
description: Självstudie – dela data med kunder och partner med Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 8e149270d8f98cbf72d3864d238a3d8ddfd61c67
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639541"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Självstudier: Dela data med Azure Data Share  

I den här självstudien får du lära dig hur du konfigurerar en ny Azure-Dataresurs och börjar dela dina data med kunder och partner utanför Azure-organisationen. 

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa en dataresurs.
> * Lägg till datauppsättningar i dataresursen.
> * Aktivera ett ögonblicks bild schema för data resursen. 
> * Lägg till mottagare i dataresursen. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).
* Om käll Azure-datalagret finns i en annan Azure-prenumeration än den som du ska använda för att skapa en data resurs resurs registrerar du [Microsoft. DataShare Resource Provider](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure Data Store finns. 

### <a name="share-from-a-storage-account"></a>Dela från ett lagrings konto

* Ett Azure Storage konto: om du inte redan har ett kan du skapa ett [Azure Storage konto](../storage/common/storage-account-create.md)
* Behörighet att skriva till lagrings kontot som finns i *Microsoft. Storage/storageAccounts/Write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet att lägga till roll tilldelning till lagrings kontot, som finns i *Microsoft. auktorisering/roll tilldelningar/Skriv*. Den här behörigheten finns i **ägarrollen**. 


### <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Nedan visas en lista över förutsättningar för att dela data från SQL-källan. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Krav för delning från Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW)

* En Azure SQL Database-eller Azure Synapse-analys (tidigare Azure SQL DW) med tabeller och vyer som du vill dela.
* Behörighet att skriva till databaserna på SQL Server, som finns i *Microsoft. SQL/Servers/databaser/skriva*. Den här behörigheten finns i **deltagarrollen**.
* **Azure Active Directory administratör** för SQL Server
* SQL Server brand Väggs åtkomst. Detta kan göras genom följande steg: 
    1. I Azure Portal går du till SQL Server. Välj *brand väggar och virtuella nätverk* från vänster navigering.
    1. Klicka på **Ja** om *du vill tillåta Azure-tjänster och-resurser åtkomst till den här servern*.
    1. Klicka på **+ Lägg till klient-IP**. Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Krav för delning från SQL-poolen för Azure Synapse Analytics (arbets yta)

* * En dedikerad SQL-pool för Azure Synapse Analytics (arbets yta) med tabeller som du vill dela. Det finns för närvarande inte stöd för att dela vyn. Det finns för närvarande inte stöd för att dela från SQL-poolen utan server.
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


### <a name="share-from-azure-data-explorer"></a>Dela från Azure Data Explorer
* Ett Azure Datautforskaren-kluster med databaser som du vill dela.
* Behörighet att skriva till Azure Datautforskaren-kluster, som finns i *Microsoft. Kusto/kluster/Write*. Den här behörigheten finns i **deltagarrollen**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Skapa ett data resurs konto

### <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

1. Välj Meny knappen i det övre vänstra hörnet i portalen och välj sedan **skapa en resurs** (+).

1. Sök efter *data resurs*.

1. Välj data resurs och välj **skapa**.

1. Fyll i grundläggande information om din Azure Data Share-resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt data resurs konto.|
    | Resursgrupp | *testresourcegroup* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *USA, östra 2* | Välj en region för ditt data resurs konto.
    | Name | *datashareaccount* | Ange ett namn för ditt data resurs konto. |
    | | |

1. Välj **Granska + skapa** och sedan **skapa** för att etablera ditt data resurs konto. Det tar vanligt vis ungefär 2 minuter att tillhandahålla ett nytt data resurs konto. 

1. När distributionen är klar väljer du **Gå till resurs**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa en Azure Data Share-resurs i en Azure-resurs grupp.

Börja med att förbereda din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Använd följande kommandon för att skapa resursen:

1. Använd kommandot [AZ Account set](/cli/azure/account#az_account_set) för att ställa in din prenumeration som den aktuella standard prenumerationen:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kör [AZ Provider register](/cli/azure/provider#az_provider_register) -kommandot för att registrera resurs leverantören:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Kör kommandot [az group create](/cli/azure/group#az_group_create) om du vill skapa en resursgrupp eller använda en befintlig resursgrupp:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Kör kommandot [AZ datashare Account Create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) för att skapa ett data resurs konto:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Kör kommandot [AZ datashare Account List](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) för att se dina data resurs konton:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Skapa en resurs

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till översikts sidan för data delning.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **börja dela dina data**.

1. Välj **Skapa**.   

1. Fyll i informationen för din resurs. Ange ett namn, resurs typ, en beskrivning av delnings innehåll och användnings villkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resurs information") 

1. Välj **Fortsätt**.

1. Om du vill lägga till data uppsättningar i din resurs väljer du **Lägg till data uppsättningar**. 

    ![Lägg till data uppsättningar till din resurs](./media/datasets.png "Datauppsättningar")

1. Välj den data uppsättnings typ som du vill lägga till. Du ser en annan lista över data uppsättnings typer beroende på vilken resurs typ (ögonblicks bild eller på plats) som du har valt i föregående steg. Om du delar från en Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW) uppmanas du att ange autentiseringsmetoden för att visa tabeller. Välj AAD-autentisering och markera kryss rutan **Tillåt att data resursen kör skriptet "skapa användare" för min räkning**. 

    ![AddDatasets](./media/add-datasets.png "Lägg till data uppsättningar")    

1. Navigera till det objekt som du vill dela och välj Lägg till data uppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj data uppsättningar")    

1. På fliken mottagare anger du e-postadresserna till din data konsument genom att välja "+ Lägg till mottagare". 

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**.

1. Om du har valt typ av ögonblicks bilds resurs kan du konfigurera ögonblicks bilds schema för att tillhandahålla uppdateringar av dina data till din data konsument. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicks bilder") 

1. Välj en start tid och upprepnings intervall. 

1. Välj **Fortsätt**.

1. På fliken Granska + skapa granskar du paket innehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Kör kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) för att skapa en data resurs:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Använd kommandot [AZ Storage container Create](/cli/azure/storage/container#az_storage_container_create) för att skapa en behållare för resursen i föregående kommando:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Kör kommandot [AZ datashare Create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) för att skapa din data resurs:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Använd kommandot [AZ datashare inbjudan Create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) för att skapa en inbjudan till den angivna adressen:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Din Azure Data-resurs har nu skapats och mottagaren av din data resurs är nu klar att acceptera din inbjudan.

## <a name="clean-up-resources"></a>Rensa resurser

När resursen inte längre behövs går du till sidan **Översikt över data resurs** och väljer **ta bort** för att ta bort den.

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig hur du skapar en Azure-Dataresurs och bjuder in mottagare. Om du vill veta mer om hur en data konsument kan godkänna och ta emot en data resurs fortsätter du till kursen för att godkänna och ta emot data.

> [!div class="nextstepaction"]
> [Självstudier: Acceptera och ta emot data med Azure Data Share](subscribe-to-data-share.md)
