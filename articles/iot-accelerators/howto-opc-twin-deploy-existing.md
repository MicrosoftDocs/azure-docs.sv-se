---
title: Så här distribuerar du en OPC-delad modul till ett befintligt Azure-projekt | Microsoft Docs
description: I den här artikeln beskrivs hur du distribuerar OPC till ett befintligt projekt. Du kan också lära dig hur du felsöker distributions fel.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0a4866db463dcf4ff17b2f7b25806806a700e8fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92071650"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Distribuera OPC-dubbla till ett befintligt projekt

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den OPC dubbla modulen körs på IoT Edge och ger flera Edge-tjänster till OPC-dubbla och register tjänster.

OPC med dubbla mikrotjänster underlättar kommunikationen mellan fabriks operatörer och OPC UA Server-enheter på fabriks golvet via en OPC-modul för dubbla IoT Edge. Mikrotjänsten exponerar OPC UA-tjänster (Bläddra, läsa, skriva och köra) via dess REST API. 

OPC UA Device Registry-mikrotjänst ger åtkomst till registrerade OPC UA-program och deras slut punkter. Operatörer och administratörer kan registrera och avregistrera nya OPC UA-program och bläddra bland befintliga, inklusive deras slut punkter. Förutom hantering av program och slut punkter, katalogiserar register tjänsten även registrerade OPCs dubbla IoT Edge-moduler. Med tjänst-API: t får du kontroll över funktioner för Edge-modul, till exempel starta eller stoppa Server identifiering (genomsöknings tjänster) eller aktivera nya slut punkter som kan nås med hjälp av OPC-dubbla mikrotjänster.

Kärnan i modulen är den överordnade identiteten. Administratören hanterar slut punkten, som motsvarar OPC UA Server-slutpunkter som aktive ras med motsvarande OPC UA Registry-API. Den här slut punkten är till för att dela OPC UA-JSON som tagits emot från OPCs dubbla mikrotjänster som körs i molnet till OPC UA Binary-meddelanden som skickas via en tillstånds känslig säker kanal till den hanterade slut punkten. Administratören tillhandahåller också identifierings tjänster som skickar enhets identifierings händelser till OPC UA Device onboarding-tjänsten för bearbetning, där dessa händelser leder till uppdateringar av OPC UA-registret.  I den här artikeln lär du dig hur du distribuerar den OPC dubbla modulen till ett befintligt projekt.

> [!NOTE]
> Mer information om distributions information och instruktioner finns i GitHub- [lagringsplatsen](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att PowerShell-och [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) -tillägg är installerade. Klona den här GitHub-lagringsplatsen om du inte redan har gjort det. Kör följande kommandon i PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Distribuera industriella IoT-tjänster till Azure

1. Kör följande i PowerShell-sessionen:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Följ anvisningarna för att tilldela distributions resurs gruppen ett namn och ett namn på webbplatsen.   Skriptet distribuerar mikrotjänster och deras Azure-plattforms beroenden till resurs gruppen i din Azure-prenumeration.  Skriptet registrerar också ett program i din Azure Active Directory-klient (AAD) som stöd för OAUTH-baserad autentisering.  Distributionen tar flera minuter.  Ett exempel på vad du ser när lösningen har distribuerats:

   ![Industriella IoT-OPC med dubbla distributioner till befintligt projekt](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Utdata inkluderar URL: en för den offentliga slut punkten. 

3. När skriptet har slutförts väljer du om du vill spara `.env` filen.  Du behöver `.env` miljö filen om du vill ansluta till moln slut punkten med verktyg som konsolen eller distribuera moduler för utveckling och fel sökning.

## <a name="troubleshooting-deployment-failures"></a>Felsöka distributions fel

### <a name="resource-group-name"></a>Namn på resursgrupp

Se till att du använder ett kort och enkelt resurs grupp namn.  Namnet används också för att ge resurser samma namn som de måste uppfylla kraven på resurs namn.  

### <a name="website-name-already-in-use"></a>Webbplats namnet används redan

Det är möjligt att namnet på webbplatsen redan används.  Om du stöter på det här felet måste du använda ett annat program namn.

### <a name="azure-active-directory-aad-registration"></a>Registrering av Azure Active Directory (AAD)

Distributions skriptet försöker registrera två AAD-program i Azure Active Directory.  Distributionen kan Miss förväntas, beroende på dina rättigheter till den valda AAD-klienten. Det finns två alternativ:

1. Om du väljer en AAD-klient från en lista över klienter startar du om skriptet och väljer ett annat i listan.
2. Du kan också distribuera en privat AAD-klient i en annan prenumeration, starta om skriptet och välja att använda det.

> [!WARNING]
> Fortsätt aldrig utan autentisering.  Om du väljer att göra det, kan vem som helst få åtkomst till dina OPC-dubbla slut punkter från Internet utan autentisering.   Du kan alltid välja ["lokalt" distributions alternativ](howto-opc-twin-deploy-dependencies.md) för att använda däcken.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Distribuera en allt-i-ett-tjänst för industriella IoT-tjänster

I stället för bara de tjänster och beroenden kan du också distribuera en allt-i-ett-demo.  Alla i en demo innehåller tre OPC UA-servrar, OPC-modul, alla mikrotjänster och ett exempel webb program.  Den är avsedd för demonstrations ändamål.

1. Kontrol lera att du har en klon av lagrings platsen (se ovan). Öppna en PowerShell-prompt i roten för lagrings platsen och kör:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Följ anvisningarna för att tilldela resurs gruppen ett nytt namn och ett namn på webbplatsen.  När den har distribuerats visar skriptet URL: en för webb programmets slut punkt.

## <a name="deployment-script-options"></a>Distributions skript alternativ

Skriptet använder följande parametrar:

```powershell
-type
```

Typ av distribution (VM, lokal, demo)

```powershell
-resourceGroupName
```

Kan vara namnet på en befintlig eller en ny resurs grupp.

```powershell
-subscriptionId
```

Valfritt, prenumerations-ID där resurser ska distribueras.

```powershell
-subscriptionName
```

Eller prenumerations namnet.

```powershell
-resourceGroupLocation
```

Valfritt, en resurs grupps plats. Om det här alternativet anges försöker att skapa en ny resurs grupp på den här platsen.

```powershell
-aadApplicationName
```

Ett namn på det AAD-program som ska registreras under.

```powershell
-tenantId
```

AAD-klient som ska användas.

```powershell
-credentials
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC till ett befintligt projekt, är här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Säker kommunikation av OPC UA-klienten och OPC UA PLC](howto-opc-vault-secure.md)