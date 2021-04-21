---
title: 'Självstudie: Skapa Azure Database for MySQL Flexibel server (förhandsversion) och Azure App Service-webbapp i samma virtuella nätverk'
description: Snabbstartsguide för att Azure Database for MySQL flexibel server (förhandsversion) med webbapp i ett virtuellt nätverk
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13baf8f033338e242610d7b8c4eec14806cd5ec5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770029"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>Självstudie: Skapa en Azure Database for MySQL – flexibel server (förhandsversion) App Services en webbapp i ett virtuellt nätverk

> [!IMPORTANT]
> Azure Database for MySQL – Flexibel server är för närvarande i offentlig förhandsversion.

Den här självstudien visar hur du skapar en Azure App Service-webbapp med MySQL – flexibel server (förhandsversion) i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

I den här självstudien får du lära dig hur man
>[!div class="checklist"]
> * Skapa en flexibel MySQL-server i ett virtuellt nätverk
> * Skapa ett undernät att delegera till App Service
> * Skapa en webbapp
> * Lägga till webbappen i det virtuella nätverket
> * Ansluta till Postgres från webbappen 

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **az login-utdata** för din prenumeration i platshållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Skapa en Azure Database for MySQL flexibel server

Skapa en privat flexibel server i ett virtuellt nätverk (VNET) med följande kommando:
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
Kopiera anslutningssträngen och namnet på det nyligen skapade virtuella nätverket. Det här kommandot utför följande åtgärder, vilket kan ta några minuter:

- Skapa resursgruppen om den inte redan finns.
- Genererar ett servernamn om det inte anges.
- Skapa ett nytt virtuellt nätverk för din nya MySQL-server. Anteckna namnet på det virtuella nätverket och undernätets namn som skapats för servern eftersom du måste lägga till webbappen i samma virtuella nätverk.
- Skapar administratörens användarnamn , lösenord för servern om inget annat anges.
- Skapar en tom databas med namnet **flexibleserverdb**

> [!NOTE]
> Anteckna lösenordet som ska genereras åt dig om du inte har angett det. Om du glömmer lösenordet måste du återställa lösenordet med ``` az mysql flexible-server update``` kommandot

## <a name="create-subnet-for-app-service-endpoint"></a>Skapa undernät för App Service slutpunkt
Nu måste vi ha ett undernät som har delegerats till App Service webbappens slutpunkt. Kör följande kommando för att skapa ett nytt undernät i samma virtuella nätverk som databasservern skapades. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Anteckna namnet på det virtuella nätverket och undernätets namn efter det här kommandot, eftersom du behöver det för att lägga till en VNET-integreringsregel för webbappen när den har skapats. 

## <a name="create-a-web-app"></a>Skapa en webbapp

I det här avsnittet skapar du en appvärd i App Service och ansluter den här appen till MySQL-databasen. Kontrollera att du är i lagringsplatsens rot i programkoden i terminalen.

Skapa en App Service -app (värdprocessen) med kommandot az webapp up

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - För argumentet --location använder du samma plats som du gjorde för databasen i föregående avsnitt.
> - Ersätt _&lt; appnamns->_ med ett unikt namn i hela Azure (serverslutpunkten är https:// \<app-name> .azurewebsites.net). Tillåtna tecken <för appnamn> A-Z, 0-9 och -. Ett bra mönster är att använda en kombination av företagets namn och en appidentifierare.
> - App Service Basic-nivån stöder inte VNET-integrering. Använd Standard eller Premium. 

Det här kommandot utför följande åtgärder, vilket kan ta några minuter:

- Skapa resursgruppen om den inte redan finns. (I det här kommandot använder du samma resursgrupp som du skapade databasen i tidigare.)
- Skapa App Service plan på ```testappserviceplan``` prisnivån Basic (B1), om den inte finns. --plan och --sku är valfria.
- Skapa App Service-appen om den inte finns.
- Aktivera standardloggning för appen, om det inte redan är aktiverat.
- Ladda upp lagringsplatsen med ZIP-distribution med byggautomatisering aktiverat.

## <a name="add-the-web-app-to-the-virtual-network"></a>Lägga till webbappen i det virtuella nätverket

Använd **kommandot az webapp vnet-integration** för att lägga till en regional integrering av virtuella nätverk i en webbapp. Ersätt _&lt; vnet-name>_ _&lt; och subnet-name_ med det virtuella nätverket och undernätsnamnet som den flexibla servern använder.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurera miljövariabler för att ansluta databasen

När koden nu distribueras till App Service är nästa steg att ansluta appen till den flexibla servern i Azure. Appkoden förväntar sig att hitta databasinformation i ett antal miljövariabler. Om du vill ange miljövariabler App Service skapar du "appinställningar" med ```az webapp config appsettings``` kommandot set.

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- Ersätt _&lt; mysql-server-name>,_ _&lt; username>_ och _&lt; password>_ för det nyligen skapade kommandot för flexibel server.
- Ersätt _&lt; användarnamnet>_ _&lt; och>_ med de autentiseringsuppgifter som kommandot också genererade åt dig.
- Resursgruppen och appnamnet hämtas från de cachelagrade värdena i .azure/config-filen.
- Kommandot skapar inställningar med namnen DBHOST, DBNAME, DBUSER och DBPASS. Om din programkod använder ett annat namn för databasinformationen använder du dessa namn för appinställningarna som anges i koden.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i självstudien med hjälp av följande kommando. Det här kommandot tar bort alla resurser i den här resursgruppen.

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
