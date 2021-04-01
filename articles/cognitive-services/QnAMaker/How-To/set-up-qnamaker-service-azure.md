---
title: Konfigurera en QnA Maker tjänst – QnA Maker
description: Innan du kan skapa en QnA Maker kunskaps banker måste du först konfigurera en QnA Maker tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan konfigurera en QnA Maker-tjänst.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102219275"
---
# <a name="manage-qna-maker-resources"></a>Hantera QnA Maker resurser

Innan du kan skapa en QnA Maker kunskaps banker måste du först konfigurera en QnA Maker tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan konfigurera en QnA Maker-tjänst.

En heltäckande förståelse av följande koncept är användbart innan du skapar din resurs:

* [QnA Maker resurser](../Concepts/azure-resources.md)
* [Redigerings-och publicerings nycklar](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Skapa en ny QnA Maker-tjänst

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Den här proceduren skapar de Azure-resurser som krävs för att hantera innehållet i kunskaps basen. När du har slutfört de här stegen hittar du _prenumerations_ nycklarna på sidan **nycklar** för resursen i Azure Portal.

1. Logga in på Azure Portal och [skapa en QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resurs.

1. Välj **skapa** efter att du har läst de allmänna villkoren:

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QNA Maker** väljer du lämpliga nivåer och regioner:

    ![Skapa en ny QnA Maker tjänst – pris nivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * I fältet **namn** anger du ett unikt namn som identifierar den här QNA Makers tjänsten. Namnet identifierar också QnA Maker slut punkten som dina kunskaps baser kommer att associeras med.
    * Välj den **prenumeration** som QNA Maker resursen ska distribueras under.
    * Välj **pris nivå** för QNA Maker hanterings tjänster (portal-och hanterings-API: er). Se [Mer information om SKU-prissättning](https://aka.ms/qnamaker-pricing).
    * Skapa en ny **resurs grupp** (rekommenderas) eller Använd en befintlig som distribuerar den här QNA Maker resursen. QnA Maker skapar flera Azure-resurser. När du skapar en resurs grupp som innehåller dessa resurser kan du enkelt hitta, hantera och ta bort dessa resurser med resurs gruppens namn.
    * Välj en **resurs grupps plats**.
    * Välj **pris nivå för sökning** i Azure kognitiv sökning-tjänsten. Om alternativet för den kostnads fria nivån inte är tillgängligt (visas nedtonat) innebär det att du redan har en kostnads fri tjänst som distribuerats via din prenumeration. I så fall måste du börja med Basic-nivån. Se [pris information för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **Sök plats** där du vill att Azure kognitiv sökning-index ska distribueras. Begränsningar för var kund information måste lagras hjälper dig att avgöra vilken plats du väljer för Azure Kognitiv sökning.
    * Ange ett namn på Azure App Service-instansen i fältet **namn på App** .
    * Standardvärdet är App Service standard nivån (S1). Du kan ändra planen när du har skapat den. Läs mer om [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).
    * Välj **plats för webbplatsen** där App Service ska distribueras.

        > [!NOTE]
        > **Sök platsen** kan skilja sig från **webbplatsens plats**.

    * Välj om du vill aktivera **Application Insights**. Om **Application Insights** är aktive rad samlar QNA Maker in telemetri om trafik, chat-loggar och fel.
    * Välj **platsen för App Insights** där Application Insightss resursen ska distribueras.
    * För kostnads besparingar kan du [dela](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) några men inte alla Azure-resurser som skapats för QNA Maker.

1. När alla fält har verifierats väljer du **skapa**. Processen kan ta några minuter att slutföra.

1. När distributionen har slutförts visas följande resurser som skapats i din prenumeration:

   ![Resurs skapade en ny QnA Maker tjänst](../media/qnamaker-how-to-setup-service/resources-created.png)

    Resursen med _Cognitive Services_ typen har dina _prenumerations_ nycklar.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Den här proceduren skapar de Azure-resurser som krävs för att hantera innehållet i kunskaps basen. När du har slutfört de här stegen hittar du *prenumerations* nycklarna på sidan **nycklar** för resursen i Azure Portal.

1. Logga in på Azure Portal och [skapa en QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resurs.

1. Välj **skapa** efter att du har läst de allmänna villkoren:

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QNA Maker** markerar du kryss rutan hanterad (för hands version) och väljer lämpliga nivåer och regioner:

    ![Skapa en ny QnA Maker hanterad tjänst – pris nivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Välj den **prenumeration** som QNA Maker resursen ska distribueras under.
    * Skapa en ny **resurs grupp** (rekommenderas) eller Använd en befintlig som distribuerar den här QNA Maker hanterade resursen (för hands version). QnA Maker Managed (för hands version) skapar några Azure-resurser. När du skapar en resurs grupp som innehåller dessa resurser kan du enkelt hitta, hantera och ta bort dessa resurser med resurs gruppens namn.
    * I fältet **namn** anger du ett unikt namn som identifierar den här QNA Maker hanterade tjänsten (för hands version). 
    * Välj den **plats** där du vill att tjänsten QNA Maker Managed (för hands version) ska distribueras. Hanterings-API: er och tjänst slut punkten kommer att finnas på den här platsen. 
    * Välj **pris nivå** för tjänsten QNA Maker Managed (för hands version) (kostnads fri för hands version). Se [Mer information om SKU-prissättning](https://aka.ms/qnamaker-pricing).
    * Välj den **Sök plats** där du vill att Azure kognitiv sökning-index ska distribueras. Begränsningar för var kund information måste lagras hjälper dig att avgöra vilken plats du väljer för Azure Kognitiv sökning.
    * Välj **pris nivå för sökning** i Azure kognitiv sökning-tjänsten. Om alternativet för den kostnads fria nivån inte är tillgängligt (visas nedtonat) innebär det att du redan har en kostnads fri tjänst som distribuerats via din prenumeration. I så fall måste du börja med Basic-nivån. Se [pris information för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).

1. När alla fält har verifierats väljer du **Granska + skapa**. Processen kan ta några minuter att slutföra.

1. När distributionen har slutförts visas följande resurser som skapats i din prenumeration:

    ![Resursen skapade en ny QnA Maker hanterad tjänst (för hands version)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    Resursen med _Cognitive Services_ typen har dina _prenumerations_ nycklar.

---

## <a name="upgrade-azure-resources"></a>Uppgradera Azure-resurser

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>Uppgradera QnA Maker SKU

Om du vill ha fler frågor och svar i din kunskaps bas, utöver din nuvarande nivå, uppgraderar du QnA Maker-tjänstens pris nivå.

Så här uppgraderar du SKU: n för QnA Maker hantering:

1. Gå till din QnA Maker-resurs i Azure Portal och välj **pris nivå**.

    ![QnA Maker resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Välj lämplig SKU och tryck på **Välj**.

    ![QnA Maker priser](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>Uppgradera App Service

När din kunskaps bas behöver hantera fler förfrågningar från din klient program, uppgraderar du App Service pris nivå.

Du kan [skala upp](../../../app-service/manage-scale-up.md) eller skala ut app service.

Gå till App Service resursen i Azure Portal och välj alternativet **skala upp** eller **skala ut** efter behov.

![QnA Maker App Service skala](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Uppgradera Azure Kognitiv sökning-tjänsten

Om du planerar att ha många kunskapsbaser uppgraderar du prisnivån för Azure Cognitive Search-tjänsten.

För närvarande kan du inte utföra en uppgradering på plats av Azure Search-SKU: n. Du kan dock skapa en ny Azure Search-resurs med önskad SKU, återställa data till den nya resursen och sedan länka den till QnA Maker stacken. Det gör du genom att följa dessa steg:

1. Skapa en ny Azure Search-resurs i Azure Portal och välj önskad SKU.

    ![QnA Maker Azure Search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Återställ indexen från din ursprungliga Azure Search-resurs till den nya. Se [exempelkoden för återställning av säkerhetskopior](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. När data har återställts går du till din nya Azure Search-resurs, väljer **nycklar** och skriver ned **namnet** och **Administratörs nyckeln**:

    ![QnA Maker Azure Search-nycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Om du vill länka den nya Azure Search-resursen till QnA Maker stack går du till QnA Maker App Service-instansen.

    ![QnA Maker App Service instans](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Välj **Programinställningar** och ändra inställningarna i fälten **AzureSearchName** och **AzureSearchAdminKey** från steg 3.

    ![QnA Maker App Service inställning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Starta om App Service-instansen.

    ![Starta om QnA Maker App Service-instansen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Princip för inaktivitet för kostnads fria Sök resurser

Om du inte använder en QnA Maker-resurs bör du ta bort alla resurser. Om du inte tar bort oanvända resurser slutar din kunskaps bas att fungera om du har skapat en kostnads fri Sök resurs.

Kostnads fria Sök resurser tas bort efter 90 dagar utan att ha fått ett API-anrop.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Uppgradera Azure Kognitiv sökning-tjänsten

Om du planerar att ha många kunskapsbaser uppgraderar du prisnivån för Azure Cognitive Search-tjänsten.

För närvarande kan du inte utföra en uppgradering på plats av Azure Search-SKU: n. Du kan dock skapa en ny Azure Search-resurs med önskad SKU, återställa data till den nya resursen och sedan länka den till QnA Maker stacken. Det gör du genom att följa dessa steg:

1. Skapa en ny Azure Search-resurs i Azure Portal och välj önskad SKU.

    ![QnA Maker Azure Search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Återställ indexen från din ursprungliga Azure Search-resurs till den nya. Se [exempelkoden för återställning av säkerhetskopior](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Information om hur du länkar den nya Azure Search-resursen till tjänsten QnA Maker Managed (för hands version) finns i avsnittet nedan.

### <a name="inactivity-policy-for-free-search-resources"></a>Princip för inaktivitet för kostnads fria Sök resurser

Om du inte använder en QnA Maker-resurs bör du ta bort alla resurser. Om du inte tar bort oanvända resurser slutar din kunskaps bas att fungera om du har skapat en kostnads fri Sök resurs.

Kostnads fria Sök resurser tas bort efter 90 dagar utan att ha fått ett API-anrop.

---

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser

Om du tar bort någon av de Azure-resurser som används för QnA Maker kunskaps baser fungerar inte längre kunskaps basen. Innan du tar bort några resurser bör du kontrol lera att du exporterar dina kunskaps baser från sidan **Inställningar** .

## <a name="next-steps"></a>Nästa steg

Läs mer om [App Service](../../../app-service/index.yml) och [search service](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Lär dig hur du skapar med andra](../index.yml)
