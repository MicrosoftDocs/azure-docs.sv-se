---
title: Konfigurera en QnA Maker tjänst – QnA Maker
description: Innan du kan skapa en QnA Maker kunskaps banker måste du först konfigurera en QnA Maker tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan konfigurera en QnA Maker-tjänst.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e09f9b8564c9319e68984df1c0f8db7a496a6d0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584812"
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
    * För kostnads besparingar kan du [dela](#configure-qna-maker-to-use-different-cognitive-search-resource) några men inte alla Azure-resurser som skapats för QNA Maker.

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

## <a name="find-authoring-keys-in-the-azure-portal"></a>Hitta redigerings nycklar i Azure Portal

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Du kan visa och återställa dina redigerings nycklar från Azure Portal, där du skapade QnA Maker resursen. Dessa nycklar kan kallas för prenumerations nycklar.

1. Gå till QnA Maker resursen i Azure Portal och välj den resurs som har _Cognitive Servicess_ typ:

    ![QnA Maker resurs lista](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar och slut punkt**:

    ![Prenumerations nyckel för QnA Maker Managed (för hands version)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Hitta fråge slut punkts nycklar i QnA Maker Portal

Slut punkten finns i samma region som resursen eftersom slut punkts nycklarna används för att anropa kunskaps basen.

Slut punkts nycklar kan hanteras från [QNA Maker-portalen](https://qnamaker.ai).

1. Logga in på [QNA Maker Portal](https://qnamaker.ai), gå till din profil och välj sedan **tjänst inställningar**:

    ![Slut punkts nyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller Återställ dina nycklar:

    > [!div class="mx-imgBorder"]
    > ![Slut punkts nyckel hanterare](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tror att de har komprometterats. Detta kan kräva motsvarande ändringar i klient programmet eller bot koden.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Du kan visa och återställa redigerings nycklarna från Azure Portal, där du skapade resursen QnA Maker Managed (för hands version). Dessa nycklar kan kallas för prenumerations nycklar.

1. Gå till resursen QnA Maker Managed (för hands version) i Azure Portal och välj den resurs som har *Cognitive Servicess* typ:

    ![Resurs lista för QnA Maker Managed (för hands version)](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Gå till **nycklar och slut punkt**:

    ![Prenumerations nyckel för QnA Maker Managed (för hands version)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>Uppdatera resurserna

Lär dig hur du uppgraderar de resurser som används av din kunskaps bas. QnA Maker Managed (för hands version) är **kostnads fritt** i för hands versionen. 

---

### <a name="recommended-settings-for-network-isolation"></a>Rekommenderade inställningar för nätverks isolering

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

1. Skydda kognitiva tjänst resurser från offentlig åtkomst genom [att konfigurera det virtuella nätverket](../../cognitive-services-virtual-networks.md?tabs=portal).
2. Skydda App Service (QnA Runtime) från offentlig åtkomst.

   ##### <a name="add-ips-to-app-service-allowlist"></a>Lägg till IP-adresser i App Service tillåten

    * Tillåt enbart trafik från kognitiva tjänst-IP: er. Dessa ingår redan i Service Tag-numret `CognitiveServicesManagement` . Detta krävs för att skapa API: er (Skapa/uppdatera KB) för att anropa app service och uppdatera Azure Search-tjänsten enligt detta. Läs [Mer information om service märken.](../../../virtual-network/service-tags-overview.md)
    * Se till att du även tillåter andra start punkter som bot service, QnA Maker portal (kan vara ditt företags nätverket) osv. för förutsägelse "GenerateAnswer" API-åtkomst.
    * Följ dessa steg om du vill lägga till IP-adressintervall till en tillåten:

      * Hämta [IP-intervall för alla tjänst Taggar](https://www.microsoft.com/download/details.aspx?id=56519).
      * Välj IP-adresserna för "CognitiveServicesManagement".
      * Gå till avsnittet nätverk i App Service resursen och klicka på alternativet för att konfigurera åtkomst begränsning för att lägga till IP-adresser i en tillåten.

    Vi har också ett automatiserat skript som gör samma sak för din App Service. Du kan hitta [PowerShell-skriptet för att konfigurera en tillåten](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) på GitHub. Du måste skriva in prenumerations-ID, resurs grupp och faktiskt App Service namn som skript parametrar. Om du kör skriptet läggs IP-adresserna automatiskt till App Service tillåten.

    ##### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Konfigurera App Service-miljön som värd QnA Maker App Service
    App Service-miljön (ASE) kan användas för att vara värd för QnA Maker app service. Följ stegen nedan:

    1. Skapa en App Service-miljön och markera den som "extern". Följ [själv studie kursen](../../../app-service/environment/create-external-ase.md) om du vill ha mer information.
    2.  Skapa en app service i App Service-miljön.
        * Kontrol lera konfigurationen för app service och Lägg till "PrimaryEndpointKey" som en program inställning. Värdet för ' PrimaryEndpointKey ' ska vara inställt på " \<app-name\> -PrimaryEndpointKey". Appens namn definieras i App Service-URL: en. Om App Service-URL: en till exempel är "mywebsite.myase.p.azurewebsite.net" är App-Name "min webbplats". I det här fallet ska värdet för ' PrimaryEndpointKey ' anges till "Website-PrimaryEndpointKey".
        * Skapa en Azure Search-tjänst.
        * Se till att Azure Search-och appinställningar har kon figurer ATS korrekt. 
          Följ den här [självstudien](../reference-app-service.md?tabs=v1#app-service).
    3.  Uppdatera nätverks säkerhets gruppen som är kopplad till App Service-miljön
        * Uppdatera i förväg skapade inkommande säkerhets regler enligt dina krav.
        * Lägg till en ny inkommande säkerhets regel med källan som service tag och käll tjänst tag gen som ' CognitiveServicesManagement '.
    4.  Skapa en QnA Maker kognitiv tjänst instans (Microsoft. CognitiveServices/Accounts) med Azure Resource Manager där QnA Maker-slutpunkten ska ställas in på App Service slut punkten som skapades ovan (https://mywebsite.myase.p.azurewebsite.net).
    
3. Konfigurera Kognitiv sökning som en privat slut punkt i ett VNET

    När en Sök instans skapas när en QnA Maker resurs skapas kan du tvinga Kognitiv sökning till att stödja en privat slut punkts konfiguration som skapats helt i en kunds VNet.

    Alla resurser måste skapas i samma region för att en privat slut punkt ska kunna användas.

    * QnA Maker resurs
    * ny Kognitiv sökning resurs
    * ny Virtual Network resurs

    Utför följande steg i [Azure Portal](https://portal.azure.com):

    1. Skapa en [QNA Maker-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
    1. Skapa en ny Kognitiv sökning resurs med slut punkts anslutning (data) inställt på _privat_. Skapa resursen i samma region som QnA Maker resursen som skapades i steg 1. Lär dig mer om att [skapa en kognitiv sökning resurs](../../../search/search-create-service-portal.md)och Använd sedan den här länken för att gå direkt till [sidan Skapa i resursen](https://ms.portal.azure.com/#create/Microsoft.Search).
    1. Skapa en ny [Virtual Network-resurs](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
    1. Konfigurera VNET på App Service-resursen som skapades i steg 1 i den här proceduren.
        1. Skapa en ny DNS-post i VNET för ny Kognitiv sökning-resurs som skapades i steg 2. till Kognitiv sökning IP-adress.
    1. [Koppla app service till den nya kognitiv sökning-resurs som](#configure-qna-maker-to-use-different-cognitive-search-resource) skapades i steg 2. Sedan kan du ta bort den ursprungliga Kognitiv sökning-resursen som skapades i steg 1.

    Skapa din första kunskaps bas i [QNA Maker-portalen](https://www.qnamaker.ai/).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

1. Skydda kognitiva tjänst resurser från offentlig åtkomst genom [att konfigurera det virtuella nätverket](../../cognitive-services-virtual-networks.md?tabs=portal).
2. [Skapa privata slut punkter](../reference-private-endpoint.md) till Azure Search resursen.

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

## <a name="configure-azure-resources"></a>Konfigurera Azure-resurser

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurera QnA Maker att använda olika Kognitiv sökning resurser

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en Sök tjänst för dig och länkas till QnA Maker tjänsten. När resurserna har skapats kan du uppdatera App Service-inställningen för att använda en tidigare befintlig Sök tjänst och ta bort den som du nyss skapade.

QnA Maker **App Service** resurs använder kognitiv sökning resursen. Du måste ändra inställningen i Azure Portal för att kunna ändra Kognitiv sökning resursen som används av QnA Maker.

1. Hämta **Administratörs nyckeln** och **namnet** på den kognitiv sökning resurs som du vill QNA Maker använda.

1. Logga in på [Azure Portal](https://portal.azure.com) och hitta **App Service** som är kopplade till din QNA Maker resurs. Båda har samma namn.

1. Välj **Inställningar** och sedan **konfiguration**. Då visas alla befintliga inställningar för QnA Maker App Service.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av Azure Portal som visar App Service konfigurations inställningar](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Ändra värdena för följande nycklar:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Om du vill använda de nya inställningarna måste du starta om App Service. Välj **Översikt** och välj sedan **starta om**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av Azure Portal starta om App Service när konfigurations inställningarna ändras](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Om du skapar en QnA-tjänst via Azure Resource Manager mallar kan du skapa alla resurser och kontrol lera App Service skapandet för att använda en befintlig Sök tjänst.

Läs mer om hur du konfigurerar [program inställningarna](../../../app-service/configure-common.md#configure-app-settings)för App Service.

### <a name="get-the-latest-runtime-updates"></a>Hämta de senaste körnings uppdateringarna

QnAMaker runtime är en del av Azure App Service-instansen som distribueras när du [skapar en QnAMaker-tjänst](./set-up-qnamaker-service-azure.md) i Azure Portal. Uppdateringar görs regelbundet till körnings miljön. QnA Maker App Service-instansen är i läget för automatisk uppdatering efter 2019-versionen av webbplats tillägget (version 5 +). Den här uppdateringen är utformad för att ta hand om noll stillestånds tid under uppgraderingar.

Du kan kontrol lera din aktuella version på https://www.qnamaker.ai/UserSettings . Om din version är äldre än version 5. x måste du starta om App Service för att tillämpa de senaste uppdateringarna:

1. Gå till din QnAMaker-tjänst (resurs grupp) i [Azure Portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure-resurs grupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj App Service instansen och öppna avsnittet **Översikt** .

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service instans](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Starta om App Service. Uppdaterings processen bör avslutas på några sekunder. Alla beroende program eller robotar som använder den här QnAMaker-tjänsten kommer inte att vara tillgängliga för slutanvändarna under den här omstarts perioden.

    ![Omstart av QnAMaker App Service-instansen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Konfigurera inställningen för inaktiv App Service för att undvika tids gräns

App Service, som hanterar QnA Maker förutsägelse körning för en publicerad kunskaps bas, har en konfiguration för tids gräns för inaktivitet, som automatiskt tar slut om tjänsten är inaktiv. För QnA Maker innebär det att ditt förutsägelse körnings generateAnswer-API ibland går ut efter perioder utan trafik.

För att hålla appen för förutsägelse slut punkt inläst även när det inte finns någon trafik, ställer du in inaktivitet till Always On.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj din QnA Maker resurss app service. Den har samma namn som den QnA Maker resursen, men har en annan **typ** av App Service.
1. Sök efter **Inställningar** och välj sedan **konfiguration**.
1. I konfigurations fönstret väljer du **allmänna inställningar** och sedan hitta **Always on** och väljer **på** som värde.

    > [!div class="mx-imgBorder"]
    > ![I konfigurations fönstret väljer du * * allmänna inställningar * * och letar sedan * * Always on * * och väljer * * på * * som värde.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Välj **Spara** för att spara konfigurationen.
1. Du tillfrågas om du vill starta om appen för att använda den nya inställningen. Välj **Fortsätt**.

Läs mer om hur du konfigurerar App Service [allmänna inställningar](../../../app-service/configure-common.md#configure-general-settings).

### <a name="business-continuity-with-traffic-manager"></a>Affärs kontinuitet med Traffic Manager

Det främsta målet med affärs kontinuitets planen är att skapa en elastisk kunskaps bas slut punkt, vilket skulle göra att det inte går att stänga av roboten eller programmet.

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-plan](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Den övergripande idén som visas ovan är följande:

1. Konfigurera två parallella [QNA Maker tjänster](set-up-qnamaker-service-azure.md) i [Azure-kopplade regioner](../../../best-practices-availability-paired-regions.md).

1. [Säkerhetskopiera](../../../app-service/manage-backup.md) den primära QNA Maker app-tjänsten och [Återställ](../../../app-service/web-sites-restore.md) den i den sekundära installationen. Detta säkerställer att båda konfigurationerna fungerar med samma värdnamn och nycklar.

1. Behåll de primära och sekundära Azure Search-indexen synkroniserade. Använd exemplet GitHub [här](https://github.com/pchoudhari/QnAMakerBackupRestore) för att se hur du säkerhetskopierar Azure index.

1. Säkerhetskopiera Application Insights med [kontinuerlig export](../../../azure-monitor/app/export-telemetry.md).

1. När de primära och sekundära stackarna har kon figurer ATS använder du [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) för att konfigurera de två slut punkterna och konfigurera en routningsmetod.

1. Du måste skapa en Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL), certifikat för din Traffic Manager-slutpunkt. [BIND TLS/SSL-certifikatet](../../../app-service/configure-ssl-bindings.md) i dina app Services.

1. Använd slutligen Traffic Manager-slutpunkten i din robot eller app.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Konfigurera QnA Maker hanterad (för hands version)-tjänsten att använda en annan Kognitiv sökning resurs

Om du skapar en QnA tjänst som hanteras (för hands version) och dess beroenden (till exempel Sök) via portalen skapas en Sök tjänst för dig och länkas till tjänsten QnA Maker Managed (för hands version). När resurserna har skapats kan du uppdatera Sök tjänsten på fliken **konfiguration** .

1. Gå till din QnA Maker hanterade tjänst (för hands version) i Azure Portal.

1. Välj **konfiguration** och välj den Azure kognitiv sökning-tjänst som du vill länka med QNA Maker hanterad (för hands version)-tjänsten.

    ![Skärm bild av konfigurations sidan för QnA Maker hanterad (för hands version)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Klicka på **Spara**.

> [!NOTE]
> Om du ändrar Azure Search tjänst som är kopplad till QnA Maker kommer du att förlora åtkomsten till alla kunskaps banker som redan finns i det. Se till att exportera befintliga kunskaps baser innan du ändrar Azure Search tjänsten.

---

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser

Om du tar bort någon av de Azure-resurser som används för QnA Maker kunskaps baser fungerar inte längre kunskaps basen. Innan du tar bort några resurser bör du kontrol lera att du exporterar dina kunskaps baser från sidan **Inställningar** .

## <a name="next-steps"></a>Nästa steg

Läs mer om [App Service](../../../app-service/index.yml) och [search service](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Lär dig hur du skapar med andra](../index.yml)
