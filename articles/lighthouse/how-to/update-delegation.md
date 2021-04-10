---
title: Uppdatera en delegering
description: Lär dig hur du uppdaterar en delegering för en kund som tidigare har publicerats till Azure-Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555762"
---
# <a name="update-a-delegation"></a>Uppdatera en delegering

När du har publicerat en prenumeration (eller resurs grupp) till Azure-Lighthouse kan du behöva göra ändringar. Till exempel kanske kunden vill utföra ytterligare hanterings uppgifter som kräver en annan inbyggd Azure-roll, eller så kan du behöva ändra den klient som en kund prenumeration är delegerad till.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet kan [företag som hanterar flera klienter](../concepts/enterprise.md) använda samma process för att konfigurera Azure-Lighthouse och konsolidera sina hanterings upplevelser.

Om du har publicerat [din kund via Azure Resource Manager mallar (arm-mallar)](onboard-customer.md)måste en ny distribution utföras för kunden. Beroende på vad du ändrar, kanske du vill uppdatera det ursprungliga erbjudandet eller ta bort det ursprungliga erbjudandet och skapa ett nytt.

- **Om du ändrar endast auktoriseringar**: du kan uppdatera din delegering genom att bara ändra avsnittet **AUKTORISERINGar** i arm-mallen.
- **Om du ändrar hanterings klient organisationen**: du måste skapa en ny arm-mall med en annan **mspOfferName** än ditt tidigare erbjudande.

## <a name="update-your-arm-template"></a>Uppdatera din ARM-mall

Om du vill uppdatera din delegering måste du distribuera en ARM-mall som innehåller de ändringar som du vill göra.

Om du bara uppdaterar auktoriseringar (till exempel att lägga till en ny användar grupp med en roll som du inte hade tidigare inkluderat eller ändra rollen för en befintlig användare) kan du använda samma **mspOfferName** som i arm- [mallen](onboard-customer.md#create-an-azure-resource-manager-template) som du använde för den tidigare delegeringen. Du kan använda din tidigare mall som utgångs punkt. Gör sedan de ändringar du behöver, till exempel att ersätta en inbyggd Azure-roll med en annan eller lägga till en helt ny auktorisering till mallen.

Om du ändrar **mspOfferName** betraktas detta som ett nytt, separat erbjudande. Detta krävs om du ändrar hanterings klienten.

Det är inte nödvändigt att ändra **mspOfferName** om den hanterande klienten är oförändrad. I de flesta fall rekommenderar vi att du bara har en **mspOfferName** som används av samma kund och hanterar klient organisationen. Om du väljer att ändra det ändå ser du till att kundens tidigare delegering tas bort innan du distribuerar den nya.

## <a name="remove-the-previous-delegation"></a>Ta bort den tidigare delegeringen

Innan du utför en ny distribution kanske du vill [ta bort åtkomsten till den tidigare delegeringen](remove-delegation.md). Detta säkerställer att alla tidigare behörigheter tas bort, så att du kan börja rensa med exakt de användare/grupper och roller som ska tillämpas framåt.

> [!IMPORTANT]
> Om du använder en ny **mspOfferName** och behåller samma **principalId** -värden måste du ta bort åtkomsten till den tidigare delegeringen innan du distribuerar det nya erbjudandet. Om du inte tar bort erbjudandet först kan användare som tidigare har beviljats behörighet förlora åtkomsten helt på grund av motstridiga tilldelningar.

Om du ändrar hanterings klienten kan du lämna det tidigare erbjudandet på plats om du vill att båda klienterna ska fortsätta att ha åtkomst. Om du bara vill att den nya hanterande klienten ska ha åtkomst måste det tidigare erbjudandet tas bort. Detta kan göras antingen före eller efter det nya erbjudandet.

Om du uppdaterar erbjudandet för att endast justera auktoriseringar och behåller samma **mspOfferName** behöver du inte ta bort den tidigare delegeringen. Den nya distributionen ersätter den tidigare delegeringen och bara auktoriseringarna i den senaste mallen kommer att gälla.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagram som visar när du ska ändra mspOfferName och ta bort en tidigare delegering.":::

Att ta bort åtkomst till delegeringen kan göras av alla användare i den hanterande klient organisationen som har beviljats [borttagnings rollen för hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) för den ursprungliga delegeringen. Om ingen användare i hanterings klienten har den här rollen kan du be kunden att [ta bort åtkomst till erbjudandet i Azure Portal](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Om du har tagit bort den tidigare delegeringen enligt stegen ovan och fortfarande inte kan distribuera den nya ARM-mallen kan du behöva [ta bort registrerings definitionen helt och hållet](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Detta kan göras av alla användare med en roll som har `Microsoft.Authorization/roleAssignments/write` behörigheten, till exempel [ägare](../../role-based-access-control/built-in-roles.md#owner), i kund innehavaren.  

## <a name="deploy-the-arm-template"></a>Distribuera ARM-mallen

Kunden kan [distribuera den uppdaterade mallen](onboard-customer.md#deploy-the-azure-resource-manager-templates) på samma sätt som tidigare: i Azure Portal, med hjälp av PowerShell eller med hjälp av Azure CLI.

När distributionen har slutförts [kontrollerar du att den lyckades](onboard-customer.md#confirm-successful-onboarding). De uppdaterade auktoriseringarna kommer sedan att tillämpas för den prenumeration eller resurs grupp (er) som kunden har delegerat.

## <a name="updating-managed-service-offers"></a>Uppdaterar hanterade tjänst erbjudanden

Om du har publicerat din kund via ett hanterat tjänst erbjudande publicerat på Azure Marketplace och du vill uppdatera auktoriseringar kan du uppdatera delegeringen genom att [publicera en ny version av ditt erbjudande](../../marketplace/partner-center-portal/update-existing-offer.md) med de [auktoriseringar](../../marketplace/plan-managed-service-offer.md) som du vill använda i planen för kunden. Kunden kommer sedan att kunna uppdatera till den senaste versionen i Azure Portal.

Om du vill ändra hanterings klienten måste du [skapa och publicera ett nytt erbjudande för en hanterad tjänst](../../marketplace/plan-managed-service-offer.md) för kunden att godkänna.

> [!TIP]
> Som tidigare nämnts rekommenderar vi att du inte använder flera olika erbjudanden mellan samma kund och hanterar klient organisationen. Om du publicerar ett nytt erbjudande för samma kund som använder samma hanterings klient måste du se till att det tidigare erbjudandet tas bort innan kunden accepterar det nya erbjudandet.

## <a name="next-steps"></a>Nästa steg

- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
- Lär dig hur du [tar bort åtkomst till en delegering](remove-delegation.md) som tidigare har publicerats.
