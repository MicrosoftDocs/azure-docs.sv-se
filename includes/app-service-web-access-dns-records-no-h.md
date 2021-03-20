---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95986558"
---
> [!NOTE]
> Du kan använda Azure DNS för att konfigurera ett anpassat DNS-namn för Azure App Service. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

1. Logga in på webbplatsen till din domänleverantör.

1. Sök upp sidan för hantering av DNS-poster. Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet **Domännamn**, **DNS**, eller **Namnserverhantering**.

   Du hittar ofta sidan DNS-poster genom att visa din konto information och sedan söka efter en länk, till exempel **Mina domäner**. Gå till sidan och leta efter en länk som heter något som **zonfilen**, **DNS-poster** eller **Avancerad konfiguration**.

   Skärmbilden nedan är ett exempel på en sida med DNS-poster:

   ![Skärm bild som visar en exempel sida med DNS-poster.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. I skärm bilden exempel väljer du **Lägg till** för att skapa en post. Vissa providrar har olika länkar för att lägga till olika posttyper. Se leverantörens dokumentation.

> [!NOTE]
> För vissa leverantörer, till exempel GoDaddy, börjar ändringar i DNS-posterna inte att gälla förrän du väljer en separat **Spara ändringar**-länk.
