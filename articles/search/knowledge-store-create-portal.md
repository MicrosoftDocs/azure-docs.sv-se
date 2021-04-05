---
title: Skapa ett kunskaps lager i Azure Portal
titleSuffix: Azure Cognitive Search
description: Använd guiden Importera data för att skapa ett kunskaps lager som används för beständigt innehåll. Anslut till ett kunskaps lager för analys från andra appar eller skicka berikat innehåll till efterföljande processer.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94741815"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Snabb start: skapa ett kunskaps lager för Azure Kognitiv sökning i Azure Portal

Knowledge Store är en funktion i Azure Kognitiv sökning som sparar utdata från en pipeline för innehålls bearbetning för efterföljande analyser eller underordnad bearbetning. 

En pipeline accepterar ostrukturerad text-och bild innehåll, använder AI som drivs av Cognitive Services (till exempel OCR och naturlig språk bearbetning) och utvärderar nya strukturer och information som inte tidigare fanns. En av de fysiska artefakterna som skapas av en pipeline är ett [kunskaps lager](knowledge-store-concept-intro.md), som du kan komma åt via verktyg för att analysera och utforska innehåll.

I den här snabb starten ska du kombinera tjänster och data i Azure-molnet för att skapa ett kunskaps lager. När allt är på plats kör du guiden **Importera data** i portalen för att hämta den tillsammans. Slut resultatet är original text innehåll och AI-genererat innehåll som du kan visa i portalen ([Storage Explorer](knowledge-store-view-storage-explorer.md)).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ Ett Azure Storage konto med [Blob Storage](../storage/blobs/index.yml).

