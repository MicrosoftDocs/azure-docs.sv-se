---
title: Granska pull-begäranden i för produktions miljöer i Azure static Web Apps
description: Lär dig hur du använder för produktions miljöer för att granska ändringar av pull-begäranden i Azures statiska Web Apps.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 78d2f3b399e26ca0c6036fd74fa1fba49d1f21f4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042784"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Granska pull-begäranden i förproduktionsmiljöer i förhandsversionen av Azures Static Web Apps

Den här artikeln visar hur du använder för produktions miljöer för att granska ändringar i program som distribueras med [Azures statiska Web Apps](overview.md).

En för produktions miljö (mellanlagring) är en fullständigt fungerande, mellanlagrad version av ditt program som innehåller ändringar som inte är tillgängliga i produktionen.

Azure static Web Apps skapar ett arbets flöde för GitHub-åtgärder i lagrings platsen. När en pull-begäran skapas mot en gren som arbets flödet bevakar, skapas för produktions miljön. I för produktions miljön går appen, som du kan använda för att utföra granskningar innan du skickar dem till produktion.

Det kan finnas flera för produktions miljöer samtidigt när du använder Azures statiska Web Apps. Varje gång du skapar en pull-begäran mot den bevakade grenen distribueras en stegvis version med dina ändringar till en tydlig för produktions miljö.

Det finns många fördelar med att använda för produktions miljöer. Du kan till exempel:

- Granska visuella ändringar mellan produktion och mellanlagring. Du kan till exempel Visa uppdateringar av innehåll och layout.
- Visa ändringarna i ditt team.
- Jämför olika versioner av ditt program.
- Verifiera ändringar med hjälp av acceptans test.
- Utför Sanity kontroller innan du distribuerar till produktion.

> [!NOTE]
> Under för hands versionen tillåts [högst tre mellanlagrings miljöer](quotas.md) i taget.

## <a name="prerequisites"></a>Förutsättningar

- En befintlig GitHub-lagringsplats som kon figurer ATS med Azure static Web Apps. Se [skapa din första statiska app](getting-started.md) om du inte har någon.

## <a name="make-a-change"></a>Gör en ändring

Börja med att göra en ändring i din lagrings plats. Du kan göra det direkt på GitHub som du ser i följande steg.

1. Navigera till projektets lagrings plats på GitHub och klicka sedan på knappen **gren** för att skapa en ny gren.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Skapa ny gren med GitHub-gränssnittet":::]

    Ange ett namn på grenen och klicka på **skapa gren**.

1. Gå till mappen _app_ och ändra lite text innehåll. Du kan till exempel ändra rubrik eller stycke. När du har hittat filen som du vill redigera klickar du på **Redigera** för att göra ändringen.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Knappen Redigera fil i GitHub-gränssnittet":::

1. När du har gjort ändringarna klickar du på **genomför ändringar** för att genomföra ändringarna i grenen.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Knappen genomför ändringar i GitHub-gränssnittet":::

## <a name="create-a-pull-request"></a>Skapa en pull-begäran

Skapa sedan en pull-begäran från den här ändringen.

1. Öppna fliken **pull-begäran** för projektet på GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Fliken pull-begäran i en GitHub-lagringsplats":::

1. Klicka på knappen **jämför & pull-begäran** för din gren.

1. Du kan också fylla i viss information om dina ändringar och sedan klicka på **skapa pull-begäran**.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Skapa pull-begäran i GitHub":::

Du kan tilldela granskare och lägga till kommentarer för att diskutera dina ändringar om det behövs.

> [!NOTE]
> Du kan göra flera ändringar genom att skicka nya incheckningar till din gren. Pull-begäran uppdateras sedan automatiskt för att återspegla alla ändringar.

## <a name="review-changes"></a>Granska ändringar

När pull-begäran har skapats kör arbets flödet [GitHub åtgärder](https://github.com/features/actions) distribution och distribuerar ändringarna till en för produktions miljö.

När arbets flödet har slutfört skapandet och distributionen av appen lägger GitHub-roboten till en kommentar till pull-begäran som innehåller URL: en för för produktions miljön. Du kan klicka på länken för att visa de mellanlagrade ändringarna.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Pull-begäran-kommentar med för produktions-URL":::

Klicka på den genererade URL: en för att se ändringarna.

Om du tar en närmare titt på webb adressen kan du se att den består av följande: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

För en viss pull-begäran förblir URL: en samma även om du push-överför nya uppdateringar. Förutom den URL som är konstant, används samma för produktions miljö för pull-begäran.

## <a name="publish-changes"></a>Publicera ändringar

När ändringarna har godkänts kan du publicera dina ändringar i produktionen genom att sammanfoga pull-begäran.

Klicka på **merge pull-begäran**:

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Knappen sammanfoga pull-begäran i GitHub-gränssnittet":::

När du slår samman kopieras dina ändringar till den spårade grenen ("produktion"-grenen). Distributions arbets flödet startar sedan på den spårade grenen och ändringarna är aktiva när ditt program har återskapats.

Om du vill kontrol lera ändringarna i produktionen öppnar du din produktions-URL för att läsa in den Live-versionen av webbplatsen.

## <a name="limitations"></a>Begränsningar

Stegvisa versioner av programmet är för närvarande allmänt tillgängliga via URL: en, även om din GitHub-lagringsplats är privat.

> [!WARNING]
> Var försiktig när du publicerar känsligt innehåll till stegvisa versioner eftersom åtkomsten till för produktions miljöer inte är begränsad.

Antalet för produktions miljöer som är tillgängliga för varje app som distribueras med statisk Web Apps beror på SKU-nivån som du använder. Med den kostnads fria nivån kan du till exempel ha 1 för produktions miljö förutom produktions miljön.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera en anpassad domän](custom-domain.md)
