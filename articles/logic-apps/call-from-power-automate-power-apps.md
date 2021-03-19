---
title: Anropa logikappar från Power Automate och Power Apps
description: Anropa Logi Kap par från Microsoft Power automatiserings flöden genom att exportera Logic Apps som kopplingar.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91762508"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Anropa logikappar från Power Automate och Power Apps

Om du vill anropa dina Logi Kap par från Microsoft Power automatisering och Microsoft Power Apps kan du exportera dina Logi Kap par som kopplingar. När du exponerar en Logic-app som en anpassad anslutning i en Energis par-eller Power Apps-miljö kan du anropa din Logic app från flöden där.

Om du vill migrera flödet från Energis par eller ström till Logic Apps i stället, se [Exportera flöden från Energis par automatisering och distribuera till Azure Logic Apps](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> Alla Power inautomatiserade anslutningar är inte tillgängliga i Azure Logic Apps. Du kan bara migrera energi automatiserade flöden som har motsvarande kopplingar i Azure Logic Apps. Till exempel är knapp utlösaren, godkännande Connector och meddelande Connector särskilt för automatisk energi förbrukning. För närvarande stöds inte OpenAPI-baserade flöden i energi spar läge för export och distribution som Logic app-mallar.
>
> * Om du vill ta reda på vilka PowerPivot-kopplingar som inte har Logic Apps motsvarigheter, se Power automater- [kopplingar](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Om du vill ta reda på vilka Logic Apps kopplingar inte har Energis par motsvarigheter, se [Logic Apps kopplingar](/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En licens för automatiserad energi eller Power Apps.

* En Logi Kap par med en begär ande utlösare att exportera.

* Ett flöde i Energis par-eller PowerPivot-appar som du vill anropa din Logic app från.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Exportera din Logic app som en anpassad anslutning

Innan du kan anropa din Logi Kap par från Power-eller Power Apps-appar måste du först exportera din Logic app som en anpassad anslutning.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Skriv i rutan Azure Portal Sök `Logic Apps` . I resultaten under **tjänster** väljer du **Logic Apps**.

1. Välj den Logic-app som du vill exportera.

1. Från din Logic Apps-meny väljer du **Exportera**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Skärm bild av sidan för logi Kap par i Azure Portal, som visar menyn med knappen Exportera markerad.":::

1. I rutan **Exportera** anger du ett namn för det anpassade anslutnings programmet till din Logic app i **namn**. I listan **miljö** väljer du den Energis par-eller Power Apps-miljö som du vill anropa din Logic app från. När du är klar väljer du **Ok**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Skärm bild av export fönstret för Logic app, som visar obligatoriska fält för namn och miljö för anpassade anslutningar.":::

1. Kontrol lera att din Logic app har exporter ATS genom att kontrol lera aviserings fönstret.

### <a name="exporting-errors"></a>Exportera fel

Här är fel som kan inträffa när du exporterar din Logic app som en anpassad koppling och deras föreslagna lösningar:

* **Det gick inte att hämta miljöer. Kontrol lera att ditt konto har kon figurer ATS för automatisk energi förbrukning och försök sedan igen.**: kontrol lera att ditt Azure-konto har ett energi schema.

* **Det går inte att exportera den aktuella Logic-appen. Om du vill exportera väljer du en logisk app som har en begär ande utlösare.**: kontrol lera att din Logic app börjar med en [begär ande utlösare](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Ansluta till din Logic-app från Power automate

Så här ansluter du till den Logic-app som du har exporterat med ditt energi automatiserade flöde:

1. Logga in för att [Automatisera energi](https://flow.microsoft.com).

1. Välj **mina flöden** på menyn Start sida för **Automatisk** start.

1. På sidan **flöden** väljer du det flöde som du vill ansluta till din Logic app.

1. Från menyn på din flödes sida väljer du **Redigera**.

1. I flödes redigeraren väljer du **&#43; nytt steg**.

1. Under **Välj en åtgärd** i sökrutan anger du namnet på din Logic app Connector. Om du vill visa enbart de anpassade anslutningarna i din miljö filtrerar du resultaten genom att välja fliken **anpassad** .

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Skärm bild av Powerally Flow Editor som visar ett nytt steg som läggs till för det anpassade anslutnings programmet och tillgängliga åtgärder.":::

1. Välj den åtgärd som du vill vidta med din Logic app-anslutning. 

1. Ange den information som åtgärden skickar till Logic app Connector.

1. Spara ändringarna genom att välja **Spara** på menyn Power autoredigerare.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp den Logic-app som du exporterade i Logic Apps tjänsten.

1. Bekräfta att din Logic app fungerar som du förväntar dig i ditt energi automatiserade flöde.

## <a name="delete-logic-app-connector-from-power-automate"></a>Ta bort Logic app Connector från energi automatisering

1. Logga in för att [Automatisera energi](https://flow.microsoft.com).

1. På Start sidan för **Automatisk** Start, väljer du  &gt; **anpassade anslutningar** för data på menyn.

1. Leta upp ditt anpassade anslutnings program i listan och välj ellipserna (**...**) knappen &gt; **ta bort**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Skärm bild av sidan anpassade anslutnings anslutningar med logi Kap par knappar för hantering av anpassade anslutningar.":::

1. Bekräfta borttagningen genom att välja **OK**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Ansluta till din Logic app från Power Apps

Så här ansluter du till den Logic-app som du har exporterat med Power Apps-flödet:

1. Logga in på [Power Apps](https://powerapps.microsoft.com/).

1. På Start sidan för **Power Apps** väljer du **flöden** på menyn.

1. På sidan **flöden** väljer du det flöde som du vill ansluta till din Logic app.

1. På ditt flödes sida väljer du **Redigera** på menyn för flödet.

1. I flödes redigeraren väljer du knappen **&#43; nytt steg** .

1. Under **Välj en åtgärd** i det nya steget anger du namnet på din Logic app Connector i sökrutan. Du kan också filtrera resultaten på fliken **anpassad** om du bara vill se anpassade anslutningar i din miljö.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Skärm bild av flödes redigeraren för Power Apps, med ett nytt steg som läggs till för den anpassade anslutningen och tillgängliga åtgärder.":::

1. Välj den åtgärd som du vill vidta med anslutningen. 

1. Konfigurera vilken information din åtgärd skickar till Logic app Connector.

1. I menyn Power Apps-redigeraren väljer du **Spara** för att spara ändringarna. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Leta upp den Logic-app som du exporterade i Logic Apps tjänsten.

1. Bekräfta att din Logic app fungerar som avsett med dina Power Apps-flöden.

## <a name="delete-logic-app-connector-from-power-apps"></a>Ta bort Logic app Connector från Power Apps

1. Logga in på [Power Apps](https://powerapps.microsoft.com).

1. På Start sidan för **Power Apps** väljer du **data** &gt; **anpassade anslutningar** på menyn.

1. Leta upp ditt anpassade anslutnings program i listan och välj ellipserna (**...**) knappen &gt; **ta bort**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Skärm bild av sidan för Power Apps-anpassade anslutningar, som visar Logi Kap par knappar för hantering av anpassade anslutningar.":::

1. Bekräfta borttagningen genom att välja **OK**.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [anslutningar för Azure Logic Apps](../connectors/apis-list.md)
* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
