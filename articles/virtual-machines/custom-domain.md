---
title: Skapa och använda en anpassad domän
description: Anslut en anpassad domän till en virtuell dator i Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251030"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Lägg till anpassad domän i virtuell Azure-dator eller resurs

I Azure finns det flera sätt att ansluta en anpassad domän till din virtuella dator eller resurs. För alla resurser med en offentlig IP-adress (virtuell dator, Load Balancer Application Gateway) är det mest enkla sättet att skapa en post uppsättning i motsvarande domän registrator. 

## <a name="prerequisites"></a>Förutsättningar 
- Du behöver en virtuell dator med en webb server som kör. Du kan använda [snabb](./linux/quick-create-cli.md) starten för att skapa en virtuell dator och lägga till nginx.

- Den virtuella datorn måste vara tillgänglig för webben (öppen port 80 eller 443). För en säkrare distribution placerar du den virtuella datorn bakom en belastningsutjämnare eller Application Gateway först. Mer information finns i [snabb start: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Ha en befintlig domän och åtkomst till DNS-inställningar. Mer information finns i [köpa en anpassad domän för Azure App Service](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Lägg till anpassad domän i offentlig VM-IP-adress

När du skapar en virtuell dator i Azure Portal skapas automatiskt en offentlig IP-resurs för den virtuella datorn. Din offentliga IP-adress visas på sidan Översikt över virtuella datorer. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Visar den offentliga IP-adressen i avsnittet Essentials på översikts sidan för den virtuella datorn.":::

Om du väljer IP-adressen kan du se mer information om den. Kontrol lera att **IP-tilldelningen** är inställd på **statisk**. En statisk IP-adress kommer inte att ändras om den virtuella datorn eller resursen startas om eller stängs av.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Visar den offentliga IP-konfigurationen så att du kan se om IP-adressen är statisk.":::

Om din IP-adress inte är statisk, måste du skapa ett fullständigt domän namn. 

1. Välj din virtuella dator i portalen. 
1. På den vänstra menyn väljer du **Egenskaper**
1. Under **namn etikett för offentlig IP-address\DNS** väljer du din IP-adress.
2. Ange prefixet som du vill använda under **DNS-namn etikett**.
3. Välj **Spara** längst upp på sidan.
4. Välj **Översikt** på den vänstra menyn för att återgå till BLADet VM-översikt.
5. Kontrol lera att *DNS-namnet* visas korrekt. 

Öppna en webbläsare och ange din IP-adress eller FQDN och kontrol lera att den visar webb innehållet som körs på den virtuella datorn.
 
När du har verifierat din statiska IP eller FQDN går du till din domän leverantör och navigerar till DNS-inställningar.

När du har lagt till en *post* som pekar på din offentliga IP-adress eller FQDN. Till exempel är förfarandet för GoDaddy-domänregistratorn följande:
1. Logga in och välj den anpassade domänen som du vill använda.
2. I avsnittet **domäner** väljer du **hantera alla** och väljer sedan **DNS | Hantera zoner**.
3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).
4. Välj Lägg till på sidan DNS-hantering och välj sedan en i listan typ.
5. Fyll i fälten för en post:
    - Skriv: lämna **ett** markerat.
    - Värd: Ange **@**
    - Pekar på: Ange den offentliga IP-adressen eller FQDN för den virtuella datorn. 
    - TTL: lämna en timme vald.
6. Välj **Spara**.

Posten A post läggs till i tabellen DNS-poster.
 
När posten har skapats tar det vanligt vis ungefär en timme för DNS-spridning, men det kan ibland ta upp till 48 timmar. 


 
## <a name="next-steps"></a>Nästa steg
[Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](../application-gateway/ssl-overview.md).

 
