---
title: Hanterade identiteter för Azure-resurser vanliga frågor och svar – Azure AD "
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
ms.openlocfilehash: 3d3ab9859eb9f85d5ca7d0573fa79443ae9fe964
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251035"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Hanterade identiteter för Azure-resurser vanliga frågor och svar – Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="administration"></a>Administration

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hur kan du hitta resurser som har en hanterad identitet?

Du kan hitta listan över resurser som har en systemtilldelad hanterad identitet med hjälp av följande Azure CLI-kommando: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Vilka Azure RBAC-behörigheter krävs för hanterade identiteter på en resurs? 

- Systemtilldelad hanterad identitet: du behöver Skriv behörighet över resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Den här åtgärden ingår i de resursbaserade inbyggda rollerna som [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Användardefinierad hanterad identitet: du behöver Skriv behörighet över resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Förutom roll tilldelningen [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) över den hanterade identiteten.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hur gör jag för att förhindra att användare tilldelade hanterade identiteter skapas?

Du kan hindra användarna från att skapa användarspecifika hanterade identiteter med hjälp av [Azure policy](../../governance/policy/overview.md)

1. Navigera till [Azure Portal](https://portal.azure.com) och gå till **princip**.
2. Välj **definitioner**
3. Välj **+ princip definition** och ange nödvändig information.
4. I avsnittet princip regel klistra in
    
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

När du har skapat principen tilldelar du den till den resurs grupp som du vill använda.

1. Navigera till resurs grupper.
2. Hitta resurs gruppen som du använder för testning.
3. Välj **principer** på den vänstra menyn.
4. Välj **tilldela princip**
5. I avsnittet **grundläggande** anger du:
    1. **Omfång** Resurs gruppen som används för testning
    1. **Princip definition**: den princip som vi skapade tidigare.
6. Lämna standardvärdena för alla andra inställningar och välj **Granska + skapa**

Vid det här läget kommer alla försök att skapa en hanterad identitet som tilldelats av användare i resurs gruppen att Miss lyckas.

  ![Princip överträdelse](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Begrepp

### <a name="do-managed-identities-have-a-backing-app-object"></a>Har Managed identiteter ett säkerhetskopierat app-objekt?

Nej. Hanterade identiteter och Azure AD App registreringar är inte samma sak i katalogen.

Appregistreringar har två komponenter: ett program objekt och ett huvud objekt för tjänsten.
Hanterade identiteter för Azure-resurser har bara en av dessa komponenter: ett huvud objekt för tjänsten.

Hanterade identiteter har inget program objekt i katalogen, vilket är det som ofta används för att bevilja app-behörigheter för MS Graph. I stället måste MS Graph-behörigheter för hanterade identiteter beviljas direkt till tjänstens huvud namn.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Vad är den autentiseringsuppgift som är associerad med en hanterad identitet? Hur länge är det giltigt och hur ofta det roteras?

> [!NOTE]
> Hur hanterade identiteter autentiserar är en intern implementerings information som kan ändras utan föregående meddelande.

Hanterade identiteter använder certifikatbaserad autentisering. Varje hanterad identitets autentiseringsuppgift har ett förfallo datum på 90 dagar och den har registrerats efter 45 dagar.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Vilken identitet kommer att IMDS standardvärdet om du inte anger identiteten i begäran?

- Om systemtilldelad hanterad identitet är aktive rad och ingen identitet anges i begäran, IMDS standardvärdet för systemtilldelad hanterad identitet.
- Om systemtilldelad hanterad identitet inte är aktive rad och endast en användare som tilldelats en hanterad identitet finns, IMDS standardvärdet för den enskilda användaren som tilldelats en hanterad identitet
- Om systemtilldelad hanterad identitet inte är aktive rad och det finns flera tilldelade hanterade identiteter, måste du ange en hanterad identitet i begäran.

## <a name="limitations"></a>Begränsningar

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Kan samma hanterade identitet användas i flera regioner?

I korthet kan du använda användare som tilldelats hanterade identiteter i mer än en Azure-region. Det längre svar är att när användare som tilldelats hanterade identiteter skapas som regionala resurser, är det associerade [tjänst huvud kontot](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) som skapats i Azure AD tillgängligt globalt. Tjänstens huvud namn kan användas från alla Azure-regioner och dess tillgänglighet är beroende av tillgängligheten för Azure AD. Om du till exempel har skapat en användare som tilldelats en hanterad identitet i South-Central region och regionen blir otillgänglig, påverkar det här problemet bara [kontroll Plans](../../azure-resource-manager/management/control-plane-and-data-plane.md) aktiviteterna på den hanterade identiteten.  De aktiviteter som utförs av alla resurser som redan har kon figurer ATS för att använda hanterade identiteter påverkas inte.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Fungerar hanterade identiteter för Azure-resurser med Azure Cloud Services?

Nej, det finns inga planer på att stödja hanterade identiteter för Azure-resurser i Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Vad är säkerhets gränserna för hanterade identiteter för Azure-resurser?

Säkerhets gränserna för identiteten är den resurs som den är kopplad till. Till exempel är säkerhets gränserna för en virtuell dator med hanterade identiteter för Azure-resurser aktiverade den virtuella datorn. All kod som körs på den virtuella datorn kan anropa de hanterade identiteterna för Azure-resursernas slut punkt och begära token. Det är samma erfarenhet av andra resurser som har stöd för hanterade identiteter för Azure-resurser.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Kommer Managed identiteter att återskapas automatiskt om jag flyttar en prenumeration till en annan katalog?

Nej. Om du flyttar en prenumeration till en annan katalog måste du återskapa dem manuellt och ge Azure-roll tilldelningar igen.
- För systemtilldelade hanterade identiteter: inaktivera och återaktivera. 
- För användare som tilldelats hanterade identiteter: ta bort, återskapa och koppla dem igen till nödvändiga resurser (till exempel virtuella datorer)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan jag använda en hanterad identitet för att få åtkomst till en resurs i en annan katalog/klient organisation?

Nej. Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Finns det några hastighets begränsningar som gäller för hanterade identiteter?

Hanterade identiteter begränsar gränserna för Azures tjänst gränser, Azure Instance Metadata Service-gränser (IMDS) och Azure Active Directory tjänst begränsningar.

- **Azure Service-gränser** definierar antalet skapande åtgärder som kan utföras på klient-och prenumerations nivåerna. Användare som tilldelats hanterade identiteter har också [begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) kring hur de kan namnges.
- **IMDS** I allmänhet är begär anden till IMDS begränsade till fem begär Anden per sekund. Begär Anden som överskrider detta tröskelvärde avvisas med 429 svar. Begär anden till den hanterade identitets kategorin är begränsade till 20 begär Anden per sekund och 5 samtidiga begär Anden. Du kan läsa mer i artikeln om [Azure instance metadata service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) .
- **Azure Active Directory tjänst** Varje hanterad identitet räknas mot objekt kvot gränsen i en Azure AD-klient enligt beskrivningen i Azure [AD-tjänstens gränser och begränsningar](../enterprise-users/directory-service-limits-restrictions.md).


## <a name="is-it-ok-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Är det OK att flytta en användardefinierad hanterad identitet till en annan resurs grupp/prenumeration?

Det finns inte stöd för att flytta en användardefinierad hanterad identitet till en annan resurs grupp.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur hanterade identiteter fungerar med virtuella datorer](how-managed-identities-work-vm.md)
