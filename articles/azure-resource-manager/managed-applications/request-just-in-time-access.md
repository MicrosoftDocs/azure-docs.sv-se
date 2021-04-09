---
title: Begära just-in-time-åtkomst
description: Beskriver hur utgivare av Azure Managed Applications begär just-in-Time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: b7c067ca82ea7fbe7eb9c0f3d50b7fe75dfb47df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377371"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Aktivera och begär just-in-Time-åtkomst för Azure Managed Applications

Konsumenter av det hanterade programmet kan vara ovilliga för att ge dig permanent åtkomst till den hanterade resurs gruppen. Som utgivare av ett hanterat program kanske du föredrar att konsumenter vet exakt när du behöver åtkomst till de hanterade resurserna. Azure Managed Applications tillhandahåller en funktion som kallas JIT-åtkomst (just-in-Time) för att ge konsumenterna större kontroll över beviljandet av åtkomst till hanterade resurser. Den här funktionen finns för närvarande som en förhandsversion.

Med JIT-åtkomst kan du begära utökad åtkomst till ett hanterat programs resurser för fel sökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tids period kan du ha större åtkomst.

Arbets flödet för att bevilja åtkomst är:

1. Du lägger till ett hanterat program på Marketplace och anger att JIT-åtkomst är tillgänglig.

1. Under distributionen aktiverar konsumenten JIT-åtkomst för den instansen av det hanterade programmet.

1. Efter distributionen kan konsumenten ändra inställningarna för JIT-åtkomst.

1. Du skickar en begäran om åtkomst när du behöver felsöka eller uppdatera de hanterade resurserna.

1. Konsumenten godkänner din begäran.

Den här artikeln fokuserar på de åtgärder som utgivaren vidtar för att aktivera JIT-åtkomst och skicka begär Anden. Information om hur du godkänner JIT-begäranden finns i [godkänna just-in-Time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Lägg till JIT-åtkomst steg i användar gränssnittet

I CreateUiDefinition.jspå fil inkluderar du ett steg som låter konsumenterna Aktivera JIT-åtkomst. För att stödja JIT-funktioner för ditt erbjudande lägger du till följande innehåll i CreateUiDefinition.jspå filen.

I "steg":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

I "utdata":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT-åtkomst är i för hands version. Schemat för JIT-konfiguration kan ändras i framtida iterationer.

## <a name="enable-jit-access"></a>Aktivera JIT-åtkomst

När du skapar erbjudandet i Partner Center, se till att aktivera JIT-åtkomst.

1. Logga in på den kommersiella Marketplace-portalen i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

1. Om du vill ha hjälp med att skapa ett nytt hanterat program följer du stegen i [skapa ett erbjudande för Azure-program](../../marketplace/create-new-azure-apps-offer.md).

1. På sidan **teknisk konfiguration** markerar du kryss rutan **Aktivera just-in-Time (JIT)-åtkomst** .

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="Aktivera just-in-Time-åtkomst":::

Du har lagt till ett konfigurations steg för JIT i användar gränssnittet och har aktiverat JIT-åtkomst i det kommersiella Marketplace-erbjudandet. När konsumenter distribuerar ditt hanterade program kan de [Aktivera JIT-åtkomst för instansen](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Begär åtkomst

När du behöver åtkomst till konsumentens hanterade resurser skickar du en begäran om en speciell roll, tid och varaktighet. Konsumenten måste sedan godkänna begäran.

Skicka en begäran om JIT-åtkomst:

1. Välj **JIT-åtkomst** för det hanterade programmet som du behöver åtkomst till.

1. Välj **berättigade roller** och välj **Aktivera** i kolumnen åtgärd för den roll som du vill använda.

   ![Aktivera åtkomstbegäran](./media/request-just-in-time-access/send-request.png)

1. I formuläret **Aktivera roll** väljer du start tid och varaktighet för att din roll ska vara aktiv. Välj **Aktivera** för att skicka begäran.

   ![Aktivera åtkomst](./media/request-just-in-time-access/activate-access.png) 

1. Visa meddelanden för att se att den nya JIT-begäran har skickats till konsumenten.

   ![Meddelande](./media/request-just-in-time-access/in-progress.png)

   Nu måste du vänta tills konsumenten har [godkänt din begäran](approve-just-in-time-access.md#approve-requests).

1. Om du vill visa status för alla JIT-begäranden för ett hanterat program väljer du **JIT-åtkomst** och **begär ande historik**.

   ![Visa status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Kända problem

Ägar-ID för det konto som begär JIT-åtkomst måste uttryckligen tas med i definitionen för hanterade program. Kontot får inte ingå i en grupp som har angetts i paketet. Den här begränsningen kommer att åtgärdas i en framtida version.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om att godkänna begär Anden om JIT-åtkomst, se [Godkänn just-in-Time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).
