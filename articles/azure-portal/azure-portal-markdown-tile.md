---
title: Använd en anpassad markdown-panel på Azure-instrumentpaneler
description: Lär dig hur du lägger till en markdown-panel på en Azure-instrumentpanel för att Visa statiskt innehåll
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774464"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Använd en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll

Du kan lägga till en markdown-panel till dina Azure-instrumentpaneler för att visa anpassat, statiskt innehåll. Du kan till exempel Visa Basic-instruktioner, en bild eller en uppsättning hyperlänkar på en markdown-panel.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Lägg till en markdown-panel på din instrument panel

1. Välj **instrument panel** från Azure Portal sid panelen.

   ![Skärm bild som visar portalens sid panelen](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. I vyn instrument panel väljer du den instrument panel där rutan för anpassade markdown ska visas och väljer sedan **Redigera**.

   ![Skärm bild som visar Redigeraren för instrument panelen](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Leta upp panelen med namnet **markdown** i **panel galleriet** och välj **Lägg till**. Panelen läggs till i instrument panelen och fönstret **Redigera markdown** öppnas.

1. Ange värden för **rubrik** och **under rubrik**, som visas på panelen när du har flyttat till ett annat fält.

   ![Skärm bild som visar resultatet av att skriva rubrik och under rubrik](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Välj ett av alternativen för att inkludera markdown-innehåll: **infogad redigering** eller **Infoga innehåll med URL**.

   - Välj **infogad redigering** om du vill ange markdown direkt.

      ![Skärm bild som visar inmatning av infogat innehåll](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Välj **Infoga innehåll med URL** om du vill använda befintligt markdown-innehåll som är online.

      ![Skärm bild som visar ange URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > För ökad säkerhet kan du skapa en MARKDOWN-fil och lagra den i en [BLOB för Azure Storage-konto där kryptering är aktiverat](../storage/common/storage-service-encryption.md). peka sedan på filen med URL-alternativet. Markdown-innehållet krypteras via lagrings kontots krypterings alternativ. Endast användare med behörighet till filen kan se markdown-innehållet på instrument panelen. Du kan behöva ange en CORS-regel [(Cross-Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) på lagrings kontot så att Azure Portal ( _https://portal.azure.com/_ ) kan komma åt markdown-filen i blobben.

1. Välj **Done** för att stänga fönstret **Redigera markdown** . Ditt innehåll visas på markdown-panelen, som du kan ändra storlek på genom att dra handtaget i det nedre högra hörnet.

   ![Skärm bild som visar en anpassad markdown-panel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funktioner och begränsningar för markdown-innehåll

Du kan använda valfri kombination av oformaterad text, markdown-syntax och HTML-innehåll på markdown-panelen. I Azure Portal används ett bibliotek med öppen källkod som heter _markerad_ för att transformera ditt innehåll till HTML som visas på panelen. Den HTML-kod som tillverkas av har _marker ATS_ i förväg av portalen innan den renderas. Det här steget ser till att anpassningen inte påverkar portalens säkerhet eller layout. Under den för bearbetningen tas alla delar av HTML-koden som utgör ett potentiellt hot bort. Följande typer av innehåll tillåts inte av portalen:

* Java Script – `<script>` taggar och infogade JavaScript-utvärderingar tas bort.
* iframes – `<iframe>` taggar tas bort.
* Style- `<style>` taggar tas bort. Inline Style-attribut för HTML-element stöds inte officiellt. Det kan hända att vissa infogade format element fungerar för dig, men om de stör portalens layout kan de sluta fungera när som helst. Markdown-panelen är avsedd för grundläggande, statiskt innehåll som använder portalens standardformat.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar en anpassad instrument panel finns [i skapa och dela instrument paneler i Azure Portal](../azure-portal/azure-portal-dashboards.md)
