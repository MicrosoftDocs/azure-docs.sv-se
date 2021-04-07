---
title: Så här skyddar du din resurs-hierarki – Azure-styrning
description: Lär dig hur du skyddar din resurs-hierarki med hierarkiska inställningar som inkluderar inställning av standard hanterings gruppen.
ms.date: 02/05/2021
ms.topic: conceptual
ms.openlocfilehash: 0f0afb5401fc646d26598a211604790af191f156
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594594"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Så här skyddar du din resurs-hierarki

Dina resurser, resurs grupper, prenumerationer, hanterings grupper och klient organisation sammanfattar tillsammans din mappstruktur. Inställningar för rot hanterings gruppen, till exempel Azure-anpassade roller eller Azure Policy princip tilldelningar, kan påverka alla resurser i din resurspool. Det är viktigt att skydda resurs-hierarkin från ändringar som kan påverka alla resurser negativt.

Hanterings grupper har nu hierarkiska inställningar som gör det möjligt för klient administratören att styra dessa beteenden. Den här artikeln beskriver var och en av de tillgängliga inställningarna för hierarkin och hur du ställer in dem.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Azure RBAC-behörigheter för hierarkiska inställningar

Om du konfigurerar någon av inställningarna för hierarkin krävs följande två resurs leverantörs åtgärder för rot hanterings gruppen:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Dessa åtgärder låter bara en användare läsa och uppdatera inställningarna för hierarkin. Åtgärderna ger inte någon annan åtkomst till hanterings gruppens hierarki eller resurser i hierarkin. Båda dessa åtgärder är tillgängliga i Azures inbyggda roll **hierarki inställningar administratör**.

## <a name="setting---default-management-group"></a>Inställning-standard hanterings grupp

Som standard läggs en ny prenumeration i en klient till som medlem i rot hanterings gruppen. Om princip tilldelningar är Azures rollbaserade åtkomst kontroll (Azure RBAC) och andra styrnings konstruktioner tilldelade till rot hanterings gruppen, kommer de direkt att påverka dessa nya prenumerationer. Därför tillämpar många organisationer inte dessa konstruktioner i rot hanterings gruppen, även om det är den önskade platsen för att tilldela dem. I andra fall är en mer begränsande uppsättning kontroller önskade för nya prenumerationer, men ska inte tilldelas alla prenumerationer. Den här inställningen stöder båda användnings fall.

Genom att tillåta att standard hanterings gruppen för nya prenumerationer definieras kan styrnings konstruktioner för hela organisationen tillämpas på rot hanterings gruppen och en separat hanterings grupp med princip tilldelningar eller Azure-roll tilldelningar som är mer lämpade för en ny prenumeration kan definieras.

### <a name="set-default-management-group-in-portal"></a>Ange standard hanterings grupp i portalen

Följ dessa steg om du vill konfigurera den här inställningen i Azure Portal:

1. Använd Sök fältet för att söka efter och välja hanterings grupper.

1. I rot hanterings gruppen väljer du **information** bredvid namnet på hanterings gruppen.

1. Under **Inställningar** väljer du **Inställningar för hierarki**.

1. Välj knappen **ändra standard hanterings grupp** .

   > [!NOTE]
   > Om knappen **ändra standard hanterings grupp** är inaktive rad är antingen hanterings gruppen som visas inte rot hanterings gruppen, eller så har ditt säkerhets objekt inte de behörigheter som krävs för att ändra inställningarna för hierarkin.

1. Välj en hanterings grupp i hierarkin och Använd knappen **Välj** .

### <a name="set-default-management-group-with-rest-api"></a>Ange standard hanterings grupp med REST API

Om du vill konfigurera den här inställningen med REST API, anropas slut punkten för [hierarki inställningar](/rest/api/resources/hierarchysettings) . Det gör du genom att använda följande REST API URI-och Body-format. Ersätt `{rootMgID}` med ID för din rot hanterings grupp och `{defaultGroupID}` med ID för hanterings gruppen som ska vara standard hanterings grupp:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Om du vill ange standard hanterings gruppen tillbaka till rot hanterings gruppen använder du samma slut punkt och anger **defaultManagementGroup** till värdet `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Inställning-Kräv auktorisering

Alla användare kan som standard skapa nya hanterings grupper inom en klient organisation. Administratörer för en klient organisation kan vilja bara ge dessa behörigheter till vissa användare för att upprätthålla konsekvens och överensstämmelse i hierarkin för hanterings grupper. Om den här inställningen är aktive rad måste en användare `Microsoft.Management/managementGroups/write` utföra åtgärden på rot hanterings gruppen för att skapa nya underordnade hanterings grupper.

### <a name="set-require-authorization-in-portal"></a>Ställ in Kräv auktorisering i portalen

Följ dessa steg om du vill konfigurera den här inställningen i Azure Portal:

1. Använd Sök fältet för att söka efter och välja hanterings grupper.

1. I rot hanterings gruppen väljer du **information** bredvid namnet på hanterings gruppen.

1. Under **Inställningar** väljer du **Inställningar för hierarki**.

1. Växla mellan **Kräv behörigheter för att skapa nya hanterings grupper.** alternativ till på.

   > [!NOTE]
   > Om **Kräv behörigheter för att skapa nya hanterings grupper.** växling är inaktiverat, antingen är hanterings gruppen som visas inte rot hanterings gruppen, eller så har ditt säkerhets objekt inte de behörigheter som krävs för att ändra inställningarna för hierarkin.

### <a name="set-require-authorization-with-rest-api"></a>Ange Kräv auktorisering med REST API

Om du vill konfigurera den här inställningen med REST API, anropas slut punkten för [hierarki inställningar](/rest/api/resources/hierarchysettings) . Det gör du genom att använda följande REST API URI-och Body-format. Värdet är ett _booleskt_ värde, så ange antingen **Sant** eller **falskt** för värdet. Värdet **True** aktiverar den här metoden för att skydda din hanterings grupp hierarki:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Du inaktiverar inställningen genom att använda samma slut punkt och ange **requireAuthorizationForGroupCreation** till värdet **false**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](../create-management-group-portal.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](../manage.md)