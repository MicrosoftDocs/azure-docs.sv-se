---
title: Återställa distributions-token i Azure static Web Apps (för hands version)
description: Återställa tokens på en statisk Azure-Web Apps webbplats
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746522"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Återställa distributions-token i Azure static Web Apps (för hands version)

När du skapar en ny Azure-statisk Web Apps webbplats genererar Azure en token som används för att identifiera programmet under distributionen. Under etableringen lagras denna token som en hemlighet i GitHub-lagringsplatsen. Den här artikeln förklarar hur du använder och hanterar denna token.

Normalt behöver du inte bekymra dig om en distributions-token, men följande är några orsaker till att du kan behöva hämta eller återställa token.

* **Komprometterande token**: Återställ din token om den exponeras för en extern part.
* **Distribuera från en separat GitHub-lagringsplats**: om du distribuerar manuellt från en separat GitHub-lagringsplats måste du ange en distributions-token i den nya lagrings platsen.

## <a name="prerequisites"></a>Förutsättningar

- En befintlig GitHub-lagringsplats som kon figurer ATS med Azure static Web Apps.
- Se [skapa din första statiska app](getting-started.md) om du inte har någon.

## <a name="reset-a-deployment-token"></a>Återställa en distributions-token

1. Klicka på länken **Hantera distributions-token** på sidan _Översikt_ på Azures statiska Web Apps webbplats.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Hantera distributions-token":::

1. Klicka på knappen **Återställ token** .

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Återställer distributions-token":::

1. När du har visat en ny token i fältet för _distributions-token_ kopierar du token genom att klicka på **Kopiera till Urklipp** .


## <a name="update-a-secret-in-the-github-repository"></a>Uppdatera en hemlighet i GitHub-lagringsplatsen

Om du vill fortsätta med automatisk distribution när du har återställt en token måste du ange det nya värdet i motsvarande GitHub-lagringsplats.

1. Navigera till projektets lagrings plats på GitHub och klicka på fliken **Inställningar** .
1. Klicka på meny alternativet **hemligheter** . Du hittar en hemlighet som genererats under den statiska webbappens etablering med namnet _AZURE_STATIC_WEB_APPS_API_TOKEN_... i avsnittet _databas hemligheter_ .

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Visar lagrings hemligheter för databasen":::

    > [!NOTE]
    > Om du har skapat den statiska Azure-Web Apps-platsen mot flera grenar av den här lagrings platsen visas flera _AZURE_STATIC_WEB_APPS_API_TOKEN_... hemligheter i den här listan. Välj rätt en genom att matcha fil namnet som anges i fältet _Redigera arbets flöde_ på fliken _Översikt_ på den statiska Web Appss platsen.

1. Klicka på knappen **Uppdatera** för att öppna redigeraren.
1. **Klistra in värdet** för Deployment token i fältet _värde_ .
1. Klicka på **Uppdatera hemlighet**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Uppdaterar lagrings platsens hemlighet":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera från en generator av statiska webbplatser](publish-gatsby.md)
