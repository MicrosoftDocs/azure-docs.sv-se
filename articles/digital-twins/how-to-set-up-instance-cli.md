---
title: Konfigurera en instans och autentisering (CLI)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av Azure Digitals dubbla tjänster med hjälp av CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7108ee956c18fbcc150b56cbcb8ae1c69841dda4
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198599"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Konfigurera en digital Azure-instans och autentisering (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom dessa steg manuellt, en i taget, med hjälp av CLI.
* Om du vill gå igenom de här stegen manuellt med Azure Portal, se Portal versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (portal)*](how-to-set-up-instance-portal.md).
* Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Konfigurera Cloud Shell-session
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Skapa Azure Digitals-instansen

I det här avsnittet ska du **skapa en ny instans av Azure Digitals dubbla** med kommandot Cloud Shell. Du måste ange:
* En resurs grupp där instansen ska distribueras. Om du inte redan har en befintlig resurs grupp i åtanke kan du skapa en nu med det här kommandot:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* En region för distributionen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Ett namn för instansen. Om din prenumeration har en annan Azure Digital-instans i den region som redan använder det angivna namnet uppmanas du att välja ett annat namn.

Använd de här värdena i följande kommando för att skapa instansen:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verifiera lyckade och samla in viktiga värden

Om instansen har skapats ser resultatet i Cloud Shell ut ungefär så här, vilket innebär att information om resursen som du har skapat visas.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Fönstret Kommando med att skapa en resurs grupp och en Azure Digital-instans":::

Observera att Azure Digitals dubbla instansen **värdnamn**, **namn** och **resourceGroup** från utdata. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen för att konfigurera autentisering och relaterade Azure-resurser. Om andra användare kommer att program mera mot instansen bör du dela dessa värden med dem.

> [!TIP]
> Du kan se dessa egenskaper, tillsammans med alla egenskaper för din instans, när som helst genom att köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-user-access-permissions"></a>Konfigurera användar åtkomst behörigheter

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Använd följande kommando för att tilldela rollen (måste köras av en användare med [tillräcklig behörighet](#prerequisites-permission-requirements) i Azure-prenumerationen). Kommandot kräver att du skickar *User Principal Name* på Azure AD-kontot för den användare som ska tilldelas rollen. I de flesta fall matchar detta användarens e-post på Azure AD-kontot.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Resultatet av det här kommandot är information om den roll tilldelning som har skapats.

> [!NOTE]
> Om det här kommandot returnerar ett fel som anger att CLI **inte kan hitta användare eller tjänstens huvud namn i diagram databasen**:
>
> Tilldela rollen med hjälp av användarens *objekt-ID* i stället. Detta kan inträffa för användare på personliga [Microsoft-konton (MSA: er)](https://account.microsoft.com/account). 
>
> Använd [Azure Portal sidan för Azure Active Directory användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) för att välja användar kontot och öppna dess information. Kopiera användarens *ObjectID*:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Visning av användar sida i Azure Portal som markerar GUID i fältet objekt-ID" lightbox="media/includes/user-id.png":::
>
> Upprepa sedan kommandot roll tilldelnings lista med användarens *objekt-ID* för `assignee` parametern ovan.

### <a name="verify-success"></a>Verifieringen lyckades

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Nu har du en Azure Digital-instansen som är redo att gå och har tilldelats behörighet att hantera den.

## <a name="next-steps"></a>Nästa steg

Testa enskilda REST API-anrop på din instans med hjälp av Azure Digitals flätat CLI-kommandon: 
* [AZ DT-referens](/cli/azure/ext/azure-iot/dt)
* [*Anvisningar: använda Azure Digitals flätat CLI*](how-to-use-cli.md)

Du kan också se hur du ansluter ett klient program till din instans med autentiserings kod:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)