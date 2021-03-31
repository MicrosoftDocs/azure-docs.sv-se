---
title: Använda Azure DNS med andra Azure-tjänster
description: I den här utbildnings vägen kommer du igång med att använda Azure DNS för att matcha namn för andra Azure-tjänster
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76937243"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Hur Azure DNS fungerar med andra Azure-tjänster

Azure DNS är en värdbaserad DNS-hantering och namn matchnings tjänst. Du kan använda den för att skapa offentliga DNS-namn för andra program och tjänster som du distribuerar i Azure. Det är enkelt att skapa ett namn för en Azure-tjänst i din anpassade domän. Du lägger bara till en post av rätt typ för din tjänst.

* För dynamiskt allokerade IP-adresser kan du skapa en DNS CNAME-post som mappar till DNS-namnet som Azure skapade för din tjänst. DNS-standarder hindrar dig från att använda en CNAME-post för zonens Apex. Du kan använda en Ali-post i stället. Mer information finns i [Självstudier: Konfigurera en aliasresurspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md).
* För statiskt allokerade IP-adresser kan du skapa en DNS-post med hjälp av valfritt namn som innehåller ett *blott-domännamn* i zonens Apex.

Följande tabell beskriver de post typer som stöds och som du kan använda för olika Azure-tjänster. Som tabellen visar stöder Azure DNS endast DNS-poster för nätverks resurser som är riktade mot Internet. Azure DNS kan inte användas för namn matchning av interna, privata adresser.

| Azure-tjänst | Nätverksgränssnitt | Beskrivning |
| --- | --- | --- |
| Azure Application Gateway |[Offentlig IP-adress på klient Sidan](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A-eller CNAME-post. |
| Azure Load Balancer |[Offentlig IP-adress på klient Sidan](dns-custom-domain.md#public-ip-address) |Du kan skapa en DNS A-eller CNAME-post. Load Balancer kan ha en offentlig IPv6-IP-adress som är dynamiskt tilldelad. Skapa en CNAME-post för en IPv6-adress. |
| Azure Traffic Manager |Offentligt namn |Du kan skapa en aliasresurspost som mappar till trafficmanager.net-namnet som tilldelats din Traffic Manager-profil. Mer information finns i [Självstudier: Konfigurera en aliasresurspost som stöder spetsiga domän namn med Traffic Manager](tutorial-alias-tm.md). |
| Azure Cloud Services |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |För statiskt allokerade IP-adresser kan du skapa en DNS A-post. För dynamiskt allokerade IP-adresser måste du skapa en CNAME-post som mappar till *cloudapp.net* namn.|
| Azure App Service | [Extern IP](dns-custom-domain.md#app-service-web-apps) |För externa IP-adresser kan du skapa en DNS A-post. Annars måste du skapa en CNAME-post som mappar till azurewebsites.net namn. Mer information finns i [mappa ett anpassat domän namn till en Azure-App](../app-service/app-service-web-tutorial-custom-domain.md). |
| Azure Resource Manager virtuella datorer |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Virtuella Resource Manager-datorer kan ha offentliga IP-adresser. En virtuell dator med en offentlig IP-adress kan också ligga bakom en belastningsutjämnare. Du kan skapa en DNS-, CNAME-eller Ali-post för den offentliga adressen. Du kan använda det här anpassade namnet för att kringgå VIP i belastningsutjämnaren. |
| Klassiska virtuella datorer |[Offentlig IP-adress](dns-custom-domain.md#public-ip-address) |Klassiska virtuella datorer som skapats med hjälp av PowerShell eller CLI kan konfigureras med en dynamisk eller statisk (reserverad) virtuell adress. Du kan skapa en DNS CNAME-post eller en A-post. |
