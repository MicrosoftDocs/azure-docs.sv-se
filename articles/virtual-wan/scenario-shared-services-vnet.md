---
title: 'Scenario: dirigera till delade tjänster virtuella nätverk'
titleSuffix: Azure Virtual WAN
description: Scenarier för routning – konfigurera vägar för att få åtkomst till en delad tjänst-VNet med en arbets belastning som du vill att varje VNet och gren ska få åtkomst till.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e0d05d2cb960e760809ab35a8f9e4ca04acf250
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442969"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenario: dirigera till delade tjänster virtuella nätverk

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot är målet att ställa in vägar för att få åtkomst till en **delad tjänst-** VNet med arbets belastningar som du vill att varje VNet och gren (VPN/er/P2s) ska ha åtkomst till. Exempel på dessa delade arbets belastningar kan vara Virtual Machines med tjänster som domänkontrollanter eller fil resurser eller Azure-tjänster som exponeras internt via [privata Azure-slutpunkter](../private-link/private-endpoint-overview.md).

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Vi kan använda en anslutnings mat ris för att sammanfatta kraven i det här scenariot:

**Anslutnings mat ris**

| Från             | Till:   |*Isolerade virtuella nätverk*|*Delat VNet*|*Grenar*|
|---|---|---|---|---|
|**Isolerade virtuella nätverk**| ->|        | Direct | Direct |
|**Delade virtuella nätverk**  |->| Direct | Direct | Direct |
|**Grenar**      |->| Direct | Direct | Direct |

Var och en av cellerna i föregående tabell beskriver om en virtuell WAN-anslutning ("från"-sidan i flödet, rad rubrikerna) kommunicerar med ett mål ("till"-sidan av flödet, kolumn rubrikerna i kursiv stil). I det här scenariot finns det inga brand väggar eller virtuella nätverks enheter, så kommunikationen flödar direkt över det virtuella WAN-nätverket (och därför ordet "Direct" i tabellen).

På samma sätt som det [isolerade VNet-scenariot](scenario-isolate-vnets.md)ger den här anslutnings matrisen två olika rad mönster, som översätts till två väg tabeller (de delade tjänsterna virtuella nätverk och grenarna har samma anslutnings krav). Det virtuella WAN-nätverket har redan en standard väg tabell, så vi behöver en annan anpassad routningstabell som vi ska anropa **RT_SHARED** i det här exemplet.

Virtuella nätverk kommer att kopplas till tabellen **RT_SHARED** väg. Eftersom de behöver anslutning till grenar och till den delade tjänsten virtuella nätverk måste den delade tjänsten VNet och grenarna spridas till **RT_SHARED** (annars skulle virtuella nätverk inte lära sig att lära sig grenen och delade VNet-prefix). Eftersom grenarna alltid är kopplade till standard väg tabellen, och anslutnings kraven är desamma för delade tjänster virtuella nätverk, kommer vi att associera den delade tjänsten virtuella nätverk till standard väg tabellen.

Därför är detta den slutliga designen:

* Isolerade virtuella nätverk:
  * Associerad routningstabell: **RT_SHARED**
  * Sprider till routningstabeller: **standard**
* Delade tjänster, virtuella nätverk:
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **RT_SHARED** och **standard**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **RT_SHARED** och **standard**

> [!NOTE]
> Om ditt virtuella WAN-nätverk har distribuerats över flera regioner måste du skapa **RT_SHARED** routningstabellen i varje hubb, och vägar från alla delade tjänster VNet-och gren anslutning måste spridas till routningstabellen i varje virtuellt nav som använder spridnings etiketter.

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Tänk på följande när du konfigurerar scenariot:

1. Identifiera de **delade tjänsternas** VNet.
2. Skapa en anpassad routningstabell. I exemplet refererar vi till routningstabellen som **RT_SHARED**. Anvisningar för hur du skapar en routningstabell finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md). Använd följande värden som en rikt linje:

   * **Föreningar**
     * För **virtuella nätverk *utom* de delade tjänsterna VNet** väljer du den virtuella nätverk som ska isoleras. Detta innebär att alla dessa virtuella nätverk (förutom det virtuella nätverket för delade tjänster) kan komma åt målet baserat på vägar i RT_SHARED Route-tabellen.

   * **Spridning**
      * För **grenar** sprider du vägar till den här routningstabellen, förutom andra routningstabeller som du kanske redan har valt. På grund av det här steget kommer RT_SHARED Route-tabellen att lära sig vägar från alla förgrenings anslutningar (VPN/ER/användare VPN).
      * För **virtuella nätverk** väljer du **delade tjänster VNet**. På grund av det här steget kommer RT_SHARED Route-tabellen att lära sig vägar från VNet-anslutningen för delade tjänster.

Detta leder till att konfigurationen för routning visas i följande figur:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Diagram för delade tjänster VNet." lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar med en ARM-mall finns i [snabb start: dirigera till delade tjänster virtuella nätverk med en arm-mall](quickstart-route-shared-services-vnet-template.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
