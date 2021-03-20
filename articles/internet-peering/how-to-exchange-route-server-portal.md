---
title: Peering-anslutning för Exchange-partner med Route Server genom att använda portalen
titleSuffix: Azure
description: Skapa eller ändra en Exchange-peering med Route Server genom att använda Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537199"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Skapa eller ändra en Exchange-peering med Route server i Azure Portal

Den här artikeln beskriver hur du skapar en Microsoft Exchange-peering med en Route-server med hjälp av Azure Portal. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.


## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.
* Om du redan har Exchange-peering med Microsoft som inte har konverterats till Azure-resurser kan du läsa mer i [konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Skapa en Exchange-peering med Route Server


Som en Internet Exchange-Provider kan du skapa en Exchange peering-begäran genom att [skapa en peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. På sidan **skapa en peering** , på fliken **grundläggande** , fyller du i rutorna som visas här:

    > [!div class="mx-imgBorder"] 
    > ![Registrera Peering Service](./media/setup-basics-tab.png)

* Välj din Azure-prenumeration.

* För resurs grupp kan du antingen välja en befintlig resurs grupp i list rutan eller skapa en ny grupp genom att välja Skapa ny. Vi ska skapa en ny resurs grupp för det här exemplet.

* Namnet motsvarar resurs namnet och kan vara allt du väljer.

* Regionen väljs automatiskt om du väljer en befintlig resurs grupp. Om du väljer att skapa en ny resurs grupp måste du också välja den Azure-region där du vill att resursen ska finnas.

    >[!NOTE]
    >Den region där en resurs grupp finns är oberoende av den plats där du vill skapa peering med Microsoft. Men det är en bra idé att organisera dina peering-resurser i resurs grupper som finns i de närmaste Azure-regionerna. För peering i Ashburn kan du till exempel skapa en resurs grupp i USA, östra eller östra 2; USA.

* Välj ditt ASN i rutan **PeerASN** .

    >[!IMPORTANT] 
    >Du kan bara välja ett ASN med ValidationState som godkänd innan du skickar en peering-begäran. Om du precis skickat in din PeerAsn-begäran, vänta i 12 timmar eller så att ASN-associationen kan godkännas. Om det ASN du väljer är väntande verifiering visas ett fel meddelande. Om du inte ser det ASN du behöver välja, kontrollerar du att du har valt rätt prenumeration. Om så är fallet kontrollerar du om du redan har skapat PeerAsn genom att använda **[associera peer-ASN till Azure-prenumerationen](https://go.microsoft.com/fwlink/?linkid=2129592)**.

* Välj **Nästa: konfiguration** för att fortsätta.

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka

1. På sidan Skapa en peering på fliken Konfiguration fyller du i rutorna som visas här:

    > [!div class="mx-imgBorder"]
    > ![Konfigurera Route Server](./media/setup-exchange-conf-tab-routeserver.png)
 
    * För peering-typ väljer du **direkt**
    * För Microsoft Network väljer du **AS8075 med Exchange Route Server**. 
    * Välj SKU som **standard kostnads fritt**. Välj inte Premium kostnads fritt eftersom det är reserverat för särskilda program.
    * Välj den **tunnelbane** plats där du vill konfigurera peering.

1. Under **peering-anslutningar** väljer du **Skapa ny**

1.  Under **direkt peering-anslutning** fyller du i följande information om BGP-sessioner:

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar fönstret direkt peering-anslutning med information tillagda.](./media/setup-exchange-conf-tab-direct-route.png)


     * Peering-funktion, Välj lämplig fysisk plats för peer koppling
     * Session Address Provider, Välj peer
     * Sessionens IPv4-prefix kommer att tillhandahållas av Exchange Provider-peer
     * Peer-sessionens IPv4-adress kommer att väljas av Exchange-peer för väg servern från deras IP-adressprefix.
     * Microsoft-sessionens IPv4-adress, är den router-IP som tilldelas från IP-prefixet.
     * Session IPv6 är valfritt för tillfället.
     * Maximalt annonseringat IPv4-prefix kan vara upp till 20000. 
     * Använd för peering-tjänsten är inaktive rad som standard. Det kan aktive ras när Exchange-providern har signerat ett peering service-avtal med Microsoft.

1. Klicka på **Spara** när du är klar. 

1. Under skapa en peering ser du att verifieringen är klar. När verifieringen har slutförts klickar du på **skapa**

    > [!div class="mx-imgBorder"]
    > ![Validering av inställningar](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >För vanliga Internet leverantörer (ISP) som är en Microsoft peering service-partner krävs registrering av kund-IP-prefix. Men när det gäller Exchange-partner med en Route-Server måste du registrera kund-ASN: er och inte prefix. Samma ASN-nyckel är giltig för kundens prefix registrering.

1. Välj **registrerad ASN: er** under avsnittet Inställningar.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar peering-fönstret med det registrerade ett S NS meny alternativ som kallas.](./media/setup-exchange-registered-asn.png)

1. Välj **Lägg till registrerat ASN** för att skapa en ny kund-ASN under din Exchange-prenumeration.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar rutan registrera ett S N-fönster med text rutorna namn och S N.](./media/setup-exchange-register-new-asn.png)

1. Under registrera ett ASN, väljer du ett namn, fyller i ASN för kunden och klickar på Spara.

1. Under registrerad ASN: er kommer det att finnas en associerad prefixlängd som är kopplad till varje ASN. Som Exchange-Provider måste du ange den här prefixlängden för kunden så att de kan registrera peering-tjänsten under prenumerationen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar fönstret registrerade ett S NS-fönster med prefix nycklar.](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifiera en Exchange-peering
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Ändra en Exchange-peering
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Avetablera en Exchange-peering
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre direkt peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md).
