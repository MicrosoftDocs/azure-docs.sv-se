---
ms.openlocfilehash: f031843243e64515549eaa4e850efcddca58d1f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633509"
---
* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.
  > [!NOTE]
  > Du behöver en Azure-prenumeration med behörigheter för att skapa tjänstens huvud namn (**ägar rollen** ger detta). Om du inte har rätt behörighet kan du kontakta konto administratören för att ge dig rätt behörigheter. 
* [Visual Studio Code](https://code.visualstudio.com/)med följande tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
        > [!TIP]
        > När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera prompten.
    * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Python 3](https://www.python.org/downloads/) (3.6.9 eller senare), [pip 3](https://pip.pypa.io/en/stable/installing/) och alternativt [venv](https://docs.python.org/3/library/venv.html).
* Om du inte har slutfört snabb starten [identifiera rörelse-och genererar händelser](../../../detect-motion-emit-events-quickstart.md) följer du dessa steg:
     1. [Ställa in Azure-resurser](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Ställt in din utvecklingsmiljö](../../../detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Skapa och distribuera distributions manifestet för IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Förbereda övervakning av händelser](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Om du stöter på problem med Azure-resurser som skapas, kan du läsa vår **[fel söknings guide](../../../troubleshoot-how-to.md#common-error-resolutions)** för att lösa vanliga problem som uppstår.
