---
title: Självstudie – rensa resurser | Azure
description: I den här självstudien får du lära dig hur du rensar de Azure-resurser som allokeras när du skapar webbappen.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: cfb42b82943f03c3633ff69662ab841bf587ede4
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221865"
---
# <a name="tutorial-clean-up-resources"></a>Självstudie: Rensa resurser

Om du har slutfört alla steg i den här självstudien har du skapat en app service, en värd plan för app service och ett lagrings konto i en resurs grupp. Du skapade också en app-registrering i Azure Active Directory. När de inte längre behövs kan du ta bort dessa resurser och registrera registreringen så att du inte fortsätter att Periodisera avgifter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Ta bort de Azure-resurser som skapats när du följer självstudien.

## <a name="delete-the-resource-group"></a>Ta bort resursgruppen

I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper** på Portal-menyn och väljer den resurs grupp som innehåller app service-och App Service-planen.

Välj **ta bort resurs grupp** för att ta bort resurs gruppen och alla resurser.

:::image type="content" alt-text="Skärm bild som visar borttagning av resurs gruppen." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Det kan ta flera minuter att köra det här kommandot.

## <a name="delete-the-app-registration"></a>Ta bort registrerings appen

Från menyn Portal väljer du **Azure Active Directory**  >  **Appregistreringar**. Välj sedan det program som du skapade.
:::image type="content" alt-text="Skärm bild som visar val av app-registrering." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

I Översikt över app Registration väljer du **ta bort**.
:::image type="content" alt-text="Skärm bild som visar borttagning av appens registrering." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Ta bort de Azure-resurser som skapats när du följer självstudien.

Lär dig hur du ansluter en [.net Core-app](tutorial-dotnetcore-sqldb-app.md), [python-app](tutorial-python-postgresql-app.md), [java-app](tutorial-java-spring-cosmosdb.md)eller [Node.js app](tutorial-nodejs-mongodb-app.md) till en databas.