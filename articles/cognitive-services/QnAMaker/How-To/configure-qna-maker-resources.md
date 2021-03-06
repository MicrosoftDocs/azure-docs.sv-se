---
title: Konfigurera QnA Maker tjänst – QnA Maker
description: Det här dokumentet beskriver avancerade konfigurationer för dina QnA Maker-resurser.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220154"
---
# <a name="configure-qna-maker-resources"></a>Konfigurera QnA Maker resurser

Användaren kan konfigurera QnA Maker att använda en annan kognitiv Sök resurs. De kan också konfigurera App Service-inställningar om de använder QnA Maker GA.

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
