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
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98044279"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Konfigurera en digital Azure-instans och autentisering (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom dessa steg manuellt, en i taget, med hjälp av [Azure PowerShell](/powershell/azure/new-azureps-module-az).

* Om du vill gå igenom de här stegen manuellt med Azure Portal, se Portal versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (portal)*](how-to-set-up-instance-portal.md).
* Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Förbered din miljö

1. Börja med att välja var du vill köra kommandona i den här artikeln. Du kan välja att köra Azure PowerShell kommandon med en lokal installation av Azure PowerShell eller i ett webbläsarfönster med hjälp av [Azure Cloud Shell](https://shell.azure.com).
    * Om du väljer att använda Azure PowerShell lokalt:
       1. [Installera AZ PowerShell-modulen](/powershell/azure/install-az-ps).
       1. Öppna ett PowerShell-fönster på din dator.
       1. Anslut till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
    * Om du väljer att använda Azure Cloud Shell:
        1. Mer information om Cloud Shell finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md) .
        1. Öppna ett Cloud Shell-fönster genom att följa [den här länken](https://shell.azure.com) i webbläsaren.
        1. Kontrol lera att Cloud Shell har angetts för att köra PowerShell-versionen i Cloud Shell ikon fältet.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell fönster som visar valet av PowerShell-version":::
    
1. Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Om det här är första gången du använder Azure Digitals sammanflätade med den här prenumerationen måste du registrera Resource-providern för **Microsoft. DigitalTwins** . (Om du inte är säker är det OK att köra det igen även om du har gjort det någon gång tidigare.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Använd följande kommando för att installera PowerShell-modulen **AZ. DigitalTwins** .
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Även om PowerShell-modulen **AZ. DigitalTwins** är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten enligt beskrivningen ovan. När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

## <a name="create-the-azure-digital-twins-instance"></a>Skapa Azure Digitals-instansen

I det här avsnittet ska du **skapa en ny instans av Azure Digitals dubbla** med Azure PowerShell.
Du måste ange:

* En [Azure-resurs grupp](../azure-resource-manager/management/overview.md) där instansen ska distribueras. Om du inte redan har en befintlig resurs grupp kan du skapa en med hjälp av cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* En region för distributionen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Ett namn för instansen. Namnet på den nya instansen måste vara unikt inom regionen för din prenumeration. Om din prenumeration har en annan Azure Digital-instans i den region som redan använder det angivna namnet uppmanas du att välja ett annat namn.

Använd värdena i följande kommando för att skapa instansen:

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

Sedan visar du egenskaperna för den nya instansen genom `Get-AzDigitalTwinsInstance` att köra och dirigeras till `Select-Object -Property *` , så här:

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> Du kan använda det här kommandot för att se alla egenskaper för din instans när som helst.

Observera Azure Digitals dubbla instansen **värdnamn**, **namn** och **ResourceGroup**. Dessa är viktiga värden som du kan behöva när du fortsätter att arbeta med Azure Digitals-instansen, för att ställa in autentisering och relaterade Azure-resurser. Om andra användare kommer att program mera mot instansen bör du dela dessa värden med dem.

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-user-access-permissions"></a>Konfigurera användar åtkomst behörigheter

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Först bestämmer du **ObjectID** för Azure AD-kontot för den användare som ska tilldelas rollen. Du kan hitta det här värdet med hjälp av cmdleten [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) genom att skicka in User Principal Name på Azure AD-kontot för att hämta sitt ObjectID (och annan användar information). I de flesta fall matchar User Principal Name användarens e-post på Azure AD-kontot.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Använd sedan **ObjectID** i följande kommando för att tilldela rollen. Kommandot kräver också att du anger samma prenumerations-ID, resurs grupp namn och det digitala Azure-exemplaret som du valde tidigare när du skapade instansen. Kommandot måste köras av en användare med [tillräcklig behörighet](#prerequisites-permission-requirements) i Azure-prenumerationen.

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
