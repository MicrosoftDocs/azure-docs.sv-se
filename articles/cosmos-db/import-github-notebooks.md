---
title: Importera och kör antecknings böcker från en GitHub-lagrings platsen till Azure Cosmos DB
description: Lär dig hur du ansluter till GitHub och importerar antecknings böckerna från en GitHub-lagrings platsen till ditt Azure Cosmos-konto. När du har importerat kan du köra, redigera dem och spara ändringarna till GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690659"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importera antecknings böcker från en GitHub-lagrings platsen till Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

När du har [aktiverat Notebook support](enable-notebooks.md) för dina Azure Cosmos-konton kan du skapa nya antecknings böcker, ladda upp nya antecknings böcker från den lokala datorn eller importera de befintliga antecknings böckerna från dina GitHub-konton. Den här artikeln visar hur du ansluter arbets ytan för antecknings böcker till GitHub och importerar antecknings böckerna från en GitHub-lagrings platsen till ditt Azure Cosmos-konto. När du har importerat kan du köra dem, göra ändringar och spara tillbaka ändringarna till GitHub.

## <a name="get-notebooks-from-github"></a>Hämta antecknings böcker från GitHub

Du kan ansluta till dina egna GitHub-databaser eller andra offentliga GitHub-databaser för att läsa, redigera och dela antecknings böcker i Azure Cosmos DB. Använd följande steg för att ansluta till ett GitHub-konto:

1. Logga in på [Azure Portal](https://portal.azure.com/) och gå till ditt Azure Cosmos-konto.

1. Öppna fliken **datautforskaren** . På den här fliken visas alla befintliga databaser, behållare och antecknings böcker.

1. Välj meny alternativet **Anslut till GitHub** .

1. En flik öppnas där du kan välja att endast ansluta till **offentliga databaser** eller **offentliga och privata databaser**.  När du har valt alternativet som krävs väljer du **ge åtkomst**. Auktorisering krävs för att Azure Cosmos DB ska kunna komma åt databaserna i ditt GitHub-konto.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Ge Azure Cosmos DB åtkomst till dina GitHub-databaser":::

1. Du omdirigeras till webb sidan "github.com" där du kan bekräfta auktoriseringen. Välj knappen **auktorisera AzureCosmosDBNotebooks** och ange lösen ordet för ditt GitHub-konto i prompten.

1. När auktoriseringen är klar tar du tillbaka ditt Azure Cosmos-konto. Du kan sedan se alla offentliga/privata databaser från ditt GitHub-konto. Du kan välja en lagrings platsen i listan som är tillgänglig eller lägga till en lagrings platsen direkt genom att använda dess URL.

1. När du har valt den nödvändiga lagrings platsen flyttas posten lagrings platsen från avsnittet **databaser** till **fästa databaser** . Om det behövs kan du även välja en speciell gren av den lagrings platsen som du vill importera antecknings böckerna från.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Välj en lagrings plats och en gren":::

1. Klicka på **OK** för att slutföra importen. Alla antecknings böcker som är tillgängliga i den valda grenen av dina lagrings platsen importeras till ditt Azure Cosmos-konto.

När du har integrerat med ett GitHub-konto kan du bara se listan över databaser och antecknings böcker i ditt Azure Cosmos-konto. Den här instruktionen är sann även om flera användare loggar in på Azure Cosmos DB konto och lägger till sina egna konton. Med andra ord kan flera användare använda samma Azure Cosmos-konto för att ansluta Notebook-arbetsytan till GitHub. Varje användare ser dock bara listan över de databaser och antecknings böcker som de har importerat. De antecknings böcker som importeras av andra är inte synliga för dig.

Om du vill koppla från ditt GitHub-konto från arbets ytan notebooks, öppnar du fliken **datautforskaren** , väljer `…` bredvid **GitHub databaser** och väljer **Koppla från GitHub**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Redigera en antecknings bok och skicka ändringar till GitHub

Du kan redigera en befintlig antecknings bok eller lägga till en ny antecknings bok i lagrings platsen och spara ändringarna igen på GitHub.

När du har redigerat en befintlig antecknings bok väljer du **Spara**. En dialog ruta öppnas där du kan ange ett bekräftelse meddelande för de ändringar du har gjort. Välj **genomför** och antecknings boken i GitHub uppdateras. Du kan validera uppdateringarna genom att logga in på ditt GitHub-konto och verifiera inchecknings historiken.

I det vanliga GitHub-flödet efter att du har genomfört ändringarna, skickar du normalt ändringar till en fjärr anslutning. I det här fallet kan du dock använda alternativet "mellanlagring, genomförande och överföring" av dina uppdateringar till GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Redigera antecknings böcker och genomför ändringar i GitHub":::

När du har redigerat antecknings boken kan du [publicera den i anteckningsbok-galleriet](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter-anteckningsböcker.](cosmosdb-jupyter-notebooks.md)
* [Utforska exempel galleriet för bärbara datorer](https://cosmos.azure.com/gallery.html)
* [Publicera antecknings böcker i Azure Cosmos DB Notebook-galleriet](publish-notebook-gallery.md)
* [Använda python-funktioner och-kommandon i python](use-python-notebook-features-and-commands.md)
* [Använda C#-funktioner och-kommandon i C#](use-csharp-notebook-features-and-commands.md)
