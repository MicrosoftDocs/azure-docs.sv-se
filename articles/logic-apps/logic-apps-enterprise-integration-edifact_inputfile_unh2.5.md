---
title: UNH 2,5-segment i EDIFACT-meddelanden
description: Lösa EDIFACT-meddelanden med UNH 2.5-segment i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c6d95cb938a341b94a32989e82c42d37956c0a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179852"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Hantera EDIFACT-dokument med UNH 2.5-segment i Azure Logic Apps

Om det finns ett UNH 2.5-segment i ett EDIFACT-dokument används segmentet för schema ökning. I det här exemplet är EDIFACT fältet UNH `EAN008` :

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Följ de här stegen som beskrivs nedan för att hantera det här meddelandet:

1. Uppdatera schemat.

1. Kontrol lera avtals inställningarna.

## <a name="update-the-schema"></a>Uppdatera schemat

För att kunna bearbeta meddelandet måste du distribuera ett schema som har rotnoden UNH 2,5. Till exempel är schema rot namnet för fältet Sample UNH `EFACT_D03B_ORDERS_EAN008` . För varje `D03B_ORDERS` som har ett annat UNH 2.5-segment måste du distribuera ett individuellt schema.

## <a name="add-schema-to-edifact-agreement"></a>Lägg till schema i EDIFACT-avtalet

### <a name="edifact-decode"></a>EDIFACT-avkodning

Om du vill avkoda det inkommande meddelandet ställer du in schemat i EDIFACT-avtalets mottagnings inställningar:

1. Öppna integrations kontot i [Azure Portal](https://portal.azure.com).

1. Lägg till schemat i integrations kontot.

1. Konfigurera schemat i EDIFACT-avtalets mottagnings inställningar.

1. Välj EDIFACT-avtalet och välj **Redigera som JSON**. Lägg till UNH 2.5-värdet i mottagar avtals `schemaReferences` avsnittet:

   ![Lägg till UNH 2.5 för att ta emot avtal](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT-kodning

Om du vill koda det inkommande meddelandet konfigurerar du schemat i EDIFACT-avtalet skicka inställningar

1. Öppna integrations kontot i [Azure Portal](https://portal.azure.com).

1. Lägg till schemat i integrations kontot.

1. Konfigurera schemat i EDIFACT-avtalets sändnings inställningar.

1. Välj EDIFACT-avtalet och välj **Redigera som JSON**. Lägg till UNH 2.5-värdet i avsnittet om sändnings avtal `schemaReferences` :

   ![Lägg till UNH 2.5 för att skicka avtal](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [integrerings konto avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
