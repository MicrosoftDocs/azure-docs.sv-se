---
title: Publicera ett meddelande till ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du publicerar ett anpassat meddelande i ett befintligt labb för att meddela användare om nyligen gjorda ändringar eller tillägg till labbet i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 72c6ed7ab691ece094673ff5a158ffa769a9f7a1
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270775"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publicera ett meddelande till ett labb i Azure DevTest Labs

Som labb administratör kan du skicka ett anpassat meddelande i ett befintligt labb för att meddela användare om nyligen gjorda ändringar eller tillägg till labbet. Du kanske till exempel vill informera användare om:

- Nya storlekar för virtuella datorer som är tillgängliga
- Bilder som för närvarande inte kan användas
- Uppdateringar av labb principer

När det har publicerats visas meddelandet på sidan med labb översikten och användaren kan välja den för mer information.

Funktionen meddelande är avsedd att användas för tillfälliga meddelanden.  Du kan enkelt inaktivera ett meddelande när det inte längre behövs.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Steg för att publicera ett meddelande i ett befintligt labb

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer du **alla tjänster**och väljer sedan **DevTest Labs** i listan. (Ditt labb kanske redan visas på instrument panelen under **alla resurser**).
1. I listan med labb väljer du det labb som du vill skicka ett meddelande till.
1. I labb **översikts** avsnittet väljer du **konfiguration och principer**.

    ![Knappen konfiguration och principer](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Välj **labb meddelande**till vänster under **Inställningar**.

    ![Knappen labb meddelande](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Om du vill skapa ett meddelande för användarna i det här labbet **ställer du** in på **Ja**.

1. Du kan ange ett **förfallo datum** för att ange ett datum och en tidpunkt då meddelandet inte längre visas för användarna. Om du inte anger ett förfallo datum förblir meddelandet kvar tills du inaktiverar det.

   > [!NOTE]
   > När meddelandet har gått ut visas det inte längre för användarna, men finns fortfarande i **labb meddelande** fönstret. Du kan redigera den och aktivera den igen för att göra den aktiv igen.
   >
   >

1. Ange en **meddelande rubrik** och **meddelande texten**.

   Rubriken kan bestå av upp till 100 tecken och visas för användaren på sidan med labb översikt. Om användaren väljer rubriken visas meddelande texten.

   Meddelande texten accepterar markdown. När du anger meddelande texten kan du visa meddelandet i förhands gransknings fältet längst ned på skärmen.

    ![Labb meddelande skärmen för att skapa meddelandet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Välj **Spara** när ditt meddelande är klart att publicera.

När du inte längre vill visa det här meddelandet för labb användare går du tillbaka till sidan **labb meddelande** **och aktiverar** **Nej**. Om du har angett ett förfallo datum inaktive ras meddelandet automatiskt vid denna tidpunkt.

## <a name="steps-for-users-to-view-an-announcement"></a>Steg för användare att visa ett meddelande

1. Välj ett labb från [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Om labbet har publicerat ett meddelande visas ett informations meddelande längst upp på sidan med labb översikt. Det här informations meddelandet är meddelande rubriken som angavs när meddelandet skapades.

    ![Labb meddelande på sidan Översikt](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Användaren kan välja meddelandet för att visa hela meddelandet.

    ![Mer information om labb meddelandet](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan ange ett meddelande som en del av en Azure Resource Manager-mall som visas i följande exempel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Du kan distribuera en Azure Resource Manager-mall genom att använda något av följande sätt:

- [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [REST-API](../azure-resource-manager/templates/deploy-rest.md)

## <a name="next-steps"></a>Nästa steg
* Om du ändrar eller anger en labb princip kanske du vill skicka ett meddelande till att informera användarna. [Ange principer och scheman](devtest-lab-set-lab-policy.md) ger information om hur du tillämpar begränsningar och konventioner i din prenumeration med hjälp av anpassade principer.
* Utforska [DevTest Labs Azure Resource Manager snabb starts galleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
