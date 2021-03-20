---
title: Använd Azure Portal för att skapa ett IoT Hub | Microsoft Docs
description: Skapa, hantera och ta bort Azure IoT-hubbar via Azure Portal. Innehåller information om pris nivåer, skalning, säkerhet och meddelande konfiguration.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536053"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Skapa en IoT-hubb med hjälp av Azure Portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln beskriver hur du skapar och hanterar IoT-hubbar med hjälp av [Azure Portal](https://portal.azure.com).

Du behöver en Azure-prenumeration för att kunna använda stegen i den här självstudien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Ändra inställningarna för IoT Hub

Du kan ändra inställningarna för en befintlig IoT-hubb när den har skapats från fönstret IoT Hub.

![Skärm bild som visar inställningarna för IoT Hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Här följer några av de egenskaper som du kan ange för en IoT-hubb:

**Priser och skala**: du kan använda den här egenskapen för att migrera till en annan nivå eller ange antalet IoT Hub enheter. 

**Drift övervakning**: Aktivera eller inaktivera olika övervaknings kategorier, till exempel loggning av händelser relaterade till meddelanden från enhet till moln eller meddelanden från moln till enhet.

**IP-filter**: Ange ett intervall med IP-adresser som ska godkännas eller avvisas av IoT Hub.

**Egenskaper**: innehåller en lista över egenskaper som du kan kopiera och använda någon annan stans, till exempel resurs-ID, resurs grupp, plats och så vidare.

### <a name="shared-access-policies"></a>Principer för delad åtkomst

Du kan också visa eller ändra listan över principer för delad åtkomst genom att klicka på **principer för delad åtkomst** i avsnittet **Inställningar** . Dessa principer definierar behörigheterna för enheter och tjänster för att ansluta till IoT Hub. 

Klicka på **Lägg till** för att öppna bladet **Lägg till en princip för delad åtkomst** .  Du kan ange det nya princip namnet och de behörigheter som du vill koppla till den här principen, som du ser i följande figur:

![Skärm bild som visar hur du lägger till en princip för delad åtkomst](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Skriv principerna för **register läsning** och **register** ger Läs-och Skriv behörighet till identitets registret. Dessa behörigheter används av Server dels moln tjänster för att hantera enhets identiteter. Om du väljer Skriv alternativet väljs alternativet Läs automatiskt.

* Principen för **tjänst anslutning** ger behörighet att komma åt tjänst slut punkter. Den här behörigheten används av Server dels moln tjänster för att skicka och ta emot meddelanden från enheter samt för att uppdatera och läsa enhets dubbla och modulens dubbla data.

* Principen för **enhets anslutning** beviljar behörigheter för att skicka och ta emot meddelanden med hjälp av IoT Hub slut punkter på enhets sidan. Den här behörigheten används av enheter för att skicka och ta emot meddelanden från en IoT-hubb, uppdatera och läsa enhets-och modulers dubbla data och utföra fil överföringar.

Klicka på **skapa** för att lägga till den nyligen skapade principen i den befintliga listan.

Mer detaljerad information om åtkomsten som beviljats av vissa behörigheter finns i [IoT Hub behörigheter](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Meddelanderoutning för en IoT Hub

Klicka **på meddelanderoutning** under **meddelanden** för att se fönstret meddelanderoutning där du definierar vägar och anpassade slut punkter för hubben. Med [meddelanderoutning kan du](iot-hub-devguide-messages-d2c.md) hantera hur data skickas från dina enheter till dina slut punkter. Det första steget är att lägga till en ny väg. Sedan kan du lägga till en befintlig slut punkt i vägen eller skapa en ny av de typer som stöds, t. ex. Blob Storage. 

![Fönstret meddelanderoutning](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Vägar

Vägar är den första fliken i fönstret meddelanderoutning. Klicka på +**Lägg** till för att lägga till en ny väg. Följande skärm visas. 

![Skärm bild som visar hur du lägger till en ny väg](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Namnge din väg. Väg namnet måste vara unikt inom listan över vägar för den hubben. 

För **slut punkt** kan du välja en i list rutan eller lägga till en ny. I det här exemplet finns redan ett lagrings konto och en behållare. Klicka på +**Lägg** till bredvid List rutan slut punkt och välj **Blob Storage** om du vill lägga till dem som en slut punkt. Följande skärm bild visar var lagrings kontot och containern har angetts.

![Skärm bild som visar hur du lägger till en lagrings slut punkt för Routningsprincipen](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klicka på **Välj en behållare** för att välja lagrings konto och behållare. När du har valt dessa fält återgår den till slut punkts fönstret. Använd standardvärdena för resten av fälten och **skapa** för att skapa slut punkten för lagrings kontot och lägga till det i routningsregler.

För **data källa** väljer du meddelanden om enhets telemetri. 

Lägg sedan till en cirkulations fråga. I det här exemplet dirigeras de meddelanden som har en program egenskap som kallas `level` med ett värde som är lika med till `critical` lagrings kontot.

![Skärm bild som visar hur du sparar en ny regel för routning](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klicka på **Spara** för att spara regeln för routning. Du kommer tillbaka till fönstret meddelanderoutning och den nya regeln för routning visas.

### <a name="custom-endpoints"></a>Anpassade slut punkter

Klicka på fliken **anpassade slut punkter** . Du ser redan anpassade slut punkter som redan har skapats. Härifrån kan du lägga till nya slut punkter eller ta bort befintliga slut punkter. 

> [!NOTE]
> Om du tar bort en väg tar den inte bort de slut punkter som tilldelats den vägen. Om du vill ta bort en slut punkt klickar du på fliken anpassade slut punkter, väljer den slut punkt som du vill ta bort och klickar på ta bort.
>

Du kan läsa mer om anpassade slut punkter i [referens-IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

Du kan definiera upp till 10 anpassade slut punkter för en IoT Hub. 

Om du vill se ett komplett exempel på hur du använder anpassade slut punkter med routning, se [meddelanderoutning med IoT Hub](tutorial-routing.md).

## <a name="find-a-specific-iot-hub"></a>Hitta en speciell IoT-hubb

Här följer två sätt att hitta en speciell IoT-hubb i din prenumeration:

1. Om du känner till resurs gruppen som IoT-hubben tillhör, klickar du på **resurs grupper** och väljer sedan resurs gruppen i listan. På skärmen resurs grupp visas alla resurser i gruppen, inklusive IoT-hubbar. Klicka på den hubb som du vill se.

2. Klicka på **Alla resurser**. I rutan **alla resurser** finns en listruta som är standard för `All types` . Klicka på list rutan och avmarkera `Select all` . Leta upp `IoT Hub` och markera det. Klicka på list rutan för att stänga den, så kommer posterna att filtreras så att de bara visar dina IoT-hubbar.

## <a name="delete-the-iot-hub"></a>Ta bort IoT-hubben

Om du vill ta bort en IoT-hubb letar du reda på IoT-hubben som du vill ta bort och klickar sedan på knappen **ta bort** under IoT Hub-namnet.

## <a name="next-steps"></a>Nästa steg

Följ dessa länkar om du vill veta mer om hur du hanterar Azure-IoT Hub:

* [Meddelanderoutning med IoT Hub](tutorial-routing.md)
* [Övervaka din IoT Hub](monitor-iot-hub.md)
