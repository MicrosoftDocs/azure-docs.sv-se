---
title: Migrera kunskaps baser – QnA Maker
description: För att migrera en kunskaps bas måste du exportera från en kunskaps bas och sedan importera till en annan.
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 4148431ebb4de288ac517c7c006a925e36c5f460
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542929"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrera en kunskaps bas med export-import

Migrering är en process där du skapar en ny kunskaps bas från en befintlig kunskaps bas. Det kan du göra på flera olika orsaker:

* process för säkerhets kopiering och återställning
* CI/CD-pipeline
* flytta regioner

För att migrera en kunskaps bas måste du exportera från en befintlig kunskaps bas och sedan importera till en annan.

## <a name="prerequisites"></a>Förutsättningar

* Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.
* Konfigurera en ny [QNA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrera en kunskaps bas från QnA Maker
1. Logga in på [QNA Maker Portal](https://qnamaker.ai).
1. Välj den ursprungliga kunskaps basen som du vill migrera.

1. På sidan **Inställningar** väljer du **Exportera kunskaps bas** för att ladda ned en. TSV-fil som innehåller innehållet i ditt ursprung i kunskaps basen – frågor, svar, metadata, uppföljnings frågor och data käll namn som de extraherades från.

1. Välj **skapa en kunskaps bas** på den översta menyn och skapa en _Tom_ kunskaps bas. Det är tomt eftersom när du skapar den, kommer du inte att lägga till några URL: er eller filer. De läggs till under steget importera när du har skapat.

    Konfigurera kunskaps basen. Ange endast det nya namnet på kunskaps basen. Dubblettnamn stöds och specialtecken stöds också.

    Välj inte något från steg 4 eftersom dessa värden kommer att skrivas över när du importerar filen.

1. I steg 5 väljer du **skapa**.

1. I den här nya kunskaps basen öppnar du fliken **Inställningar** och väljer **Importera kunskaps bas**. Detta importerar frågor, svar, metadata, uppföljnings frågor och behåller de data käll namn som de extraherades från.

   > [!div class="mx-imgBorder"]
   > [![Importera kunskaps bas](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Testa** den nya kunskaps basen med hjälp av test panelen. Lär dig hur du [testar din kunskaps bas](../How-To/test-knowledge-base.md).

1. **Publicera** kunskaps basen och skapa en chatt-robot. Lär dig hur du [publicerar din kunskaps bas](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migrera en kunskaps bas program mässigt från QnA Maker

Migreringsprocessen är program mässigt tillgänglig med följande REST-API: er:

**Export**

* [Hämta kunskaps bas-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importera**

* [Ersätt API (Läs in med samma kunskaps bas-ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Skapa API (Load med nytt kunskaps bas-ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Chat-loggar och ändringar
Skift läges känsliga ändringar (synonymer) importeras inte automatiskt. Använd [v4-API: er](https://go.microsoft.com/fwlink/?linkid=2092179) för att flytta ändringarna i den nya kunskaps basen.

Det finns inget sätt att migrera chatt loggar, eftersom den nya kunskaps basen använder Application Insights för att lagra chatt loggar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-To/edit-knowledge-base.md)
