---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 614a13a140453e3c7ed55a7fc0f9173626ad2f2f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627733"
---
1. Välj **Anslut VPN-platser** för att öppna sidan **Anslut platser** .

    ![Skärm bild som visar rutan anslutna webbplatser för den virtuella NAVEt som är klar för en I förväg delad nyckel och tillhör ande inställningar.](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "connect")

   Fyll i följande fält:

   * Ange en i förväg delad nyckel. Om du inte anger någon nyckel genererar Azure automatiskt en åt dig.
   * Välj protokoll-och IPsec-inställningar. Mer information finns i [standard-/anpassad IPsec](../articles/virtual-wan/virtual-wan-ipsec.md).
   * Välj lämpligt alternativ för att **sprida standard väg**. Alternativet **Aktivera** gör det möjligt för den virtuella hubben att sprida en inlärd standard väg till den här anslutningen. Den här flaggan aktiverar standard vägs spridning enbart till en anslutning om standard vägen redan har belärts av den virtuella WAN-hubben på grund av distribution av en brand vägg i hubben, eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad. Standard vägen kommer inte från den virtuella WAN-hubben.

2. Välj **Anslut**.
3. Efter några minuter visas anslutningen och anslutnings statusen för platsen.

   ![Skärm bild som visar en plats-till-plats-anslutning och anslutnings status.](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Anslutnings status:** Detta är statusen för Azure-resursen för den anslutning som ansluter VPN-platsen till Azure Hub-VPN-gatewayen. När den här kontroll Plans åtgärden lyckas fortsätter Azure VPN-gatewayen och den lokala VPN-enheten att upprätta anslutningen.

   **Anslutnings status:** Detta är den faktiska anslutnings statusen (data Sök vägen) mellan Azures VPN-gateway på NAV-och VPN-platsen. Det kan visa något av följande tillstånd:

    * **Okänd**: det här tillståndet visas vanligt vis om backend-systemen arbetar för att övergå till en annan status.
    * **Ansluter**: Azure VPN-gatewayen försöker kontakta den faktiska lokala VPN-platsen.
    * **Ansluten**: anslutningen upprättas mellan Azure VPN-gatewayen och den lokala VPN-platsen.
    * **Frånkopplad**: den här statusen visas, oavsett orsak (lokalt eller i Azure), anslutningen kopplades från.
4. På en hubb VPN-webbplats kan du göra följande: 

   * Redigera eller ta bort VPN-anslutningen.
   * Ta bort platsen i Azure Portal.
   * Hämta en Branch-/regionsspecifika konfiguration för information om Azure-sidan med hjälp av kontexten (...) bredvid webbplatsen. Om du vill ladda ned konfigurationen för alla anslutna platser i hubben väljer du **Hämta VPN-** konfiguration på den översta menyn.
