---
title: 'Självstudie: Konfigurera miljö – Machine Learning på Azure IoT Edge'
description: 'Självstudie: Förbered din miljö för utveckling och distribution av moduler för maskin inlärning i gränsen.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 723d7b6ae5b96cf6dc622be1b1bb98065151787f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463161"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Självstudie: Konfigurera en miljö för maskin inlärning på IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Den här artikeln hjälper dig att förbereda din miljö för utveckling och distribution. Börja med att konfigurera en utvecklings dator med alla verktyg du behöver. Skapa sedan nödvändiga moln resurser i Azure.

I det här avsnittet av självstudien får du lära dig att:

> [!div class="checklist"]
>
> * Konfigurera en virtuell dator för utveckling.
> * Konfigurera en IoT Hub och moln lagring för utvecklings miljön som ska användas.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit till den här artikeln direkt kan du gå till den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien.

## <a name="set-up-the-development-vm"></a>Konfigurera den virtuella utvecklings datorn

Det här steget utförs vanligt vis av en molnbaserad utvecklare. En del program vara kan också vara till hjälp för en data expert.

Vi har skapat ett PowerShell-skript som skapar en virtuell Azure-dator med många av de krav som redan har kon figurer ATS. Den virtuella dator som vi skapar måste kunna hantera [kapslad virtualisering](../virtual-machines/windows/nested-virtualization.md), vilket är anledningen till att vi valde en [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) dator storlek.

