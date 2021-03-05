---
title: Använd privata slut punkter för att integrera Azure Functions med ett virtuellt nätverk
description: En steg-för-steg-självstudie som visar hur du ansluter en funktion till ett virtuellt Azure-nätverk och låser den med privata slut punkter
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200214"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Självstudie: integrera Azure Functions med ett virtuellt Azure-nätverk med privata slut punkter

Den här självstudien visar hur du använder Azure Functions för att ansluta till resurser i ett virtuellt Azure-nätverk med privata slut punkter. Du skapar en funktion med ett lagrings konto som är låst bakom ett virtuellt nätverk som använder en Service Bus-kö.

> [!div class="checklist"]
> * Skapa en Function-app i Premium-planen
> * Skapa Azure-resurser (Service Bus, lagrings konto Virtual Network)
> * Lås ditt lagrings konto bakom en privat slut punkt
> * Lås Service Bus bakom en privat slut punkt
> * Distribuera en Function-app med både Service Bus-och HTTP-utlösare.
> * Lås din Function-app bakom en privat slut punkt
> * Testa att se att din Function-app är säker bakom det virtuella nätverket
> * Rensa resurser

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en Function-app i en Premium-plan

Först skapar du en .NET Function-app i [Premium-planen] eftersom den här självstudien använder C#. Andra språk stöds också i Windows. Den här planen ger en server lös skalning utan stöd för integrering av virtuella nätverk.

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. På den **nya** sidan väljer du **Compute**  >  **Funktionsapp**.

1. På sidan **grundläggande** inställningar använder du funktionen appinställningar som anges i följande tabell:

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. |
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Funktionsappens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.  |
    |**Publicera**| Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | .NET | I den här självstudien används .NET |
    |**Region**| Önskad region | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner kommer åt. |

