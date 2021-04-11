---
title: Kontroll Azure Functions utgående IP-adress med en Azure Virtual Network NAT-gateway
description: En steg-för-steg-självstudie som visar hur du konfigurerar NAT för en funktion som är ansluten till ett virtuellt Azure-nätverk
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658246"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Självstudie: kontrol lera Azure Functions utgående IP-adress med en Azure Virtual Network NAT-gateway

Virtuella Network Address Translation (NAT) fören klar endast utgående Internet anslutning för virtuella nätverk. När den konfigureras i ett undernät använder all utgående anslutning dina angivna statiska offentliga IP-adresser. En NAT kan vara användbar för Azure Functions eller Web Apps som behöver använda en tjänst från tredje part som använder en tillåten IP-adress som säkerhets åtgärd. Mer information finns i [Vad är Virtual Network NAT?](../virtual-network/nat-overview.md).

Den här självstudien visar hur du använder virtuella nätverks-NAT för att dirigera utgående trafik från en HTTP-utlöst funktion. Med den här funktionen kan du kontrol lera sin egen utgående IP-adress. Under den här självstudien får du:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en app för Premium Plans funktion
> * Skapa en offentlig IP-adress
> * Skapa en NAT-gateway
> * Konfigurera Function-appen för att dirigera utgående trafik via NAT-gatewayen

## <a name="topology"></a>Topologi

Följande diagram visar arkitekturen för den lösning som du skapar:

![GRÄNSSNITT för NAT-gateway-integrering](./media/functions-how-to-use-nat-gateway/topology.png)

