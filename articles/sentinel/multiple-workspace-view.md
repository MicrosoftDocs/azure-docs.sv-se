---
title: Arbeta med Azure Sentinel-incidenter i många arbets ytor på en gång | Microsoft Docs
description: Visa incidenter i flera arbets ytor samtidigt i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83124199"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Arbeta med incidenter i flera arbets ytor samtidigt 

 Microsoft rekommenderar att du använder en miljö med en enda arbets yta för att dra full nytta av Azure Sentinel-funktionerna. Det finns dock vissa användnings fall som kräver flera arbets ytor, i vissa fall – till exempel för en [hanterad säkerhets tjänst leverantör (MSsP)](./multiple-tenants-service-providers.md) och dess kunder – över flera klienter. Med **vyn flera arbets ytor** kan du se och arbeta med säkerhets incidenter över flera arbets ytor samtidigt, även mellan klienter, så att du får fullständig insyn och kontroll över din organisations säkerhets svars tid.

## <a name="entering-multiple-workspace-view"></a>Ange en vy för flera arbets ytor

När du öppnar Azure Sentinel visas en lista över alla arbets ytor som du har åtkomst behörighet till över alla valda klienter och prenumerationer. Till vänster om varje arbets ytans namn är en kryss ruta. Genom att klicka på namnet på en enskild arbets yta kommer du till arbets ytan. Välj flera arbets ytor genom att klicka på alla motsvarande kryss rutor och klicka sedan på knappen för **vyn flera arbets ytor** överst på sidan.

> [!IMPORTANT]
> Vyn för flera arbets ytor stöder för närvarande högst 10 arbets ytor som visas samtidigt. 
> 
> Om du markerar fler än 10 arbets ytor visas ett varnings meddelande.

Observera att du kan se katalogen, prenumerationen, platsen och resurs gruppen som är kopplad till varje arbets yta i listan över arbets ytor. Katalogen motsvarar klient organisationen.

   ![Välj flera arbets ytor](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Arbeta med incidenter

I **vyn flera arbets ytor** är endast fönstret **incidenter** tillgängligt för tillfället. Den ser ut och fungerar på de flesta sätt som skärmen vanliga **incidenter** . Det finns några viktiga skillnader, men:

   ![Visa incidenter i flera arbets ytor](./media/multiple-workspace-view/incidents.png)

- Räknarna överst i de *Öppna incidenterna*, *nya incidenter*, *pågår* osv. – visar numren för alla valda arbets ytor gemensamt.

- Du ser incidenter från alla valda arbets ytor och kataloger (klienter) i en enda enhetlig lista. Du kan filtrera listan efter arbets yta och katalog, förutom filter från skärmen vanliga **incidenter** .

- Du måste ha läs-och Skriv behörighet för alla arbets ytor som du har valt incidenter från. Om du bara har Läs behörighet på vissa arbets ytor visas varnings meddelanden om du väljer incidenter på dessa arbets ytor. Du kan inte ändra dessa incidenter eller andra som du har valt tillsammans med dem (även om du har behörighet för de andra).

- Om du väljer en enda incident och klickar på **Visa fullständig information** eller **Undersök** kommer du från sedan att finnas i data kontexten för den incidentens arbets yta och inga andra.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du visar och arbetar med incidenter i flera Azure Sentinel-arbetsytor samtidigt. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

