---
title: Använd privata slut punkter för att integrera Azure Functions med ett virtuellt nätverk
description: Den här självstudien visar hur du ansluter en funktion till ett virtuellt Azure-nätverk och låser den med hjälp av privata slut punkter.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: e8ca853908b366b99e150f04ced404f42acc7d21
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027422"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Självstudie: integrera Azure Functions med ett virtuellt Azure-nätverk med hjälp av privata slut punkter

Den här självstudien visar hur du använder Azure Functions för att ansluta till resurser i ett virtuellt Azure-nätverk med hjälp av privata slut punkter. Du skapar en funktion med ett lagrings konto som är låst bakom ett virtuellt nätverk. Det virtuella nätverket använder en Service Bus-kös utlösare.

I den här självstudien får du:

> [!div class="checklist"]
> * Skapa en Function-app i Premium-planen.
> * Skapa Azure-resurser, till exempel Service Bus, lagrings konto och virtuellt nätverk.
> * Lås ditt lagrings konto bakom en privat slut punkt.
> * Lås din Service Bus bakom en privat slut punkt.
> * Distribuera en Function-app som använder både Service Bus-och HTTP-utlösare.
> * Lås din Function-app bakom en privat slut punkt.
> * Testa att se att din Function-app är säker i det virtuella nätverket.
> * Rensa resurser.

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en Function-app i en Premium-plan

Du skapar en .NET Function-app i Premium-planen eftersom den här självstudien använder C#. Andra språk stöds också i Windows. Premium-planen tillhandahåller Server lös skalning utan stöd för integrering av virtuella nätverk.

1. På Azure Portal-menyn eller **Start** sidan väljer du **skapa en resurs**.

1. Välj **Compute** Funktionsapp på sidan **nytt**  >  .

