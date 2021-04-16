---
title: Vanliga frågor och svar om hanterade identiteter för Azure-resurser – Azure AD"
description: Vanliga frågor och svar om hanterade identiteter
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534127"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Vanliga frågor och svar om hanterade identiteter för Azure-resurser – Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="administration"></a>Administration

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hur hittar du resurser som har en hanterad identitet?

Du hittar listan över resurser som har en system tilldelad hanterad identitet med hjälp av följande Azure CLI-kommando: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Vilka Azure RBAC-behörigheter krävs för hanterade identiteter på en resurs? 

- System tilldelad hanterad identitet: Du behöver skrivbehörighet för resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Den här åtgärden ingår i resursspecifika inbyggda roller som [Virtuell datordeltagare.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- Användartilldelning av hanterad identitet: Du behöver skrivbehörighet för resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Förutom rolltilldelning för [hanterad identitetsoperatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) över den hanterade identiteten.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hur gör jag för att förhindra att användar tilldelade hanterade identiteter skapas?

Du kan hålla dina användare från att skapa användar tilldelade hanterade identiteter med [hjälp av Azure Policy](../../governance/policy/overview.md)

1. Gå till [Azure Portal](https://portal.azure.com) och gå till **Princip.**
2. Välj **definitioner**
3. Välj **+ Principdefinition** och ange nödvändig information.
4. I avsnittet principregel klistrar du in
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

När du har skapat principen tilldelar du den till den resursgrupp som du vill använda.

1. Gå till resursgrupper.
2. Hitta den resursgrupp som du använder för testning.
3. Välj **Principer** på den vänstra menyn.
4. Välj **Tilldela princip**
5. I **avsnittet Grundläggande anger** du:
    1. **Omfång** Resursgruppen som vi använder för testning
    1. **Principdefinition:** Den princip som vi skapade tidigare.
6. Lämna standardinställningarna för alla andra inställningar och välj **Granska + skapa**

I det här läget misslyckas alla försök att skapa en användar tilldelad hanterad identitet i resursgruppen.

  ![Principöverträdelse](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Begrepp

### <a name="do-managed-identities-have-a-backing-app-object"></a>Har hanterade identiteter ett appobjekt som backar upp?

Nej. Hanterade identiteter Azure AD App registreringar är inte samma sak i katalogen.

Appregistreringar två komponenter: ett programobjekt + ett objekt för tjänstens huvudnamn.
Hanterade identiteter för Azure-resurser har bara en av dessa komponenter: ett objekt för tjänstens huvudnamn.

Hanterade identiteter har inget programobjekt i katalogen, vilket är det som ofta används för att bevilja appbehörigheter för MS Graph. I stället måste MS Graph-behörigheter för hanterade identiteter beviljas direkt till tjänstens huvudnamn.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Vilka autentiseringsuppgifter är associerade med en hanterad identitet? Hur länge är det giltigt och hur ofta roteras det?

> [!NOTE]
> Hur hanterade identiteter autentiseras är en intern implementeringsdetaljer som kan ändras utan föregående meddelande.

Hanterade identiteter använder certifikatbaserad autentisering. Varje hanterad identitets autentiseringsuppgifter har en giltighetstid på 90 dagar och den rullas efter 45 dagar.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Vilken identitet kommer IMDS som standard att vara om inte identiteten anges i begäran?

- Om system tilldelad hanterad identitet är aktiverat och ingen identitet anges i begäran, får IMDS som standard den system tilldelade hanterade identiteten.
- Om system tilldelad hanterad identitet inte är aktiverat och det bara finns en användar tilldelad hanterad identitet, får IMDS som standard den hanterade identiteten som tilldelats av en enskild användare.
- Om system tilldelad hanterad identitet inte är aktiverat och det finns flera användar tilldelade hanterade identiteter måste du ange en hanterad identitet i begäran.

## <a name="limitations"></a>Begränsningar

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Kan samma hanterade identitet användas i flera regioner?

Kort sagt, ja, du kan använda användar tilldelade hanterade identiteter i mer än en Azure-region. Det längre svaret är att även om användar tilldelade [](../develop/app-objects-and-service-principals.md#service-principal-object) hanterade identiteter skapas som regionala resurser är det associerade tjänsthuvudnamn (SP) som skapas i Azure AD tillgängligt globalt. Tjänstens huvudnamn kan användas från valfri Azure-region och dess tillgänglighet beror på tillgängligheten för Azure AD. Om du till exempel har skapat en användar tilldelad hanterad identitet i [](../../azure-resource-manager/management/control-plane-and-data-plane.md) South-Central-regionen och den regionen blir otillgänglig påverkar det här problemet endast kontrollplansaktiviteter på själva den hanterade identiteten.  De aktiviteter som utförs av resurser som redan har konfigurerats för att använda hanterade identiteter påverkas inte.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Fungerar hanterade identiteter för Azure-resurser med Azure Cloud Services?

Nej, det finns inga planer på att stödja hanterade identiteter för Azure-resurser i Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Vad är säkerhetsgränsen för hanterade identiteter för Azure-resurser?

Säkerhetsgränsen för identiteten är den resurs som den är kopplad till. Säkerhetsgränsen för en virtuell dator med hanterade identiteter för Azure-resurser aktiverad är till exempel den virtuella datorn. All kod som körs på den virtuella datorn kan anropa de hanterade identiteterna för azure-resursslutpunkter och begära token. Det är en liknande upplevelse med andra resurser som stöder hanterade identiteter för Azure-resurser.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Kommer hanterade identiteter att återskapas automatiskt om jag flyttar en prenumeration till en annan katalog?

Nej. Om du flyttar en prenumeration till en annan katalog måste du manuellt skapa om dem och bevilja Azure-rolltilldelningar igen.
- För system tilldelade hanterade identiteter: inaktivera och återaktivera. 
- För användar tilldelade hanterade identiteter: ta bort, skapa dem igen och bifoga dem igen till nödvändiga resurser (till exempel virtuella datorer)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan jag använda en hanterad identitet för att få åtkomst till en resurs i en annan katalog/klientorganisation?

Nej. Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Finns det några hastighetsbegränsningar som gäller för hanterade identiteter?

Gränser för hanterade identiteter har beroenden av Begränsningar för Azure-tjänster, Begränsningar för Azure Instance Metadata Service (IMDS) och Azure Active Directory tjänstbegränsningar.

- **Azure-tjänstbegränsningar** definierar antalet create-åtgärder som kan utföras på klient- och prenumerationsnivå. Användar tilldelade hanterade identiteter har också [begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) för hur de kan namnges.
- **IMDS** I allmänhet är begäranden till IMDS begränsade till fem begäranden per sekund. Begäranden som överskrider det här tröskelvärdet avvisas med 429 svar. Begäranden till kategorin Hanterad identitet är begränsade till 20 begäranden per sekund och 5 samtidiga begäranden. Du kan läsa mer i [artikeln Azure Instance Metadata Service (Windows).](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity)
- **Azure Active Directory tjänst** Varje hanterad identitet räknas mot objektkvotgränsen i en Azure AD-klientorganisation enligt beskrivningen i Azure [AD-tjänstens gränser och begränsningar.](../enterprise-users/directory-service-limits-restrictions.md)


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Går det att flytta en användar tilldelad hanterad identitet till en annan resursgrupp/prenumeration?

Det går inte att flytta en användar tilldelad hanterad identitet till en annan resursgrupp.

## <a name="next-steps"></a>Nästa steg

- Lär [dig hur hanterade identiteter fungerar med virtuella datorer](how-managed-identities-work-vm.md)
