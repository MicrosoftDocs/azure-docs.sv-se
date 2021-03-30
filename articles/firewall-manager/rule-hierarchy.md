---
title: Använd Azures brand Väggs princip för att definiera en regelmall
description: Lär dig hur du använder Azures brand Väggs princip för att definiera en regelmall och tillämpa efterlevnad.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331744"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Använd Azures brand Väggs princip för att definiera en regelmall

Säkerhets administratörer behöver hantera brand väggar och säkerställa efterlevnad för lokala och molnbaserade distributioner. En viktig komponent är möjligheten att tillhandahålla program team med flexibilitet för att implementera CI/CD-pipeliner för att skapa brand Väggs regler på ett automatiserat sätt.

Med Azure Firewall-principen kan du definiera en regelmall och genomdriva efterlevnad:

- Innehåller en hierarkisk struktur för att täcka en central bas princip ovanpå en underordnad program grupp princip. Bas principen har högre prioritet och körs före den underordnade principen.
- Använd en anpassad roll definition i Azure för att förhindra oavsiktlig grund policy borttagning och ge selektiv åtkomst till regel samlings grupper i en prenumeration eller resurs grupp. 

## <a name="solution-overview"></a>Lösningsöversikt

De övergripande stegen för det här exemplet är:

1. Skapa en grundläggande brand Väggs princip i resurs gruppen säkerhets team. 
3. Definiera säkerhetsrelaterade regler i bas principen. Detta lägger till en gemensam uppsättning regler som tillåter/nekar trafik.
4. Skapa grup principer för program som ärver bas principen. 
5. Definiera program team-specifika regler i principen. Du kan också migrera regler från befintliga brand väggar.
6. Skapa Azure Active Directory anpassade roller för att ge detaljerad åtkomst till regel samlings gruppen och Lägg till roller i en brand Väggs princip omfattning. I följande exempel kan Sälj grupp medlemmar redigera regel samlings grupper för brand Väggs principen för försäljnings team. Samma sak gäller för databas-och teknik team.
7. Koppla principen till motsvarande brand vägg. En Azure-brandvägg kan bara ha en tilldelad princip. Detta kräver att varje program team har sin egen brand vägg.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Team och krav" border="false":::

### <a name="create-the-firewall-policies"></a>Skapa brand Väggs principer

- En grundläggande brand Väggs princip.

Skapa principer för var och en av program teamen:

- En princip för försäljnings brand vägg. Principen för försäljnings brand väggen ärver den grundläggande brand Väggs principen.
- En princip för brand vägg för databas. Principen för databas brand väggen ärver bas brand Väggs principen.
- En ingenjörs brand Väggs princip. Den tekniska brand Väggs principen ärver också den grundläggande brand Väggs principen.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Principmall" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Skapa anpassade roller för att få åtkomst till regel samlings grupper 

Anpassade roller definieras för varje program grupp. Rollen definierar åtgärder och omfattning. Program teamen kan redigera regel samlings grupper för sina respektive program.

Använd följande övergripande procedur för att definiera anpassade roller:

1. Hämta prenumerationen:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Kör följande kommando:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Använd kommandot Get-AzRoleDefinition för att mata ut rollen läsare i JSON-format. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Öppna ReaderSupportRole.jsfilen i ett redigerings program.

   Följande visar JSON-utdata. Information om de olika egenskaperna finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Redigera JSON-filen för att lägga till 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   åtgärd till egenskapen **åtgärder**   . Se till att inkludera ett kommatecken efter läsåtgärden. Med den här åtgärden kan användaren skapa och uppdatera regel samlings grupper.
6. I **AssignableScopes** lägger du till ditt PRENUMERATIONS-ID med följande format: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   Du måste lägga till explicita prenumerations-ID:n; annars tillåts du inte importera rollen i din prenumeration.
7. Ta bort ****   egenskaps raden för ID och ändra egenskapen **IsCustom**   till true.
8. Ändra  ****   egenskaperna för namn och  **Beskrivning**   till *AZFM för regel samlings grupp* och *användare med den här rollen kan redigera regel samlings grupper för brand Väggs principer*

Din JSON-fil bör se ut ungefär som i följande exempel:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Om du vill skapa den nya anpassade rollen använder du kommandot New-AzRoleDefinition och anger definitions filen för JSON-rollen. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista över alla anpassade roller kan du använda kommandot Get-AzRoleDefinition:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Du kan också se de anpassade rollerna i Azure Portal. Gå till din prenumeration, Välj **åtkomst kontroll (IAM)**, **roller**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Läs behörighet för SalesAppPolicy":::

Mer information finns i [Självstudier: skapa en anpassad Azure-roll med hjälp av Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Lägg till användare i den anpassade rollen

På portalen kan du lägga till användare i rollen AZFM för regel samlings grupp och ge åtkomst till brand Väggs principerna.

1. Välj program teamets brand Väggs princip (till exempel SalesAppPolicy) från portalen.
2. Välj **Access Control**.
3. Välj **Lägg till rolltilldelning**.
4. Lägg till användare/användar grupper (till exempel Sälj teamet) till rollen.

Upprepa proceduren för de andra brand Väggs principerna.

### <a name="summary"></a>Sammanfattning

Brand Väggs principen med anpassade roller ger nu selektiv åtkomst till samlings grupper för brand Väggs principer.

Användare har inte behörighet att:
- Ta bort Azure Firewall eller brand Väggs principen.
- Uppdatera brand Väggs principens hierarki eller DNS-inställningar eller hot information.
- Uppdatera brand Väggs principen där de inte är medlemmar i gruppen AZFM Rule Collection Group.

Säkerhets administratörer kan använda bas principen för att genomdriva guardrails och blockera vissa typer av trafik (till exempel ICMP) som krävs av företaget. 

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Firewall-principen](policy-overview.md).