Den virtuella utvecklings datorn kommer att konfigureras med:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop för Windows](https://www.docker.com/products/docker-desktop)
* [Git för Windows](https://gitforwindows.org/)
* [Git Credential Manager för Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/)
* [VS Code-tillägg](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Den virtuella Developer-datorn är inte absolut nödvändig – alla utvecklingsverktyg kan köras på en lokal dator. Vi rekommenderar dock starkt att du använder den virtuella datorn för att säkerställa ett fält som spelas upp.

Det tar cirka 30 minuter att skapa och konfigurera den virtuella datorn.

1. Klona eller ladda ned [Machine Learning och IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) exempel lagrings platsen till din lokala dator.

1. Öppna PowerShell som administratör och navigera till **\IoTEdgeAndMlSample\DevVM** -katalogen som finns under rot katalogen där du laddade ned koden. Vi kommer att referera till rot katalogen för din källa som `srcdir` .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM-katalogen innehåller de filer som behövs för att skapa en virtuell Azure-dator för att kunna slutföra den här kursen.

1. Kör följande kommando för att tillåta körning av skript. Välj **Ja till alla** när du uppmanas till det.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Kör Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    När du uppmanas att göra det anger du följande information:

    * **ID för Azure-prenumeration**: ditt prenumerations-ID, som du hittar i [Azure-prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i portalen.
    * **Resurs grupp namn**: namnet på en ny eller en befintlig resurs grupp i Azure.
    * **Plats**: Välj en Azure-plats där den virtuella datorn ska skapas. Till exempel "västra USA 2" eller "Nord Europa". Mer information finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Användar namn**: Ange ett minnes namn för administratörs kontot för den virtuella datorn.
    * **Lösen ord**: Ange ett lösen ord för administratörs kontot för den virtuella datorn.

   Skriptet körs i flera minuter eftersom det utför följande steg:

    1. Installerar [modulen för Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).
    1. Du blir ombedd att logga in på Azure.
    1. Bekräftar informationen för att skapa den virtuella datorn. Tryck på **j** eller **RETUR** för att fortsätta.
    1. Skapar resurs gruppen om den inte finns.
    1. Distribuerar den virtuella datorn.
    1. Aktiverar Hyper-V på den virtuella datorn.
    1. Installerar program varu behov för utveckling och klona exempel lagrings platsen.
    1. Startar om den virtuella datorn.
    1. Skapar en RDP-fil på Skriv bordet för att ansluta till den virtuella datorn.

   Om du uppmanas att ange namnet på den virtuella datorn för att starta om den, kan du kopiera dess namn från utdata från skriptet. Utdata visar också sökvägen till RDP-filen för att ansluta till den virtuella datorn.

### <a name="set-auto-shutdown-schedule"></a>Ange schema för automatisk avstängning

För att hjälpa dig att minska kostnaderna har den virtuella utvecklings datorn skapats med ett schema för automatisk avstängning som är inställt på 1900 PST. Du kan behöva uppdatera inställningen beroende på din plats och ditt schema. Så här uppdaterar du schemat för avstängning:

1. I Azure Portal navigerar du till den virtuella dator som skriptet har skapat.

1. Från menyn i den vänstra rutan under **åtgärder** väljer du **Automatisk avstängning**.

1. Justera den **schemalagda avstängningen** och **tids zonen** efter behov och välj **Spara**.

## <a name="connect-to-the-development-vm"></a>Anslut till den virtuella utvecklings datorn

Nu när vi har skapat en virtuell dator måste vi slutföra installationen av den program vara som krävs för att slutföra självstudien.

1. Dubbelklicka på RDP-filen som skriptet skapade på Skriv bordet.

1. Du kommer att visas i en dialog ruta som säger att den fjärranslutna anslutningens utgivare är okänd. Detta är acceptabelt, så välj **Anslut**.

1. Ange det administratörs lösen ord som du angav för att skapa den virtuella datorn och klicka på **OK**.

1. Du uppmanas att godkänna certifikatet för den virtuella datorn. Välj **Ja**.

## <a name="install-visual-studio-code-extensions"></a>Installera Visual Studio Code-tillägg

Nu när du har anslutit till utvecklings datorn lägger du till några användbara tillägg i Visual Studio Code för att förenkla utvecklings upplevelsen.

1. Anslut till den virtuella utvecklings datorn, öppna ett PowerShell-fönster och navigera till **C:\source\IoTEdgeAndMlSample\DevVM** -katalogen. Den här katalogen skapades av skriptet som skapade den virtuella datorn.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Kör följande kommando för att tillåta körning av skript. Välj **Ja till alla** när du uppmanas till det.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Kör Visual Studio Code Extensions-skriptet.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Skriptet kommer att köras under några minuter vid installation av VS Code-tillägg:

    * Azure IoT-verktyg
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurera IoT Hub och lagring

De här stegen utförs vanligt vis av en molnbaserad utvecklare.

Azure IoT Hub är hjärtat i alla IoT-program som hanterar säker kommunikation mellan IoT-enheter och molnet. Det är den huvudsakliga koordinations punkten för driften av IoT Edge Machine Learning-lösning.

* IoT Hub använder vägar för att dirigera inkommande data från IoT-enheter till andra underordnade tjänster. Vi kommer att dra nytta av IoT Hub vägar för att skicka enhets data till Azure Storage. I Azure Storage används enhets data av Azure Machine Learning för att träna vår återstående användbara livs cykel (RUL) klassificerare.

* Senare i självstudien kommer vi att använda IoT Hub för att konfigurera och hantera vår Azure IoT Edge-enhet.

I det här avsnittet ska du använda ett skript för att skapa en Azure IoT-hubb och ett Azure Storage-konto. Sedan konfigurerar du en väg i Azure Portal som vidarebefordrar data som tas emot av hubben till en Azure Storage-behållare. De här stegen tar cirka 10 minuter att slutföra.

1. Anslut till den virtuella utvecklings datorn, öppna ett PowerShell-fönster och navigera till **IoTHub** -katalogen.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Kör skriptet för att skapa. Använd samma värden för prenumerations-ID, plats och resurs grupp som du gjorde när du skapade den virtuella utvecklings datorn.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Du uppmanas att logga in i Azure.
    * Skriptet bekräftar informationen för att skapa hubben och lagrings kontot. Tryck på **j** eller **RETUR** för att fortsätta.

Det tar cirka två minuter att köra skriptet. När du är klar matas skriptet ut namnet på IoT-hubben och lagrings kontot.

## <a name="review-route-to-storage-in-iot-hub"></a>Granska routning till lagring i IoT Hub

Som en del av att skapa IoT-hubben skapade även skriptet som vi körde i föregående avsnitt en anpassad slut punkt och en väg. IoT Hub vägar består av ett frågeuttryck och en slut punkt. Om ett meddelande matchar uttrycket skickas data längs vägen till den associerade slut punkten. Slut punkter kan vara Event Hubs, Service Bus köer och ämnen. I det här fallet är slut punkten en BLOB-behållare i ett lagrings konto. Vi använder Azure Portal för att granska den väg som skapats av vårt skript.

1. Öppna [Azure Portal](https://portal.azure.com) och gå till resurs gruppen som du använder för den här självstudien.

1. Välj den IoT Hub som skriptet har skapat i listan över resurser. Det får ett namn som slutar med slumpmässiga tecken som `IotEdgeAndMlHub-jrujej6de6i7w` .

1. I menyn i den vänstra rutan under **meddelanden** väljer du **meddelanderoutning.**

1. **På sidan meddelanderoutning** väljer du fliken **anpassade slut punkter** .

1. Expandera avsnittet **lagring** :

   ![Kontrol lera att turbofanDeviceStorage finns i listan med anpassade slut punkter](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Vi ser **turbofanDeviceStorage** finns i listan med anpassade slut punkter. Observera följande egenskaper för den här slut punkten:

   * Den pekar på Blob storage-behållaren som du skapade med namnet `devicedata` enligt **namnet på containern**.
   * Dess **fil namns format** har partition som det sista elementet i namnet. Vi hittar det här formatet är enklare för de fil åtgärder som vi ska göra med Azure Notebooks senare i självstudien.
   * Dess **status** bör vara felfri.

1. Välj fliken **Vägar**.

1. Välj den väg som heter **turbofanDeviceDataToStorage**.

1. På sidan **cirkulations information** noterar du att vägens slut punkt är **turbofanDeviceStorage** -slutpunkten.

   ![Granska information om turbofanDeviceDataToStorage-vägen](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Titta på **cirkulations frågan**, som är inställd på **Sant**. Den här inställningen innebär att alla meddelanden i enhetens telemetri matchar den här vägen. och därför skickas alla meddelanden till **turbofanDeviceStorage** -slutpunkten.

1. Stäng bara den här sidan eftersom inga redigeringar gjordes.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du är klar med att rensa alla resurser tills du är klar med den sista självstudien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi skapat en IoT Hub och konfigurerat en väg till ett Azure Storage-konto. Nu ska vi skicka data från en uppsättning simulerade enheter genom IoT Hub till lagrings kontot. Senare i självstudien, när vi har konfigurerat vår IoT Edge enhet och moduler, kommer vi att gå tillbaka till vägar och se lite mer på cirkulations frågan.

Fortsätt till nästa artikel för att skapa en simulerad enhet som ska övervakas.

> [!div class="nextstepaction"]
> [Generera enhets data](tutorial-machine-learning-edge-03-generate-data.md)
