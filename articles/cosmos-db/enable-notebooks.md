---
title: Aktivera antecknings böcker i Azure Cosmos DB konto (förhands granskning)
description: Med Azure Cosmos DB inbyggda antecknings böcker kan du analysera och visualisera dina data i portalen. I den här artikeln beskrivs hur du aktiverar den här funktionen för Cosmos-konton.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692784"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Aktivera antecknings böcker för Azure Cosmos DB-konton (förhands granskning)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Inbyggda antecknings böcker för Azure Cosmos DB är för närvarande tillgängliga i [29 regioner](#supported-regions). Om du vill använda antecknings böcker [skapar du ett nytt Cosmos-konto](#create-a-new-cosmos-account) eller [aktiverar antecknings böcker på ett befintligt konto](#enable-notebooks-in-an-existing-cosmos-account) i någon av dessa regioner. 

Inbyggda Jupyter Notebook i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure Portal. Den här artikeln beskriver hur du aktiverar den här funktionen för ditt Azure Cosmos DB-konto.

## <a name="create-a-new-cosmos-account"></a>Skapa ett nytt Cosmos-konto
Från och med 10 februari 2021 har nya Azure Cosmos-konton som skapats i en av de [regioner som stöds](#supported-regions) aktiverat automatiskt antecknings böcker. Det krävs ingen ytterligare konfiguration för att aktivera antecknings böcker. Använd följande instruktioner för att skapa ett nytt konto:
1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **skapa en resurs**  >  **databas**  >  **Azure Cosmos DB**.
1. Ange grundläggande inställningar för kontot.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Den nya kontosidan för Azure Cosmos DB":::

1. Välj **Granska + skapa**. Du kan hoppa över alternativet **nätverk** och **taggar** . 
1. Granska kontoinställningarna och välj sedan **Skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Meddelandefönstret på Azure-portalen":::

1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Sidan för Azure Cosmos DB-kontot":::

1. Navigera till rutan **datautforskaren** . Nu bör du se din arbets yta för antecknings böcker.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Arbets ytan ny Azure Cosmos DB Notebooks":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Aktivera antecknings böcker i ett befintligt Cosmos-konto

Du kan också aktivera antecknings böcker på befintliga konton. Det här steget måste bara utföras en gång per konto.

1. Navigera till fönstret **datautforskaren** i ditt Cosmos-konto.
1. Välj **Aktivera antecknings böcker**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Skapa en ny arbets yta för antecknings böcker i Datautforskaren":::

1. Då uppmanas du att skapa en ny arbets yta för antecknings böcker. Välj **Slutför installationen.**
1. Ditt konto har nu Aktiver ATS för att använda antecknings böcker!

## <a name="create-and-run-your-first-notebook"></a>Skapa och kör din första bärbara dator

Du kan kontrol lera att du kan använda antecknings böcker genom att välja en av antecknings böckerna under exempel antecknings böcker. Då sparas en kopia av antecknings boken på din arbets yta och den öppnas.

I det här exemplet använder vi **GettingStarted. ipynb**.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Visa GettingStarted. ipynb Notebook":::

Så här kör du antecknings boken:
1. Välj den första kod cellen som innehåller python-kod.
1. Välj **Kör** för att köra cellen. Du kan också använda **Shift + Retur** för att köra cellen.
1. Uppdatera resurs fönstret om du vill se databasen och behållaren som har skapats.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Kör kom igång-anteckningsbok":::

Du kan också välja **ny Notebook** för att skapa en ny antecknings bok eller ladda upp en befintlig Notebook-fil (. ipynb) genom att välja **Ladda upp fil** på menyn **Mina antecknings böcker** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Skapa eller ladda upp en ny antecknings bok":::

## <a name="supported-regions"></a>Regioner som stöds
Inbyggda notebook-filer för Azure Cosmos DB är för närvarande tillgängliga i 29 Azure-regioner. Nya Azure Cosmos-konton som skapats i dessa regioner kommer att ha antecknings böcker automatiskt aktiverade. Antecknings böcker är kostnads fria med ditt konto. 

- Australien, centrala
- Australien, centrala 2
- Australien, östra
- Australien, sydöstra
- Brasilien, södra
- Kanada, centrala
- Kanada, östra
- Indien, centrala
- Central US
- East US
- USA, östra 2
- Frankrike, centrala
- Frankrike, södra
- Tyskland, norra
- Tyskland, västra centrala
- Japan, västra
- Sydkorea, södra
- USA, norra centrala
- Europa, norra
- USA, södra centrala
- Sydostasien
- Schweiz, norra
- Förenade Arabemiraten Central
- Storbritannien, södra
- Storbritannien, västra
- USA, västra centrala
- Europa, västra
- Indien, västra
- USA, västra 2

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
* [Utforska exempel galleriet för bärbara datorer](https://cosmos.azure.com/gallery.html)
* [Publicera antecknings böcker i Azure Cosmos DB Notebook-galleriet](publish-notebook-gallery.md)
* [Använda python-funktioner och-kommandon i python](use-python-notebook-features-and-commands.md)
* [Använda C#-funktioner och-kommandon i C#](use-csharp-notebook-features-and-commands.md)
* [Importera antecknings böcker från en GitHub-lagrings platsen](import-github-notebooks.md)
