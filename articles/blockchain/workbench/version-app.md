---
title: Blockchain app-versioner – Azure blockchain Workbench
description: Så här använder du program versioner i Azure blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 2a70112fd0ab6e2f664ca48265c121936b01e58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85209886"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Versions hantering av Azure blockchain Workbench Preview-program

Du kan skapa och använda flera versioner av en Azure blockchain Workbench Preview-app. Om flera versioner av samma program laddas upp är en versions historik tillgänglig och användarna kan välja vilken version de vill använda.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain Workbench-distribution. Mer information finns i [distribution av Azure blockchain Workbench](deploy.md) för information om distribution
* Ett distribuerat blockkedjeprogram i Blockchain Workbench. Mer information finns i avsnittet om hur du [skapar ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Lägg till en app-version

Om du vill lägga till en ny version laddar du upp den nya konfigurationen och smarta kontrakts filer till blockchain Workbench.

1. I en webbläsare går du till webbadressen för Blockchain Workbench. Till exempel `https://{workbench URL}.azurewebsites.net/` information om hur du hittar din blockchain Workbench-webbadress, se [blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Logga in som [Blockchain Workbench-administratör](manage-users.md#manage-blockchain-workbench-administrators).
3. Välj det blockchain-program som du vill uppdatera med en annan version.
4. Välj **Lägg till version**. Fönstret **Lägg till version** visas.
5. Välj den nya version kontrakts konfigurationen och de kontrakt kod filer som ska laddas upp. Konfigurationsfilen verifieras automatiskt. Åtgärda eventuella verifierings fel innan du distribuerar programmet.
6. Välj **Lägg till version** för att lägga till den nya versionen av blockchain-programmet.

    ![Lägga till en ny version](media/version-app/add-version.png)

Det kan ta några minuter att distribuera blockchain-programmet. Uppdatera program sidan när distributionen är färdig. Om du väljer programmet och väljer **versions historik** visas programmets versions historik.

> [!IMPORTANT]
> Tidigare versioner av programmet är inaktiverade. Du kan individuellt återaktivera tidigare versioner.
>
> Du kan behöva lägga till medlemmar i program roller igen om ändringar har gjorts i program rollerna i den nya versionen.

## <a name="using-app-versions"></a>Använda app-versioner

Som standard används den senaste aktiverade versionen av programmet i blockchain Workbench. Om du vill använda en tidigare version av ett program måste du först välja version på program sidan.

1. I avsnittet blockchain Workbench-program markerar du kryss rutan program som innehåller det kontrakt som du vill använda. Om tidigare versioner är aktiverade är knappen versions historik tillgänglig.
2. Välj knappen **versions historik** .
3. I fönstret versions Historik väljer du versionen av programmet genom att välja länken i kolumnen *ändra datum* .

    ![Välj en tidigare version](media/version-app/use-version.png)

    Du kan skapa nya kontrakt eller vidta åtgärder för tidigare versions avtal. Versionen av programmet visas efter programmets namn och en varning visas om den äldre versionen.

## <a name="next-steps"></a>Nästa steg

* [Fel sökning av Azure blockchain Workbench](troubleshooting.md)
