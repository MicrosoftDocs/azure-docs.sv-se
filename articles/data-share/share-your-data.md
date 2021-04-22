---
title: 'Självstudie: Dela utanför organisationen – Azure Data Share'
description: Självstudie – Dela data med kunder och partner med hjälp av Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: b8d49e3b3c6f6407fe241e00ada5039bd94fd706
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870885"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Självstudier: Dela data med Azure Data Share  

I den här självstudien lär du dig att konfigurera en ny Azure Data Share och börja dela data med kunder och partner utanför din Azure-organisation. 

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa en dataresurs.
> * Lägg till datauppsättningar i dataresursen.
> * Aktivera ett schema för ögonblicksbilder för Data Share. 
> * Lägg till mottagare i dataresursen. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Mottagarens e-postadress för Azure-inloggning (med deras e-postalias fungerar inte).
* Om Azure-källdatalagret finns i en annan Azure-prenumeration än den som du kommer att använda för att skapa Data Share-resurs registrerar du resursprovidern [Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) i prenumerationen där Azure-datalagret finns. 

### <a name="share-from-a-storage-account"></a>Dela från ett lagringskonto

* Ett Azure Storage-konto: Om du inte redan har ett kan du skapa ett [Azure Storage konto](../storage/common/storage-account-create.md)
* Behörighet att skriva till lagringskontot, som finns i *Microsoft.Storage/storageAccounts/write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet att lägga till rolltilldelning till lagringskontot, som finns i *Microsoft.Authorization/rolltilldelningar/skriv*. Den här behörigheten finns i **ägarrollen**. 


### <a name="share-from-a-sql-based-source"></a>Dela från en SQL-baserad källa
Nedan visas en lista över förhandskrav för att dela data från SQL-källa. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Krav för delning från Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW)

* En Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW) med tabeller och vyer som du vill dela.
* Behörighet att skriva till databaserna på SQL Server, som finns i *Microsoft.Sql/servers/databases/write*. Den här behörigheten finns i **deltagarrollen**.
* **Azure Active Directory administratör** för SQL-servern
* SQL Server Firewall-åtkomst. Detta kan göras med följande steg: 
    1. I Azure Portal navigerar du till SQL Server. Välj *Brandväggar och virtuella nätverk i* det vänstra navigeringsfönstret.
    1. Klicka **på Ja** för Tillåt Att *Azure-tjänster och -resurser får åtkomst till den här servern.*
    1. Klicka **på +Lägg till klient-IP.** Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Krav för att dela från Azure Synapse Analytics SQL-pool (arbetsyta)

* * En Azure Synapse Analytics (arbetsyta) dedikerad SQL-pool med tabeller som du vill dela. Delning av vyn stöds inte för närvarande. Delning från serverlös SQL-pool stöds inte för närvarande.
* Behörighet att skriva till SQL-poolen i Synapse-arbetsytan, som finns i *Microsoft.Synapse/workspaces/sqlPools/write*. Den här behörigheten finns i **deltagarrollen**.
* Behörighet för den Data Share resursens hanterade identitet för åtkomst till Synapse-arbetsytans SQL-pool. Detta kan göras med följande steg: 
    1. I Azure Portal du till Synapse-arbetsytan. Välj SQL Active Directory-administratör i det vänstra navigeringsfönstret och ange dig **själv som Azure Active Directory administratör.**
    1. Öppna Synapse Studio väljer du *Hantera i* det vänstra navigeringsfönstret. Välj *Åtkomstkontroll* under Säkerhet. Tilldela dig själv **rollen som SQL-administratör** **eller arbetsyteadministratör.**
    1. I Synapse Studio du Utveckla *i* det vänstra navigeringsfönstret. Kör följande skript i SQL-poolen för att lägga till Data Share hanterad identitet som en db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Observera att *<share_acc_name>* är namnet på din Data Share resurs. Om du inte har skapat Data Share resurs ännu kan du komma tillbaka till det här kravet senare.  

* Åtkomst till Synapse-arbetsytans brandvägg. Detta kan göras med följande steg: 
    1. I Azure Portal du till Synapse-arbetsytan. Välj *Brandväggar i* det vänstra navigeringsfönstret.
    1. Klicka **på PÅ** för Tillåt *Azure-tjänster och resurser att komma åt den här arbetsytan.*
    1. Klicka **på +Lägg till klient-IP.** Klientens IP-adress kan komma att ändras. Den här processen kan behöva upprepas nästa gång du delar SQL-data från Azure Portal. Du kan också lägga till ett IP-intervall.
    1. Klicka på **Spara**. 


### <a name="share-from-azure-data-explorer"></a>Dela från Azure Data Explorer
* Ett Azure Data Explorer kluster med databaser som du vill dela.
* Behörighet att skriva till Azure Data Explorer som finns i *Microsoft.Kusto/clusters/write*. Den här behörigheten finns i **deltagarrollen**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Skapa ett Data Share konto

### <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa en Azure Data Share resurs i en Azure-resursgrupp.

