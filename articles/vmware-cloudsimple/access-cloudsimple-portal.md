---
title: Få åtkomst till Azure VMware-lösningen av CloudSimple-Portal
description: Beskriver hur du kommer åt portalen för VMware Solution by CloudSimple via Azure Portal
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895163"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Få åtkomst till VMware-lösningen från CloudSimple-portalen från Azure Portal

Enkel inloggning stöds för åtkomst till CloudSimple-portalen. När du har loggat in på Azure Portal kan du komma åt CloudSimple-portalen utan att logga in igen. Första gången du öppnar CloudSimple-portalen uppmanas du att auktorisera [CloudSimple](#consent-to-cloudsimple-service-authorization-application) .  Auktorisering är en engångs åtgärd.

## <a name="before-you-begin"></a>Innan du börjar

Användare med rollen fördefinierad **ägare** och **deltagare** har åtkomst till CloudSimple-portalen.  Rollerna måste konfigureras i resurs gruppen där CloudSimple-tjänsten har distribuerats.  Rollerna kan också konfigureras för CloudSimple-serviceobjektet.  Mer information om hur du kontrollerar din roll finns i artikeln [Visa roll tilldelningar](../role-based-access-control/check-access.md) . Endast användare med inbyggda **ägare** och **deltagar** roller kan komma åt CloudSimple-portalen.  Rollerna måste konfigureras för prenumerationen.  Mer information om hur du kontrollerar din roll finns i artikeln [Visa roll tilldelningar](../role-based-access-control/check-access.md) .

Om du använder anpassade roller bör rollen ha någon av följande åtgärder under ```Actions``` .  Mer information om anpassade roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).  Om någon av åtgärderna är en del av ```NotActions``` , kan användaren inte komma åt CloudSimple-portalen.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple-tjänster**.

3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.

4. På sidan **Översikt** klickar du på **gå till CloudSimple-portalen**.  Om du har åtkomst till CloudSimple-portalen från Azure Portal för första gången uppmanas du att auktorisera [CloudSimple](#consent-to-cloudsimple-service-authorization-application) . 

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Om du väljer en privat moln åtgärd (t. ex. genom att skapa eller expandera ett privat moln) direkt från Azure Portal, öppnas CloudSimple-portalen på den angivna sidan.

I CloudSimple-portalen väljer du **Start** på sido menyn för att Visa sammanfattnings information om dina privata moln. Resurserna och kapaciteten för dina privata moln visas, tillsammans med aviseringar och uppgifter som kräver åtgärder. För vanliga uppgifter klickar du på de namngivna ikonerna överst på sidan.

![Start sida](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Medgivande till CloudSimple-program för programauktorisering

Att starta CloudSimple-portalen från Azure Portal för första gången kräver ditt medgivande för CloudSimple-tjänstens Authorization-program.  Välj **acceptera** för att bevilja de begärda behörigheterna och få åtkomst till CloudSimple-portalen.

![Medgivande till CloudSimple-auktorisering av tjänst – administratörer](media/cloudsimple-azure-consent.png)

Om du har global administratörs behörighet kan du godkänna din organisation.  Välj **Samtyck åt din organisation**.

![Medgivande till CloudSimple-tjänstens auktorisering – global administratör](media/cloudsimple-azure-consent-global-admin.png)

Om dina behörigheter inte tillåter åtkomst till CloudSimple-portalen kontaktar du den globala administratören för din klient organisation för att bevilja nödvändiga behörigheter.  En global administratör kan samtycka åt din organisation.

![Medgivande till CloudSimple-tjänstens auktorisering – kräver administratörer](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett privat moln](./create-private-cloud.md)
* Lär dig hur du [konfigurerar en privat moln miljö](quickstart-create-private-cloud.md)
