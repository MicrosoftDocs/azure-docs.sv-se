---
title: Självstudie – bygga en säker webbapp på Azure App Service | Azure
description: I den här självstudien får du lära dig hur du skapar en webbapp med hjälp av Azure App Service, aktiverar autentisering, anropar Azure Storage och anropar Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 5b94000ce59b3a115e53ecdcd0849b97aae552c5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221848"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Självstudie: aktivera autentisering i App Service och åtkomst till lagring och Microsoft Graph

I den här självstudien beskrivs ett gemensamt program scenario där du får lära dig att:

- [Konfigurera autentisering för en webbapp](scenario-secure-app-authentication-app-service.md) och begränsa åtkomsten till användare i din organisation. Se en i diagrammet.
- [Säker åtkomst Azure Storage](scenario-secure-app-access-storage.md) för webb programmet med hanterade identiteter. Se B i diagrammet.
- Få åtkomst till data i Microsoft Graph [för den inloggade användaren](scenario-secure-app-access-microsoft-graph-as-user.md) eller [för webb programmet](scenario-secure-app-access-microsoft-graph-as-app.md) med hanterade identiteter. Se C i diagrammet.
- [Rensa resurserna](scenario-secure-app-clean-up-resources.md) som du skapade för den här självstudien.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagram som visar program scenarier i Microsoft Identity Platform." border="false":::

Börja med att lära dig att aktivera autentisering för en webbapp.

> [!div class="nextstepaction"]
> [Konfigurera autentisering för en webbapp](scenario-secure-app-authentication-app-service.md)
