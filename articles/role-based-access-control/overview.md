---
title: Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?
description: Få en översikt över rollbaserad åtkomst kontroll i Azure (Azure RBAC). Använd rolltilldelningar för att styra åtkomsten till resurser i Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: overview
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, azuread-video-2020
ms.openlocfilehash: 4241e476b2f778ff63057d0491b5dc8666c7520c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650872"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?

Åtkomsthantering för molnresurser är en viktig funktion för alla organisationer som använder molnet. Rollbaserad åtkomst kontroll i Azure (Azure RBAC) hjälper dig att hantera vem som har åtkomst till Azure-resurser, vad de kan göra med dessa resurser och vilka områden de har åtkomst till.

Azure RBAC är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger detaljerad åtkomst hantering av Azure-resurser.

Den här videon ger en snabb översikt över Azure RBAC.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>Vad kan jag göra med Azure RBAC?

Här följer några exempel på vad du kan göra med Azure RBAC:

- Tillåt en användare att hantera virtuella datorer i en prenumeration och en annan användare att hantera virtuella nätverk
- Tillåta en DBA-grupp att hantera SQL-databaser i en prenumeration
- Tillåt en användare att hantera alla resurser i en resursgrupp, till exempel virtuella datorer, webbplatser och undernät
- Tillåt att ett program får åtkomst till alla resurser i en resursgrupp

## <a name="how-azure-rbac-works"></a>Så här fungerar Azure RBAC

Hur du styr åtkomsten till resurser med hjälp av Azure RBAC är att tilldela Azure-roller. Det här är ett viktigt begrepp att förstå – det är hur behörigheter tillämpas. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång.

### <a name="security-principal"></a>Säkerhetsobjekt

En *säkerhetsobjekt* är ett objekt som representerar en användare, en grupp, tjänstens huvudnamn eller hanterad identitet som begär åtkomst till Azure-resurser. Du kan tilldela en roll till alla dessa säkerhets objekt.

![Säkerhetsobjekt för en rolltilldelning](./media/shared/rbac-security-principal.png)

### <a name="role-definition"></a>Rolldefinition

En *rolldefinition* är en samling behörigheter. Den brukar bara kallas en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Roller kan vara på hög nivå, som ägare, eller specifika, som läsare för virtuell dator.

![Rolldefinition för en rolltilldelning](./media/shared/rbac-role-definition.png)

Azure innehåller flera [inbyggda roller](built-in-roles.md) som du kan använda. Till exempel tillåter rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) att en användare skapar och hanterar virtuella datorer. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa egna [Azure-anpassade roller](custom-roles.md).

Den här videon ger en snabb översikt över inbyggda roller och anpassade roller.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

Azure har data åtgärder som gör att du kan bevilja åtkomst till data i ett objekt. Till exempel kan en användare med dataläsningsåtkomst till ett lagringskonto läsa blobar eller meddelanden i det lagringskontot.

Mer information finns i [förstå definitioner av Azure-roller](role-definitions.md).

### <a name="scope"></a>Omfång

*Omfång* är den uppsättning resurser som åtkomsten som gäller för. När du tilldelar en roll kan du ytterligare begränsa de åtgärder som tillåts genom att definiera ett omfång. Detta är användbart om du vill göra någon till en [webbplats deltagare](built-in-roles.md#website-contributor), men bara för en resurs grupp.

I Azure kan du ange ett omfång på fyra nivåer: [hanterings grupp](../governance/management-groups/overview.md), prenumeration, [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups)eller resurs. Omfång är strukturerade i en överordnad/underordnad relation. Du kan tilldela roller på någon av dessa nivåer av omfång.

![Omfång för en rolltilldelning](./media/shared/rbac-scope.png)

Mer information om omfång finns i [förstå omfattning](scope-overview.md).

### <a name="role-assignments"></a>Rolltilldelningar

En *rolltilldelning* är processen att koppla en rolldefinition till en användare, grupp, tjänstens huvudnamn eller hanterad identitet i ett visst omfång för att bevilja åtkomst. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort.

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har marknadsföringsgruppen tilldelats rollen [Deltagare](built-in-roles.md#contributor) för resursgruppen pharma-sales (läkemedelsförsäljning). Det innebär att användare i marknadsföringsgruppen kan skapa och hantera vilka Azure-resurser som helst i resursgruppen pharma-sales. Marknadsföringsanvändare har inte åtkomst till resurser utanför resursgruppen pharma-sales såvida de inte är en del av en annan rolltilldelning.

![Rolltilldelning för att kontrollera åtkomst](./media/overview/rbac-overview.png)

Du kan tilldela roller med hjälp av Azure Portal, Azure CLI, Azure PowerShell, Azure SDK: er eller REST-API: er.

Mer information finns i [steg för att tilldela en Azure-roll](role-assignments-steps.md).

## <a name="multiple-role-assignments"></a>Flera rolltilldelningar

Så vad händer om du har flera överlappande rolltilldelningar? Azure RBAC är en additiv modell, så dina effektiva behörigheter är summan av dina roll tilldelningar. Fundera på följande exempel där en användare beviljas deltagarrollen i prenumerationsomfånget och läsarrollen på en resursgrupp. Summan av deltagar behörigheterna och läsar behörigheterna är i praktiken rollen deltagare för prenumerationen. Därför har läsarrolltilldelningen ingen inverkan i det här fallet.

![Flera rolltilldelningar](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Avvisa tilldelning

Tidigare var Azure RBAC en modell utan neka, men nu stöder Azure RBAC neka-tilldelningar på ett begränsat sätt. På samma sätt som en rolltilldelning kopplar ett *tilldelningsnekande* en uppsättning nekandeåtgärder till en användare, grupp, tjänstens huvudnamn eller hanterad identitet i ett visst omfång för att neka åtkomst. En rolltilldelning definierar en uppsättning åtgärder som är *tillåtna*, medan en nekandetilldelning definierar en uppsättning åtgärder som *inte är tillåtna*. Med andra ord blockerar tilldelningsnekanden användare från att utföra angivna åtgärder, även om en rolltilldelning ger dem åtkomst. Tilldelningsnekanden åsidosätter rolltilldelningar.

Mer information finns i [förstå Azure Deny-tilldelningar](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Hur Azure RBAC bestämmer om en användare har åtkomst till en resurs

Följande är de övergripande steg som Azure RBAC använder för att avgöra om du har åtkomst till en resurs i hanterings planet. Det här är bra att förstå om du vill felsöka ett problem med åtkomst.

1. En användare (eller tjänstens huvudnamn) hämtar en token för Azure Resource Manager.

    Token innehåller användarens gruppmedlemskap (inklusive transitiva gruppmedlemskap).

1. Användaren gör ett REST API-anrop till Azure Resource Manager med den token som är ansluten.

1. Azure Resource Manager hämtar alla rolltilldelningar och avvisar tilldelningar som tillämpas på resursen som åtgärden som utförs på.

1. Azure Resource Manager begränsar rolltilldelningarna som gäller för den här användaren eller gruppen och avgör vilka roller som användaren har för den här resursen.

1. Azure Resource Manager anger om åtgärden i API-anropet ingår i de roller som användaren har för den här resursen.

1. Om användaren inte har någon roll med åtgärden i det begärda omfånget beviljas inte åtkomst. I annat fall kontrollerar Azure Resource Manager om det gäller ett tilldelningsnekande.

1. Om det gäller ett tilldelningsnekanden blockeras åtkomsten. Annars beviljas åtkomst.

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md)
- [Förstå de olika rollerna](rbac-and-directory-admin-roles.md)
- [Ramverk för moln införande: resurs åtkomst hantering i Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
