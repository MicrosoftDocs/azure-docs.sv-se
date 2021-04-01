---
title: Azure AD Connect Synchronization Service Manager åtgärder | Microsoft Docs
description: Förstå fliken åtgärder i Synchronization Service Manager för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357111"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Använda fliken Synkronisera Service Manager åtgärder

![Synkronisera Service Manager](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

På fliken åtgärder visas resultaten från de senaste åtgärderna. Den här fliken är viktig för att förstå och felsöka problem.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Förstå informationen som visas på fliken åtgärder
Den övre halvan visar alla körningar i kronologisk ordning. Som standard behålls information om de senaste sju dagarna i drifts loggen, men den här inställningen kan ändras med [Scheduler](how-to-connect-sync-feature-scheduler.md). Du vill söka efter alla körningar som inte visar statusen lyckades. Du kan ändra sorteringen genom att klicka på rubrikerna.

Kolumnen **status** är den viktigaste informationen och visar det allvarligaste problemet för en körning. Här är en snabb översikt över de vanligaste statusarna i prioritetsordning för att undersöka (där * visar flera möjliga fel strängar).

| Status | Kommentar |
| --- | --- |
| stoppats\* |Det gick inte att slutföra körningen. Om fjärrsystemet till exempel är nere och inte kan kontaktas. |
| stoppat-fel-gräns |Det finns fler än 5 000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutförd- \* -fel |Körningen slutfördes, men det finns fel (mindre än 5 000) som bör undersökas. |
| slutförd- \* -varningar |Körningen slutfördes, men vissa data är inte i förväntat tillstånd. Om du har fel är det här meddelandet vanligt vis bara ett problem. Du bör inte undersöka varningar förrän du har åtgärdat fel. |
| lyckades |Inga problem. |

När du väljer en rad uppdateras de nedersta uppdateringarna för att visa information om den här körningen. Längst till vänster längst ned kan du ha en lista som säger **steg #**. Den här listan visas bara om du har flera domäner i din skog där varje domän representeras av ett steg. Du kan hitta domän namnet under Heading- **partitionen**. Under **synkroniserings statistik** kan du hitta mer information om antalet ändringar som har bearbetats. Du kan klicka på länkarna för att hämta en lista över de ändrade objekten. Om du har objekt med fel visas dessa fel under **synkroniseringsfel**.

Mer information finns i [Felsöka ett objekt som inte synkroniseras](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
