---
ms.openlocfilehash: 6b1df49ed0f2c543706d7584a1ade5dc5554a564
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750043"
---
I den här självstudien krävs följande Azure-resurser:

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure, med [IoT Edge runtime](../../../../../iot-edge/how-to-install-iot-edge.md) installerat

I den här snabb starten rekommenderar vi att du använder [installations skriptet Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera de nödvändiga resurserna i din Azure-prenumeration. Det gör du på följande sätt:

1. Öppna [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och en Microsoft Azure fil resurs. Välj **skapa lagring** för att skapa ett lagrings konto för din Cloud Shell sessionsinformation. Det här lagrings kontot är skilt från det konto som skriptet kommer att skapa för att använda med ditt Azure Media Services-konto.
1. I den nedrullningsbara menyn på vänster sida av Cloud Shells fönstret väljer du **bash** som din miljö.

    ![Miljö väljare](../../../media/quickstarts/env-selector.png)
1. Kör följande kommando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    När skriptet har slutförts bör du se alla nödvändiga resurser i din prenumeration. Totalt 12 resurser kommer att konfigureras med skriptet:
    1. **Slut punkt för direkt uppspelning** – det här hjälper dig att spela upp den registrerade AMS-till gången.
    1. **Virtuell dator** – det här är en virtuell dator som fungerar som din gräns enhet.
    1. **Disk** – det här är en lagrings disk som är ansluten till den virtuella datorn för att lagra medier och artefakter.
    1. **Nätverks säkerhets grupp** – detta används för att filtrera nätverks trafik till och från Azure-resurser i ett virtuellt Azure-nätverk.
    1. **Nätverks gränssnitt** – detta gör att en virtuell Azure-dator kan kommunicera med Internet, Azure och andra resurser.
    1. **Skydds-anslutning** – med det här alternativet kan du ansluta till din virtuella dator med webbläsaren och Azure Portal.
    1. **Offentlig IP-adress** – detta gör att Azure-resurser kan kommunicera med Internet och offentliga Azure-tjänster
    1. **Virtuellt nätverk** – Detta möjliggör många typer av Azure-resurser, till exempel den virtuella datorn, för att kommunicera på ett säkert sätt med varandra, Internet och lokala nätverk. Läs mer om [virtuella nätverk](../../../../../virtual-network/virtual-networks-overview.md)
    1. **IoT Hub** – detta fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-programmet, IoT Edge moduler och de enheter som hanteras.
    1. **Media Service-konto** – detta hjälper till att hantera och strömma medie innehåll i Azure.
    1. **Lagrings konto** – du måste ha ett primärt lagrings konto och du kan ha valfritt antal sekundära lagrings konton som är kopplade till ditt Media Services-konto. Mer information finns i [Azure Storage konton med Azure Media Services-konton](../../../../latest/storage-account-concept.md).
    1. **Behållar register** – det hjälper till att lagra och hantera dina privata Docker-behållar avbildningar och relaterade artefakter.

1. När skriptet har körts väljer du klammerparenteserna för att exponera mappstrukturen. Du ser några filer i katalogen *~/clouddrive/lva-Sample* . Intressanthet i den här snabb starten är:

     * ***~/clouddrive/lva-Sample/Edge-Deployment/.env*** – den här filen innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en Edge-enhet.
     * ***~/clouddrive/lva-sample/appsetting.jspå*** -Visual Studio Code använder den här filen för att köra exempel koden.
     
    Du behöver dessa filer när du konfigurerar din utvecklings miljö i Visual Studio Code i nästa avsnitt. Du kanske vill kopiera dem till en lokal fil för tillfället.
    
    ![Appinställningar](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Om du stöter på problem med Azure-resurser som skapas, kan du läsa vår **[fel söknings guide](../../../troubleshoot-how-to.md#common-error-resolutions)** för att lösa vanliga problem som uppstår.