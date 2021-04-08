---
title: Skapa eller ändra en Exchange-peering med hjälp av Azure Portal
titleSuffix: Azure
description: Skapa eller ändra en Exchange-peering med hjälp av Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700488"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Skapa eller ändra en Exchange-peering med hjälp av Azure Portal

I den här artikeln beskrivs hur du skapar en Microsoft Exchange-peering med hjälp av Azure Portal. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.
* Om du redan har Exchange-peering med Microsoft som inte har konverterats till Azure-resurser kan du läsa mer i [konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Skapa en Exchange-peering


Som en Internet Exchange-Provider kan du skapa en Exchange peering-begäran genom att [skapa en peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. På sidan **skapa en peering** , på fliken **grundläggande** , fyller du i rutorna som visas här:

    > [!div class="mx-imgBorder"] 
    > ![Registrera Peering Service](./media/setup-basics-tab.png)

*    Välj din Azure-prenumeration.

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
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

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
