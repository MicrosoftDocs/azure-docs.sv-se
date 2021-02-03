---
title: Konfigurera ett anpassat domän namn för din egen värd för Azure API Management Gateway | Microsoft Docs
description: Det här avsnittet beskriver stegen för att konfigurera ett anpassat domän namn för Azure API Management Gateway med egen värd.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491040"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Konfigurera ett anpassat domän namn för en egen värd-Gateway

När du etablerar en [Azure API Management-Gateway med egen värd](self-hosted-gateway-overview.md), tilldelas den inte något värdnamn och måste refereras till av dess IP-adress. Den här artikeln visar hur du mappar ett befintligt anpassat DNS-namn (kallas även hostname) till en egen värd-Gateway.

## <a name="prerequisites"></a>Förutsättningar

För att utföra stegen som beskrivs i den här artikeln måste du ha:

-   En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   En API Management-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).
- En egen värd-Gateway. Mer information finns i [så här etablerar du Gateway för egen värd](api-management-howto-provision-self-hosted-gateway.md)
-   Ett anpassat domän namn som ägs av dig eller din organisation. Det här avsnittet innehåller inga instruktioner för hur du kan köpa ett anpassat domän namn.
-   En DNS-post som finns på en DNS-server som mappar det anpassade domän namnet till IP-adressen för den egna värdbaserade gatewayen. Det här avsnittet innehåller inga instruktioner för hur du kan vara värd för en DNS-post.
-   Du måste ha ett giltigt certifikat med en offentlig och privat nyckel (. PFX). Ämnet eller det alternativa ämnes namnet (SAN) måste matcha domän namnet (Detta gör att API Management-instansen på ett säkert sätt kan exponera URL: er över TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Lägg till anpassat domän certifikat till din API Management-tjänst

Lägg till ett anpassat domän certifikat (. PFX) till API Management-instansen eller referera till ett certifikat som lagras i Azure Key Vault. Följ stegen i [säkra server dels tjänster med autentisering av klient certifikat i Azure API Management](api-management-howto-mutual-certificates.md).

> [!NOTE]
> Vi rekommenderar att du använder ett Key Vault-certifikat för den egen värdbaserade gateway-domänen.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Använd Azure Portal för att ange ett anpassat domän namn för din egen värdbaserade Gateway

1. Välj **gatewayerna** under **distribution och infrastruktur**.
2. Välj den gateway för egen värd som du vill konfigurera domän namnet för.
3. Välj **värdnamn** under **Inställningar**.
4. Välj **+ Lägg till**
5. Ange resurs namnet för hostname i fältet **namn** .
6. Ange domän namnet i fältet **hostname** .
7. Välj ett certifikat i list rutan **certifikat** .
8. Markera kryss rutan **förhandla klient certifikat** om något av de API: er som exponeras via denna gateway använder autentisering av klient certifikat.
    > [!WARNING]
    > Den här inställningen delas av alla domän namn som har kon figurer ATS för gatewayen.
9. Välj **Lägg till** för att tilldela det anpassade domän namnet till den valda egen värdbaserade gatewayen.

## <a name="next-steps"></a>Nästa steg

[Uppgradera och skala din tjänst](upgrade-and-scale.md)
