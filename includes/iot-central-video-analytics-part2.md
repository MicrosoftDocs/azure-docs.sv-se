---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763434"
---
## <a name="deploy-and-configure-azure-media-services"></a>Distribuera och konfigurera Azure Media Services

Lösningen använder ett Azure Media Services-konto för att lagra videoklipp om objekt identifiering som skapats av IoT Edge gateway-enheten.

När du skapar Media Services kontot:

- Du måste ange ett konto namn, en Azure-prenumeration, en plats, en resurs grupp och ett lagrings konto. Skapa ett nytt lagrings konto med hjälp av standardinställningarna när du skapar Media Services-kontot.

- Välj regionen **USA, östra** för platsen.

- Skapa en ny resurs grupp med namnet *lva-RG* i regionen **USA, östra** för Media Services-och lagrings konton. När du har slutfört självstudierna är det enkelt att ta bort alla resurser genom att ta bort resurs gruppen *lva-RG* .

Skapa [Media Services kontot i Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> De här självstudierna använder regionen **USA, östra** i alla exempel. Du kan använda din närmaste region om du vill.

Anteckna ditt **Media Services** konto namn i *scratchpad.txt* -filen.

När distributionen är klar öppnar du en Cloud Shell och kör följande kommando för att hämta **resurs-ID** för ditt Media Service-konto:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Använd Cloud Shell för att hämta resurs-ID":::

Anteckna **resurs-ID: t** i *scratchpad.txt* -filen, du använder det här värdet senare när du konfigurerar IoT Edge-modulen.

Konfigurera sedan en Azure Active Directory tjänstens huvud namn för din Media Services-resurs. Välj **API-åtkomst** och sedan **tjänstens huvud namns autentisering**. Skapa en ny Azure Active Directory-app med samma namn som din Media Services resurs och skapa en hemlighet med en beskrivning *IoT Edge åtkomst*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Konfigurera Azure A D-appen för Azure Media Services":::

När hemligheten har genererats rullar du ned till avsnittet **Kopiera dina autentiseringsuppgifter för att ansluta ditt tjänst huvud program** . Välj sedan **JSON**. Du kan kopiera all inloggnings information från en enda go. Anteckna den här informationen i *scratchpad.txt* -filen, du använder den senare när du konfigurerar IoT Edge enheten.

> [!WARNING]
> Det här är din enda chans att visa och spara hemligheten. Om du förlorar det måste du generera en annan hemlighet.

## <a name="create-the-azure-iot-central-application"></a>Skapa Azure IoT Central-programmet

I det här avsnittet skapar du ett nytt Azure IoT Central-program från en mall. Du använder det här programmet i själv studie serien för att bygga en komplett lösning.

Så här skapar du ett nytt Azure IoT Central-program:

1. Gå till webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .

1. Logga in med de autentiseringsuppgifter du använder för att få åtkomst till din Azure-prenumeration.

1. Om du vill börja skapa ett nytt Azure IoT Central-program väljer du **nytt program** på sidan **skapa** .

1. Välj **åter försäljning**. På sidan detalj handel visas flera mallar för åter försäljning.

Så här skapar du ett nytt video analys program:

1. Välj program mal len **video analys-objekt och rörelse identifiering** . Den här mallen innehåller enhetsspecifika för de enheter som används i självstudien. Mallen innehåller exempel på instrument paneler som operatörer kan använda för att utföra åtgärder som att övervaka och hantera kameror.

1. Du kan också välja ett eget **program namn**. Det här programmet baseras på ett fiktivt detalj handels lager med namnet Northwind Traders. I självstudien används **program namnet** *Northwind Traders video analys*.

    > [!NOTE]
    > Om du använder ett eget **program namn** måste du fortfarande använda ett unikt värde för programmets **URL**.

1. Om du har en Azure-prenumeration väljer du **katalog**, **Azure-prenumeration** och **USA** som **plats**. Om du inte har någon prenumeration kan du aktivera den **kostnads fria utvärderings versionen på 7 dagar** och slutföra den kontakt information som krävs. I den här självstudien används tre enheter – två kameror och en IoT Edge enhet – så om du inte använder den kostnads fria utvärderings versionen debiteras du för användning.

    Mer information om kataloger, prenumerationer och platser finns i [snabb start för att skapa ett program](../articles/iot-central/core/quick-deploy-iot-central.md).

1. Välj **Skapa**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Sidan Skapa program i Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Hämta konfigurations data

Senare i den här självstudien när du konfigurerar IoT Edge Gateway, behöver du vissa konfigurations data från IoT Central programmet. Den IoT Edge enheten behöver den här informationen för att registrera dig hos och ansluta till programmet.

I avsnittet **Administration** väljer **du ditt program** och noterar **programmets URL** och **program-ID: t** i *scratchpad.txt* -filen:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Skärm bild som visar fönstret Administration på en video analys sida med programmet U R L och program I D markerat.":::

Välj **API-token** och generera en ny token med namnet **LVAEdgeToken** för **operatörs** rollen:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Generera token":::

Anteckna token i *scratchpad.txt* -filen för senare. När dialog rutan stängs kan du inte Visa token igen.

I avsnittet **Administration** väljer du **enhets anslutning** och väljer sedan **SAS-IoT-Devices**.

Anteckna **primär nyckeln** för enheter i *scratchpad.txts* filen. Du använder den här *primära gruppen signatur för delad åtkomst* senare när du konfigurerar IoT Edge-enheten.
