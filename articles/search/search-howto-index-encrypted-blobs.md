---
title: Sök över krypterat innehåll i Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Lär dig att indexera och extrahera text från krypterade dokument i Azure Blob Storage med Azure Kognitiv sökning.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96530943"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Så här indexerar du krypterade blobbar med BLOB-indexerare och färdighetsuppsättningar i Azure Kognitiv sökning

Den här artikeln visar hur du använder [azure kognitiv sökning](search-what-is-azure-search.md) för att indexera dokument som tidigare har krypterats i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) med [Azure Key Vault](../key-vault/general/overview.md). Normalt kan en indexerare inte extrahera innehåll från krypterade filer eftersom den inte har åtkomst till krypterings nyckeln. Men genom att använda den anpassade [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) -kunskapen som följer av [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)kan du ge kontrollerad åtkomst till nyckeln för att dekryptera filerna och sedan låta innehållet hämtas från dem. Detta låser upp möjligheten att indexera dessa dokument utan att kompromissa med krypterings statusen för dina lagrade dokument.

Från och med tidigare krypterade hela dokument (ostrukturerad text) som PDF, HTML, DOCX och PPTX i Azure Blob Storage, använder den här guiden Postman och Sök REST-API: er för att utföra följande uppgifter:

> [!div class="checklist"]
> * Definiera en pipeline som dekrypterar dokumenten och extraherar text från dem.
> * Definiera ett index för att lagra utdata.
> * Kör pipelinen för att skapa och läsa in indexet.
> * Utforska resultat med fullständig texts ökning och en omfattande frågesyntax.

Om du inte har någon Azure-prenumeration kan du öppna ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

