---
title: Aktivera åtkomst till privat plats till Azure Functions
description: Lär dig hur du konfigurerar åtkomst till Azure Virtual Network-webbplatser för Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937050"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Självstudie: upprätta Azure Functions åtkomst till privat webbplats

I den här självstudien får du se hur du aktiverar [åtkomst till privata platser](./functions-networking-options.md#private-endpoint-connections) med Azure Functions. Genom att använda privat plats åtkomst kan du kräva att funktions koden bara utlöses från ett speciellt virtuellt nätverk.

Åtkomst till privata webbplatser är användbart i scenarier när åtkomst till Function-appen måste begränsas till ett visst virtuellt nätverk. Till exempel kan Function-appen endast användas för anställda i en viss organisation eller tjänster som finns i det angivna virtuella nätverket (till exempel en annan Azure-funktion, en virtuell Azure-dator eller ett AKS-kluster).

Om en Functions-app behöver åtkomst till Azure-resurser i det virtuella nätverket, eller är ansluten via [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md), krävs [integrering av virtuella nätverk](./functions-create-vnet.md) .

I den här självstudien får du lära dig hur du konfigurerar privat plats åtkomst för din Function-app:

> [!div class="checklist"]
> * Skapa en virtuell dator
> * Skapa en Azure skydds-tjänst
> * Skapa en Azure-funktionsapp
> * Konfigurera en tjänst slut punkt för virtuellt nätverk
> * Skapa och distribuera en Azure-funktion
> * Anropa funktionen från utsidan och inom det virtuella nätverket

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="topology"></a>Topologi

Följande diagram visar arkitekturen för den lösning som ska skapas:

![Högnivå arkitektur diagram för åtkomst lösning för privat webbplats](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien är det viktigt att du förstår IP-adressering och undernät. Du kan börja med [den här artikeln som beskriver grunderna för adressering och undernät](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor är tillgängliga online.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Det första steget i den här självstudien är att skapa en ny virtuell dator i ett virtuellt nätverk.  Den virtuella datorn kommer att användas för att få åtkomst till din funktion när du har begränsat åtkomsten till den som bara är tillgänglig i det virtuella nätverket.

1. Välj knappen **skapa en resurs** .

1. Skriv **Windows Server** i Sök fältet och välj **Windows Server** i Sök resultatet.

1. Välj **Windows server 2019 Data Center** i listan över Windows Server-alternativ och klicka på knappen **skapa** .

1. På fliken _grundläggande_ använder du de VM-inställningar som anges i tabellen nedanför bilden:

    >[!div class="mx-imgBorder"]
    >![Fliken grundläggande för en ny virtuell Windows-dator](./media/functions-create-private-site-access/create-vm-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | _Prenumeration_ | Din prenumeration | Den prenumeration som dina resurser skapas under. |
    | [_Resurs grupp_](../azure-resource-manager/management/overview.md) | myResourceGroup | Välj den resurs grupp som innehåller alla resurser för den här självstudien.  Med samma resurs grupp blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | _Namn på virtuell dator_ | myVM | Det virtuella dator namnet måste vara unikt i resurs gruppen |
    | [_Region_](https://azure.microsoft.com/regions/) | USA Norra centrala USA | Välj en region nära dig eller nära de funktioner som ska nås. |
    | _Offentliga inkommande portar_ | Inga | Välj **ingen** för att se till att det inte finns någon inkommande anslutning till den virtuella datorn från Internet. Fjärråtkomst till den virtuella datorn kommer att konfigureras via Azure skydds-tjänsten. |

1. Välj fliken _nätverk_ och välj **Skapa ny** för att konfigurera ett nytt virtuellt nätverk.

    >[!div class="mx-imgBorder"]
    >![Skärm bild som visar fliken "nätverk" med åtgärden "Skapa ny" markerad i avsnittet "virtuellt nätverk".](./media/functions-create-private-site-access/create-vm-networking.png)

1. I _Skapa virtuellt nätverk_ använder du inställningarna i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![Skapa ett nytt virtuellt nätverk för den nya virtuella datorn](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | _Namn_ | myResourceGroup-VNet | Du kan använda standard namnet som genereras för det virtuella nätverket. |
    | _Adressintervall_ | 10.10.0.0/16 | Använd ett enda adress intervall för det virtuella nätverket. |
    | _Namn på undernät_ | Självstudie | Namnet på under nätet. |
    | _Adress intervall_ (undernät) | 10.10.1.0/24 | Under näts storleken definierar hur många gränssnitt som kan läggas till i under nätet. Det här under nätet används av den virtuella datorn. Ett/24-undernät tillhandahåller 254-värd adresser. |

1. Välj **OK** för att skapa det virtuella nätverket.
1. Gå tillbaka till fliken _nätverk_ , se till att **ingen** är markerad för _offentlig IP_.
1. Välj fliken _hantering_ och sedan **Skapa nytt** lagrings konto i _diagnostik Storage-konto_.
1. Lämna standardvärdena för avsnitten _identitet_, _Automatisk avstängning_ och _säkerhets kopiering_ .
1. Välj _Granska + skapa_. När verifieringen är klar väljer du **skapa**. Processen för att skapa virtuella datorer tar några minuter.

## <a name="configure-azure-bastion"></a>Konfigurera Azure-skydds

[Azure skydds](https://azure.microsoft.com/services/azure-bastion/) är en helt hanterad Azure-tjänst som ger säker RDP-och SSH-åtkomst till virtuella datorer direkt från Azure Portal. Genom att använda Azure skydds-tjänsten tar du bort behovet av att konfigurera nätverks inställningar som rör RDP-åtkomst.

1. I portalen väljer du **Lägg till** överst i vyn resurs grupp.
1. Skriv **skydds** i Sök fältet.
1. Välj **skydds** i Sök resultaten.
1. Välj **skapa** för att påbörja processen med att skapa en ny Azure skydds-resurs. Du kommer att märka ett fel meddelande i det _virtuella nätverket_ -avsnittet eftersom det inte finns något AzureBastionSubnet-undernät än. Under nätet skapas i följande steg. Använd inställningarna i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![Börja skapa Azure-skydds](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | _Namn_ | myBastion | Namnet på den nya skydds-resursen |
    | _Region_ | USA, norra centrala | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner kommer åt. |
    | _Virtuellt nätverk_ | myResourceGroup-VNet | Det virtuella nätverk där skydds-resursen ska skapas i |
    | _Undernät_ | AzureBastionSubnet | Under nätet i det virtuella nätverket som den nya skydds-värd resursen ska distribueras till. Du måste skapa ett undernät med namnet Value **AzureBastionSubnet**. Med det här värdet kan Azure veta vilket undernät som skydds-resurserna ska distribueras till. Du måste använda ett undernät på minst **/27** eller större (/27,/26 osv.). |

    > [!NOTE]
    > En detaljerad steg-för-steg-guide om hur du skapar en Azure skydds-resurs finns i själv studie kursen [skapa en Azure skydds-värd](../bastion/tutorial-create-host-portal.md) .

1. Skapa ett undernät där Azure kan etablera Azure skydds-värden. Om du väljer **Hantera under näts konfiguration** öppnas ett nytt fönster där du kan definiera ett nytt undernät.  Välj **+ undernät** för att skapa ett nytt undernät.
1. Under nätet måste ha namnet **AzureBastionSubnet** och under nätets prefix måste vara minst **/27**.  Välj **OK** för att skapa under nätet.

    >[!div class="mx-imgBorder"]
    >![Skapa undernät för Azure skydds-värd](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. På sidan _skapa en skydds_ väljer du den nyligen skapade **AzureBastionSubnet** i listan över tillgängliga undernät.

    >[!div class="mx-imgBorder"]
    >![Skapa en Azure skydds-värd med ett speciellt undernät](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Välj **granska & skapa**. När verifieringen är klar väljer du **skapa**. Det tar några minuter för Azure skydds-resursen att skapas.

## <a name="create-an-azure-functions-app"></a>Skapa en Azure-funktionsapp

Nästa steg är att skapa en Function-app i Azure med hjälp av [förbruknings planen](consumption-plan.md). Du distribuerar funktions koden till den här resursen senare i självstudien.

1. I portalen väljer du **Lägg till** överst i vyn resurs grupp.
1. Välj **compute > Funktionsapp**
1. I avsnittet _grundläggande_ inställningar använder du funktionen appinställningar som anges i tabellen nedan.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | _Resursgrupp_ | myResourceGroup | Välj den resurs grupp som innehåller alla resurser för den här självstudien.  Genom att använda samma resurs grupp för Function-appen och den virtuella datorn blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | _Funktionsappens namn_ | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är a-z (Skift läges okänslig), 0-9 och-. |
    | _Publicera_ | Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | _Körningsstack_ | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. |
    | _Region_ | USA, norra centrala | Välj en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner kommer åt. |

    Välj **Nästa: Hosting >** -knappen.
1. I avsnittet _värd_ väljer du rätt _lagrings konto_, _operativ system_ och _plan_ enligt beskrivningen i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | _Lagringskonto_ | Globalt unikt namn | Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](storage-considerations.md#storage-account-requirements). |
    | _Operativsystem_ | Önskat operativ system | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | _Planera_ | Förbrukning | [Värd planen](./functions-scale.md) styr hur Function-appen skalas och vilka resurser som är tillgängliga för varje instans. |
1. Välj **Granska + skapa** för att granska konfigurations valen för appen.
1. Välj **Skapa** för att etablera och distribuera funktionsappen.

## <a name="configure-access-restrictions"></a>Konfigurera åtkomst begränsningar

Nästa steg är att konfigurera [åtkomst begränsningar](../app-service/app-service-ip-restrictions.md) för att säkerställa att endast resurser i det virtuella nätverket kan anropa funktionen.

Åtkomst till [privat webbplats](functions-networking-options.md#private-endpoint-connections) är aktiverat genom att skapa en Azure Virtual Network [service-slutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) mellan Function-appen och det angivna virtuella nätverket. Åtkomst begränsningar implementeras via tjänstens slut punkter. Tjänst slut punkter säkerställer endast trafik från det angivna virtuella nätverket som har åtkomst till den angivna resursen. I det här fallet är den angivna resursen Azure-funktionen.

1. I Function-appen väljer du **nätverks** länken under rubriken för avsnittet _Inställningar_ .
1. Sidan _nätverk_ är start punkten för att konfigurera Azure-frontend, Azure CDN och även åtkomst begränsningar.
1. Välj **Konfigurera åtkomst begränsningar** för att konfigurera åtkomst till privat plats.
1. På sidan _åtkomst begränsningar_ ser du bara standard begränsningen på plats. Som standard placeras inga begränsningar för åtkomst till Function-appen.  Välj **Lägg till regel** för att skapa en åtkomst begränsnings konfiguration för privata platser.
1. I fönstret _Lägg till åtkomst begränsning_ anger du ett _namn_, en _prioritet_ och en _Beskrivning_ för den nya regeln.
1. Välj **Virtual Network** i list rutan _typ_ och välj sedan det tidigare skapade virtuella nätverket och välj sedan under nätet för **självstudier** . 
    > [!NOTE]
    > Det kan ta flera minuter att aktivera tjänstens slut punkt.
1. Sidan _åtkomst begränsningar_ visar nu att det finns en ny begränsning. Det kan ta några sekunder innan _slut punkts status_ ändras från att inaktive ras via etableringen till aktive rad.

    >[!IMPORTANT]
    > Varje Function-app har en [kudu-webbplats (Advanced Tool)](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) som används för att hantera Function app-distributioner. Den här webbplatsen nås från en URL som: `<FUNCTION_APP_NAME>.scm.azurewebsites.net` . Att aktivera åtkomst begränsningar på kudu-webbplatsen förhindrar att projekt koden distribueras från en lokal utvecklare och att en agent krävs inom det virtuella nätverket för att distributionen ska kunna utföras.

## <a name="access-the-functions-app"></a>Öppna Functions-appen

1. Gå tillbaka till den tidigare skapade funktions appen.  I avsnittet _Översikt_ kopierar du URL: en.

    >[!div class="mx-imgBorder"]
    >![Hämta URL för Function-appen](./media/functions-create-private-site-access/access-function-overview.png)

    Om du försöker komma åt Function-appen nu från din dator utanför det virtuella nätverket får du en HTTP 403-sida som visar att åtkomst är förbjuden.
1. Gå tillbaka till resurs gruppen och välj den tidigare skapade virtuella datorn. För att få åtkomst till platsen från den virtuella datorn måste du ansluta till den virtuella datorn via Azure skydds-tjänsten.
1. Välj **Anslut** och välj sedan **skydds**.
1. Ange det användar namn och lösen ord som krävs för att logga in på den virtuella datorn.
1. Välj **Anslut**. Ett nytt webbläsarfönster öppnas så att du kan interagera med den virtuella datorn.
Det går att komma åt webbplatsen från webbläsaren på den virtuella datorn eftersom den virtuella datorn ansluter till platsen via det virtuella nätverket.  Även om platsen endast är tillgänglig från det angivna virtuella nätverket förblir en offentlig DNS-post.

## <a name="create-a-function"></a>Skapa en funktion

Nästa steg i den här självstudien är att skapa en HTTP-utlöst Azure-funktion. Om du anropar funktionen via en HTTP GET-eller POST-POST ska du svara på "Hello, {name}".  

1. Följ någon av följande snabb starter för att skapa och distribuera din Azure Functions-app.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Kommandorad](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. När du publicerar Azure Functions-projektet väljer du den Function app-resurs som du skapade tidigare i den här självstudien.
1. Kontrol lera att funktionen har distribuerats.

    >[!div class="mx-imgBorder"]
    >![Distribuerad funktion i en lista över funktioner](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Anropa funktionen direkt

1. För att kunna testa åtkomsten till funktionen måste du kopiera funktions webb adressen. Välj den distribuerade funktionen och välj sedan **Hämta funktions webb adress**. Klicka sedan på **kopierings** knappen för att kopiera webb adressen till Urklipp.

    >[!div class="mx-imgBorder"]
    >![Kopiera funktions webb adressen](./media/functions-create-private-site-access/get-function-url.png)

1. Klistra in URL-adressen i en webbläsare. När du försöker få åtkomst till Function-appen från en dator utanför det virtuella nätverket får du ett HTTP 403-svar som anger att åtkomsten till appen är förbjuden.

## <a name="invoke-the-function-from-the-virtual-network"></a>Anropa funktionen från det virtuella nätverket

Att komma åt funktionen via en webbläsare (med hjälp av Azure skydds-tjänsten) på den konfigurerade virtuella datorn i det virtuella nätverket resulterar i lyckad!

>[!div class="mx-imgBorder"]
>![Få åtkomst till Azure-funktionen via Azure skydds](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg


> [!div class="nextstepaction"]
> [Lär dig mer om nätverks alternativen i functions](./functions-networking-options.md)