1. På sidan **grundläggande** inställningar använder du följande tabell för att konfigurera funktionen för appens inställningar.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumeration som den här nya Function-appen skapas under. |
    | **[Resursgrupp](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Namn för den nya resurs gruppen där du ska skapa din Function-app. |
    | **Funktionsappens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.  |
    |**Publicera**| Kod | Välj att publicera kod filer eller en Docker-behållare. |
    | **Körningsstack** | .NET | I den här självstudien används .NET. |
    |**Region**| Önskad region | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner har åtkomst till. |

1. Välj **Nästa: värd**. Ange följande inställningar på sidan **värd** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Lagrings konto namn måste innehålla mellan 3 och 24 tecken. De får bara innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](./storage-considerations.md#storage-account-requirements). |
    |**Operativsystem**| Windows | I den här självstudien används Windows. |
    | **[Planera](./functions-scale.md)** | Premium | Värdplan som definierar hur resurser allokeras till din funktionsapp. Som standard skapas en ny App Service plan när du väljer **Premium**. Standard- **SKU och-storlek** är **EP1**, där *EP* står för _elastisk Premium_. Mer information finns i listan över Premium- [SKU: er](./functions-premium-plan.md#available-instance-skus).<br/><br/>När du kör JavaScript-funktioner i en Premium-plan väljer du en instans som har färre virtuella processorer. Mer information finns i [Välj Premium-](./functions-reference-node.md#considerations-for-javascript-functions)prenumerationer med en kärna.  |

1. Välj **Nästa: övervakning**. Ange följande inställningar på sidan **övervakning** .

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Standardvärde | Skapa en Application Insights resurs av samma app-namn i den närmaste region som stöds. Expandera den här inställningen om du behöver ändra det **nya resurs namnet** eller lagra dina data på en annan **plats** i ett [Azure-geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/)område. |

1. Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. På sidan **Granska och skapa** granskar du inställningarna. Välj sedan **skapa** för att etablera och distribuera Function-appen.

1. I det övre högra hörnet i portalen väljer du ikonen **meddelanden** och väntar på att **distributionen ska lyckas** .

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.

Grattis! Du har skapat din Premium Function-app.

## <a name="create-azure-resources"></a>Skapa Azure-resurser

Därefter skapar du ett lagrings konto, en Service Bus och ett virtuellt nätverk. 
### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Dina virtuella nätverk behöver ett lagrings konto som är skilt från det som du skapade med din Function-app.

1. På Azure Portal-menyn eller **Start** sidan väljer du **skapa en resurs**.

1. På sidan **ny** söker du efter *lagrings konto*. Välj sedan **Skapa**.

1. På fliken **grundläggande** inställningar använder du följande tabell för att konfigurera inställningarna för lagrings kontot. Alla andra inställningar kan använda standardvärdena.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Resurs gruppen som du skapade med din Function-app. |
    | **Namn** | mysecurestorage| Namnet på det lagrings konto som den privata slut punkten ska tillämpas på. |
    | **[Nationella](https://azure.microsoft.com/regions/)** | myFunctionRegion | Den region där du skapade din Function-app. |

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

### <a name="create-a-service-bus"></a>Skapa en Service Bus

1. På Azure Portal-menyn eller **Start** sidan väljer du **skapa en resurs**.

1. På sidan **ny** söker du efter *Service Bus*. Välj sedan **Skapa**.

1. På fliken **grundläggande** inställningar använder du följande tabell för att konfigurera Service Bus-inställningarna. Alla andra inställningar kan använda standardvärdena.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. |
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Resurs gruppen som du skapade med din Function-app. |
    | **Namn** | myServiceBus| Namnet på den Service Bus som den privata slut punkten ska tillämpas på. |
    | **[Nationella](https://azure.microsoft.com/regions/)** | myFunctionRegion | Den region där du skapade din Function-app. |
    | **Prisnivå** | Premium | Välj den här nivån om du vill använda privata slut punkter med Azure Service Bus. |

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Azure-resurserna i den här självstudien integreras antingen med eller placeras i ett virtuellt nätverk. Du använder privata slut punkter för att inkludera nätverks trafik i det virtuella nätverket.

I självstudien skapas två undernät:
- **standard**: undernät för privata slut punkter. Privata IP-adresser anges från det här under nätet.
- **Functions**: undernät för Azure Functions virtuell nätverks integrering. Det här under nätet är delegerat till Function-appen.

Skapa det virtuella nätverk som Function-appen ska integreras med:

1. På Azure Portal-menyn eller **Start** sidan väljer du **skapa en resurs**.

1. Sök efter *virtuellt nätverk* på den **nya** sidan. Välj sedan **Skapa**.

1. På fliken **grundläggande** inställningar använder du följande tabell för att konfigurera inställningarna för det virtuella nätverket.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Resurs gruppen som du skapade med din Function-app. |
    | **Namn** | myVirtualNet| Namnet på det virtuella nätverk som din Function-app ska ansluta till. |
    | **[Nationella](https://azure.microsoft.com/regions/)** | myFunctionRegion | Den region där du skapade din Function-app. |

1. På fliken **IP-adresser** väljer du **Lägg till undernät**. Använd följande tabell för att konfigurera under näts inställningarna.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Skärm bild av vyn Skapa konfiguration för virtuellt nätverk.":::

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn på undernät** | funktionen | Namnet på under nätet som Function-appen kommer att ansluta till. | 
    | **Adressintervall för undernätet** | 10.0.1.0/24 | Under nätets adress intervall. Observera att IPv4-adress utrymmet är 10.0.0.0/16 i föregående bild. Om värdet var 10.1.0.0/16 skulle det rekommenderade adress intervallet för under nätet vara 10.1.1.0/24. |

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

## <a name="lock-down-your-storage-account"></a>Lås ditt lagrings konto

Privata Azure-slutpunkter används för att ansluta till vissa Azure-resurser med hjälp av en privat IP-adress. Den här anslutningen säkerställer att nätverks trafiken förblir i det valda virtuella nätverket och att åtkomst endast är tillgängligt för vissa resurser. 

Skapa privata slut punkter för Azure Files lagring och Azure Blob Storage genom att använda ditt lagrings konto:

1. I det nya lagrings kontot väljer du **nätverk** i menyn till vänster.

1. På fliken **privat slut punkt anslutningar** väljer du **privat slut punkt**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Skärm bild av hur du skapar privata slut punkter för lagrings kontot.":::

1. På fliken **grundläggande** inställningar använder du inställningarna för privata slut punkter som visas i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj den resurs grupp som du skapade med din Function-app. | |
    | **Namn** | fil slut punkt | Namnet på den privata slut punkten för filer från ditt lagrings konto. |
    | **[Nationella](https://azure.microsoft.com/regions/)** | myFunctionRegion | Välj den region där du skapade ditt lagrings konto. |

1. På fliken **resurs** använder du inställningarna för den privata slut punkten som visas i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **Resurstyp**  | Microsoft. Storage/storageAccounts | Resurs typen för lagrings konton. |
    | **Resurs** | mysecurestorage | Det lagrings konto som du har skapat. |
    | **Målunderresurs** | file | Den privata slut punkt som ska användas för filer från lagrings kontot. |

1. Välj **standard** under **under näts** inställningen på fliken **konfiguration** .

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Resurser i det virtuella nätverket kan nu kommunicera med Storage-filer.

1. Skapa en annan privat slut punkt för blobbar. På fliken **resurser** använder du de inställningar som visas i följande tabell. För alla andra inställningar använder du samma värden som du använde för att skapa den privata slut punkten för filer.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **Resurstyp**  | Microsoft. Storage/storageAccounts | Resurs typen för lagrings konton. |
    | **Resurs** | mysecurestorage | Det lagrings konto som du har skapat. |
    | **Målunderresurs** | blob | Den privata slut punkt som ska användas för blobbar från lagrings kontot. |

## <a name="lock-down-your-service-bus"></a>Lås din Service Bus

Skapa den privata slut punkten för att låsa din Service Bus:

1. I den nya Service Bus-menyn till vänster väljer du **nätverk**.

1. På fliken **privat slut punkt anslutningar** väljer du **privat slut punkt**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Skärm bild av hur du går till privata slut punkter för Service Bus.":::

1. På fliken **grundläggande** inställningar använder du inställningarna för privata slut punkter som visas i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Resurs gruppen som du skapade med din Function-app. |
    | **Namn** | SB-slutpunkt | Namnet på den privata slut punkten för filer från ditt lagrings konto. |
    | **[Nationella](https://azure.microsoft.com/regions/)** | myFunctionRegion | Den region där du skapade ditt lagrings konto. |

1. På fliken **resurs** använder du inställningarna för den privata slut punkten som visas i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **Resurstyp**  | Microsoft. Service Bus/namnrymder | Resurs typen för Service Bus. |
    | **Resurs** | myServiceBus | Service Bus som du skapade tidigare i självstudien. |
    | **Mål under resurs** | namnområde | Den privata slut punkt som ska användas för namn området från Service Bus. |

1. Välj **standard** under **under näts** inställningen på fliken **konfiguration** .

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. 

Resurser i det virtuella nätverket kan nu kommunicera med Service Bus.

## <a name="create-a-file-share"></a>Skapa en filresurs

1. I det lagrings konto som du skapade väljer du **fil resurser** på menyn till vänster.

1. Välj **+ fil resurser**. I den här själv studie kursen namnger du *fil resursen.*

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Skärm bild av hur du skapar en fil resurs i lagrings kontot.":::

1. Välj **Skapa**.

## <a name="get-the-storage-account-connection-string"></a>Hämta anslutnings strängen för lagrings kontot

1. I det lagrings konto som du har skapat går du till menyn till vänster och väljer **åtkomst nycklar**.

1. Välj **Visa nycklar**. Kopiera och spara anslutnings strängen för **KEY1**. Du behöver den här anslutnings strängen när du konfigurerar inställningarna för appen.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Skärm bild av hur du hämtar en anslutnings sträng för lagrings konton.":::

## <a name="create-a-queue"></a>Skapa en kö

Skapa kön där din Azure Functions Service Bus-utlösare kommer att hämta händelser:

1. I din Service Bus, i menyn till vänster, väljer du **köer**.

1. Välj **principer för delad åtkomst**. I den här själv studie kursen namnger du List *kön*.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Skärm bild av hur du skapar en Service Bus-kö.":::

1. Välj **Skapa**.

## <a name="get-a-service-bus-connection-string"></a>Hämta en anslutnings sträng för Service Bus

1. I din Service Bus går du till menyn till vänster och väljer **principer för delad åtkomst**.

1. Välj **RootManageSharedAccessKey**. Kopiera och spara den **primära anslutnings strängen**. Du behöver den här anslutnings strängen när du konfigurerar inställningarna för appen.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Skärm bild av hur du hämtar en anslutnings sträng för Service Bus.":::

## <a name="integrate-the-function-app"></a>Integrera Function-appen

Om du vill använda din Function-app med virtuella nätverk måste du ansluta den till ett undernät. Du använder ett speciellt undernät för integrering av Azure Functions virtuella nätverk. Du använder standard under nätet för andra privata slut punkter som du skapar i den här självstudien.

1. I din Function-app går du till menyn till vänster och väljer **nätverk**.

1. Under **VNet-integrering** väljer **du klicka här för att konfigurera**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Skärm bild av hur du går till integrering med virtuella nätverk.":::

1. Välj **Lägg till VNet**.

1. Under **Virtual Network** väljer du det virtuella nätverk som du skapade tidigare.

1. Välj under nätet för **funktioner** som du skapade tidigare. Din Function-app är nu integrerad med ditt virtuella nätverk!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Skärm bild av hur du ansluter en Function-app till ett undernät.":::

## <a name="configure-your-function-app-settings"></a>Konfigurera inställningar för Function-appen

1. I din Function-app går du till menyn till vänster och väljer **konfiguration**.

1. Om du vill använda din Function-app med virtuella nätverk uppdaterar du inställningarna för appen som visas i följande tabell. Om du vill lägga till eller redigera en inställning väljer du **+ ny program inställning** eller ikonen **Redigera** i kolumnen längst till höger i tabellen app-inställningar. När du är klar väljer du **Spara**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Skärm bild av hur du konfigurerar funktions program inställningar för privata slut punkter.":::

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Anslutnings strängen för det lagrings konto som du har skapat. Anslutnings strängen för lagrings kontot hämtas från avsnittet [Hämta anslutnings strängen för lagrings kontot](#get-the-storage-account-connection-string) . Den här inställningen tillåter att din Function-app använder det säkra lagrings kontot för normala åtgärder vid körning. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Anslutnings strängen för det lagrings konto som du har skapat. Med den här inställningen kan din Function-app använda det säkra lagrings kontot för Azure Files, som används under distributionen. |
    | **WEBSITE_CONTENTSHARE** | filer | Namnet på fil resursen som du skapade i lagrings kontot. Använd den här inställningen med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Skapa den här appens inställning för anslutnings strängen för din Service Bus. Den här anslutnings strängen för lagringen hämtas från avsnittet [Hämta en anslutnings sträng för Service Bus](#get-a-service-bus-connection-string) .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Skapa den här appens inställningen. Värdet 1 gör att din Function-app kan skalas när ditt lagrings konto är begränsat till ett virtuellt nätverk. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Skapa den här appens inställningen. När din app integreras med ett virtuellt nätverk, kommer den att använda samma DNS-server som det virtuella nätverket. Din funktions app behöver den här inställningen så att den fungerar med Azure DNS privata zoner. Det krävs när du använder privata slut punkter. Den här inställningen och WEBSITE_VNET_ROUTE_ALL skickar alla utgående samtal från din app till ditt virtuella nätverk. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Skapa den här appens inställningen. När din app integreras med ett virtuellt nätverk använder den samma DNS-server som det virtuella nätverket. Din funktions app behöver den här inställningen så att den fungerar med Azure DNS privata zoner. Det krävs när du använder privata slut punkter. Den här inställningen och WEBSITE_DNS_SERVER skickar alla utgående samtal från din app till ditt virtuella nätverk. |

1. I vyn **konfiguration** väljer du fliken **funktion körnings inställningar** .

1. Ange **körnings skalnings övervakning** till **på**. Välj sedan **Spara**. Med körnings driven skalning kan du ansluta funktioner som inte är HTTP-utlösare till tjänster som körs i det virtuella nätverket.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Skärm bild av hur du aktiverar körnings driven skalning för Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Distribuera en Service Bus-utlösare och HTTP-utlösare

1. I GitHub går du till följande exempel lagrings plats. Den innehåller en Function-app och två funktioner, en HTTP-utlösare och en utlösare för Service Bus-kön.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Längst upp på sidan väljer du **förgrening** för att skapa en förgrening av den här lagrings platsen i ditt eget GitHub-konto eller organisation.

1. I din Function-app går du till menyn till vänster och väljer **Deployment Center**. Välj sedan **Inställningar**.

1. På fliken **Inställningar** använder du de distributions inställningar som visas i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Källa** | GitHub | Du bör ha skapat en GitHub-lagringsplats för exempel koden i steg 2. | 
    | **Organisation**  | Organisation | Organisationen som din lagrings platsen checkas in på. Det är vanligt vis ditt konto. |
    | **Lagringsplats** | myRepo | Den lagrings plats som du skapade för exempel koden. |
    | **Gren** | main | Huvud grenen för den lagrings plats som du har skapat. |
    | **Körningsstack** | .NET | Exempel koden finns i C#. |

1. Välj **Spara**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Skärm bild av hur du distribuerar Azure Functions kod via portalen.":::

1. Den första distributionen kan ta några minuter. När din app har distribuerats på fliken **loggar** visas status meddelandet **lyckad (aktiv)** . Om det behövs uppdaterar du sidan.

Grattis! Du har distribuerat exempel funktions programmet.

## <a name="lock-down-your-function-app"></a>Lås din Function-app

Skapa nu den privata slut punkten för att låsa din Function-app. Den här privata slut punkten ansluter din funktions app privat och säkert till ditt virtuella nätverk genom att använda en privat IP-adress. 

Mer information finns i dokumentationen för den [privata slut punkten](../private-link/private-endpoint-overview.md).

1. I din Function-app går du till menyn till vänster och väljer **nätverk**.

1. Under **anslutningar för privata slut punkter** väljer **du konfigurera dina privata slut punkts anslutningar**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Skärm bild av hur du navigerar till en privat slut punkt för Function-appar.":::

1. Välj **Lägg till**.

1. Använd följande inställningar för privata slut punkter i fönstret som öppnas:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Skärm bild av hur du skapar en privat slut punkt för Function-appar. Namnet är functionapp-Endpoint. Prenumerationen är &quot;privat test sub CACHHAI&quot;. Det virtuella nätverket är MyVirtualNet – självstudier. Under nätet är standard.":::

1. Välj **OK** för att lägga till den privata slut punkten. 
 
Grattis! Du har skyddat din Function-app, Service Bus och lagrings konto genom att lägga till privata slut punkter!

### <a name="test-your-locked-down-function-app"></a>Testa din låsta Function-app

1. I din Function-app går du till menyn till vänster och väljer **funktioner**.

1. Välj **ServiceBusQueueTrigger**.

1. Välj **övervaka** i menyn till vänster. 
 
Du ser att du inte kan övervaka din app. Din webbläsare har inte åtkomst till det virtuella nätverket, så den kan inte direkt komma åt resurser i det virtuella nätverket. 
 
Här är ett alternativt sätt att övervaka din funktion med hjälp av Application Insights:

1. I din Function-app går du till menyn till vänster och väljer **Application Insights**. Välj sedan **visa Application Insights data**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Skärm bild av hur du visar Application Insights för en Function-app.":::

1. Välj **Live Metrics** i menyn till vänster.

1. Öppna en ny flik. I din Service Bus, i menyn till vänster, väljer du **köer**.

1. Välj din kö.

1. Välj **Service Bus Explorer** i menyn till vänster. Under **Skicka**, för **innehålls typ**, väljer du **text/plain**. Ange sedan ett meddelande. 

1. Välj **Skicka** för att skicka meddelandet.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Skärm bild av hur du skickar Service Bus-meddelanden med hjälp av portalen.":::

1. På fliken **Live-mått** bör du se att Service Bus-köns utlösare har utlöst. Om den inte har det kan du skicka meddelandet från **Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Skärm bild av hur du visar meddelanden med hjälp av Live-mått för Function-appar.":::

Grattis! Du har testat installationen av Function-appen med privata slut punkter.

## <a name="understand-private-dns-zones"></a>Förstå privata DNS-zoner
Du har använt en privat slut punkt för att ansluta till Azure-resurser. Du ansluter till en privat IP-adress i stället för den offentliga slut punkten. Befintliga Azure-tjänster konfigureras för att använda en befintlig DNS för att ansluta till den offentliga slut punkten. Du måste åsidosätta DNS-konfigurationen för att ansluta till den privata slut punkten.

En privat DNS-zon skapas för varje Azure-resurs som har kon figurer ATS med en privat slut punkt. En DNS-post skapas för varje privat IP-adress som är associerad med den privata slut punkten.

Följande DNS-zoner skapades i den här självstudien:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Premium Function-app, ett lagrings konto och en Service Bus. Du har skyddat alla dessa resurser bakom privata slut punkter. 

Använd följande länkar om du vill veta mer om tillgängliga nätverksfunktioner:

> [!div class="nextstepaction"]
> [Nätverks alternativ i Azure Functions](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Azure Functions Premium-plan](./functions-premium-plan.md)