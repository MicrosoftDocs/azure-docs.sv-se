---
title: Förbered för ändring av utgående IP-adress
description: Om den utgående IP-adressen ska ändras kan du läsa vad du kan göra så att din app fortsätter att fungera efter ändringen.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74671673"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Förbereda för en utgående IP-adress ändring

Om du har fått ett meddelande om att de utgående IP-adresserna för din Azure App Service-appen ändras följer du anvisningarna i den här artikeln.

## <a name="determine-if-you-have-to-do-anything"></a>Ta reda på om du behöver göra något

* Alternativ 1: om din App Service-app inte använder IP-filtrering, en explicit inkluderings lista eller särskild hantering av utgående trafik, till exempel routning eller brand vägg, krävs ingen åtgärd.

* Alternativ 2: om din app har särskild hantering för de utgående IP-adresserna (se exemplen nedan) lägger du till de nya utgående IP-adresserna där de befintliga visas. Ersätt inte de befintliga IP-adresserna. Du kan hitta de nya utgående IP-adresserna genom att följa anvisningarna i nästa avsnitt.

  Till exempel kan en utgående IP-adress uttryckligen inkluderas i en brand vägg utanför din app, eller så kan en extern betalnings tjänst ha en lista över tillåtna IP-adresser som innehåller den utgående IP-adressen för din app. Om den utgående adressen är konfigurerad i en lista var som helst utanför appen, måste den ändras.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Hitta de utgående IP-adresserna i Azure Portal

De nya utgående IP-adresserna visas i portalen innan de börjar gälla. När Azure börjar använda de nya kommer de gamla inte längre att användas. Endast en uppsättning i taget används, så poster i inkluderings listor måste ha både gamla och nya IP-adresser för att förhindra ett avbrott när växeln sker. 

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  I den vänstra navigerings menyn väljer du **app Services**.

3.  Välj din App Service-app i listan.

1.  Om appen är en Function-app, se [funktion app utgående IP-adresser](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  Under rubriken **Inställningar** klickar du på **Egenskaper** i det vänstra navigerings fältet och letar upp avsnittet med etiketten **utgående IP-adresser**.

5. Kopiera IP-adresserna och Lägg till dem i din särskilda hantering av utgående trafik, till exempel ett filter eller en lista över tillåtna. Ta inte bort de befintliga IP-adresserna i listan.

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklaras hur du förbereder en ändring av IP-adressen som initierades av Azure. Mer information om IP-adresser i Azure App Service finns i [inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).
