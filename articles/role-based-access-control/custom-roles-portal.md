---
title: Skapa eller uppdatera anpassade Azure-roller med hjälp av Azure Portal – Azure RBAC
description: Lär dig hur du skapar anpassade Azure-roller med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC). Detta inkluderar hur du visar, skapar, uppdaterar och tar bort anpassade roller.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/11/2020
ms.author: rolyon
ms.openlocfilehash: ecda0edcd34999e8cbb6c7ab9039953d17c119e5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369234"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal"></a>Skapa eller uppdatera anpassade roller i Azure med Microsoft Azure-portalen

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa egna Azure-anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänst huvud namn i prenumerations-och resurs grupps omfång. Anpassade roller lagras i en Azure Active Directory (Azure AD)-katalog och kan delas mellan prenumerationer. Varje katalog kan ha upp till 5000 anpassade roller. Du kan skapa anpassade roller med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API. I den här artikeln beskrivs hur du skapar anpassade roller med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)

## <a name="step-1-determine-the-permissions-you-need"></a>Steg 1: Bestäm vilka behörigheter du behöver

Azure har tusentals behörigheter som du kan inkludera i din anpassade roll. Här följer några metoder som kan hjälpa dig att avgöra vilka behörigheter som du vill lägga till i din anpassade roll:

- Titta på befintliga [inbyggda roller](built-in-roles.md).
- Lista de Azure-tjänster som du vill bevilja åtkomst till.
- Bestäm vilka [resurs leverantörer som ska mappas till Azure-tjänsterna](../azure-resource-manager/management/azure-services-resource-providers.md). En Sök metod beskrivs senare i [steg 4: behörigheter](#step-4-permissions).
- Sök efter de behörigheter som du vill inkludera i de [tillgängliga behörigheterna](resource-provider-operations.md) . En Sök metod beskrivs senare i [steg 4: behörigheter](#step-4-permissions).

## <a name="step-2-choose-how-to-start"></a>Steg 2: Välj hur du vill starta

Det finns tre sätt som du kan börja skapa en anpassad roll på. Du kan klona en befintlig roll, börja från början eller börja med en JSON-fil. Det enklaste sättet är att hitta en befintlig roll som har de flesta av de behörigheter du behöver och sedan klona och ändra den för ditt scenario. 

### <a name="clone-a-role"></a>Klona en roll

Om en befintlig roll inte har de behörigheter du behöver kan du klona den och sedan ändra behörigheterna. Följ de här stegen för att börja klona en roll.

1. Öppna en prenumeration eller resurs grupp i Azure Portal där du vill att den anpassade rollen ska kunna tilldelas och öppna sedan **åtkomst kontroll (IAM)**.

    Följande skärm bild visar IAM-sidan (Access Control) som öppnats för en prenumeration.

    ![Sidan åtkomst kontroll (IAM) för en prenumeration](./media/custom-roles-portal/access-control-subscription.png)

1. Klicka på fliken **roller** om du vill se en lista över alla inbyggda och anpassade roller.

1. Sök efter en roll som du vill klona, t. ex. rollen för fakturerings läsare.

1. Klicka på ellipsen (**...**) i slutet av raden och klicka sedan på **klona**.

    ![Klona snabb menyn](./media/custom-roles-portal/clone-menu.png)

    Detta öppnar Redigeraren för anpassade roller med alternativet **klona en roll** markerat.

1. Fortsätt till [steg 3: grunderna](#step-3-basics).

### <a name="start-from-scratch"></a>Börja från början

Om du vill kan du följa de här stegen för att starta en anpassad roll från grunden.

1. Öppna en prenumeration eller resurs grupp i Azure Portal där du vill att den anpassade rollen ska kunna tilldelas och öppna sedan **åtkomst kontroll (IAM)**.

1. Klicka på **Lägg till** och sedan på **Lägg till anpassad roll**.

    ![Menyn Lägg till anpassad roll](./media/custom-roles-portal/add-custom-role-menu.png)

    Detta öppnar Redigeraren för anpassade roller med alternativet **starta från början** valt.

1. Fortsätt till [steg 3: grunderna](#step-3-basics).

### <a name="start-from-json"></a>Starta från JSON

Om du vill kan du ange de flesta av dina anpassade roll värden i en JSON-fil. Du kan öppna filen i redigeraren för anpassade roller, göra ytterligare ändringar och sedan skapa den anpassade rollen. Följ dessa steg för att börja med en JSON-fil.

1. Skapa en JSON-fil med följande format:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. I JSON-filen anger du värden för de olika egenskaperna. Här är ett exempel med vissa värden som läggs till. Information om de olika egenskaperna finns i [förstå Azure Role definitions](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. Öppna sidan **åtkomst kontroll (IAM)** i Azure Portal.

1. Klicka på **Lägg till** och sedan på **Lägg till anpassad roll**.

    ![Menyn Lägg till anpassad roll](./media/custom-roles-portal/add-custom-role-menu.png)

    Detta öppnar Redigeraren för anpassade roller.

1. På fliken grundläggande, i **bas linje behörigheter**, väljer du **starta från JSON**.

1. Klicka på knappen mapp bredvid rutan Välj en fil för att öppna dialog rutan Öppna.

1. Välj din JSON-fil och klicka sedan på **Öppna**.

1. Fortsätt till [steg 3: grunderna](#step-3-basics).

## <a name="step-3-basics"></a>Steg 3: grunderna

På fliken **grundläggande** anger du namn, beskrivning och bas linje behörigheter för din anpassade roll.

1. I rutan **namn på anpassad roll** anger du ett namn för den anpassade rollen. Namnet måste vara unikt för Azure AD-katalogen. Namnet kan innehålla bokstäver, siffror, blank steg och specialtecken.

1. I rutan **Beskrivning** anger du en valfri beskrivning för den anpassade rollen. Detta kommer att bli knapp beskrivning för den anpassade rollen.

    Alternativet för **bas linje behörigheter** ska redan anges baserat på föregående steg, men du kan ändra.

    ![Fliken grunder med värden som anges](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Steg 4: behörigheter

På fliken **behörigheter** anger du behörigheterna för den anpassade rollen. Beroende på om du har klonat en roll eller om du har börjat med JSON kanske fliken behörigheter redan innehåller en lista över behörigheter.

![Fliken behörigheter för skapa anpassad roll](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Lägg till eller ta bort behörigheter

Följ dessa steg om du vill lägga till eller ta bort behörigheter för din anpassade roll.

1. Om du vill lägga till behörigheter klickar du på **Lägg till behörigheter** för att öppna fönstret Lägg till behörigheter.

    I det här fönstret visas alla tillgängliga behörigheter grupperade i olika kategorier i ett kort format. Varje kategori representerar en *resurs leverantör*, som är en tjänst som tillhandahåller Azure-resurser.

1. I rutan **Sök efter en behörighet** skriver du en sträng för att söka efter behörigheter. Sök till exempel efter *faktura* för att hitta behörigheter relaterade till faktura.

    En lista över resurs leverantörs kort visas baserat på din Sök sträng. En lista över hur resurs leverantörer mappar till Azure-tjänster finns i [Resource providers för Azure-tjänster](../azure-resource-manager/management/azure-services-resource-providers.md).

    ![Fönstret Lägg till behörigheter med Resource Provider](./media/custom-roles-portal/add-permissions-provider.png)

1. Klicka på ett resurs leverantörs kort som kan ha de behörigheter som du vill lägga till i din anpassade roll, till exempel **Microsoft-fakturering**.

    En lista över hanterings behörigheter för den resurs leverantören visas baserat på din Sök sträng.

    ![Lägg till behörighets lista](./media/custom-roles-portal/add-permissions-list.png)

1. Om du letar efter behörigheter som gäller för data planet klickar du på **data åtgärder**. Annars lämnar du åtgärderna växla inställt på **åtgärder** för att lista behörigheter som gäller för hanterings planet. Mer information om skillnaderna mellan hanterings planet och data planet finns i [hantering och data åtgärder](role-definitions.md#management-and-data-operations).

1. Vid behov kan du uppdatera Sök strängen för att ytterligare förfina sökningen.

1. När du har hittat en eller flera behörigheter som du vill lägga till i din anpassade roll, lägger du till en bock bredvid behörigheterna. Lägg exempelvis till en bock bredvid **övrigt: Ladda ned faktura** om du vill lägga till behörighet att ladda ned fakturor.

1. Klicka på **Lägg** till för att lägga till behörigheten i behörighets listan.

    Behörigheten läggs till som en `Actions` eller `DataActions` .

    ![Tillagd behörighet](./media/custom-roles-portal/permissions-list-add.png)

1. Om du vill ta bort behörigheter klickar du på ikonen Ta bort i slutet av raden. I det här exemplet, eftersom en användare inte behöver kunna skapa support biljetter, `Microsoft.Support/*` kan behörigheten tas bort.

### <a name="add-wildcard-permissions"></a>Lägg till behörigheter för jokertecken

Beroende på hur du valde att starta kan du ha behörigheter med jokertecken ( `*` ) i listan med behörigheter. Ett jokertecken ( `*` ) utökar en behörighet till allt som matchar åtgärds strängen som du anger. Följande jokertecken innehåller till exempel alla behörigheter som rör Azure Cost Management och export. Detta inkluderar även eventuella framtida export behörigheter som kan läggas till.

```
Microsoft.CostManagement/exports/*
```

Om du vill lägga till en ny behörighet för jokertecken kan du inte lägga till den med hjälp av rutan **Lägg till behörigheter** . Om du vill lägga till en behörighet med jokertecken måste du lägga till den manuellt med hjälp av **JSON** -fliken. Mer information finns i [steg 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Undanta behörigheter

Om din roll har en jokertecken ( `*` )-behörighet och du vill undanta eller subtrahera vissa behörigheter från den jokertecken kan du undanta dem. Anta till exempel att du har följande behörighet för jokertecken:

```
Microsoft.CostManagement/exports/*
```

Om du inte vill tillåta att en export tas bort kan du undanta följande behörighet för borttagning:

```
Microsoft.CostManagement/exports/delete
```

När du undantar en behörighet läggs den till som `NotActions` eller `NotDataActions` . De effektiva hanterings behörigheterna beräknas genom att lägga till alla `Actions` och sedan subtrahera alla `NotActions` . De effektiva data behörigheterna beräknas genom att lägga till alla `DataActions` och sedan subtrahera alla `NotDataActions` .

> [!NOTE]
> Att undanta en behörighet är inte samma sak som neka. Att utesluta behörigheter är helt enkelt ett bekvämt sätt att dra behörighet från en behörighet med jokertecken.

1. Om du vill utesluta eller subtrahera en behörighet från en tillåten behörighet för jokertecken klickar du på **undanta behörigheter** för att öppna fönstret undantags behörigheter.

    I det här fönstret anger du de hanterings-eller data behörigheter som ska undantas eller subtraheras.

1. När du har hittat en eller flera behörigheter som du vill exkludera, lägger du till en bock bredvid behörigheterna och klickar sedan på knappen **Lägg till** .

    ![Fönstret exkludera behörighet – behörigheten vald](./media/custom-roles-portal/exclude-permissions-select.png)

    Behörigheten läggs till som `NotActions` eller `NotDataActions` .

    ![Behörighet Exkluderad](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Steg 5: tilldelnings bara omfång

På fliken **tilldelnings bara omfattningar** anger du var den anpassade rollen är tillgänglig för tilldelning, t. ex. prenumeration eller resurs grupp. Beroende på hur du valde att starta, kan den här fliken innehålla en lista över omfattningen där du öppnade sidan åtkomst kontroll (IAM). Det finns inte stöd för att ange tilldelnings bara omfång till rot omfånget ("/"). För närvarande kan du inte lägga till en hanterings grupp som ett tilldelnings bara omfång.

1. Klicka på **Lägg till tilldelnings bara omfång** för att öppna fönstret Lägg till tilldelnings bara scope.

    ![Fliken tilldelnings bara omfattningar](./media/custom-roles-portal/assignable-scopes.png)

1. Klicka på ett eller flera omfång som du vill använda, vanligt vis prenumerationen.

    ![Lägg till tilldelnings bara omfång](./media/custom-roles-portal/add-assignable-scopes.png)

1. Klicka på knappen **Lägg** till för att lägga till ett tilldelnings bara omfång.

## <a name="step-6-json"></a>Steg 6: JSON

På fliken **JSON** visas din anpassade roll som är FORMATERAD i JSON. Om du vill kan du redigera JSON direkt. Om du vill lägga till en wildcard ( `*` )-behörighet måste du använda den här fliken.

1. Klicka på **Redigera** om du vill redigera JSON.

    ![Fliken JSON visar den anpassade rollen](./media/custom-roles-portal/json.png)

1. Gör ändringar i JSON.

    Om JSON-filen inte är korrekt formaterad visas en röd Taggad linje och en indikator i den lodräta fästmarginal.

1. Klicka på **Spara** när du är klar med redigeringen.

## <a name="step-7-review--create"></a>Steg 7: granska + skapa

På fliken **Granska + skapa** kan du granska dina anpassade roll inställningar.

1. Granska inställningarna för anpassade roller.

    ![Granska + fliken Skapa](./media/custom-roles-portal/review-create.png)

1. Skapa din anpassade roll genom att klicka på **skapa** .

    Efter en liten stund visas en meddelande ruta som anger att den anpassade rollen har skapats.

    ![Skapa ett anpassat roll meddelande](./media/custom-roles-portal/custom-role-success.png)

    Om några fel upptäcks visas ett meddelande.

    ![Granska + skapa fel](./media/custom-roles-portal/review-create-error.png)

1. Visa din nya anpassade roll i listan **roller** . Om du inte ser den anpassade rollen klickar du på **Uppdatera**.

     Det kan ta några minuter innan din anpassade roll visas överallt.

## <a name="list-custom-roles"></a>Lista anpassade roller

Följ de här stegen om du vill visa dina anpassade roller.

1. Öppna en prenumeration eller resurs grupp och öppna sedan **åtkomst kontroll (IAM)**.

1. Klicka på fliken **roller** om du vill se en lista över alla inbyggda och anpassade roller.

1. I listan **typ** väljer du **CustomRole** för att bara se dina anpassade roller.

    Om du precis har skapat den anpassade rollen och inte ser den i listan klickar du på **Uppdatera**.

    ![Lista med anpassade roller](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

1. På det sätt som beskrivs tidigare i den här artikeln öppnar du listan över anpassade roller.

1. Klicka på ellipsen (**...**) för den anpassade roll som du vill uppdatera och klicka sedan på **Redigera**. Observera att du inte kan uppdatera inbyggda roller.

    Den anpassade rollen öppnas i redigeraren.

    ![Menyn anpassad roll](./media/custom-roles-portal/edit-menu.png)

1. Använd de olika flikarna för att uppdatera den anpassade rollen.

1. När du är färdig med ändringarna klickar du på fliken **Granska + skapa** för att granska ändringarna.

1. Klicka på knappen **Uppdatera** för att uppdatera din anpassade roll.

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

1. På det sätt som beskrivs tidigare i den här artikeln öppnar du listan över anpassade roller.

1. Ta bort alla roll tilldelningar som använder den anpassade rollen.

1. Klicka på ellipsen (**...**) för den anpassade rollen som du vill ta bort och klicka sedan på **ta bort**.

    ![Skärm bild som visar en lista över anpassade roller som kan markeras för borttagning.](./media/custom-roles-portal/delete-menu.png)

    Det kan ta några minuter innan den anpassade rollen har tagits bort helt.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa en anpassad Azure-roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Azure Resource Provider-åtgärder](resource-provider-operations.md)