1. Välj **Nästa: värd**. Ange följande inställningar på sidan **värd** :

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Lagringskonto](../storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](./storage-considerations.md#storage-account-requirements). |
    |**Operativsystem**| Windows | I den här självstudien används Windows |
    | **[Planera](./functions-scale.md)** | Premium | Värdplan som definierar hur resurser allokeras till din funktionsapp. Välj **Premium**. Som standard skapas en ny App Service plan. Standard- **SKU och-storlek** är **EP1**, där EP står för _elastisk Premium_. Mer information finns i [listan över Premium-SKU: er](./functions-premium-plan.md#available-instance-skus).<br/>När du kör JavaScript-funktioner i en Premium-plan bör du välja en instans som har färre virtuella processorer. Mer information finns i [Välj Premium-](./functions-reference-node.md#considerations-for-javascript-functions)prenumerationer med en kärna.  |

1. Välj **Nästa: övervakning**. Ange följande inställningar på sidan **övervakning** :

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Standardvärde | Skapar en Application Insights resurs av samma *app-namn* i den närmaste region som stöds. Genom att utöka den här inställningen kan du ändra det **nya resurs namnet** eller välja en annan plats i ett [Azure-geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) **område** för att lagra dina data. |

1. Välj **Granska + skapa** för att granska konfigurations valen för appen.

1. På sidan **Granska + skapa** granskar du inställningarna och väljer sedan **skapa** för att etablera och distribuera Function-appen.

1. Välj **aviserings** ikonen i det övre högra hörnet i portalen och titta efter ett meddelande om att **distributionen har slutförts** .

1. Välj **Gå till resurs** att visa den nya funktionsappen. Du kan också välja **Fäst vid instrument panelen**. Genom att fästa blir det enklare att återgå till den här funktions program resursen från instrument panelen.

1. Grattis! Du har skapat Premium Function-appen!

## <a name="create-azure-resources"></a>Skapa Azure-resurser

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Ett separat lagrings konto från det som skapades i den första skapandet av din Function-app krävs för virtuella nätverk.

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. På sidan ny söker du efter **lagrings konto** och väljer **skapa**

1. På fliken **grundläggande** inställningar anger du inställningarna som anges i tabellen nedan. Resten kan lämnas som standard:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj den resurs grupp som du skapade med din Function-app. |
    | **Namn** | mysecurestorage| Namnet på ditt lagrings konto som den privata slut punkten ska tillämpas på. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Välj den region som du skapade din Function-app i. |

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

### <a name="create-a-service-bus"></a>Skapa en Service Bus

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**.

1. På sidan ny söker du efter **Service Bus** och väljer **skapa**.

1. På fliken **grundläggande** inställningar anger du inställningarna som anges i tabellen nedan. Resten kan lämnas som standard:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. |
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj den resurs grupp som du skapade med din Function-app. |
    | **Namn** | myServiceBus| Namnet på din Service Bus som den privata slut punkten ska tillämpas på. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Välj den region som du skapade din Function-app i. |
    | **Prisnivå** | Premium | Välj den här nivån om du vill använda privata slut punkter med Service Bus. |

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Azure-resurser i den här självstudien integreras antingen med eller placeras i ett virtuellt nätverk. Du använder privata slut punkter för att hålla nätverks trafiken i det virtuella nätverket.

I självstudien skapas två undernät:
- **standard**: undernät för privata slut punkter. Privata IP-adresser anges från det här under nätet.
- **Functions**: undernät för Azure Functions virtuell nätverks integrering. Det här under nätet är delegerat till Function-appen.

Skapa nu det virtuella nätverk som Function-appen ska integreras med.

1. I menyn i Azure-portalen eller på sidan Start väljer du **Skapa en resurs**.

1. På sidan ny söker du efter **Virtual Network** och väljer **skapa**.

1. På fliken **grundläggande** inställningar använder du de virtuella nätverks inställningarna som anges nedan:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj den resurs grupp som du skapade med din Function-app. |
    | **Namn** | myVirtualNet| Namnet på ditt virtuella nätverk som din Function-app ska ansluta till. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Välj den region som du skapade din Function-app i. |

1. På fliken **IP-adresser** väljer du **Lägg till undernät**. Använd de inställningar som anges nedan när du lägger till ett undernät:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Skärm bild av vyn Skapa konfiguration för virtuellt nätverk.":::

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn på undernät** | funktionen | Namnet på under nätet som Function-appen kommer att ansluta till. | 
    | **Adressintervall för undernätet** | 10.0.1.0/24 | Observera att vårt IPv4-adress utrymme i bilden ovan är 10.0.0.0/16. Om ovanstående var 10.1.0.0/16 skulle det rekommenderade *adress intervallet för under nätet* vara 10.1.1.0/24. |

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Lås ditt lagrings konto med privata slut punkter

Privata Azure-slutpunkter används för att ansluta till vissa Azure-resurser med en privat IP-adress. Den här anslutningen säkerställer att nätverks trafiken förblir i det valda virtuella nätverket och att åtkomst endast är tillgängligt för vissa resurser. Skapa nu privata slut punkter för Azure File Storage och Azure Blob Storage med ditt lagrings konto.

1. I ditt nya lagrings konto väljer du **nätverk** på den vänstra menyn.

1. Välj fliken **anslutningar för privata slut punkter** och välj **privat slut punkt**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Skärm bild av hur du navigerar för att skapa privata slut punkter för lagrings kontot.":::

1. På fliken **grundläggande** inställningar använder du inställningarna för privata slut punkter som anges nedan:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj den resurs grupp som du skapade med din Function-app. | |
    | **Namn** | fil slut punkt | Namnet på den privata slut punkten för filer från ditt lagrings konto. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Välj den region som du skapade ditt lagrings konto i. |

1. På fliken **resurs** använder du inställningarna för privata slut punkter som anges nedan:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **Resurstyp**  | Microsoft. Storage/storageAccounts | Detta är resurs typen för lagrings konton. |
    | **Resurs** | mysecurestorage | Det lagrings konto som du nyss skapade |
    | **Målunderresurs** | file | Den här privata slut punkten kommer att användas för filer från lagrings kontot. |

1. Välj **standard** för under näts inställningen på fliken **konfiguration** .

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Resurser i det virtuella nätverket kan nu prata med Storage-filer.

1. Skapa en annan privat slut punkt för blobbar. På fliken **resurser** använder du inställningarna nedan. För alla andra inställningar använder du samma inställningar från filen för att skapa filer med privat slut punkt som du precis följt av.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **Resurstyp**  | Microsoft. Storage/storageAccounts | Detta är resurs typen för lagrings konton. |
    | **Resurs** | mysecurestorage | Det lagrings konto som du nyss skapade |
    | **Målunderresurs** | blob | Den här privata slut punkten kommer att användas för blobbar från lagrings kontot. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Lås din Service Bus med en privat slut punkt

Skapa nu den privata slut punkten för Azure Service Bus.

1. I din nya Service Bus väljer du **nätverk** på den vänstra menyn.

1. Välj fliken **anslutningar för privata slut punkter** och välj **privat slut punkt**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Skärm bild av hur du navigerar till privata slut punkter för Service Bus.":::

1. På fliken **grundläggande** inställningar använder du inställningarna för privata slut punkter som anges nedan:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj den resurs grupp som du skapade med din Function-app. |
    | **Namn** | SB-slutpunkt | Namnet på den privata slut punkten för filer från ditt lagrings konto. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Välj den region som du skapade ditt lagrings konto i. |

1. På fliken **resurs** använder du inställningarna för privata slut punkter som anges nedan:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **Resurstyp**  | Microsoft. Service Bus/namnrymder | Detta är resurs typen för Service Bus. |
    | **Resurs** | myServiceBus | Service Bus som du skapade tidigare i självstudien. |
    | **Mål under resurs** | namnområde | Den här privata slut punkten kommer att användas för namn området från Service Bus. |

1. Välj **standard** för under näts inställningen på fliken **konfiguration** .

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Resurser i det virtuella nätverket kan nu kommunicera med Service Bus.

## <a name="create-a-file-share"></a>Skapa en filresurs

1. I det lagrings konto som du har skapat väljer du **fil resurser** på den vänstra menyn.

1. Välj **+ fil resurser**. Ange **filer** som fil resursens namn i den här självstudien.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Skärm bild av hur du skapar en fil resurs i lagrings kontot.":::

## <a name="get-storage-account-connection-string"></a>Hämta anslutnings sträng för lagrings konto

1. I det lagrings konto som du har skapat väljer du **åtkomst nycklar** i den vänstra menyn.

1. Välj **Visa nycklar**. Kopiera anslutnings strängen för KEY1 och spara den. Vi behöver den här anslutnings strängen senare när du konfigurerar inställningarna för appen.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Skärm bild av hur du hämtar en anslutnings sträng för lagrings konton.":::

## <a name="create-a-queue"></a>Skapa en kö

Detta är den kö som Azure Functions Service Bus-utlösaren kommer att hämta händelser från.

1. I Service Bus väljer du **köer** på den vänstra menyn.

1. Välj **principer för delad åtkomst**. Ange **kö** som namn för kön i den här självstudien.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Skärm bild av hur du skapar en Service Bus-kö.":::

## <a name="get-service-bus-connection-string"></a>Hämta anslutnings sträng för Service Bus

1. I Service Bus väljer du **principer för delad åtkomst** på den vänstra menyn.

1. Välj **RootManageSharedAccessKey**. Kopiera den **primära anslutnings strängen** och spara den. Vi behöver den här anslutnings strängen senare när du konfigurerar inställningarna för appen.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Skärm bild av hur du hämtar en anslutnings sträng för Service Bus.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Integrera Function-appen med ditt virtuella nätverk

Om du vill använda din Function-app med virtuella nätverk måste du ansluta den till ett undernät. Vi använder ett särskilt undernät för Azure Functions virtuell nätverks integrering och standard under nätet för alla andra privata slut punkter som skapas i den här självstudien.

1. I din Function-app väljer du **nätverk** på den vänstra menyn.

1. Välj **Klicka här för att konfigurera** under VNet-integrering.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Skärm bild av hur du navigerar till integrering med virtuella nätverk.":::

1. Välj **Lägg till VNet**

1. På bladet som öppnas under **Virtual Network** väljer du det virtuella nätverk som du skapade tidigare.

1. Välj **under nätet** som vi skapade tidigare anropade **funktioner**. Din Function-app är nu integrerad med ditt virtuella nätverk!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Skärm bild av hur du ansluter en Function-app till ett undernät.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Konfigurera dina funktions program inställningar för privata slut punkter

1. I din Function-app väljer du **konfiguration** på den vänstra menyn.

1. Följande appinställningar måste uppdateras för att du ska kunna använda din Function-app med virtuella nätverk. Välj **+ ny program inställning** eller blyertspennan genom att **Redigera** i kolumnen längst till höger i tabellen App Settings efter behov. Välj **Spara** när du är klar.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Skärm bild av hur du konfigurerar funktions program inställningar för privata slut punkter.":::

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Anslutnings strängen för det lagrings konto som du har skapat. Det här är lagrings anslutnings strängen från [Hämta lagrings kontots anslutnings sträng](#get-storage-account-connection-string). Genom att ändra den här inställningen använder din Function-app nu det säkra lagrings kontot för normala åtgärder vid körning. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Anslutnings strängen för det lagrings konto som du har skapat. Genom att ändra den här inställningen använder din Function-app nu det säkra lagrings kontot för Azure Files, som används vid distribution. |
    | **WEBSITE_CONTENTSHARE** | filer | Namnet på fil resursen som du skapade i lagrings kontot. Den här appens inställning används tillsammans med WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Skapa en app-inställning för anslutnings strängen för din Service Bus. Det här är lagrings anslutnings strängen från [Hämta Service Bus-anslutningssträng](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Skapa den här appens inställningen. Värdet 1 gör att din Function-app kan skalas när ditt lagrings konto är begränsat till ett virtuellt nätverk. Du bör aktivera den här inställningen när du begränsar ditt lagrings konto till ett virtuellt nätverk. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Skapa den här appens inställningen. När din app integreras med ett virtuellt nätverk, kommer den att använda samma DNS-server som det virtuella nätverket. Det här är en av två inställningar som krävs för att din Function-app ska fungera med Azure DNS privata zoner och krävs när du använder privata slut punkter. De här inställningarna kommer att skicka alla utgående samtal från din app till ditt virtuella nätverk. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Skapa den här appens inställningen. När din app integreras med ett virtuellt nätverk, kommer den att använda samma DNS-server som det virtuella nätverket. Det här är en av två inställningar som krävs för att din Function-app ska fungera med Azure DNS privata zoner och krävs när du använder privata slut punkter. De här inställningarna kommer att skicka alla utgående samtal från din app till ditt virtuella nätverk. |

1. Håll dig uppdaterad i vyn **konfiguration** och välj fliken **funktion runtime-inställningar** .

1. Ange **körnings skalnings övervakning** till **på** och välj **Spara**. Med runtime drived skalning kan du ansluta funktioner som inte är HTTP-utlösare till tjänster som körs i det virtuella nätverket.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Skärm bild av hur du aktiverar körnings driven skalning för Azure Functions.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Distribuera en Service Bus-utlösare och http-utlösare till din Function-app

1. I GitHub bläddrar du till följande exempel lagrings plats, som innehåller en Function-app med två funktioner, en HTTP-utlösare och en Service Bus Queue-utlösare.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Klicka på knappen **förgrening** längst upp på sidan för att skapa en förgrening av den här lagrings platsen i ditt eget GitHub-konto eller organisation.

1. I din Function-app väljer du **Deployment Center** på den vänstra menyn. Välj sedan **Inställningar**.

1. På fliken **Inställningar** använder du distributions inställningarna som anges nedan:

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Källa** | GitHub | Du borde ha skapat en GitHub-lagrings platsen med exempel koden i steg 2. | 
    | **Organisation**  | Organisation | Det här är organisationen som din lagrings platsen checkas in i, vanligt vis ditt konto. |
    | **Lagringsplats** | myRepo | Lagrings platsen som du skapade med exempel koden. |
    | **Gren** | main | Det här är den lagrings platsen som du nyss skapade, så Använd huvud grenen. |
    | **Körningsstack** | .NET | Exempel koden finns i C#. |

1. Välj **Spara**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Skärm bild av hur du distribuerar Azure Functions kod via portalen.":::

1. Den första distributionen kan ta några minuter. Ett **lyckat (aktivt)** status meddelande visas på fliken **loggar** när appen har distribuerats. Om det behövs uppdaterar du sidan. 

1. Grattis! Vi har distribuerat exempel funktions programmet.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Lås din funktions app med en privat slut punkt

Skapa nu den privata slut punkten för din Function-app. Den här privata slut punkten ansluter din funktions app privat och säkert till ditt virtuella nätverk med en privat IP-adress. Mer information om privata slut punkter finns i [dokumentationen för privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. I din Function-app väljer du **nätverk** på den vänstra menyn.

1. Välj **Klicka här för att konfigurera** under privata slut punkts anslutningar.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Skärm bild av hur du navigerar till en Funktionsapp privat slut punkt.":::

1. Välj **Lägg till**.

1. På menyn som öppnas, använder du inställningarna för den privata slut punkten som anges nedan:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Skärm bild av hur du skapar en Funktionsapp privat slut punkt.":::

1. Välj **OK** för att lägga till den privata slut punkten. Grattis! Du har skyddat din Function-app, Service Bus och lagrings konto med privata slut punkter!

### <a name="test-your-locked-down-function-app"></a>Testa din låsta Function-app

1. I din Function-app väljer du **Functions** i den vänstra menyn.

1. Välj **ServiceBusQueueTrigger**.

1. På den vänstra menyn väljer du **övervaka**. du ser att du inte kan övervaka din app. Det beror på att din webbläsare inte har åtkomst till det virtuella nätverket, så den kan inte komma åt resurser i det virtuella nätverket direkt. Nu visar vi en annan metod som gör att du fortfarande kan övervaka din funktion, Application Insights.

1. I din Function-app väljer du **Application Insights** på menyn till vänster och väljer **Visa Application Insights data**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Skärm bild av hur du visar Application Insights för en Funktionsapp.":::

1. Välj **Live-mått** på den vänstra menyn.

1. Öppna en ny flik. I Service Bus väljer du **köer** på den vänstra menyn.

1. Välj din kö.

1. Välj **Service Bus Explorer** på den vänstra menyn. Under **Skicka** väljer du **text/oformaterad** som **innehålls typ** och anger ett meddelande. 

1. Välj **Skicka** för att skicka meddelandet.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Skärm bild av hur du skickar Service Bus meddelanden med hjälp av portalen.":::

1. På fliken med **Live-måtten** öppen bör du se att utlösaren för Service Bus kö har utlöst. Om den inte har det kan du skicka meddelandet från **Service Bus Explorer**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Skärm bild av hur du visar meddelanden med Live-mått för Function-appar.":::

1. Grattis! Du har testat din funktions program konfiguration med privata slut punkter!

### <a name="private-dns-zones"></a>Privat DNS zoner
Att använda en privat slut punkt för att ansluta till Azure-resurser innebär att ansluta till en privat IP-adress i stället för den offentliga slut punkten. Befintliga Azure-tjänster konfigureras för att använda befintlig DNS för att ansluta till den offentliga slut punkten. DNS-konfigurationen måste åsidosättas för att ansluta till den privata slut punkten.

En privat DNS-zon har skapats för varje Azure-resurs som kon figurer ATS med en privat slut punkt. En DNS A-post skapas för varje privat IP-adress som är associerad med den privata slut punkten.

Följande DNS-zoner skapades i den här självstudien:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Premium Function-app, ett lagrings konto och Service Bus och du har skyddat dem alla bakom privata slut punkter! Lär dig mer om de olika nätverks funktionerna som finns nedan:

> [!div class="nextstepaction"]
> [Lär dig mer om nätverks alternativen i functions](./functions-networking-options.md)

[Premiumplan]: functions-premium-plan.md
