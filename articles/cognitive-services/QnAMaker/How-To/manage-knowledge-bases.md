---
title: Hantera kunskaps banker – QnA Maker
description: Med QnA Maker kan du hantera dina kunskaps baser genom att ge åtkomst till kunskaps bas inställningar och innehåll.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 08736ce61e6768cfd9ac3d3ded7092a4ab93fca9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551786"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Skapa kunskaps bas och hantera inställningar

Med QnA Maker kan du hantera dina kunskaps baser genom att ge åtkomst till kunskaps bas inställningar och data källor.

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.
> * En [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) som skapats i Azure Portal. Kom ihåg Azure Active Directory ID, prenumeration, QnA-resurs namn som du valde när du skapade resursen.

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

1. Logga in på [QnAMaker.AI](https://QnAMaker.ai) -portalen med dina Azure-autentiseringsuppgifter.

1. I QnA Maker-portalen väljer du **skapa en kunskaps bas**.

1. På sidan **skapa** hoppar du över **steg 1** om du redan har din QNA Maker-resurs.

    Om du inte har skapat resursen ännu väljer du **skapa en QNA-tjänst**. Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Kom ihåg Azure Active Directory ID, prenumeration, QnA-resurs namn som du valde när du skapade resursen.

    När du är klar med att skapa resursen i Azure Portal går du tillbaka till QnA Maker Portal, uppdaterar sidan webbläsare och fortsätter till **steg 2**.

1. I **steg 3**väljer du din Active Directory, prenumeration, tjänst (resurs) och språket för alla kunskaps baser som skapats i tjänsten.

   ![Skärm bild av att välja en QnA Maker tjänst kunskaps bas](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Ge din kunskaps bas i **steg 3** `My Sample QnA KB` .

1. I **steg 4**konfigurerar du inställningarna med följande tabell:

    |Inställning|Värde|
    |--|--|
    |**Aktivera extrahering av flera adresser från URL: er, PDF-eller docx-filer.**|Markerad|
    |**Standard svars text**| `Quickstart - default answer not found.`|
    |**+ Lägg till URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Välj **Professional**|

1. I **steg 5**väljer **du skapa din KB**.

    Extraherings processen tar en stund att läsa dokumentet och identifiera frågor och svar.

    När QnA Maker har skapat kunskaps basen öppnas sidan **kunskaps bas** . Du kan redigera innehållet i kunskaps basen på den här sidan.

## <a name="edit-knowledge-base"></a>Redigera kunskapsbas

1.  Välj **Mina kunskaps banker** i det övre navigerings fältet.

       Du kan se alla tjänster som du har skapat eller delat med sorterade i fallande ordning efter **senaste ändrings** datum.

       ![Mina kunskaps baser](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Välj en viss kunskaps bas för att göra ändringar i den.

1.  Välj **inställningar**. Följande lista innehåller fält som du kan ändra.

       |Mål|Action|
       |--|--|
       |Lägg till URL|Du kan lägga till nya URL: er för att lägga till nya FAQ-innehåll i kunskaps basen genom att klicka på **Hantera kunskaps bas-> ' + Lägg till URL '**|
       |Ta bort URL|Du kan ta bort befintliga URL: er genom att välja borttagnings ikonen så kan pappers korgen.|
       |Uppdatera innehåll|Om du vill att din kunskaps bas ska crawla det senaste innehållet i befintliga webb adresser markerar du kryss rutan **Uppdatera** . Detta uppdaterar kunskaps basen med senaste URL-innehåll en gång. Detta ställer inte in ett regelbundet uppdaterings schema.|
       |Lägg till fil|Du kan lägga till ett fil dokument som stöds som en del av en kunskaps bas genom att välja **Hantera kunskaps bas**och sedan välja **+ Lägg till fil**|
    |Importera|Du kan också importera en befintlig kunskaps bas genom att välja knappen **Importera kunskaps bas** . |
    |Uppdatera|Uppdatering av kunskaps basen beror på vilken **hanterings pris nivå** som används när du skapar QNA Maker tjänst som är kopplad till din kunskaps bas. Du kan också uppdatera hanterings nivån från Azure Portal vid behov.

  1. När du är klar med att göra ändringar i kunskaps basen väljer du **Spara och träna** i det övre högra hörnet på sidan för att spara ändringarna.

       ![Spara och träna](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Om du lämnar sidan innan du väljer **Spara och träna**, kommer alla ändringar att gå förlorade.



## <a name="manage-large-knowledge-bases"></a>Hantera stora kunskaps baser

* **Data käll grupper**: kring grupperas efter den data källa som de extraherades från. Du kan expandera eller komprimera data källan.

    ![Använd QnA Maker data källans fält för att komprimera och expandera frågor och svar för data källor](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Sök i kunskaps basen**: du kan söka i kunskaps basen genom att skriva i text rutan längst upp i tabellen kunskaps bas. Klicka på RETUR för att söka efter frågan, svaret eller innehållet i metadata. Klicka på X-ikonen för att ta bort Sök filtret.

    ![Använd sökrutan QnA Maker ovanför frågorna och Svaren för att minska vyn till enbart filter matchnings objekt](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Sid brytning**: flytta snabbt genom data källor för att hantera stora kunskaps baser

    ![Använd QnA Maker sid brytnings funktioner ovanför frågorna och Svaren för att flytta genom sidor med frågor och svar](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Ta bort kunskaps baser

Att ta bort en kunskaps bas (KB) är en permanent åtgärd. Det går inte att återställa. Innan du tar bort en kunskaps bas bör du exportera kunskaps basen från sidan **Inställningar** i QNA Maker portalen.

Om du delar din kunskaps bas med samarbets partners,] (samarbets-Knowledge-base.md) och sedan tar bort den, förlorar alla åtkomst till KB.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att hantera språket](language-knowledge-base.md) i alla kunskaps baser i en resurs.

* Redigera QnA-par
* Hantera Azure-resurser som används av QnA Maker