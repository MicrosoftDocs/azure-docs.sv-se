---
title: Associera peer-ASN till en Azure-prenumeration med hjälp av portalen
titleSuffix: Azure
description: Associera peer-ASN till en Azure-prenumeration med hjälp av portalen
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91540276"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associera peer-ASN till en Azure-prenumeration med hjälp av portalen

Innan du skickar en peering-begäran bör du först associera ditt ASN med en Azure-prenumeration med hjälp av stegen nedan.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Skapa PeerAsn för att koppla ditt ASN med Azure-prenumeration

### <a name="sign-in-to-the-portal"></a>Logga in på portalen
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registrera dig för peering Resource Provider
Registrera dig för peering Resource Provider i prenumerationen genom att följa stegen nedan. Om du inte kör det går det inte att komma åt de Azure-resurser som krävs för att konfigurera peering.

1. Klicka på **prenumerationer** i det övre vänstra hörnet i portalen. Om du inte ser det klickar du på **fler tjänster** och söker efter den.

    > [!div class="mx-imgBorder"]
    > ![Öppna prenumerationer](./media/rp-subscriptions-open.png)

1. Klicka på den prenumeration som du vill använda för peering.

    > [!div class="mx-imgBorder"]
    > ![Starta prenumeration](./media/rp-subscriptions-launch.png)

1. När prenumerationen har öppnats klickar du på **resurs leverantörer** till vänster. Sök sedan efter *peering* i sökfönstret i den högra rutan eller Använd rullnings listen för att hitta **Microsoft. peering** och titta på **status**. Om statusen är **_registrerad_*_ hoppar du över stegen nedan och fortsätter till avsnittet _* skapa PeerAsn**. Om statusen är **_NotRegistered_*_ väljer du _* Microsoft. peering** och klickar på **Registrera**.

    > [!div class="mx-imgBorder"]
    > ![Registrering start](./media/rp-register-start.png)

1. Observera att statusen ändras till ***registrering***.

    > [!div class="mx-imgBorder"]
    > ![Registrering pågår](./media/rp-register-progress.png)

1. Vänta en stund och slutför registreringen. Klicka sedan på **Uppdatera** och kontrol lera att statusen är **_registrerad_**.

    > [!div class="mx-imgBorder"]
    > ![Registreringen är klar](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Skapa PeerAsn
Som Internet leverantör eller Internet Exchange-Provider kan du skapa en ny PeerAsn-resurs för att associera ett autonomt system nummer (ASN) med Azure-prenumeration på [sidan koppla en peer-ASN](https://go.microsoft.com/fwlink/?linkid=2129592) . Du kan koppla flera ASN: er till en prenumeration genom att skapa en **PeerAsn** för varje ASN som du behöver associera.

1. På fliken **associera en peer-ASN** , under fliken **grundläggande** , fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![Fliken grundläggande för PeerAsn](./media/peerasn-basics-tab.png)

    * **Namnet** motsvarar resurs namnet och kan vara allt du väljer.  
    * Välj den **prenumeration** som du behöver för att associera ASN med.
    * **Peer-namnet** motsvarar företagets namn och måste vara så nära som möjligt för din PeeringDB-profil. Observera att värdet endast stöder tecknen a – z, A-Z och blank steg
    * Ange ditt ASN i fältet **peer-ASN** .
    * Klicka på **Skapa ny** och ange **e-postadress** och **telefonnummer** för din nätverks åtgärds Center (NOC)
1. Klicka sedan på **Granska + skapa** och Observera att portalen kör grundläggande verifiering av den information som du har angett. Detta visas i ett menyfliksområde överst, som *körning av slutgiltig verifiering...*.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar fliken koppla en peer A S N-grunder.](./media/peerasn-review-tab-validation.png)

1. När meddelandet i menyfliksområdet är klar med *verifieringen* verifierar du din information och skickar in begäran genom att klicka på **skapa**. Om verifieringen inte godkänns klickar du på **föregående** och upprepar stegen ovan för att ändra din begäran och se till att de värden du anger inte innehåller några fel.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar fliken koppla en peer A S N-grunder med verifieringen godkänd.](./media/peerasn-review-tab.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas som nedan.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn lyckades](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Visa status för en PeerAsn
När PeerAsn-resursen har distribuerats måste du vänta på att Microsoft ska godkänna associerings förfrågan. Det kan ta upp till 12 timmar för godkännande. När den har godkänts får du ett meddelande till den e-postadress som anges i avsnittet ovan.

> [!IMPORTANT]
> Vänta tills ValidationState för att aktivera "godkänd" innan du skickar en peering-begäran. Det kan ta upp till 12 timmar innan detta godkännande.

## <a name="modify-peerasn"></a>Ändra PeerAsn
Det finns för närvarande inte stöd för att ändra PeerAsn. Kontakta [Microsoft-peering](mailto:peering@microsoft.com)om du behöver ändra.

## <a name="delete-peerasn"></a>Ta bort PeerAsn
Det finns för närvarande inte stöd för att ta bort en PeerAsn. Om du behöver ta bort PeerAsn kontaktar du [Microsoft-peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Direct-peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)
* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md)
