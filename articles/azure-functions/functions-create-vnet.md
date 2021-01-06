---
title: Integrera Azure Functions med ett virtuellt Azure-nätverk
description: En steg-för-steg-självstudie som visar hur du ansluter en funktion till ett virtuellt Azure-nätverk
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: efc936111d162d73b1cc5465ae6b677c9006ab32
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937030"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Självstudie: integrera Functions med ett virtuellt Azure-nätverk

Den här självstudien visar hur du använder Azure Functions för att ansluta till resurser i ett virtuellt Azure-nätverk. du skapar en funktion som har åtkomst till både Internet och en virtuell dator som kör WordPress i det virtuella nätverket.

> [!div class="checklist"]
> * Skapa en Function-app i Premium-planen
> * Distribuera en WordPress-webbplats till en virtuell dator i ett virtuellt nätverk
> * Anslut Function-appen till det virtuella nätverket
> * Skapa en Function proxy för att få åtkomst till WordPress-resurser
> * Begär en WordPress-fil inifrån det virtuella nätverket

## <a name="topology"></a>Topologi

Följande diagram visar arkitekturen för den lösning som du skapar:

 ![Användar gränssnitt för integrering av virtuella nätverk](./media/functions-create-vnet/topology.png)

Funktioner som körs i Premium-planen har samma värd funktioner som webbappar i Azure App Service, vilket omfattar funktionen VNet-integrering. Mer information om VNet-integrering, inklusive fel sökning och Avancerad konfiguration finns i [integrera din app med ett virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Krav

I den här självstudien är det viktigt att du förstår IP-adressering och undernät. Du kan börja med [den här artikeln som beskriver grunderna för adressering och undernät](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor är tillgängliga online.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en Function-app i en Premium-plan

Först skapar du en Function-app i [Premium-planen]. Den här planen ger en server lös skalning utan stöd för integrering av virtuella nätverk.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Du kan fästa appens funktion på instrument panelen genom att välja ikonen fäst i det övre högra hörnet. Genom att fästa blir det enklare att återgå till den här funktions appen när du har skapat din virtuella dator.

## <a name="create-a-vm-inside-a-virtual-network"></a>Skapa en virtuell dator i ett virtuellt nätverk

Skapa sedan en förkonfigurerad virtuell dator som kör WordPress i ett virtuellt nätverk ([WordPress LEMP7 Max prestanda](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) per Jetware). En virtuell WordPress-dator används på grund av sin låga kostnad och bekvämlighet. Samma scenario fungerar med alla resurser i ett virtuellt nätverk, till exempel REST-API: er, App Service miljöer och andra Azure-tjänster. 

1. I portalen väljer du **+ skapa en resurs** i det vänstra navigerings fönstret, i Sök fältet typ `WordPress LEMP7 Max Performance` och trycker på RETUR.

1. Välj **WordPress Lemp högsta prestanda** i Sök resultaten. Välj en program plan för **WordPress Lemp högsta prestanda för CentOS** som **program varu plan** och välj **skapa**.

1. På fliken **grundläggande** använder du de VM-inställningar som anges i tabellen nedanför bilden:

    ![Fliken grunder för att skapa en virtuell dator](./media/functions-create-vnet/create-vm-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. | 
    | **[Resurs grupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj `myResourceGroup` eller resurs gruppen som du skapade med din Function-app. Om du använder samma resurs grupp för Function-appen, WordPress VM och värd prenumerationen blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Namn på virtuell dator** | VNET-Wordpress | Det virtuella dator namnet måste vara unikt i resurs gruppen |
    | **[Region](https://azure.microsoft.com/regions/)** | Östeuropa Västeuropa | Välj en region nära dig eller nära de funktioner som har åtkomst till den virtuella datorn. |
    | **Storlek** | B1S | Välj **ändra storlek** och välj sedan B1S Standard avbildning, som har 1 vCPU och 1 GB minne. |
    | **Autentiseringstyp** | Lösenord | Om du vill använda lösenordsautentisering måste du också ange ett **användar namn**, ett säkert **lösen ord** och sedan **Bekräfta lösen ordet**. I den här självstudien behöver du inte logga in på den virtuella datorn om du inte behöver felsöka. |

1. Välj fliken **nätverk** och under Konfigurera virtuella nätverk väljer du **Skapa ny**.

1. I **Skapa virtuellt nätverk** använder du inställningarna i tabellen under bilden:

    ![Fliken nätverk i Create VM](./media/functions-create-vnet/create-vm-2.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myResourceGroup-VNet | Du kan använda standard namnet som genereras för det virtuella nätverket. |
    | **Adressintervall** | 10.10.0.0/16 | Använd ett enda adress intervall för det virtuella nätverket. |
    | **Namn på undernät** | Tutorial-Net | Namnet på under nätet. |
    | **Adress intervall** (undernät) | 10.10.1.0/24   | Under näts storleken definierar hur många gränssnitt som kan läggas till i under nätet. Det här under nätet används av WordPress-webbplatsen.  Ett `/24` undernät tillhandahåller 254-värd adresser. |

1. Välj **OK** för att skapa det virtuella nätverket.

1. Gå tillbaka till fliken **nätverk** och välj **ingen** för **offentlig IP**.

1. Välj fliken **hantering** , sedan i **lagrings konto för diagnostik** väljer du det lagrings konto som du skapade med din Function-app.

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Processen för att skapa virtuella datorer tar några minuter. Den skapade virtuella datorn har bara åtkomst till det virtuella nätverket.

1. När den virtuella datorn har skapats väljer du **gå till resurs** för att visa sidan för din nya virtuella dator och väljer sedan **nätverk** under **Inställningar**.

1. Kontrol lera att det inte finns någon **offentlig IP-adress**. Anteckna den **privata IP-adressen** som du använder för att ansluta till den virtuella datorn från din Function-app.

    ![Nätverks inställningar på den virtuella datorn](./media/functions-create-vnet/vm-networking.png)

Nu har du en WordPress-webbplats som har distribuerats helt i det virtuella nätverket. Den här webbplatsen kan inte nås från det offentliga Internet.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Anslut din Function-app till det virtuella nätverket

Med en WordPress-webbplats som körs i en virtuell dator i ett virtuellt nätverk kan du nu ansluta din Function-app till det virtuella nätverket.

1. I din nya Function-app väljer du **nätverk** på den vänstra menyn.

1. Under **VNet-integrering** väljer **du klicka här för att konfigurera**.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Välj nätverk i Function-appen":::

1. På sidan **VNet-integration** väljer du **Lägg till VNet**.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="Lägg till för hands versionen av VNet-integrering":::

1. I **nätverks funktions status** använder du inställningarna i tabellen under bilden:

    ![Definiera funktions programmet virtuellt nätverk](./media/functions-create-vnet/networking-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-VNet | Det här virtuella nätverket är det som du skapade tidigare. |
    | **Undernät** | Skapa nytt undernät | Skapa ett undernät i det virtuella nätverket som din Function-app ska använda. VNet-integrering måste konfigureras för att använda ett tomt undernät. Det spelar ingen roll att dina funktioner använder ett annat undernät än den virtuella datorn. Det virtuella nätverket dirigerar automatiskt trafik mellan de två under näten. |
    | **Namn på undernät** | Function-Net | Namnet på det nya undernätet. |
    | **Adress block för virtuellt nätverk** | 10.10.0.0/16 | Välj samma adress block som används av WordPress-platsen. Du bör bara ha ett definierat adress block. |
    | **Adressintervall** | 10.10.2.0/24   | Under näts storleken begränsar det totala antalet instanser som din Premium plan Function-app kan skala ut till. I det här exemplet används ett `/24` undernät med 254 tillgängliga värd adresser. Det här under nätet är överallokerat, men enkelt att beräkna. |

1. Välj **OK** för att lägga till under nätet. Stäng sidan **VNet-integrering** och **status för nätverks funktions** sidan för att återgå till din Function-app-sida.

Function-appen kan nu komma åt det virtuella nätverk där WordPress-platsen körs. Sedan använder du [Azure Functions-proxyservrar](functions-proxies.md) för att returnera en fil från WordPress-webbplatsen.

## <a name="create-a-proxy-to-access-vm-resources"></a>Skapa en proxy för att komma åt VM-resurser

Med VNet-integrering aktiverat kan du skapa en proxy i din Function-app för att vidarebefordra begär anden till den virtuella datorn som körs i det virtuella nätverket.

1. I din Function-app väljer du  **proxyservrar** på den vänstra menyn och väljer sedan **Lägg till**. Använd proxyinställningarna i tabellen under bilden:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Definiera proxyinställningarna":::

    | Inställning  | Föreslaget värde  | Beskrivning      |
    | -------- | ---------------- | ---------------- |
    | **Namn** | Anläggning | Namnet kan vara vilket värde som helst. Den används för att identifiera proxyservern. |
    | **Route-mall** | /plant | Väg som mappar till en VM-resurs. |
    | **Webbadress för serverdel** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Ersätt `<YOUR_VM_IP>` med IP-adressen för din WordPress-VM som du skapade tidigare. Den här mappningen returnerar en enskild fil från platsen. |

1. Välj **skapa** för att lägga till proxy i din Function-app.

## <a name="try-it-out"></a>Prova

1. Försök att komma åt den URL som du använde som **Server dels-URL** i webbläsaren. Som förväntat, tids gränsen för begäran. En timeout inträffar eftersom WordPress-platsen bara är ansluten till ditt virtuella nätverk och inte Internet.

1. Kopiera URL-värdet för **proxyn** från din nya proxy och klistra in det i adress fältet i webbläsaren. Den returnerade avbildningen är från WordPress-webbplatsen som körs i det virtuella nätverket.

    ![Växt avbildnings fil som returneras från WordPress-platsen](./media/functions-create-vnet/plant.png)

Din Function-app är ansluten till både Internet och det virtuella nätverket. Proxyservern tar emot en begäran via det offentliga Internet och agerar sedan som en enkel HTTP-proxy för att vidarebefordra begäran till det anslutna virtuella nätverket. Proxyn vidarebefordrar sedan svaret tillbaka till dig offentligt via Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien fungerar WordPress-webbplatsen som ett API som anropas med hjälp av en proxy i Function-appen. Det här scenariot gör en bra självstudie eftersom det är enkelt att konfigurera och visualisera. Du kan använda alla andra API som distribueras i ett virtuellt nätverk. Du kan också ha skapat en funktion med kod som anropar API: er som distribueras i det virtuella nätverket. Ett mer realistiskt scenario är en funktion som använder API: er för data klient för att anropa en SQL Server-instans som distribueras i det virtuella nätverket.

Funktioner som körs i en Premium-plan delar samma underliggande App Service-infrastruktur som webbappar på PremiumV2-planer. All dokumentation för [webbappar i Azure App Service](../app-service/overview.md) gäller för dina Premium plan-funktioner.

> [!div class="nextstepaction"]
> [Lär dig mer om nätverks alternativen i functions](./functions-networking-options.md)

[Premiumplan]: functions-premium-plan.md
