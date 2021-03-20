---
title: Skapa eller ändra en direkt peering med hjälp av Azure Portal
titleSuffix: Azure
description: Skapa eller ändra en direkt peering med hjälp av Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537216"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Skapa eller ändra en direkt peering med hjälp av Azure Portal

Den här artikeln beskriver hur du skapar en Microsoft Direct-peering för en Internet-leverantör eller en Internet Exchange-Provider med hjälp av Azure Portal. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.

Om du vill kan du slutföra den här guiden med hjälp av Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången direkt peering](walkthrough-direct-all.md) innan du påbörjar konfigurationen.
* Om du redan har direkt peering-anslutningar med Microsoft som inte har konverterats till Azure-resurser kan du läsa mer i [konvertera en äldre direkt peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Skapa och etablera en direkt peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Skapa en direkt peering

Som Internet leverantör eller Internet Exchange-Provider kan du skapa en ny direkt peering-begäran genom att [skapa en peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. På sidan **skapa en peering** , på fliken **grundläggande** , fyller du i rutorna som visas här:


    ![Skärm bild som visar fliken grunderna för att skapa en peering-sida med den skapa nya länken som kallas för.](./media/setup-basics-tab.png)

2. Välj din Azure-prenumeration.

3. För resurs grupp kan du antingen välja en befintlig resurs grupp i list rutan eller skapa en ny grupp genom att välja Skapa ny. Vi ska skapa en ny resurs grupp för det här exemplet.

4. Namnet motsvarar resurs namnet och kan vara allt du väljer.

5. Regionen väljs automatiskt om du väljer en befintlig resurs grupp. Om du väljer att skapa en ny resurs grupp måste du också välja den Azure-region där du vill att resursen ska finnas.

    >[!NOTE]
    > Den region där en resurs grupp finns är oberoende av den plats där du vill skapa peering med Microsoft. Men det är en bra idé att organisera dina peering-resurser i resurs grupper som finns i de närmaste Azure-regionerna. För peering i Ashburn kan du till exempel skapa en resurs grupp i USA, östra eller östra 2; USA.

6. Välj ditt ASN i rutan **PeerASN** .

    >[!IMPORTANT]
    >Du kan bara välja ett ASN med ValidationState som godkänd innan du skickar en peering-begäran. Om du precis skickat in din PeerAsn-begäran, vänta i 12 timmar eller så att ASN-associationen kan godkännas. Om det ASN du väljer är väntande verifiering visas ett fel meddelande. Om du inte ser det ASN du behöver välja, kontrollerar du att du har valt rätt prenumeration. Om så är fallet kontrollerar du om du redan har skapat PeerAsn genom att använda **[associera peer-ASN till Azure-prenumerationen](https://go.microsoft.com/fwlink/?linkid=2129592)**.

7. Välj **Nästa: konfiguration** för att fortsätta.



    ![Skärm bild som visar fliken grunderna för att skapa en peering-sida med alla värden som anges.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Ändra en direkt peering
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Avetablera en direkt peering
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md).
