---
title: Hantera prenumerationer
description: Prenumerationer består av hanterade allokerade enheter och kan registreras eller offboarded efter behov.
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387352"
---
# <a name="manage-a-subscription"></a>Hantera en prenumeration

Prenumerationer hanteras per månad. När du registrerar en prenumeration debiteras du för den prenumerationen fram till slutet av månaden. På samma sätt som när du avpublicera en prenumeration debiteras du för resten av den månad då du offboarded den prenumerationen.

## <a name="onboard-a-subscription"></a>Publicera en prenumeration

För att komma igång med Azure Defender för IoT måste du ha en Microsoft Azure-prenumeration. Om du inte har någon prenumeration kan du registrera dig för ett kostnadsfritt konto. Om du redan har åtkomst till en Azure-prenumeration, men den inte finns med i listan, kontrollerar du konto informationen och bekräftar dina behörigheter med prenumerations ägaren.

För att publicera en prenumeration:

1. Gå till sidan med priser för Azure Portal. 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Gå till sidan med priser för Azure Portal.":::

1. Välj **onboard-prenumeration**.

1. I fönstret **onboard-prenumeration** väljer du din prenumeration och antalet allokerade enheter på de nedrullningsbara menyerna. 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="Välj din prenumeration och antalet allokerade enheter.":::

1. Välj **publicera**.

## <a name="offboard-a-subscription"></a>Avpublicera en prenumeration

Prenumerationer hanteras per månad. När du avpublicera en prenumeration debiteras du för den prenumerationen fram till slutet av månaden.

Avinstallera alla sensorer som är associerade med prenumerationen innan du offboarding prenumerationen. Mer information om hur du tar bort en sensor finns i [ta bort en sensor](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor). 

Så här avpublicera du en prenumeration:

1. Gå till sidan med **priser** .
1. Välj prenumerationen och välj sedan ikonen **ta bort** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. I popup-fönstret för bekräftelse markerar du kryss rutan för att bekräfta att du har tagit bort alla sensorer som är associerade med prenumerationen.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Markera kryss rutan och välj avpublicera för att avpublicera sensorn.":::

1. Välj knappen **avpublicera** . 

## <a name="next-steps"></a>Nästa steg

[Aktivera och konfigurera den lokala hanteringskonsolen](how-to-activate-and-set-up-your-on-premises-management-console.md)
