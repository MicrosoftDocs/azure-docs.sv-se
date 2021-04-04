---
title: Distribuera en belastningsutjämnare mot Internet med IPv6-Azure-mall
titleSuffix: Azure Load Balancer
description: Lär dig hur du distribuerar IPv6-stöd för Azure Load Balancer och belastningsutjämnade virtuella datorer med en Azure-mall.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dubbel stack, offentlig IP, inbyggd IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 415c95a441ac0cc6ed2dbf2d6a37f57d7a9e7341
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90562527"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Distribuera en Internetbaserad lösning för belastnings utjämning med IPv6 med hjälp av en mall

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>I den här artikeln beskrivs en inledande IPv6-funktion för att tillåta grundläggande belastningsutjämnare att tillhandahålla både IPv4-och IPv6-anslutning. En omfattande IPv6-anslutning är nu tillgänglig med [IPv6 för Azure-virtuella nätverk](../virtual-network/ipv6-overview.md) som integrerar IPv6-anslutningar med dina virtuella nätverk och innehåller viktiga funktioner som IPv6-regler för nätverks säkerhets grupper, IPv6-användardefinierad routning, IPv6 Basic och standard belastnings utjämning med mera.  IPv6 för Azure virtuella nätverk är den rekommenderade standarden för IPv6-program i Azure. Se [IPv6 för Azure VNet PowerShell-distribution](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Lastbalanseraren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en lastbalanseringsuppsättning. Azure Load Balancer kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

## <a name="example-deployment-scenario"></a>Exempel på distributions scenario

Följande diagram illustrerar belastnings Utjämnings lösningen som distribueras med hjälp av exempel mal len som beskrivs i den här artikeln.

![Diagram visar ett exempel scenario som används i den här artikeln, inklusive en arbets Stations klient som är ansluten till en Azure Load Balancer via Internet, ansluten i tur till två virtuella datorer.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

I det här scenariot kommer du att skapa följande Azure-resurser:

* ett virtuellt nätverks gränssnitt för varje virtuell dator med både IPv4-och IPv6-adresser tilldelade
* en Internetbaserad Load Balancer med en IPv4-och en offentlig IP-adress för IPv6
* två belastnings Utjämnings regler för att mappa offentliga VIP-adresser till privata slut punkter
* en tillgänglighets uppsättning som innehåller de två virtuella datorerna
* två virtuella datorer (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Distribuera mallen med hjälp av Azure Portal

Den här artikeln hänvisar till en mall som publiceras i galleriet för [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Du kan hämta mallen från galleriet eller starta distributionen i Azure direkt från galleriet. Den här artikeln förutsätter att du har hämtat mallen till den lokala datorn.

1. Öppna Azure Portal och logga in med ett konto som har behörighet att skapa virtuella datorer och nätverks resurser i en Azure-prenumeration. Även om du inte använder befintliga resurser måste kontot ha behörighet att skapa en resurs grupp och ett lagrings konto.
2. Klicka på "+ nytt" på menyn och skriv sedan "mall" i sökrutan. Välj "Malldistribution" från Sök resultaten.

    ![Skärm bild som visar Azure Portal med nytt och Malldistribution valt.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Klicka på "Malldistribution" på bladet allt.

    ![Skärm bild som visar Malldistribution på Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klicka på "skapa".

    ![Skärm bild som visar beskrivningen av Malldistribution i Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klicka på Redigera mall. Ta bort det befintliga innehållet och kopiera/klistra in hela innehållet i mallfilen (om du vill ta med start och slut {}) klickar du på Spara.

    > [!NOTE]
    > Om du använder Microsoft Internet Explorer när du klistrar in får du en dialog ruta där du uppmanas att tillåta åtkomst till Urklipp i Windows. Klicka på Tillåt åtkomst.

    ![Skärm bilden visar det Fire-ste steget i en anpassad distribution, vilket är Redigera mall.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klicka på Redigera parametrar. I bladet parametrar anger du värdena per vägledning i avsnittet mallparametrar och klickar sedan på Spara för att stänga bladet parametrar. På bladet anpassad distribution väljer du din prenumeration, en befintlig resurs grupp eller skapar en. Om du skapar en resurs grupp väljer du en plats för resurs gruppen. Klicka sedan på **juridiska villkor** och sedan på **köp** för de juridiska villkoren. Azure börjar distribuera resurserna. Det tar flera minuter att distribuera alla resurser.

    ![Skärm bild som visar de steg som ingår i den anpassade distributionen och som börjar med att ange parameter värden för mallen.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Mer information om dessa parametrar finns i avsnittet [mallparametrar och variabler](#template-parameters-and-variables) längre fram i den här artikeln.

7. Om du vill se resurserna som skapats av mallen klickar du på Bläddra, bläddrar nedåt i listan tills du ser "resurs grupper" och klickar sedan på den.

    ![Skärm bild som visar Azure Portal med valda Bläddra och resurs grupper.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. På bladet resurs grupper klickar du på namnet på den resurs grupp som du angav i steg 6. Du ser en lista över alla resurser som har distribuerats. Om alla gick bra bör det stå "lyckades" under "senaste distribution". Om inte bör du kontrol lera att det konto som du använder har behörighet att skapa nödvändiga resurser.

    ![Skärm bild som visar status för den senaste distributionen för en resurs grupp, i det här exemplet lyckades.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Om du bläddrar i resurs grupperna direkt efter att ha slutfört steg 6, visar "senaste distribution" statusen för "distribution" medan resurserna distribueras.

9. Klicka på "myIPv6PublicIP" i listan över resurser. Du ser att den har en IPv6-adress under IP-adress och att dess DNS-namn är det värde som du angav för parametern dnsNameforIPv6LbIP i steg 6. Den här resursen är den offentliga IPv6-adressen och värd namnet som är tillgängligt för Internet-klienter.

    ![Skärm bild som visar den offentliga IPv6-adressen.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Verifiera anslutning

När mallen har distribuerats kan du verifiera anslutningen genom att utföra följande uppgifter:

1. Logga in på Azure Portal och Anslut till var och en av de virtuella datorerna som skapats av mall distributionen. Om du har distribuerat en virtuell Windows Server-dator kör du ipconfig/all från en kommando tolk. Du ser att de virtuella datorerna har både IPv4-och IPv6-adresser. Om du har distribuerat virtuella Linux-datorer måste du konfigurera Linux-operativsystemet för att ta emot dynamiska IPv6-adresser med hjälp av anvisningarna för din Linux-distribution.
2. Från en IPv6 Internet-ansluten klient initierar du en anslutning till belastningsutjämnaren för den offentliga IPv6-adressen. För att bekräfta att belastningsutjämnaren balanserar mellan de två virtuella datorerna kan du installera en webb server som Microsoft Internet Information Services (IIS) på varje virtuell dator. Standard webb sidan på varje server kan innehålla texten "Server0" eller "server1" för att unikt identifiera den. Öppna sedan en webbläsare på en IPv6-ansluten klient och bläddra till det värdnamn du angav för dnsNameforIPv6LbIP-parametern för att bekräfta IPv6-anslutningen från slut punkt till slut punkt för varje virtuell dator. Om du bara ser webb sidan från en enda server kan du behöva rensa webbläsarens cacheminne. Öppna flera privata webbläsarsessionen. Du bör se ett svar från varje server.
3. Från en IPv4-ansluten klient initierar du en anslutning till belastningsutjämnaren för den offentliga IPv4-adressen. För att bekräfta att belastningsutjämnaren är belastnings utjämning på de två virtuella datorerna kan du testa att använda IIS, enligt beskrivningen i steg 2.
4. Starta en utgående anslutning till en IPv6-eller IPv4-ansluten Internet-enhet från varje virtuell dator. I båda fallen är belastnings utjämningens offentliga IPv4-eller IPv6-adress den källa-IP-adress som visas av mål enheten.

> [!NOTE]
> ICMP för både IPv4 och IPv6 är blockerat i Azure-nätverket. Det innebär att ICMP-verktyg som ping alltid inte fungerar. Om du vill testa anslutningen använder du ett TCP-alternativ, till exempel TCPing eller PowerShell Test-NetConnection cmdlet. Observera att IP-adresserna som visas i diagrammet är exempel på värden som kan visas. Eftersom IPv6-adresserna tilldelas dynamiskt, kommer de adresser som du får skilja sig åt och kan variera beroende på region. Det är också vanligt att den offentliga IPv6-adressen på belastningsutjämnaren börjar med ett annat prefix än de privata IPv6-adresserna i backend-poolen.

## <a name="template-parameters-and-variables"></a>Mallparametrar och variabler

En Azure Resource Manager-mall innehåller flera variabler och parametrar som du kan anpassa efter dina behov. Variabler används för fasta värden som du inte vill att en användare ska ändra. Parametrar används för värden som du vill att en användare ska tillhandahålla när du distribuerar mallen. Exempel mal len är konfigurerad för det scenario som beskrivs i den här artikeln. Du kan anpassa den här miljön efter behov.

Exempel mal len som används i den här artikeln innehåller följande variabler och parametrar:

| Parameter/variabel | Kommentarer |
| --- | --- |
| adminUsername |Ange namnet på det administratörs konto som används för att logga in på de virtuella datorerna med. |
| adminPassword |Ange lösen ordet för det administratörs konto som används för att logga in på de virtuella datorerna med. |
| dnsNameforIPv4LbIP |Ange DNS-värdnamnet som du vill tilldela som Last balansers offentliga namn. Det här namnet matchar belastningsutjämnarens offentliga IPv4-adress. Namnet måste vara i gemener och matcha regex: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. |
| dnsNameforIPv6LbIP |Ange DNS-värdnamnet som du vill tilldela som Last balansers offentliga namn. Det här namnet matchar belastningsutjämnarens offentliga IPv6-adress. Namnet måste vara i gemener och matcha regex: ^ [a-z] [a-Z0-9-] {1,61} [a-Z0-9] $. Detta kan vara samma namn som IPv4-adressen. När en klient skickar en DNS-fråga för det här namnet kommer Azure att returnera både A-och AAAA-posterna när namnet delas. |
| vmNamePrefix |Ange prefixet för den virtuella datorns namn. Mallen lägger till ett tal (0, 1 osv.) till namnet när de virtuella datorerna skapas. |
| nicNamePrefix |Ange prefixet för nätverks gränssnittets namn. Mallen lägger till ett tal (0, 1 osv.) till namnet när nätverks gränssnitten skapas. |
| storageAccountName |Ange namnet på ett befintligt lagrings konto eller ange namnet på ett nytt som ska skapas av mallen. |
| availabilitySetName |Ange sedan namnet på den tillgänglighets uppsättning som ska användas med de virtuella datorerna |
| addressPrefix |Adressprefixet som används för att definiera adress intervallet för Virtual Network |
| subnetName |Namnet på under nätet som har skapats för VNet |
| subnetPrefix |Adressprefixet som används för att definiera adress intervallet för under nätet |
| vnetName |Ange namnet på det VNet som används av de virtuella datorerna. |
| ipv4PrivateIPAddressType |Den allokeringsmetod som används för den privata IP-adressen (statisk eller dynamisk) |
| ipv6PrivateIPAddressType |Den allokeringsmetod som används för den privata IP-adressen (dynamisk). IPv6 stöder endast dynamisk allokering. |
| numberOfInstances |Antalet belastningsutjämnade instanser som distribuerats av mallen |
| ipv4PublicIPAddressName |Ange det DNS-namn som du vill använda för att kommunicera med belastningsutjämnarens offentliga IPv4-adress. |
| ipv4PublicIPAddressType |Den allokeringsmetod som används för den offentliga IP-adressen (statisk eller dynamisk) |
| Ipv6PublicIPAddressName |Ange det DNS-namn som du vill använda för att kommunicera med belastnings utjämningens offentliga IPv6-adress. |
| ipv6PublicIPAddressType |Den allokeringsmetod som används för den offentliga IP-adressen (dynamisk). IPv6 stöder endast dynamisk allokering. |
| lbName |Ange namnet på belastningsutjämnaren. Det här namnet visas i portalen eller används för att referera till det med ett CLI-eller PowerShell-kommando. |

De återstående variablerna i mallen innehåller härledda värden som tilldelas när Azure skapar resurserna. Ändra inte dessa variabler.

## <a name="next-steps"></a>Nästa steg

JSON-syntaxen och egenskaperna för en belastningsutjämnare i en mall finns i [Microsoft. Network/belastningsutjämnare](/azure/templates/microsoft.network/loadbalancers).
