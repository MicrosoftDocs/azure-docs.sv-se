---
title: Använd rollbaserad åtkomst kontroll i Azure för StorSimple | Microsoft Docs
description: Beskriver hur du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) i kontexten för StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518984"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Rollbaserad åtkomst kontroll i Azure för StorSimple

Den här artikeln innehåller en kort beskrivning av hur Azure-rollbaserad åtkomst kontroll (Azure RBAC) kan användas för din StorSimple-enhet. Azure RBAC ger detaljerad åtkomst hantering för Azure. Använd Azure RBAC för att bevilja enbart rätt mängd åtkomst till StorSimple-användare för att utföra sina jobb i stället för att ge alla obegränsad åtkomst. Mer information om grunderna i åtkomst hantering i Azure finns i [Vad är rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).

Den här artikeln gäller för enheter med StorSimple 8000-serien som kör uppdatering 3,0 eller senare i Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Azure-roller för StorSimple

Azure RBAC kan tilldelas baserat på roller. Rollerna säkerställer vissa behörighets nivåer baserat på tillgängliga resurser i miljön. Det finns två typer av roller som StorSimple användare kan välja bland: inbyggda eller anpassade.

* **Inbyggda roller** – de inbyggda rollerna kan vara ägare, deltagare, läsare eller användar åtkomst administratör. Mer information finns i [inbyggda roller för Azure Role-baserade Access Control](../role-based-access-control/built-in-roles.md).

* **Anpassade roller** – om de inbyggda rollerna inte passar dina behov kan du skapa Azure-anpassade roller för StorSimple. Om du vill skapa en anpassad Azure-roll börjar du med en inbyggd roll, redigerar den och importerar den sedan igen i-miljön. Hämtning och uppladdning av rollen hanteras med hjälp av antingen Azure PowerShell eller Azure CLI. Mer information finns i [skapa anpassade roller för rollbaserad Access Control](../role-based-access-control/custom-roles.md).

Om du vill visa de olika rollerna som är tillgängliga för en StorSimple enhets användare i Azure Portal går du till StorSimple Enhetshanteraren-tjänsten och går sedan till **åtkomst kontroll (IAM) > roller**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Skapa en anpassad roll för StorSimple Infrastructure Administrator

I följande exempel börjar vi med den inbyggda roll **läsaren** som gör att användarna kan se alla resurs omfattningar, men inte redigera dem eller skapa nya. Vi utökar sedan den här rollen för att skapa en ny anpassad roll StorSimple infrastruktur administratör. Den här rollen tilldelas till användare som kan hantera infrastrukturen för StorSimple-enheter.

1. Kör Windows PowerShell som administratör.

2. Logga in på Azure.

    `Connect-AzAccount`

3. Exportera rollen läsare som en JSON-mall på din dator.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Öppna JSON-filen i Visual Studio. Du ser att en typisk Azure-roll består av tre huvud avsnitt, **åtgärder**, **NotActions** och **AssignableScopes**.

    I avsnittet **åtgärd** visas alla tillåtna åtgärder för den här rollen. Varje åtgärd tilldelas från en resurs leverantör. För en infrastruktur administratör för StorSimple använder du `Microsoft.StorSimple` resurs leverantören.

    Använd PowerShell för att se alla tillgängliga resurs leverantörer i din prenumeration.

    `Get-AzResourceProvider`

    Du kan också söka efter alla tillgängliga PowerShell-cmdletar för att hantera resurs leverantörer.

    I avsnittet **NotActions** visas alla begränsade åtgärder för en viss Azure-roll. I det här exemplet är inga åtgärder begränsade.
    
    Prenumerations-ID: n visas under **AssignableScopes**. Se till att Azure-rollen innehåller det explicita prenumerations-ID där den används. Om rätt prenumerations-ID inte har angetts får du inte importera rollen i din prenumeration.

    Redigera filen genom att tänka på föregående överväganden.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importera den anpassade Azure-rollen till miljön igen.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Den här rollen bör nu visas i listan över roller i **åtkomst kontroll** bladet.

![Visa Azure-roller](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Mer information finns i [anpassade roller](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Exempel på utdata för att skapa en anpassad roll via PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Lägg till användare i den anpassade rollen

Du beviljar åtkomst inifrån resursen, resursgruppen eller prenumerationen som rolltilldelningen omfattar. När du ger åtkomst måste du tänka på att åtkomsten som beviljats på den överordnade noden ärvs av den underordnade noden. Mer information finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md).

1. Gå till **åtkomst kontroll (IAM)**. Klicka på **+ Lägg till** på bladet åtkomst kontroll.

    ![Lägg till åtkomst till Azure-rollen](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Välj den roll som du vill tilldela, i det här fallet är det **StorSimple Infrastructure admin**.

3. Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.

4. Klicka på **Spara** för att skapa tilldelningen.

    ![Lägg till behörigheter i Azure-rollen](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Om du **lägger till ett användar** meddelande spåras förloppet. När användaren har lagts till uppdateras listan över användare i åtkomst kontrollen.

## <a name="view-permissions-for-the-custom-role"></a>Visa behörigheter för den anpassade rollen

När den här rollen har skapats kan du Visa de behörigheter som är associerade med den här rollen i Azure Portal.

1. Om du vill visa de behörigheter som är associerade med den här rollen går du till **åtkomst kontroll (IAM) > roller > StorSimple Infrastructure admin**. Listan med användare i den här rollen visas.

2. Välj en StorSimple infrastruktur administratörs användare och klicka på **behörigheter**.

    ![Visa behörigheter för StorSimple-infraröd administratörs roll](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Behörigheterna som är associerade med den här rollen visas.

    ![Visa användare i rollen StorSimple-infraröd administratör](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [tilldelar anpassade roller för interna och externa användare](../role-based-access-control/role-assignments-external-users.md).
