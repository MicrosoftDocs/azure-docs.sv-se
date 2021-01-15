---
title: Hantera extern åtkomst med Azure Active Directory villkorlig åtkomst
description: Använda Azure Active Directory principer för villkorlig åtkomst för att skydda extern åtkomst till resurser.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222317"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Hantera extern åtkomst med principer för villkorlig åtkomst 

[Villkorlig åtkomst](../conditional-access/overview.md) är verktyget som Azure AD använder för att samla signaler, tillämpa principer och avgöra om en användare ska få åtkomst till resurser. Detaljerad information om hur du skapar och använder principer för villkorlig åtkomst (principer för villkorlig åtkomst) finns i [Planera en distribution av villkorlig åtkomst](../conditional-access/plan-conditional-access.md). 

![Diagram över signaler och beslut om villkorlig åtkomst](media/secure-external-access//7-conditional-access-signals.png)



I den här artikeln beskrivs hur du använder principer för villkorlig åtkomst för externa användare och förutsätter att du inte har åtkomst till funktioner för [rättighets hantering](../governance/entitlement-management-overview.md) . Principer för villkorlig åtkomst kan användas tillsammans med hantering av rättigheter.

Tidigare i den här dokument uppsättningen [skapade du ett säkerhets plan](3-secure-access-plan.md) som beskrivs:

* Program och resurser har samma säkerhets krav och kan grupperas för åtkomst.

* Inloggnings krav för externa användare.

Du kommer att använda den planen för att skapa principer för villkorlig åtkomst för extern åtkomst. 

> [!IMPORTANT]
> Skapa ett par externa användar test konton så att du kan testa de principer som du skapar innan du tillämpar dem på alla externa användare.

## <a name="conditional-access-policies-for-external-access"></a>Principer för villkorlig åtkomst för extern åtkomst

Följande är metod tips som rör hantering av extern åtkomst med principer för villkorlig åtkomst.

* Om du inte kan använda anslutna organisationer i rättighets hantering skapar du en Azure AD-säkerhetsgrupp eller Microsoft 365 grupp för varje partner organisation som du arbetar med. Tilldela alla användare från den partnern till gruppen. Du kan sedan använda dessa grupper i principer för villkorlig åtkomst.

* Skapa så få villkorliga åtkomst principer som möjligt. För program som har samma åtkomst måste du lägga till dem i samma princip.  
‎ 
   > [!NOTE]
   > Principer för villkorlig åtkomst kan tillämpas på högst 250 program. Om fler än 250 appar har samma åtkomst behov skapar du duplicerade principer. Principen A gäller för apparna 1-250, princip B gäller för apparna 251-500 osv.

* Tydliga namn på principer som är speciella för extern åtkomst med en namngivnings konvention. En namngivnings konvention är *ExternalAccess_actiontaken_AppGroup*. Till exempel ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Blockera alla externa användare från resurser

Du kan blockera externa användare från att komma åt vissa resurs uppsättningar med principer för villkorlig åtkomst. När du har bestämt vilken uppsättning av resurser som du vill blockera åtkomst till skapar du en princip.

Så här skapar du en princip som blockerar åtkomst för externa användare till en uppsättning program:

1. Öppna **Azure Portal**, Välj **Azure Active Directory**, Välj **säkerhet** och välj sedan **villkorlig åtkomst**.

2. Välj **ny princip** och ange ett **namn**, till exempel ExternalAccess_Block_FinanceApps

3. Välj **användare och grupp** s. På fliken inkludera väljer du **Välj användare och grupper** och väljer sedan **alla gäster och externa användare**. 

4. Välj **exkludera** och ange din administratörs grupp (er) och eventuella återställnings konton (brytar glas).

5. Välj **molnappar eller åtgärder**, Välj **Välj appar**, Välj alla appar som du vill blockera extern åtkomst till och välj sedan **Välj**.

6. Välj **villkor**, Välj **platser**, under Konfigurera Välj **Ja** och välj **valfri plats**.

7. Under åtkomst kontroller väljer du **bevilja**, ändrar växla till **blockera** och väljer **Välj**.

8. Se till att inställningen Aktivera princip är inställd på **endast rapport** och välj sedan **skapa**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Blockera extern åtkomst till alla utom vissa externa användare

Det kan finnas tillfällen då du vill blockera externa användare utom en speciell grupp. Du kanske till exempel vill blockera alla externa användare förutom de som arbetar för ekonomi teamet från ekonomi programmen. Gör så här:

1. Skapa en säkerhets grupp som ska innehålla externa användare som ska ha åtkomst till ekonomi gruppen.

2. Följ steg 1-3 i blockera extern åtkomst från resurser ovan.

3. I steg 4 lägger du till den säkerhets grupp som du vill undanta från att blockeras från ekonomi-apparna.

4. Utför resten av stegen.

## <a name="implement-conditional-access"></a>Implementera villkorlig åtkomst

Många vanliga principer för villkorlig åtkomst dokumenteras. Se följande som du kan anpassa för externa användare.

* [Kräv Multi-Factor Authentication för alla användare](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Risk- och villkorsbaserad åtkomst – användare](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Kräv Multi-Factor Authentication för åtkomst från ej betrodda nätverk](../conditional-access/untrusted-networks.md) 

* [Kräv användnings villkor](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Fastställ önskad säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md) (du är här)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)
