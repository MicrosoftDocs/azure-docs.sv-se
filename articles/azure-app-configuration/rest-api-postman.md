---
title: Azure Active Directory REST API-test med Postman
description: Använd Postman för att testa Azure App-konfigurationen REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932497"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Testa Azure App konfiguration REST API med Postman

Om du vill testa REST API med [Postman](https://www.getpostman.com/)måste du inkludera de HTTP-huvuden som krävs för [autentisering](./rest-api-authentication-hmac.md) i dina begär Anden. Så här konfigurerar du Postman för att testa REST API, vilket genererar autentiseringsinställningarna automatiskt:

1. Skapa en ny [begäran](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)
1. Lägg till `signRequest` funktionen från [Java Script Authentication-exemplet](./rest-api-authentication-hmac.md#javascript) till [skriptet före begäran](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/) för begäran
1. Lägg till följande kod i slutet av skriptet för för begäran. Uppdatera åtkomst nyckeln enligt vad som anges i kommentaren om att göra

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. Skicka begäran
