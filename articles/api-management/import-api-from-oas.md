---
title: Importera en OpenAPI-specifikation som använder Azure Portal | Microsoft Docs
description: 'Lär dig hur du importerar en OpenAPI-specifikation med API Management och sedan testar API: et i Azure-och Developer-portalerna.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994728"
---
# <a name="import-an-openapi-specification"></a>Importera en OpenAPI-specifikation

I den här artikeln visas hur du importerar en OpenAPI-specifikation för serverdels-API som finns på https://conferenceapi.azurewebsites.net?format=json. Detta serverdels-API tillhandahålls av Microsoft och finns i Azure. Artikeln beskriver också hur du testar APIM-API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importerar en OpenAPI-specifikation för serverdels-API
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importera och publicera ett serverdels-API

1. Gå till din API Management-tjänst i Azure Portal och välj **API: er** på menyn.
2. Välj **OpenAPI-specifikation** i listan **Lägg till ett nytt API**.

    ![OpenAPI-specifikation](./media/import-api-from-oas/oas-api.png)
3. Ange API-inställningar. Du kan ange värden när du skapar eller konfigurerar dem senare genom att gå till fliken **Inställningar** . Inställningarna beskrivs i själv studie kursen [Importera och publicera din första API](import-and-publish.md#import-and-publish-a-backend-api) .
4. Välj **Skapa**.

> [!NOTE]
> Begränsningar för API-import finns dokumenterade i [en annan artikel](api-management-api-import-restrictions.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
