---
title: Förbered för ändring av IP-adress för SSL
description: Om din SSL-IP-adress ska ändras kan du läsa vad du kan göra så att din app fortsätter att fungera efter ändringen.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020967"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Förbereda för en ändring av en SSL-IP-adress

Om du har fått ett meddelande om att din Azure App Service-appens SSL-adress ändras följer du anvisningarna i den här artikeln för att frigöra befintlig SSL-IP-adress och tilldela en ny.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Frigör SSL IP-adresser och tilldela nya

1.  Öppna [Azure-portalen](https://portal.azure.com).

2.  I den vänstra navigerings menyn väljer du **app Services**.

3.  Välj din App Service-app i listan.

4.  Under rubriken **Inställningar** klickar du på **SSL-inställningar** i det vänstra navigerings fältet.

1. I avsnittet TLS/SSL-bindningar väljer du värd namn posten. I redigeraren som öppnas väljer du **SNI SSL** på den nedrullningsbara menyn för **SSL-typ** och klickar på **Lägg till bindning**. När meddelandet åtgärden lyckades visas har den befintliga IP-adressen släppts.

6.  I avsnittet **SSL-bindningar** igen väljer du samma värd namn post med certifikatet. I redigeraren som öppnas väljer den här gången **IP-baserad SSL** på den nedrullningsbara menyn för **SSL-typ** och klickar på **Lägg till bindning**. När du ser ett meddelande om att åtgärden lyckades har du skaffat en ny IP-adress.

7.  Om en post (DNS-post som pekar direkt till din IP-adress) är konfigurerad i din domän registrerings portal (tredjeparts-DNS-provider eller Azure DNS) ersätter du den befintliga IP-adressen med den nya som genererades. Du kan hitta den nya IP-adressen genom att följa anvisningarna i nästa avsnitt.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Hitta den nya SSL-IP-adressen i Azure Portal

1.  Vänta några minuter och öppna sedan [Azure Portal](https://portal.azure.com).

2.  I den vänstra navigerings menyn väljer du **app Services**.

3.  Välj din App Service-app i listan.

4.  Under rubriken **Inställningar** klickar du på **Egenskaper** i det vänstra navigerings fältet och letar upp avsnittet med etiketten **virtuell IP-adress**.

5. Kopiera IP-adressen och konfigurera om domän posten eller IP-mekanismen.

## <a name="next-steps"></a>Nästa steg

Den här artikeln förklaras hur du förbereder en ändring av IP-adressen som initierades av Azure. Mer information om IP-adresser i Azure App Service finns i [inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).
