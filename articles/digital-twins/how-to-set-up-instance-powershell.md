---
title: Konfigurera en instans och autentisering (PowerShell)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla tjänster med hjälp av Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040695"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Konfigurera en digital Azure-instans och autentisering (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom dessa steg manuellt, en i taget, med hjälp av Azure PowerShell.

* Om du vill gå igenom de här stegen manuellt med Azure Portal, se Portal versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (portal)*](how-to-set-up-instance-portal.md).
* Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Om du väljer att använda Azure PowerShell lokalt:
   1. [Installera AZ PowerShell-modulen](/powershell/azure/install-az-ps).
   1. Anslut till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Om du väljer att använda Azure Cloud Shell:
   1. Mer information finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md) .
   1. Kontrol lera att Cloud Shell har angetts för att köra PowerShell-versionen i Cloud Shell ikon fältet.

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell fönster som visar valet av PowerShell-version":::

1. Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Om det här är första gången du använder Azure Digitals sammanflätade med den här prenumerationen måste du registrera Resource-providern för **Microsoft. DigitalTwins** .

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> Även om **AZ. DigitalTwins** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>Skapa Azure Digitals-instansen

I det här avsnittet ska du **skapa en ny instans av Azure Digitals dubbla** med Azure PowerShell.
Du måste ange:

* En [Azure-resurs grupp](../azure-resource-manager/management/overview.md). Om du inte redan har en befintlig resurs grupp kan du skapa en med hjälp av cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* En region för distributionen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Ett namn för instansen. Namnet på den nya instansen måste vara unikt inom regionen för din prenumeration. Om din prenumeration har en annan Azure Digital-instans i den region som redan använder det angivna namnet uppmanas du att välja ett annat namn.

Använd värdena i följande exempel för att skapa instansen:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verifiera lyckade och samla in viktiga värden

Om instansen har skapats ser resultatet ut ungefär som i följande utdata som innehåller information om den resurs som du har skapat:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> Du kan se dessa egenskaper, tillsammans med alla egenskaper för din instans, när som helst genom att köra `Get-AzDigitalTwinsInstance` och skicka till `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

Observera Azure Digitals dubbla instansen **värdnamn**, **namn** och **ResourceGroup**. Dessa är viktiga värden som du kan behöva när du fortsätter att arbeta med Azure Digitals-instansen, för att ställa in autentisering och relaterade Azure-resurser. Om andra användare kommer att program mera mot instansen bör du dela dessa värden med dem.

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-user-access-permissions"></a>Konfigurera användar åtkomst behörigheter

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Bestäm **ObjectID** för Azure AD-kontot för den användare som ska tilldelas rollen med hjälp av cmdleten [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) .

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Använd följande kommando för att tilldela rollen. Den måste köras av en användare med [tillräcklig behörighet](#prerequisites-permission-requirements) i Azure-prenumerationen. Kommandot kräver att du skickar **ObjectID** för Azure AD-kontot för den användare som ska tilldelas rollen. Du måste också använda samma prenumerations-ID, resurs grupp namn och det digitala Azure-instans namnet som du valde tidigare.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Resultatet av det här kommandot är information om den roll tilldelning som har skapats.

### <a name="verify-success"></a>Verifieringen lyckades

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Nu har du en Azure Digital-instansen som är redo att gå och har tilldelats behörighet att hantera den.

## <a name="next-steps"></a>Nästa steg

Se så här ansluter du ett klient program till din instans med autentiserings kod:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)
