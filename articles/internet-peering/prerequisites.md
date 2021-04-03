---
title: Förutsättningar för att konfigurera peering med Microsoft
titleSuffix: Azure
description: Förutsättningar för att konfigurera peering med Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586848"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Förutsättningar för att konfigurera peering med Microsoft

Se till att kraven nedan är uppfyllda innan du begär en ny peering eller konvertera en äldre peering till Azure-resursen.

## <a name="azure-related-prerequisites"></a>Azure-relaterade krav
* **Microsoft Azure konto:** Om du inte har ett Microsoft Azure konto skapar du ett [Microsoft Azure-konto](https://azure.microsoft.com/free). Det krävs en giltig och aktiv Microsoft Azure-prenumeration för att konfigurera peering, eftersom peering modelleras som resurser i Azure-prenumerationer. Det är viktigt att notera att:
    * De Azure-resurs typer som används för att konfigurera peering är alltid kostnads fria Azure-produkter, d.v.s. du debiteras inte för att skapa ett Azure-konto eller skapa en prenumeration eller komma åt Azure-resursernas **PeerAsn** och **peering** för att konfigurera peering. Detta ska inte förväxlas med peering-avtal för direkt peering mellan dig och Microsoft, villkoren för vilka beskrivs tydligt med vårt peering-team. Kontakta [Microsoft-peering](mailto:peering@microsoft.com) om några frågor i detta hänseende.
    * Du kan använda samma Azure-prenumeration för att få åtkomst till andra Azure-produkter eller moln tjänster som kan vara kostnads fria eller betalda. När du får åtkomst till en betald produkt debiteras du avgifter.
    * Om du skapar ett nytt Azure-konto och/eller-prenumeration kan du vara berättigad till kostnads fri Azure-kredit under en utvärderings period som du kan använda för att testa Azure Cloud Services. Om du vill kan du besöka [Microsoft Azure-kontot](https://azure.microsoft.com/free) för mer information.

* **Koppla ASN för peer:** Innan du begär peering måste du först associera ditt ASN och kontakta din prenumeration. Följ instruktionerna i [associera peer-ASN till Azure-prenumerationen](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Övriga krav
* **PeeringDB profil:** Peer-datorer förväntas ha en fullständig och aktuell profil på [PeeringDB](https://www.peeringdb.com). Vi använder den här informationen i registrerings systemet för att verifiera motpartens information, till exempel NOC information, teknisk kontakt information och deras närvaro vid peering-funktionerna osv.

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md).
* [Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)
* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)