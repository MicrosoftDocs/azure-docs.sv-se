---
title: Konvertera en äldre direkt peering till en Azure-resurs med hjälp av Azure Portal
titleSuffix: Azure
description: Konvertera en äldre direkt peering till en Azure-resurs med hjälp av Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 496774f034c861131624b552b1acefca9f399ea8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84700271"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konvertera en äldre direkt peering till en Azure-resurs med hjälp av Azure Portal

Den här artikeln beskriver hur du konverterar en befintlig äldre direkt peering till en Azure-resurs med hjälp av Azure Portal.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången direkt peering](walkthrough-direct-all.md) innan du påbörjar konfigurationen.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Konvertera en äldre direkt peering till en Azure-resurs

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Logga in på portalen och välj din prenumeration
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Konvertera en äldre direkt peering

Som Internet leverantör kan du konvertera äldre direkt peering-anslutningar med hjälp av att [skapa en peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

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

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Starta resursen och konfigurera grundläggande inställningar
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurera anslutningar och skicka
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verifiera direkt peering
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)
