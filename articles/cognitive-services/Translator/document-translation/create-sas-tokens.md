---
title: Skapa token för signatur för delad åtkomst (SAS) för behållare och blobbar med Microsoft Storage Explorer
description: Så här skapar du en token för delad åtkomst (SAS) för behållare och blobbar med Microsoft Storage Explorer och Azure Portal
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489641"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Skapa SAS-token för dokument översättnings bearbetning

I den här artikeln får du lära dig hur du skapar SAS-token (signatur för delad åtkomst) med hjälp av Azure Storage Explorer eller Azure Portal. En SAS-token ger säker och delegerad åtkomst till resurser i ditt Azure Storage-konto.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Skapa SAS-token med Azure Storage Explorer

### <a name="prerequisites"></a>Förutsättningar

* Du behöver en [**Azure Storage Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) -app installerad i din Windows-, MacOS-eller Linux-utvecklings miljö. Azure Storage Explorer är ett kostnads fritt verktyg som gör att du enkelt kan hantera dina moln lagrings resurser i Azure.
* När du har installerat Azure Storage Explorer-appen [ansluter du den till det lagrings konto](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) som du använder för dokument översättning.

### <a name="create-your-tokens"></a>Skapa dina token

### <a name="sas-tokens-for-containers"></a>[SAS-token för behållare](#tab/Containers)

1. Öppna appen Azure Storage Explorer på den lokala datorn och gå till dina anslutna **lagrings konton**.
1. Expandera noden lagrings konton och välj **BLOB-behållare**.
1. Expandera noden BLOB-behållare och högerklicka på en nod för lagrings **behållare** eller Visa alternativ-menyn.
1. Välj **Hämta signatur för delad åtkomst...** från alternativ-menyn.
1. Gör följande val i fönstret **signatur för delad åtkomst** :
    * Välj din **åtkomst princip** (Standardvärdet är ingen).
    * Ange **Start** -och **utgångs** datum och-tid för signerad nyckel. En kort livs längd rekommenderas eftersom en SAS, när den har genererats, inte kan återkallas.
    * Välj tidszon **för start** -och förfallo datum och-tid (standard är lokalt).
    * Definiera dina behållar **behörigheter** genom att kontrol lera och/eller avmarkera lämplig kryss ruta.
    * Granska och välj **skapa**.

1. Ett nytt fönster kommer att visas med **behållarens** namn, **URI** och **frågesträng** för din behållare.  
1. **Kopiera och klistra in värdena för behållare, URI och frågesträng på en säker plats. De visas bara en gång och kan inte hämtas när fönstret har stängts.**
1. Om du vill skapa en SAS-URL lägger du till SAS-token (URI) till URL: en för en lagrings tjänst.

### <a name="sas-tokens-for-blobs"></a>[SAS-token för blobbar](#tab/blobs)

1. Öppna appen Azure Storage Explorer på den lokala datorn och gå till dina anslutna **lagrings konton**.
1. Expandera noden lagring och välj **BLOB-behållare**.
1. Expandera noden BLOB-behållare och välj en **container** -nod för att visa innehållet i huvud fönstret.
1. Välj den BLOB där du vill delegera SAS-åtkomst och högerklicka för att Visa menyn Alternativ.
1. Välj **Hämta signatur för delad åtkomst...** från alternativ-menyn.
1. Gör följande val i fönstret **signatur för delad åtkomst** :
    * Välj din **åtkomst princip** (Standardvärdet är ingen).
    * Ange **Start** -och **utgångs** datum och-tid för signerad nyckel. En kort livs längd rekommenderas eftersom en SAS, när den har genererats, inte kan återkallas.
    * Välj tidszon **för start** -och förfallo datum och-tid (standard är lokalt).
    * Definiera dina behållar **behörigheter** genom att kontrol lera och/eller avmarkera lämplig kryss ruta.
    * Granska och välj **skapa**.
1. Ett nytt fönster visas med **BLOB** -namnet, **URI: n** och **frågesträngen** för din BLOB.  
1. **Kopiera och klistra in BLOB-, URI-och frågesträng-värden på en säker plats. De visas bara en gång och kan inte hämtas när fönstret har stängts.**
1. Om du vill skapa en SAS-URL lägger du till SAS-token (URI) till URL: en för en lagrings tjänst.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Skapa SAS-token för blobbar i Azure Portal

> [!NOTE]
> Det finns för närvarande inte stöd för att skapa SAS-token för behållare direkt i Azure Portal. Du kan dock skapa en SAS-token med [**Azure Storage Explorer**](#create-your-sas-tokens-with-azure-storage-explorer) eller slutföra aktiviteten [program mässigt](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

* Ett aktivt [**Azure-konto**](https://azure.microsoft.com/free/cognitive-services/).  Om du inte har någon kan du [**skapa ett kostnads fritt konto**](https://azure.microsoft.com/free/).
* En [**Translator**](https://ms.portal.azure.com/#create/Microsoft) -tjänst resurs (**inte** en Cognitive Services flera tjänst resurser.  *Se* [skapa en ny Azure-resurs](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Ett [**Azure Blob Storage-konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du skapar behållare för att lagra och organisera dina BLOB-data i ditt lagrings konto.

### <a name="create-your-tokens"></a>Skapa dina token

Gå till [Azure Portal](https://ms.portal.azure.com/#home) och navigera enligt följande:  

 **Ditt lagrings konto** → **behållare** → din **behållare** → **din BLOB**

1. Välj **skapa SAS** på menyn längst upp på sidan.

1. Välj **signerings metod** → **användar Delegerings nyckel**.

1. Definiera **behörigheter** genom att markera och/eller avmarkera lämplig kryss ruta.

1. Ange **Start** -och **förfallo** tider för signerade nycklar.

1. Fältet **tillåtna IP-adresser** är valfritt och anger en IP-adress eller ett intervall med IP-adresser som begär Anden ska accepteras från. Om IP-adressen för begäran inte matchar IP-adressen eller adress intervallet som angetts för SAS-token, är den inte auktoriserad.

1. Fältet **tillåtna protokoll** är valfritt och anger det protokoll som tillåts för en begäran som gjorts med SAS. Standardvärdet är HTTPS.

1. Granska och välj sedan **skapa SAS-token och URL**.

1. **Token för blobb-SAS-token** och **blobb-SAS-URL** visas i det nedre fönstret.  

1. **Kopiera och klistra in BLOB SAS-token och URL-värden på en säker plats. De visas bara en gång och kan inte hämtas när fönstret har stängts.**

1. Om du vill skapa en SAS-URL lägger du till SAS-token (URI) till URL: en för en lagrings tjänst.

## <a name="learn-more"></a>Läs mer

* [Skapa SAS-token för blobbar eller behållare program mässigt](../../../storage/blobs/sas-service-create.md)
* [Behörigheter för en katalog, behållare eller BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med dokument Översättning](get-started-with-document-translation.md)
>
>