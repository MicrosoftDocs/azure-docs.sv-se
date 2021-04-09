---
title: Visa och hantera tjänstleverantörer
description: Kunder kan använda sidan tjänst leverantörer i Azure Portal för att visa information om tjänst leverantörer, Service Provider-erbjudanden och delegerade resurser.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555581"
---
# <a name="view-and-manage-service-providers"></a>Visa och hantera tjänstleverantörer

Sidan **tjänst leverantörer** i [Azure Portal](https://portal.azure.com) ger kunderna kontroll och synlighet för sina tjänst leverantörer som använder [Azure Lighthouse](../overview.md). Kunder kan visa information om tjänst leverantörer, delegera resurser, söka efter nya erbjudanden, ta bort åtkomst till tjänst leverantören och mycket annat.

Om du vill visa sidan **tjänst leverantörer** i Azure Portal väljer du **alla tjänster** och söker efter **tjänst leverantörer** och väljer den. Du kan också hitta den här sidan genom att ange "tjänst leverantörer" eller "Azure-Lighthouse" i sökrutan längst upp i Azure Portal.

> [!NOTE]
> För att kunna visa sidan **tjänst leverantörer** måste en användare i kundens klient organisation ha den [inbyggda rollen läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst).
>
> Om du vill lägga till eller uppdatera erbjudanden, delegera resurser och ta bort erbjudanden måste användaren ha en roll med `Microsoft.Authorization/roleAssignments/write` behörigheten, t. ex. [ägare](../../role-based-access-control/built-in-roles.md#owner).

Tänk på att sidan **tjänst leverantörer** endast visar information om de tjänst leverantörer som har åtkomst till kundens prenumerationer eller resurs grupper via Azure Lighthouse. Om en kund arbetar med ytterligare tjänst leverantörer som inte använder Azure-Lighthouse kan du inte se information om dessa tjänst leverantörer här.

## <a name="view-service-provider-details"></a>Visa information om tjänst leverantör

Om du vill visa information om aktuella tjänste leverantörer med Azure Lighthouse för att arbeta med kundens klient väljer du **tjänst leverantörs erbjudanden** till vänster på sidan **tjänst leverantörer** .

För varje erbjudande ser du tjänst leverantörens namn och erbjudandet som är associerat med det. Du kan välja ett erbjudande för att visa en beskrivning och annan information, inklusive roll tilldelningar som tjänste leverantören har beviljats.

I kolumnen **delegeringar** kan du se hur många prenumerationer och/eller resurs grupper som har delegerats till tjänst leverantören för det erbjudandet. Tjänste leverantören kommer att kunna komma åt och hantera dessa prenumerationer och/eller resurs grupper enligt de åtkomst nivåer som anges i erbjudandet.

## <a name="add-or-remove-service-provider-offers"></a>Lägg till eller ta bort tjänst leverantörs erbjudanden

Om du vill lägga till ett nytt Service Provider-erbjudande på sidan **tjänst leverantörs erbjudanden** väljer du **Lägg till erbjudande**. Välj **privata erbjudanden** för att Visa erbjudanden som en tjänst leverantör har publicerat för den här kunden. Du kan sedan välja det erbjudandet från den **privata erbjudanden** -skärmen och sedan välja **Konfigurera + prenumerera**.

Du kan när som helst ta bort ett tjänst leverantörs erbjudande genom att välja pappers korgs ikonen på raden för det erbjudandet. När du har bekräftat borttagningen kommer leverantören inte längre att ha åtkomst till de resurser som tidigare har delegerats för det erbjudandet.

## <a name="delegate-resources"></a>Delegera resurser

Innan en tjänst leverantör kan komma åt och hantera en kunds resurser måste en eller flera specifika prenumerationer och/eller resurs grupper delegeras. Om en kund har accepterat ett erbjudande men ännu inte har delegerat några resurser visas en anteckning överst i avsnittet **tjänst leverantörs erbjudanden** . På så sätt kan kunden veta att de måste vidta åtgärder innan tjänste leverantören kan komma åt någon av kundens resurser.

Så här delegerar du prenumerationer eller resurs grupper:

1. Markera kryss rutan för raden som innehåller tjänst leverantören, erbjudandet och namnet. Välj sedan **delegera resurser** överst på skärmen.
1. I avsnittet **erbjudande information** på sidan **delegera resurser** granskar du information om tjänst leverantören och erbjudandet. Om du vill granska roll tilldelningar för erbjudandet väljer du **Klicka här för att visa information om det valda erbjudandet**.
1. I avsnittet **ombud** väljer du **delegera prenumerationer** eller **delegera resurs grupper**.
1. Välj de prenumerationer och/eller resurs grupper som du vill delegera för erbjudandet och välj sedan **Lägg till**.
1. Markera kryss rutan längst ned på sidan för att bekräfta att du vill ge denna tjänst leverantör åtkomst till de resurser som du har valt och välj sedan **delegera**.

## <a name="update-service-provider-offers"></a>Uppdatering av tjänst leverantören erbjuder

När en kund har lagt till ett erbjudande kan en tjänste leverantör publicera en uppdaterad version av samma erbjudande på Azure Marketplace. De kan till exempel vilja lägga till en ny roll definition. Om en ny version av erbjudandet har publicerats visas en "uppdatering"-ikon på raden för det erbjudandet i **tjänst leverantörs** sidan. Välj den här ikonen om du vill se skillnaderna mellan den aktuella versionen av erbjudandet och den nya.

 ![Uppdatera erbjudande ikon](../media/update-offer.jpg)

Efter att ha granskat ändringarna kan kunden välja att uppdatera till den nya versionen. Auktoriseringarna och andra inställningar som anges i den nya versionen gäller sedan för alla prenumerationer och/eller resurs grupper som har delegerats för det erbjudandet.

## <a name="view-delegations"></a>Visa delegeringar

Delegeringarna representerar roll tilldelningarna som beviljar behörigheter till tjänste leverantören för de resurser som en kund har delegerat. Om du vill visa den här informationen väljer du **delegeringar** på vänster sida av sidan **tjänst leverantörer** .

Med filter högst upp på sidan kan du sortera och gruppera din Delegerings information. Du kan också filtrera efter vissa kunder, erbjudanden eller nyckelord.

> [!NOTE]
> Kunderna ser inte roll tilldelningarna eller användare från tjänst leverantörens klient organisation som har beviljats dessa roller när [roll tilldelningar för det delegerade omfånget visas i Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) eller via API: er.

## <a name="audit-delegations-in-your-environment"></a>Granska delegeringar i din miljö

Kunderna kanske vill få insyn i de prenumerationer och/eller resurs grupper som har delegerats till Azure Lighthouse. Detta är särskilt användbart för de kunder som har ett stort antal prenumerationer eller som har många användare som utför hanterings uppgifter.

Vi tillhandahåller en [Azure policy inbyggd princip definition](../../governance/policy/samples/built-in-policies.md#lighthouse) för att [Granska delegering av omfattningar till en hanterings klient](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Du kan tilldela den här principen till en hanterings grupp som innehåller alla prenumerationer som du vill granska. När du kontrollerar efterlevnaden av den här principen visas alla delegerade prenumerationer och/eller resurs grupper (i hanterings gruppen som principen är tilldelad) i ett tillstånd som inte är kompatibelt. Du kan sedan granska resultaten och bekräfta att det inte finns några oväntade delegeringar.

Med en annan [inbyggd princip definition](../../governance/policy/samples/built-in-policies.md#lighthouse) kan du [begränsa delegeringar till vissa hanterings klienter](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Den här principen kan användas på samma sätt i en hanterings grupp som innehåller alla prenumerationer som du vill begränsa delegeringarna för. När principen har distribuerats kommer alla försök att delegera en prenumeration till en klient utanför de som du anger att nekas.

Mer information om hur du tilldelar en princip och visar resultat för kompatibilitetstillstånd finns i [snabb start: skapa en princip tilldelning](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Lighthouse](../overview.md).
- Lär dig hur du [granskar tjänste leverantörs aktivitet](view-service-provider-activity.md).
- Lär dig hur tjänst leverantörer kan [Visa och hantera kunder](view-manage-customers.md) på sidan **mina kunder** i Azure Portal.
- Lär dig hur [företag som hanterar flera klienter](../concepts/enterprise.md) kan använda Azure-Lighthouse för att konsolidera sina hanterings upplevelser.

