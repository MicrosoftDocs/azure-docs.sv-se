---
title: Distribuera Azures industriella IoT-plattform
description: I den här självstudien får du lära dig hur du distribuerar IIoT-plattformen.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787869"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Självstudie: Distribuera Azures industriella IoT-plattform

I den här självstudien får du lära dig:

> [!div class="checklist"]
> * Om huvud komponenterna i IIoT-plattformen
> * Om de olika installations typerna
> * Så här distribuerar du den industriella IoT-plattformen

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration måste skapas
- Hämta [git](https://git-scm.com/downloads)
- Registrerings program för Azure Active Directory (AAD) som används för autentisering kräver administratörs behörighet för global administratör, program administratör eller moln program för att ge administratörs tillåtelse för hela klienten (se nedan för ytterligare alternativ)
- Operativ system som stöds för distribution är Windows, Linux och Mac
- IoT Edge stöder Windows 10 IoT Enterprise-LTSC och Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Huvud komponenter

- Minsta beroenden: IoT Hub, Cosmos DB, Service Bus, Händelsehubben, Key Vault, lagring
- Standard beroenden: minst + SignalR-tjänsten, AAD-appens registreringar, enhets etablerings tjänst, Time Series Insights, arbets bok, Log Analytics, Application Insights
- Mikrotjänster: App Service plan App Service
- Användar gränssnitt (webbapp): App Service plan (delat med mikrotjänster) App Service
- Simulering: virtuell dator, virtuellt nätverk, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Installations typer

- Minimum: minsta beroenden
- Lokalt: minimi-och standard beroenden
- Tjänster: lokala och mikrotjänster
- Simulering: minsta beroenden och simulerings komponenter
- App: tjänster och användar gränssnittet
- Alla (standard): app och simuleringen

## <a name="deployment"></a>Distribution

1. För att komma igång med distributionen av IIoT-plattformen, klona lagrings platsen från kommando tolken eller terminalen.

    git https://github.com/Azure/Industrial-IoT  -klon-cd Industrial-IoT

2. Starta den guidade distributionen. skriptet samlar in nödvändig information, till exempel Azure-konto, prenumeration, mål resurs och grupp-och program namn.

I Windows:
    ```
    .\deploy
    ```

På Linux eller Mac:
    ```
    ./deploy.sh
    ```

3. Mikrotjänster och användar gränssnittet är webb program som kräver autentisering, detta kräver tre app-registreringar i AAD. Om de nödvändiga rättigheterna saknas finns det två möjliga lösningar:

- Be AAD-administratören att bevilja klient organisationens administratörs medgivande för programmet
- En AAD-administratör kan skapa AAD-program. Mappen Deploy/scripts innehåller AAD-register.ps1-skriptet för att utföra AAD-registreringen separat från distributionen. Utdata från skriptet är en fil som innehåller relevant information som ska användas som en del av distributionen och måste skickas till deploy.ps1-skriptet i samma mapp med argumentet-aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

För produktions distributioner som kräver mellanlagring, återställning, skalning och återhämtning kan plattformen distribueras till [Azure Kubernetes service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Reference
- [Distribuera Azures industriella IoT-plattform](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Distribuera allt-i-ett](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Så här distribuerar du plattform till AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Nästa steg
Nu när du har distribuerat IIoT-plattformen kan du lära dig hur du anpassar konfigurationen av komponenterna:

> [!div class="nextstepaction"]
> [Anpassa konfigurationen av komponenterna](tutorial-configure-industrial-iot-components.md)
