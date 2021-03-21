---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: c344d7e1aa1f6d45131295ba9aad1294c5ba548c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930752"
---
1. Om du vill skapa ett nytt konfigurations Arkiv för appen loggar du in på [Azure Portal](https://portal.azure.com). I det övre vänstra hörnet på Start sidan väljer du **skapa en resurs**. I rutan **Sök på Marketplace anger du** *app-konfiguration* och väljer <kbd>RETUR</kbd>.

    ![Sök efter App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Välj **app-konfiguration** från Sök resultaten och välj sedan **skapa**.

    ![Välj Skapa](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. I fönstret **skapa app-konfiguration** anger du följande inställningar:

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration väljs den automatiskt och ingen **prenumerations** lista visas. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resurs grupp för din app Configuration Store-resurs. Den här gruppen är användbar för att organisera flera resurser som du kanske vill ta bort samtidigt genom att ta bort resurs gruppen. Mer information finns i [använda resurs grupper för att hantera dina Azure-resurser](../articles/azure-resource-manager/management/overview.md). |
    | **Resurs namn** | Globalt unikt namn | Ange ett unikt resurs namn som ska användas för appens konfigurations Arkiv resurs. Namnet måste vara en sträng mellan 5 och 50 tecken och får bara innehålla siffror, bokstäver och `-` tecknet. Namnet får inte börja eller sluta med `-` specialtecknet. |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. För bästa prestanda skapar du resursen i samma region som andra komponenter i ditt program. |
    | **Prisnivå** | *Kostnadsfri* | Välj önskad pris nivå. Mer information finns på sidan med [prissättning för app-konfiguration](https://azure.microsoft.com/pricing/details/app-configuration). |

1. Välj **Granska + skapa** för att validera inställningarna.

1. Välj **Skapa**. Distributionen kan ta några minuter.

1. När distributionen är klar går du till appen konfigurations resurs. Välj **Inställningar** > **Åtkomstnycklar**. Anteckna den primära skrivskyddade nyckel anslutnings strängen. Du kommer att använda den här anslutnings strängen senare för att konfigurera programmet för att kommunicera med det app-konfigurations lager som du har skapat.