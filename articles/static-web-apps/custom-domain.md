---
title: Konfigurera en anpassad domän i Azure static Web Apps
description: Lär dig att mappa en anpassad domän till Azure static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173690"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Konfigurera en anpassad domän i förhandsversionen av Azure Static Web Apps

Som standard tillhandahåller Azure static Web Apps ett automatiskt genererat domän namn. Den här artikeln visar hur du mappar ett anpassat domän namn till ett statiskt Azure-Web Apps program.

## <a name="prerequisites"></a>Förutsättningar

- Ett köpt domän namn
- Åtkomst till DNS-konfigurationens egenskaper för din domän

När du konfigurerar domän namn används "A"-poster för att mappa rot domäner (till exempel `example.com` ) till en IP-adress. Rot domäner måste mappas direkt till en IP-adress, eftersom DNS-specifikationen inte tillåter mappning av en domän till en annan.

## <a name="dns-configuration-options"></a>Alternativ för DNS-konfiguration

Det finns några olika typer av DNS-konfigurationer som är tillgängliga för ett program.

| Om du vill | Dra |
|--|--|
| Support `www.example.com` eller `blog.example.net` | [Mappa en CNAME-post](#map-a-cname-record) |
| Hjälp `example.com` | [Konfigurera en rot domän](#configure-a-root-domain) |
| Peka alla under domäner till `www.example.com` | [Mappa ett jokertecken](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>Mappa en CNAME-post

En CNAME-post mappar en domän till en annan. Du kan använda en CNAME-post för att mappa `www.example.com` , `blog.example.com` eller någon annan under domän till den automatiskt genererade domän som tillhandahålls av Azures statiska Web Apps.

1. Öppna [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Sök efter och välj **statisk Web Apps**

1. På sidan _statiska Web Apps_ väljer du namnet på din app.

1. Klicka på **anpassade domäner** på menyn.

1. Klicka på knappen **Lägg till**

1. I fönstret _anpassade domäner_ kopierar du webb adressen i fältet **värde** .

### <a name="configure-dns-provider"></a>Konfigurera DNS-Provider

1. Logga in på webbplatsen till din domänleverantör.

2. Sök upp sidan för hantering av DNS-poster. Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet **Domännamn**, **DNS**, eller **Namnserverhantering**.

3. Du kan ofta hitta sidan med DNS-poster genom att visa din kontoinformation och sedan söka efter en länk som heter exempelvis **Mina domäner**. Gå till sidan och leta efter en länk som har samma namn som **zonfilen**, **DNS-poster** eller **Avancerad konfiguration**.

    Skärmbilden nedan är ett exempel på en sida med DNS-poster:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Exempel på konfiguration av DNS-Provider":::

4. Skapa en ny **CNAME** -post med följande värden...

    | Inställning             | Värde                     |
    | ------------------- | ------------------------- |
    | Typ                | CNAME                     |
    | Värd                | www                       |
    | Värde               | Klistra in från Urklipp |
    | TTL (om tillämpligt) | Lämna standard värde    |

5. Spara ändringarna med DNS-providern.

### <a name="validate-cname"></a>Verifiera CNAME

1. Gå tillbaka till fönstret _anpassade domäner_ i Azure Portal.

1. Ange din domän, inklusive `www` delen i avsnittet _Verifiera anpassad domän_ .

1. Klicka på knappen **Verifiera** .

Nu när den anpassade domänen har kon figurer ATS kan det ta flera timmar för DNS-providern att sprida ändringarna över hela världen. Du kan kontrol lera status för spridningen genom att gå till [dnspropagation.net](https://dnspropagation.net). Ange din anpassade domän inklusive `www` , Välj CNAME i list rutan och välj **Starta**.

Om dina DNS-ändringar har fyllts i returnerar webbplatsen den automatiskt genererade URL: en för din statiska webbapp (till exempel _Random-Name-123456789c.azurestaticapps.net_).

## <a name="configure-a-root-domain"></a>Konfigurera en rot domän

Rot domäner är din domän minus alla under domäner, inklusive `www` . Till exempel rot domänen för `www.example.com` är `example.com` . Detta kallas även för en "APEX"-domän.

Även om stöd för rot domäner inte är tillgängligt under för hands versionen kan du se blogg inlägget [Konfigurera rot domäner i Azures statiska Web Apps](https://burkeholland.github.io/posts/static-app-root-domain) för mer information om hur du konfigurerar stöd för rotdomän med en statisk webbapp.

## <a name="map-a-wildcard-domain"></a>Mappa en domän med jokertecken

Ibland vill du att all trafik som skickas till en under domän ska dirigeras till en annan domän. Ett vanligt exempel är att mappa all under domän trafik till `www.example.com` . På så sätt, även om någon typ i `w.example.com` stället för `www.example.com` , skickas begäran till `www.example.com` .

### <a name="configure-dns-provider"></a>Konfigurera DNS-Provider

1. Logga in på webbplatsen till din domänleverantör.

2. Sök upp sidan för hantering av DNS-poster. Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet **Domännamn**, **DNS**, eller **Namnserverhantering**.

3. Du kan ofta hitta sidan med DNS-poster genom att visa din kontoinformation och sedan söka efter en länk som heter exempelvis **Mina domäner**. Gå till sidan och leta efter en länk som heter liknar **zonfilen**, **DNS-poster** eller **Avancerad konfiguration**.

    Skärmbilden nedan är ett exempel på en sida med DNS-poster:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Exempel på konfiguration av DNS-Provider":::

4. Skapa en ny **CNAME** -post med följande värden och Ersätt `www.example.com` med det anpassade domän namnet.

    | Inställning | Värde                  |
    | ------- | ---------------------- |
    | Typ    | CNAME                  |
    | Värd    | \*                     |
    | Värde   | www.example.com        |
    | TTL     | Lämna standard värde |

5. Spara ändringarna med DNS-providern.

Nu när domänen med jokertecken har kon figurer ATS kan det ta flera timmar innan ändringarna sprids över hela världen. Du kan kontrol lera status för spridningen genom att gå till [dnspropagation.net](https://dnspropagation.net). Ange din anpassade domän domän med valfri under domän (förutom `www` ), Välj CNAME i list rutan och välj **Starta**.

Om dina DNS-ändringar har fyllts i, returnerar webbplatsen din anpassade domän som kon figurer ATS för din statiska webbapp (till exempel `www.example.com` ).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera appinställningar](application-settings.md)