1. Välj menyknappen i det övre vänstra hörnet i portalen och välj sedan **Skapa en resurs** (+).

1. Sök efter *Data Share*.

1. Välj Data Share och Välj **Skapa.**

1. Fyll i den grundläggande informationen om Azure Data Share resurs med följande information. 

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för ditt dataresurskonto.|
    | Resursgrupp | *testresourcegroup* | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *USA, östra 2* | Välj en region för ditt dataresurskonto.
    | Name | *datashareaccount* | Ange ett namn för ditt dataresurskonto. |
    | | |

1. Välj **Granska + skapa och** sedan Skapa **för** att etablera ditt dataresurskonto. Etableringen av ett nytt dataresurskonto tar vanligtvis cirka 2 minuter eller mindre. 

1. När distributionen är klar väljer du **Gå till resurs**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa en Azure Data Share resurs i en Azure-resursgrupp.

Börja med att förbereda din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Använd de här kommandona för att skapa resursen:

1. Använd kommandot [az account set](/cli/azure/account#az_account_set) för att ange att din prenumeration ska vara den aktuella standardprenumerationen:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kör kommandot [az provider register](/cli/azure/provider#az_provider_register) för att registrera resursprovidern:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Kör kommandot [az group create](/cli/azure/group#az_group_create) om du vill skapa en resursgrupp eller använda en befintlig resursgrupp:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Kör kommandot [az datashare account create för](/cli/azure/datashare/account#az_datashare_account_create) att skapa ett Data Share konto:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Kör kommandot [az datashare account list för](/cli/azure/datashare/account#az_datashare_account_list) att se dina Data Share konton:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Skapa en resurs

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till Data Share översiktssidan.

    ![Dela dina data](./media/share-receive-data.png "Dela dina data") 

1. Välj **Börja dela dina data.**

1. Välj **Skapa**.   

1. Fyll i informationen för din resurs. Ange namn, resurstyp, beskrivning av resursinnehåll och användningsvillkor (valfritt). 

    ![EnterShareDetails](./media/enter-share-details.png "Ange resursinformation") 

1. Välj **Fortsätt**.

1. Om du vill lägga till datauppsättningar i resursen väljer du **Lägg till datauppsättningar**. 

    ![Lägga till datauppsättningar i din resurs](./media/datasets.png "Datauppsättningar")

1. Välj den datauppsättningstyp som du vill lägga till. Du ser en annan lista över datauppsättningstyper beroende på resurstyp (ögonblicksbild eller på plats) som du valde i föregående steg. Om du delar från Azure SQL Database eller Azure Synapse Analytics (tidigare Azure SQL DW) uppmanas du att ange autentiseringsmetod för att lista tabeller. Välj AAD-autentisering och markera kryssrutan Tillåt Data Share köra skriptet **"skapa användare"** ovan för min räkning. 

    ![AddDatasets](./media/add-datasets.png "Lägga till datauppsättningar")    

1. Gå till det objekt som du vill dela och välj Lägg till datauppsättningar. 

    ![SelectDatasets](./media/select-datasets.png "Välj Datauppsättningar")    

1. På fliken Mottagare anger du e-postadresserna för datakonsumenten genom att välja +Lägg till mottagare. 

    ![AddRecipients](./media/add-recipient.png "Lägga till mottagare") 

1. Välj **Fortsätt**.

1. Om du har valt resurstyp för ögonblicksbilder kan du konfigurera schemat för ögonblicksbilder för att tillhandahålla uppdateringar av dina data till datakonsumenten. 

    ![EnableSnapshots](./media/enable-snapshots.png "Aktivera ögonblicksbilder") 

1. Välj starttid och upprepningsintervall. 

1. Välj **Fortsätt**.

1. På fliken Granska + skapa granskar du paketinnehåll, inställningar, mottagare och synkroniseringsinställningar. Välj **Skapa**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Kör kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create) för att skapa en Data Share:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Använd kommandot [az storage container create](/cli/azure/storage/container#az_storage_container_create) för att skapa en container för resursen i föregående kommando:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Kör kommandot [az datashare create](/cli/azure/datashare#az_datashare_create) för att skapa Data Share:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Använd kommandot [az datashare invitation create](/cli/azure/datashare/invitation#az_datashare_invitation_create) för att skapa inbjudan för den angivna adressen:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Din Azure Data Share har nu skapats och mottagaren av din Data Share är nu redo att acceptera din inbjudan.

## <a name="clean-up-resources"></a>Rensa resurser

När resursen inte längre behövs går du till översiktssidan Data Share **och väljer Ta** **bort** för att ta bort den.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar Azure Data Share och bjuder in mottagare. Om du vill veta mer om hur en datakonsument kan acceptera och ta emot en dataresurs fortsätter du till självstudien om att acceptera och ta emot data.

> [!div class="nextstepaction"]
> [Självstudier: Acceptera och ta emot data med Azure Data Share](subscribe-to-data-share.md)