Funktioner som körs i Premium-planen har samma värd funktioner som webbappar i Azure App Service, vilket omfattar funktionen VNet-integrering. Mer information om VNet-integrering, inklusive fel sökning och Avancerad konfiguration finns i [integrera din app med ett virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien är det viktigt att du förstår IP-adressering och undernät. Du kan börja med [den här artikeln som beskriver grunderna för adressering och undernät](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor är tillgängliga online.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du redan har slutfört guiden [integrera med ett virtuellt nätverk i Azure](./functions-create-vnet.md) kan du gå vidare till [skapa en http-utlösnings funktion](#create-function).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. På Azure Marketplace väljer du **nätverk**  >  **virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du de inställningar som anges i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Name | Ange *myResourceGroup-VNet*. |
    | Location | Välj **USA, östra**.|

1. Välj **Nästa: IP-adresser** och för **IPv4-adress utrymme** anger du *10.10.0.0/16*.

1. Välj **Lägg till undernät** och ange sedan *självstudie – net* för **under nätets namn** och *10.10.1.0/24* för **under nätets adress intervall**.

    ![Fliken IP-adresser för att skapa ett VNet](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Välj **Lägg till** och välj sedan **Granska + skapa**. Lämna resten som standard och välj **skapa**.

1. I **Skapa virtuellt nätverk** väljer du **skapa**.

Därefter skapar du en Function-app i [Premium-planen](functions-premium-plan.md). Den här planen ger en server lös skalning utan stöd för integrering av virtuella nätverk.

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en Function-app i en Premium-plan

> [!NOTE]  
> För den bästa upplevelsen i den här självstudien väljer du .NET för körnings stack och väljer Windows för operativ system. Du kan också skapa en Function-app i samma region som ditt virtuella nätverk.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Anslut din Function-app till det virtuella nätverket

Nu kan du ansluta din Function-app till det virtuella nätverket.

1. I din Function-app väljer du **nätverk** i den vänstra menyn och väljer sedan **Konfigurera** i **VNet-integrering**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Välj nätverk i Function-appen":::

1. På sidan **VNet-integration** väljer du **Lägg till VNet**.

1. I **nätverks funktions status** använder du inställningarna i tabellen under bilden:

    ![Definiera funktions programmet virtuellt nätverk](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-VNet | Det här virtuella nätverket är det som du skapade tidigare. |
    | **Undernät** | Skapa nytt undernät | Skapa ett undernät i det virtuella nätverket som din Function-app ska använda. VNet-integrering måste konfigureras för att använda ett tomt undernät. |
    | **Namn på undernät** | Function-Net | Namnet på det nya undernätet. |
    | **Adress block för virtuellt nätverk** | 10.10.0.0/16 | Du bör bara ha ett definierat adress block. |
    | **Adress block för undernät** | 10.10.2.0/24   | Under näts storleken begränsar det totala antalet instanser som din Premium plan Function-app kan skala ut till. I det här exemplet används ett `/24` undernät med 254 tillgängliga värd adresser. Det här under nätet är överallokerat, men enkelt att beräkna. |

1. Välj **OK** för att lägga till under nätet. Stäng sidan **VNet-integrering** och **status för nätverks funktions** sidan för att återgå till din Function-app-sida.

Function-appen kan nu komma åt det virtuella nätverket. Sedan lägger du till en HTTP-utlöst funktion i Function-appen.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Skapa en HTTP-utlösnings funktion

1. På den vänstra menyn i fönstret **funktioner** väljer du **Functions** och väljer sedan **Lägg till** på den översta menyn. 
 
1. I fönstret **ny funktion** väljer du **http-utlösare** och godkänner standard namnet för **ny funktion** eller anger ett nytt namn. 

1. I **kod + test** ersätter du den mall-genererade C#-skript koden (. CSX) med följande kod: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Den här koden anropar en extern webbplats som returnerar anroparens IP-adress, som i det här fallet är den här funktionen. Med den här metoden kan du enkelt avgöra vilken utgående IP-adress som används av din Function-app.

Nu är du redo att köra funktionen och kontrol lera de aktuella utgående IP-adresserna.

## <a name="verify-current-outbound-ips"></a>Verifiera aktuella utgående IP-adresser

Nu kan du köra funktionen. Men börja med att kontrol lera i portalen och se vilka utgående IP-adresser som används av Function-appen.  

1. I din Function-app väljer du **Egenskaper** och granskar fältet **utgående IP-adresser** .

    ![Visa funktion app utgående IP-adresser](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Gå tillbaka till funktionen HTTP-utlösare, Välj **kod + test** och sedan **testa/kör**.

    ![Test funktion](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Välj **Kör** för att köra funktionen och växla sedan till **utdata**. 

    ![Utdata för test funktion](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Kontrol lera att IP-adressen i HTTP-svars texten är ett av värdena från de utgående IP-adresser som du visade tidigare.

Nu kan du skapa en offentlig IP-adress och använda en NAT-gateway för att ändra den utgående IP-adressen.

## <a name="create-public-ip"></a>Skapa offentlig IP

1. Välj **Lägg till** i resurs gruppen, Sök på Azure Marketplace efter **offentlig IP-adress** och välj **skapa**. Använd inställningarna i tabellen under bilden:

    ![Skapa offentlig IP-adress](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Inställning      | Föreslaget värde  |
    | ------------ | ---------------- |
    | **IP-version** | IPv4 |
    | **SKU** | Standard |
    | **Nivå** | Regional |
    | **Namn** | Utgående IP |
    | **Prenumeration** | Se till att din prenumeration visas | 
    | **Resursgrupp** | myResourceGroup (eller namn som du har tilldelat till din resurs grupp) |
    | **Plats** | USA, östra (eller plats som du har tilldelat till andra resurser) |
    | **Tillgänglighetszon** | Ingen zon |

1. Välj **skapa** för att skicka distributionen.

1. När distributionen är klar navigerar du till den offentliga IP-adressresurs som du har skapat och visar IP-adressen i **översikten**.

    ![Visa offentlig IP-adress](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Skapa NAT-gateway

Nu ska vi skapa NAT-gatewayen. När du börjar med den [tidigare guiden för virtuella nätverk](functions-create-vnet.md) `Function-Net` var det föreslagna under näts namnet och `MyResourceGroup-vnet` var det föreslagna virtuella nätverks namnet i den själv studie kursen.

1. Välj **Lägg till** i resurs gruppen, Sök på Azure Marketplace för **NAT-gateway** och välj **skapa**. Använd inställningarna i tabellen under bilden för att fylla i fliken **grundläggande** :

    ![Skapa NAT-gateway](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Inställning      | Föreslaget värde  |
    | ------------ | ---------------- |  
    | **Prenumeration** | Din prenumeration | 
    | **Resursgrupp** | myResourceGroup (eller namn som du har tilldelat till din resurs grupp) |
    | **Namn på NAT-gateway** | myNatGateway |
    | **Region** | USA, östra (eller plats som du har tilldelat till andra resurser) |
    | **Tillgänglighetszon** | Inget |

1. Välj **Nästa: utgående IP-adress**. I fältet **offentliga IP-adresser** väljer du den tidigare skapade offentliga IP-adressen. Lämna **prefix för offentliga IP-prefix** omarkerade.

1. Välj **Nästa: undernät**. Välj resursen *myResourceGroup-VNet* i fältet för det **virtuella nätverket** och *funktion-net-* undernätet.

    ![Välj undernät](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Välj **Granska + skapa** och sedan **skapa** för att skicka distributionen.

När distributionen är klar är NAT-gatewayen redo att dirigera trafik från ditt Function app-undernät till Internet.

## <a name="update-function-configuration"></a>Uppdatera funktions konfiguration

Nu måste du lägga till en program inställning `WEBSITE_VNET_ROUTE_ALL` som är inställd på värdet `1` .  Den här inställningen tvingar utgående trafik via det virtuella nätverket och kopplad NAT-gateway. Utan den här inställningen dirigeras inte Internet trafik via det integrerade virtuella nätverket och du ser samma utgående IP-adresser. 

1. Navigera till din Function-app i Azure Portal och välj **konfiguration** på menyn till vänster.

1. Under **program inställningar** väljer du **+ ny program inställning** och slutför Använd följande värden för att fylla i fälten:

    |Fältnamn  |Värde |
    |---|---|
    |**Namn**    |WEBSITE_VNET_ROUTE_ALL|
    |**Värde**   |1|

1. Stäng dialog rutan ny program inställning genom att klicka på **OK** .

1. Välj **Spara** och **Fortsätt** sedan att spara inställningarna.

Function-appen har nu kon figurer ATS för att dirigera trafik via dess associerade virtuella nätverk.

## <a name="verify-new-outbound-ips"></a>Verifiera nya utgående IP-adresser

Upprepa [stegen ovan](#verify-current-outbound-ips) för att köra funktionen igen. Nu bör du se den utgående IP-adress som du konfigurerade i NAT-filen som visas i funktions resultatet.

## <a name="clean-up-resources"></a>Rensa resurser

Du har skapat resurser för att slutföra den här kursen. Du debiteras för de här resurserna, beroende på din [konto status](https://azure.microsoft.com/account/) och [service prissättning](https://azure.microsoft.com/pricing/). För att undvika extra kostnader tar du bort resurserna när du vet att de behövs längre. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Nätverksalternativ för Azure Functions](functions-networking-options.md)