> [!NOTE]
> I den här snabb starten används även [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. Eftersom arbets belastningen är så liten, överCognitive Servicess i bakgrunden för kostnads fri bearbetning för upp till 20 transaktioner. Det innebär att du kan slutföra den här övningen utan att behöva skapa ytterligare Cognitive Services-resurser.

## <a name="set-up-your-data"></a>Konfigurera dina data

I följande steg konfigurerar du en BLOB-behållare i Azure Storage att lagra heterogena innehållsfiler.

1. [Ladda ned HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dessa data är hotell gransknings data som sparats i en CSV-fil (härstammar från Kaggle.com) och innehåller 19 stycken kundfeedback om ett enda hotell. 

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under din aktuella prenumeration. Du använder Azure Storage för både det råa innehåll som ska importeras och kunskaps lagret som är slut resultatet.

   + Välj konto typen **StorageV2 (General Purpose v2)** .

1. Öppna BLOB Services-sidorna och skapa en behållare med namnet *Hotell – recensioner*.

1. Klicka på **Överför**.

    ![Överför data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp Hotell recensioner")

1. Välj den **HotelReviews-Free.csv** -fil som du laddade ned i det första steget.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

1. Innan du avslutar Blob Storage-sidorna använder du en länk i det vänstra navigerings fönstret för att öppna sidan **åtkomst nycklar** . Hämta en anslutnings sträng för att hämta data från Blob Storage. En anslutnings sträng ser ut ungefär som i följande exempel: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Nu kan du gå vidare till guiden **Importera data** .

## <a name="run-the-import-data-wizard"></a>Kör guiden Importera data

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

1. [Hitta Sök tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) och klicka på **Importera data** i kommando fältet för att skapa ett kunskaps lager i fyra steg på sidan Översikt.

   ![Kommandot Importera data](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

1. I **Anslut till dina data** väljer du **Azure Blob Storage** och väljer kontot och containern som du skapade. 

1. Som **namn** anger du `hotel-reviews-ds` .

1. För **tolknings läge** väljer du **avgränsad text** och markerar sedan kryss rutan **första raden innehåller rubrik** . Se till att **avgränsnings tecken** är ett kommatecken (,).

1. I **anslutnings strängen** klistrar du in anslutnings strängen som du kopierade från sidan **åtkomst nycklar** i Azure Storage.

1. I **behållare** anger du namnet på BLOB-behållaren som innehåller data.

    Sidan bör se ut ungefär som på följande skärm bild.

    ![Skapa ett data käll objekt](media/knowledge-store-create-portal/hotel-reviews-ds.png "Skapa ett data käll objekt")

1. Fortsätt till nästa sida.

### <a name="step-2-add-cognitive-skills"></a>Steg 2: Lägga till kognitiva kunskaper

I den här guiden ska du skapa en färdigheter med kognitiva färdigheter. Källdata består av kund granskningar på flera språk. Kunskaper som är relevanta för den här data uppsättningen inkluderar extrahering av nyckel fraser, sentiment identifiering och text översättning. I ett senare steg kommer dessa att bli "projiceras" i ett kunskaps lager som Azure-tabeller.

1. Expandera **bifoga Cognitive Services**. **Kostnads fri (begränsade anrikninger)** är valt som standard. Du kan använda den här resursen eftersom antalet poster i HotelReviews-Free.csv är 19 och den här kostnads fria resursen tillåter upp till 20 transaktioner per dag.

1. Expandera **Lägg till anrikninger**.

1. För **färdigheter-namn** anger du `hotel-reviews-ss` .

1. I **fältet käll data** väljer du **reviews_text**.

1. Välj **sidor (5000-segment)** för **detaljerad granularitet nivå**

1. Välj dessa kognitiva kunskaper:
    + **Extrahering av diskussionsämne**
    + **Översätt text**
    + **Identifiera sentiment**

      ![Skapa en kunskapsuppsättning](media/knowledge-store-create-portal/hotel-reviews-ss.png "Skapa en kunskapsuppsättning")

1. Expandera **Spara berikade kunskaper i kunskaps lagret**.

1. Välj följande **Azure Table-projektioner**:
    + **Dokument**
    + **Sidor**
    + **Nyckel fraser**

1. Ange **lagrings kontots anslutnings sträng** som du sparade i föregående steg.

    ![Konfigurera kunskaps lager](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurera kunskaps lager")

1. Du kan också hämta en Power BI-mall. När du öppnar mallen från guiden anpassas den lokala. pbit-filen så att den återspeglar form på dina data.

1. Fortsätt till nästa sida.

### <a name="step-3-configure-the-index"></a>Steg 3: Konfigurera indexet

I det här steget konfigurerar du ett index för valfria full texts öknings frågor. Guiden kommer att sampla data källan för att härleda fält och data typer. Du behöver bara välja attributen för det önskade beteendet. Attributet för **hämtning** kan till exempel tillåta Sök tjänsten att returnera ett fält värde medan **sökbart** aktiverar fullständig texts ökning i fältet.

1. För **index namn** anger du `hotel-reviews-idx` .

1. För attribut accepterar du standard valen: **hämtnings** Bart och **sökbara** för de nya fälten som pipelinen skapar.

    Ditt index bör se ut ungefär som på följande bild. Eftersom listan är lång visas inte alla fält i bilden.

    ![Konfigurera ett index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurera ett index")

1. Fortsätt till nästa sida.

### <a name="step-4-configure-the-indexer"></a>Steg 4: Konfigurera indexeraren

I det här steget ska du konfigurera en indexerare som hämtar data källan, färdigheter och det index som du definierade i föregående steg i guiden.

1. Som **namn** anger du `hotel-reviews-idxr` .

1. Behåll **standardvärdet** för **schema**.

1. Klicka på **Skicka** för att köra indexeraren. Data extrahering, indexering, program av kognitiva kunskaper alla sker i det här steget.

## <a name="monitor-status"></a>Övervaka status

Kognitiva kunskaps indexering tar längre tid än vanlig text baserad indexering. Guiden ska öppna listan med indexerare på översiktssidan så att du kan följa förloppet. För självnavigering går du till översikten över sidan och klickar på **indexerare**.

I Azure Portal kan du också övervaka aktivitets loggen för meddelanden som länkar till en klicknings bara **Azure kognitiv sökning meddelande** status. Det kan ta flera minuter att slutföra körningen.

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data med Cognitive Services och projicerat resultaten i ett kunskaps lager, kan du använda Storage Explorer eller Power BI för att utforska din omfattande data uppsättning.

Du kan visa innehåll i Storage Explorer eller ta ett steg ytterligare med Power BI för att få insikter genom visualisering.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md) 
>  [Anslut till Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Om du vill upprepa den här övningen eller testa en annan AI-förhands granskning tar du bort indexet *hotell reidxrs-* . Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll för Cognitive Services bearbetning.
