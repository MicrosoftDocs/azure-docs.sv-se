---
title: 'Snabb start: skapa din första statiska webbapp med Azures statiska Web Apps'
description: Lär dig att bygga en Azure static Web Apps-instans med ditt prioriterade klient dels ramverk.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: bbc06b657525880f22bd5fb38e902f906d438c9c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565918"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Snabb start: skapa din första statiska webbapp

Azure Static Web Apps publicerar webbplatser i en produktionsmiljö genom att bygga appar från en GitHub-lagringsplats. I den här snabb starten skapar du ett webb program med hjälp av det önskade front-end-ramverket från en GitHub-lagringsplats.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com) -konto
- [Azure](https://portal.azure.com) -konto

## <a name="create-a-repository"></a>Klona en lagringsplats

Den här artikeln använder GitHub för att göra det enkelt för dig att skapa en ny lagrings plats. Funktionerna för mallar startappar har skapats med olika klient dels ramverk.

# <a name="angular"></a>[Angular](#tab/angular)

- Kontrol lera att du är inloggad på GitHub och navigera till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/angular-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

# <a name="react"></a>[React](#tab/react)

- Kontrol lera att du är inloggad på GitHub och navigera till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/react-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Kontrol lera att du är inloggad på GitHub och navigera till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/vue-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

# <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

- Kontrol lera att du är inloggad på GitHub och navigera till följande plats för att skapa en ny lagrings plats
  - https://github.com/staticwebdev/vanilla-basic/generate
- Namnge din lagrings plats **först statisk-webb-app**

> [!NOTE]
> Azures statiska Web Apps kräver minst en HTML-fil för att skapa en webbapp. Lagrings platsen som du skapar i det här steget omfattar en enda _index.html_ -fil.

---

Klicka på knappen **skapa databas från mall** .

:::image type="content" source="media/getting-started/create-template.png" alt-text="Skapa lagrings plats från mall":::

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

Nu när du har skapat databasen kan du skapa en statisk webbapp från Azure Portal.

- Navigera till [Azure Portal](https://portal.azure.com)
- Klicka på **Skapa en resurs**
- Sök efter **Statiska webbappar**
- Klicka på **Statiska webbappar (förhandsversion)**
- Klicka på **Skapa**

### <a name="basics"></a>Grunder

Börja med att konfigurera din nya app och länka den till en GitHub-lagringsplats.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Fliken Grundläggande":::

- Välj din _Azure-prenumeration_
- Välj eller skapa en ny _resurs grupp_
- Namnge appen **mitt-första-statiskt-Web-App**.
  - Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.
- Välj en _region_ som är närmast dig
- Välj den **kostnads fria** _SKU: n_
- Klicka på knappen **Logga in med GitHub** och autentisera med GitHub

När du har loggat in med GitHub anger du lagrings plats informationen.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Lagrings information":::

- Välj önskad _organisation_
- Välj **min-första-webb-statisk-app** från List rutan _databas_
- Välj **original** i list rutan _gren_
- Klicka på **Nästa: Skapa >** för att redigera versionskonfigurationen

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Nästa build-knapp":::

> [!NOTE]
>  Om du inte ser några databaser kan du behöva auktorisera Azures statiska Web Apps i GitHub. Bläddra till [Start sidan för GitHub](https://github.com) och klicka på din konto avbildning för att öppna den nedrullningsbara menyn. Klicka på **Inställningar**och sedan på **program > auktoriserade OAuth-appar > Azure static Web Apps**och slutligen väljer du **bevilja**. Du måste vara ägare till organisationen för att kunna bevilja behörighet för organisations databaser.

### <a name="build"></a>Skapa

Lägg sedan till konfigurationsinformation som är specifik för ditt klientramverk.

# <a name="angular"></a>[Angular](#tab/angular)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Ange **förd/vinkel-Basic** i rutan _app artefakt plats_

# <a name="react"></a>[React](#tab/react)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Ange **build** i rutan _plats för app-artefakt_

# <a name="vue"></a>[Vue](#tab/vue)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Ange **Dist** i rutan _plats för app-artefakt_

# <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

- Ange **/** i rutan _app location_
- Rensa standardvärdet från rutan _API-plats_
- Rensa rutan Standardvärde från _appens artefakt plats_

---

Klicka på knappen **Granska + skapa**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Knappen granska skapande":::

Om du vill ändra dessa värden när du har skapat appen kan du redigera [arbets flödes filen](github-actions-workflow.md).

### <a name="review--create"></a>Granska + skapa

När begäran har validerats kan du fortsätta att skapa programmet.

Klicka på **Skapa**

:::image type="content" source="media/getting-started/create-button.png" alt-text="Knappen Skapa":::

När resursen har skapats klickar du på knappen **gå till resurs**

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Knappen Gå till resurs":::

## <a name="view-the-website"></a>Visa webbplatsen

Det finns två aspekter av att distribuera en statisk app. Den första etablerar de underliggande Azure-resurserna som utgör din app. Den andra är ett GitHub Actions-arbetsflöde som bygger och publicerar din app.

Innan du kan navigera till den nya statiska platsen måste distributions versionen först slutföras.

I fönstret Översikt över statisk Web Apps visas en serie länkar som hjälper dig att interagera med din webbapp.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Översikts fönster":::

1. Klicka på banderollen som säger "Klicka här om du vill kontrol lera status för dina GitHub-åtgärder körs" tar dig till de GitHub åtgärder som körs mot din lagrings plats. När du har kontrollerat att distributions jobbet är klart kan du gå till webbplatsen via den genererade URL: en.

2. När GitHub-åtgärds arbets flödet är klart kan du klicka på _URL_ -länken för att öppna webbplatsen på ny flik.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen genom följande steg:

1. Öppna [Azure-portalen](https://portal.azure.com)
1. Sök efter **min-First-Web-static-app** från det övre Sök fältet
1. Klicka på appens namn
1. Klicka på knappen **ta bort**
1. Bekräfta borttagnings åtgärden genom att klicka på **Ja**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