I det här exemplet förutsätts att du redan har laddat upp dina filer till Azure Blob Storage och har krypterat dem i processen. Om du behöver hjälp med att hämta filer som ursprungligen har laddats upp och krypteras kan du kolla in [den här självstudien](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) för hur du gör det.

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) i samma prenumeration som Azure kognitiv sökning. Nyckel valvet måste ha aktiverat **skydd mot** **borttagning** och rensning.
+ [Azure kognitiv sökning](search-create-service-portal.md) på en [fakturerbar nivå](search-sku-tier.md#tier-descriptions) (Basic eller över, i valfri region)
+ [Azure-funktion](https://azure.microsoft.com/services/functions/)
+ [Skrivbordsappen Postman](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 – skapa tjänster och samla in autentiseringsuppgifter

### <a name="set-up-the-custom-skill"></a>Konfigurera den anpassade kompetensen

I det här exemplet används [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) -projektet från [Azure Search Power färdigheter](https://github.com/Azure-Samples/azure-search-power-skills) GitHub-lagringsplatsen. I det här avsnittet ska du distribuera kunskapen till en Azure-funktion så att den kan användas i en färdigheter. Ett inbyggt distributions skript skapar en Azure Function-resurs med namnet som börjar med **psdbf-Function-app-** och läser in kunskapen. Du uppmanas att ange en prenumeration och en resurs grupp. Se till att välja samma prenumeration som din Azure Key Vault-instans finns i.

DecryptBlobFile-kompetensen tar till exempel URL: en och SAS-token för varje blob som indata, och den visar den nedladdade, dekrypterade filen med det fil referens avtal som Azure Kognitiv sökning förväntar sig. Kom ihåg att DecryptBlobFile behöver krypterings nyckeln för att utföra dekrypteringen. Som en del av inställningen skapar du också en åtkomst princip som ger DecryptBlobFile-funktionen åtkomst till krypterings nyckeln i Azure Key Vault.

1. Klicka på knappen **distribuera till Azure** som finns på [DecryptBlobFile-landnings sidan](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), som öppnar den tillhandahållna Resource Manager-mallen i Azure Portal.

1. Välj **den prenumeration där Azure Key Vault-instansen finns** (den här guiden fungerar inte om du väljer en annan prenumeration) och antingen väljer en befintlig resurs grupp eller skapar en ny (om du skapar en ny) måste du också välja en region som du vill distribuera till.

1. Välj **Granska + skapa**, se till att du godkänner villkoren och välj sedan **skapa** för att distribuera Azure-funktionen.

    ![ARM-mall i portalen](media/indexing-encrypted-blob-files/arm-template.jpg "ARM-mall i portalen")

1. Vänta tills distributionen är färdig.

1. Navigera till din Azure Key Vault-instans i portalen. [Skapa en åtkomst princip](../key-vault/general/assign-access-policy-portal.md) i Azure Key Vault som ger nyckel åtkomst till den anpassade kunskapen.
 
    1. Under **Inställningar** väljer du **åtkomst principer** och väljer sedan **Lägg till åtkomst princip**
     
       ![Nyckel valv Lägg till åtkomst princip](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Åtkomst principer för nyckel valv")

    1. Under **Konfigurera från mall** väljer du **Azure Data Lake Storage eller Azure Storage**.

    1. För huvud kontot väljer du den Azure Function-instans som du har distribuerat. Du kan söka efter den med hjälp av det resurs-prefix som användes för att skapa det i steg 2, som har ett standardvärde för **psdbf-Function-app**.

    1. Välj inte något för alternativet för auktoriserade program.
     
        ![Lägg till åtkomst princip mal len för nyckel valv](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Mall för åtkomst principer för nyckel valv")

    1. Se till att klicka på **Spara** på sidan åtkomst principer innan du navigerar för att lägga till åtkomst principen.
     
         ![Spara åtkomst princip för nyckel valv](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Spara åtkomst princip för nyckel valv")

1. Navigera till funktionen **psdbf-Function-app** i portalen och anteckna följande egenskaper eftersom du kommer att behöva dem senare i guiden:

    1. Funktions webb adressen, som du hittar under **Essentials** på huvud sidan för funktionen.
    
        ![Funktionswebbadress](media/indexing-encrypted-blob-files/function-uri.jpg "Var du hittar URL: en för Azure Function")

    1. Värd nyckel koden, som du hittar genom att navigera till app- **nycklar**, klicka för att visa **standard** nyckeln och kopiera värdet.
     
        ![Funktions värd nyckel kod](media/indexing-encrypted-blob-files/function-host-key.jpg "Var du hittar nyckel koden för Azure Function Host")

### <a name="cognitive-services"></a>Cognitive Services

AI-anrikning och färdigheter-körning backas upp av Cognitive Services, inklusive Textanalys och Visuellt innehåll för naturligt språk och bild bearbetning. Om målet var att slutföra en faktisk prototyp eller ett projekt, skulle du i den här punkten etablera Cognitive Services (i samma region som Azure Kognitiv sökning) så att du kan koppla den till indexerings åtgärder.

I den här övningen kan du hoppa över resurs etableringen eftersom Azure Kognitiv sökning kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnads fria transaktioner per indexerare. När den bearbetar 20 dokument Miss kommer indexeraren att köras om inte en Cognitive Services nyckel är kopplad till färdigheter. För större projekt bör du planera för etablering Cognitive Services på S0-nivån betala per användning. Mer information finns i [bifoga Cognitive Services](cognitive-search-attach-cognitive-services.md). Observera att en Cognitive Services nyckel krävs för att köra en färdigheter med fler än 20 dokument även om ingen av dina valda kognitiva kunskaper ansluter till Cognitive Services (till exempel med den angivna färdigheter om inga kunskaper läggs till).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den sista komponenten är Azure-Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här guiden. 

Som med Azure-funktionen kan du ägna en stund åt att samla in administratörs nyckeln. Vidare måste du, när du börjar strukturera förfrågningar, ange slut punkt och Admin-API-nyckel som används för att autentisera varje begäran.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Hämta en Admin API-nyckel och URL för Azure Kognitiv sökning

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta namnet på din Sök tjänst på sidan **Översikt över** Sök tjänsten. Du kan bekräfta tjänst namnet genom att granska slut punkts-URL: en. Om slut punkts-URL: en var `https://mydemo.search.windows.net` , är tjänstens namn `mydemo` .

2. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   ![Hämta tjänstens namn och administratör och fråge nycklar](media/search-get-started-javascript/service-name-and-keys.png)

Alla begär Anden kräver en API-nyckel i rubriken för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende per begäran mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-postman"></a>2 – Konfigurera PostMan

Installera och konfigurera Postman.

### <a name="download-and-install-postman"></a>Hämta och installera Postman

1. Hämta [käll koden för Postman-samlingen](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Välj **fil**  >  **import** för att importera käll koden till Postman.
1. Välj fliken **samlingar** och välj sedan knappen **...** (ellips).
1. Välj **Redigera**. 
   
   ![Postman-app som visar navigering](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Gå till redigerings menyn i Postman")
1. I dialog rutan **Redigera** väljer du fliken **variabler** . 

På fliken **variabler** kan du lägga till värden som Postman växlar i varje gång det påträffar en speciell variabel inom dubbla klammerparenteser. Postman ersätter exempelvis symbolen `{{admin-key}}` med det aktuella värde som du anger för `admin-key` . Postman gör ersättningen i URL: er, sidhuvuden, begär ande texten och så vidare. 

Hämta värdet för genom att `admin-key` använda Azure kognitiv sökning Admin API-nyckeln som du noterade tidigare. Ange `search-service-name` till namnet på den Azure kognitiv sökning-tjänst som du använder. Ange `storage-connection-string` med hjälp av värdet på fliken **åtkomst nycklar** för lagrings kontot och ange `storage-container-name` namnet på BLOB-behållaren på det lagrings konto där de krypterade filerna lagras. Ange `function-uri` den URL för Azure-funktionen som du noterade tidigare, och Ställ in `function-code` på den nyckel kod för Azure-funktionen värd som du noterade tidigare. Du kan lämna standardvärdena för de andra värdena.

![Fliken Postman-app-variabler](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Fönstret för Postman-variabler")

| Variabel    | Hämta det på |
|-------------|-----------------|
| `admin-key` | På sidan **nycklar** i Azure kognitiv sökning-tjänsten.  |
| `search-service-name` | Namnet på Azure Kognitiv sökning-tjänsten. URL: en är `https://{{search-service-name}}.search.windows.net` . |
| `storage-connection-string` | I lagrings kontot går du till fliken **åtkomst nycklar** och väljer **KEY1**-  >  **anslutningssträng**. |
| `storage-container-name` | Namnet på BLOB-behållaren som innehåller de krypterade filerna som ska indexeras. |
| `function-uri` |  I Azure-funktionen under **Essentials** på huvud sidan. |
| `function-code` | I Azure-funktionen, genom att navigera till **app-nycklar**, klicka för att visa **standard** nyckeln och kopiera värdet. |
| `api-version` | Lämna som **2020-06-30**. |
| `datasource-name` | Lämna som **krypterade-blobbar – DS**. |
| `index-name` | Lämna som **Encrypted-blobbar – IDX**. |
| `skillset-name` | Lämna as **Encrypted-blobs-SS**. |
| `indexer-name` | Lämna som **Encrypted-blobs-IXR**. |

### <a name="review-the-request-collection-in-postman"></a>Granska Request-samlingen i Postman

När du kör den här guiden måste du utfärda fyra HTTP-förfrågningar:

- **Skicka begäran om att skapa indexet**: det här indexet innehåller de data som Azure kognitiv sökning använder och returnerar.
- **Post-begäran för att skapa data källan**: den här data källan ansluter din Azure kognitiv sökning-tjänst till ditt lagrings konto och därmed krypterade BLOB-filer. 
- **Skicka begäran om att skapa färdigheter**: färdigheter anger den anpassade kunskaps definitionen för Azure-funktionen som dekrypterar BLOB-fildata och en [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) för att extrahera texten från varje dokument efter att det har dekrypterats.
- **Skicka begäran om att skapa indexeraren**: att köra indexeraren läser data, tillämpar färdigheter och lagrar resultaten. Du måste köra den här begäran senast.

[Käll koden](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) innehåller en Postman-samling som har fyra förfrågningar, samt några användbara uppföljnings begär Anden. Om du vill utfärda förfrågningarna väljer du fliken för begär anden i Postman och väljer **Skicka** för var och en av dem.

## <a name="3---monitor-indexing"></a>3 – övervaka indexering

Indexering och anrikning börjar så snart du skickar en begäran om att skapa indexerare. Beroende på hur många dokument som finns i ditt lagrings konto kan indexeringen ta en stund. Om du vill ta reda på om indexeraren fortfarande körs använder du den **Hämta status för indexeraren** som ingår i Postman-samlingen och granskar svaret för att lära dig om indexeraren körs eller om du vill visa information om fel och varningar.  

Om du använder den kostnads fria nivån förväntas följande meddelande: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Det här meddelandet visas eftersom BLOB-indexering på den kostnads fria nivån har en [32k-gräns för tecken extrahering](search-limits-quotas-capacity.md#indexer-limits). Det här meddelandet visas inte för den här data uppsättningen på högre nivåer. 

## <a name="4---search"></a>4 – Sök

När indexeraren har körts klart kan du köra vissa frågor för att verifiera att data har dekrypterats och indexerats. Gå till Azure Kognitiv sökning-tjänsten i portalen och Använd [Sök Utforskaren](search-explorer.md) för att köra frågor över indexerade data.

## <a name="next-steps"></a>Nästa steg

Nu när du har indexerat krypterade filer kan du [iterera över den här pipelinen genom att lägga till fler kognitiva kunskaper](cognitive-search-defining-skillset.md). Detta gör att du kan utöka och få ytterligare insikter om dina data.

Om du arbetar med dubbla krypterade data kanske du vill undersöka vilka index krypterings funktioner som finns tillgängliga i Azure Kognitiv sökning. Även om indexeraren behöver dekrypterade data för indexerings ändamål, kan den vara krypterad med en kundhanterad nyckel när indexet finns. På så vis kan du se till att dina data alltid krypteras när de är i vilo läge. Mer information finns i [Konfigurera Kundhanterade nycklar för data kryptering i Azure kognitiv sökning](search-security-manage-encryption-keys.md).