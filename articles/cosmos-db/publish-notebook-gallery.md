---
title: Publicera antecknings böcker i Azure Cosmos DB Notebook-galleriet
description: Lär dig hur du hämtar antecknings böcker från det offentliga galleriet, redigerar dem och publicerar dina egna antecknings böcker i galleriet.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039333"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publicera antecknings böcker i Azure Cosmos DB Notebook-galleriet
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB inbyggda antecknings böcker för Jupyter är direkt integrerade i dina Azure Cosmos DB-konton i Azure Portal. Med hjälp av dessa antecknings böcker kan du analysera och visualisera data från Azure Portal. Inbyggda antecknings böcker för Azure Cosmos DB är för närvarande tillgängliga i [många regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Om du vill använda antecknings böcker [skapar du ett nytt Cosmos-konto](create-cosmosdb-resources-portal.md) eller [aktiverar antecknings böcker på ett befintligt konto](enable-notebooks.md) i någon av dessa regioner.

Notebook-miljön i Azure Portal har några exempel som publicerats av Azure Cosmos DBs teamet. Det har också ett offentligt galleri där du kan publicera och dela dina egna antecknings böcker. När en antecknings bok har publicerats i galleriet är den tillgänglig för alla Azure Cosmos DB användare att visa och använda. I den här artikeln får du lära dig hur du använder antecknings böcker från det offentliga galleriet och publicerar din antecknings bok i galleriet.

## <a name="download-a-notebook-from-the-gallery"></a>Ladda ned en bärbar dator från galleriet

Använd följande steg för att visa och ladda ned antecknings böcker från galleriet till din egna antecknings bok arbets yta:

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till ditt Azure Cosmos DB-konto som är aktiverat med Notebook-miljön.

1. Gå till fliken för det  >    >    >  **offentliga galleriet** för Galleri för datautforskaren-anteckningsbok.

1. Godkänn [uppföranderegler](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  innan du visar eller publicerar antecknings böcker i galleriet. Uppförandekoden loggas per användare, enligt prenumerations nivån. När du växlar till en annan prenumeration måste du acceptera den igen innan du får åtkomst till galleriet. Om du vill acceptera uppförandekoden markerar du kryss rutan och **fortsätter**:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Navigera till det offentliga galleriet för antecknings böcker och godkänn uppförandekoden.":::

1. Sedan kan du lägga till en speciell antecknings bok på fliken favoriter eller ladda ned den. När du laddar ned en antecknings bok kopieras den till arbets ytan för antecknings böcker där du kan köra eller redigera den.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Ladda ned en antecknings bok från galleriet till din arbets yta.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publicera en antecknings bok i galleriet

När du skapar egna antecknings böcker eller redigerar befintliga antecknings böcker för att passa ett annat scenario, kanske du vill publicera dem i galleriet. När en antecknings bok har publicerats i galleriet kan andra användare komma åt den. Du kan [utforska exempel galleriet för bärbara datorer](https://cosmos.azure.com/gallery.html) för att visa tillgängliga antecknings böcker.

Använd följande steg för att publicera en antecknings bok:

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till ditt Azure Cosmos DB-konto som är aktiverat med Notebook-miljön.

1. Gå till fliken **datautforskaren** antecknings böcker på  >  **bärbara**  >  **datorer** .

1. Gå till den bärbara dator som du vill publicera. Välj knappen **Spara** i kommando fältet och välj sedan **Publicera till Galleri**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Välj en antecknings bok att publicera i galleriet.":::

   Du kan också hitta alternativet **Publicera till Galleri** genom att välja **...** bredvid namnet på antecknings boken:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="En annan metod för att välja en antecknings bok att publicera i galleriet.":::

1. Fyll i formuläret **Publicera till Galleri** med följande information:

   * **Namn:** Ett eget namn som identifierar din bärbara dator.
   * **Beskrivning:**  En kort beskrivning av vad din antecknings bok gör.
   * **Taggar:** Taggarna är valfria och används för att filtrera resultat när de genomsöks med ett nyckelord.
   * **Bild av omslag:** En bild som används på försättsbladet när antecknings boken publiceras. Du kan välja något av följande alternativ:
   * **Anpassad avbildning** – du kan ladda upp en bild från datorn. Välj en avbildnings fil med proportioner 256x144.
   * **URL** – ange en offentligt tillgänglig URL där bilden finns.
   * **Ta skärm bild** – en skärm bild av din öppna antecknings bok tas automatiskt emot och laddas upp till för hands versionen.
   * **Använd första visningen av utdata** för den första cellen som har ett visnings resultat. Celler som endast visar markdown/text räknas inte som en visning av utdata.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Fyll i formuläret publicera till Galleri.":::

   > [!NOTE]
   > Om du använder alternativet **Publicera på galleriet** från **...** bredvid namnet på antecknings boken visas inte alla alternativ för **bild** . Det beror på att antecknings boken kanske inte är öppen och att Azure Cosmos DB inte har åtkomst att ta en skärm bild eller komma åt den första skärmens utdata.

1. Kontrol lera att förhands granskningen ser bra ut och välj **publicera**. När den publiceras visas den här antecknings boken i det offentliga galleriet för Azure Cosmos DB Notebooks. Kontrol lera att du har tagit bort känsliga data eller utdata innan du publicerar. Antecknings bokens innehåll genomsöks efter överträdelser av Microsofts principer innan publicering. Det tar vanligt vis några sekunder att slutföra den här processen. Om det finns några överträdelser visas ett meddelande på fliken meddelande. Din bärbara dator kommer inte att publiceras om den hittar en överträdelse. du tar bort den överträdande texten och publicerar den igen.

   > [!NOTE]
   > När antecknings böckerna har publicerats i det offentliga galleriet kan alla Azure Cosmos DB användare se dem. Åtkomsten är inte begränsad till per prenumeration eller per organisations nivå.

1. När antecknings boken har publicerats i galleriet kan du se den på fliken **Mina publicerade arbeten** . Du kan också ta bort eller ta bort antecknings böcker som du har publicerat från det offentliga galleriet.

1. Du kan också rapportera en antecknings bok som strider mot uppförandekoden. Om en överträdelse hittas tar Cosmos DB automatiskt bort antecknings boken från galleriet. Om en antecknings bok tas bort kan användarna se den på fliken **Mina publicerade arbeten** med den borttagna anteckningen. Om du vill rapportera en antecknings bok går du till fliken för galleriet för **datautforskaren**-  >  **anteckningsbok**  >  **Galleri**  >   . öppna den antecknings bok som du vill rapportera, Hovra över **Hjälp** knappen i det högra hörnet och välj **rapportera missbruk**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Rapportera en antecknings bok som bryter mot uppföranderegler.":::

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
* [Använda python-funktioner och-kommandon i python](use-python-notebook-features-and-commands.md)
* [Använda C#-funktioner och-kommandon i C#](use-csharp-notebook-features-and-commands.md)
* [Importera antecknings böcker från en GitHub-lagrings platsen](import-github-notebooks.md)
