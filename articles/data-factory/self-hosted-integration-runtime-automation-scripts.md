---
title: Automatisera installation av egen värd för integration runtime med hjälp av lokala PowerShell-skript
description: Automatisera installationen av lokala Integration Runtime på lokala datorer.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376215"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatisera installation av egen värd för integration runtime med hjälp av lokala PowerShell-skript
Om du vill automatisera installationen av egen värd Integration Runtime på lokala datorer (andra än virtuella Azure-datorer där vi kan använda Resource Manager-mallen i stället) kan du använda lokala PowerShell-skript. I den här artikeln beskrivs två skript som du kan använda.

## <a name="prerequisites"></a>Förutsättningar

* Starta PowerShell på den lokala datorn. Om du vill köra skripten måste du välja **Kör som administratör**.
* [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) program varan för egen värd integrerings körning. Kopiera sökvägen till den hämtade filen. 
* Du behöver också en **autentiseringsnyckel** för att registrera integration runtime med egen värd.
* För att automatisera manuella uppdateringar måste du ha en förkonfigurerad integration runtime med egen värd.

## <a name="scripts-introduction"></a>Introduktion till skript 

> [!NOTE]
> De här skripten skapas med hjälp av [kommando rads verktyget dokumenterad](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) i integration runtime med egen värd. Om det behövs kan en anpassning av skripten anpassas efter deras automatiserings behov.
> Skripten måste användas per nod, så se till att köra det på alla noder om du vill ha en hög tillgänglighets installation (2 eller fler noder).

* För automatisering av installations programmet: installera och registrera en ny egen värd för integration runtime-noden med **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** – skriptet kan användas för att installera noden för integration runtime med egen värd och registrera den med en autentiseringsnyckel. Skriptet accepterar två argument, **först** anger platsen för den lokala [integrerings körningen](https://www.microsoft.com/download/details.aspx?id=39717) på en lokal disk, **andra** som anger **autentiseringsnyckel** (för registrering av en IR-nod med egen värd).

* För att automatisera manuella uppdateringar: uppdatera IR-noden med en angiven version eller till den senaste versionen **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** – detta stöds också om du har stängt av den automatiska uppdateringen eller vill ha mer kontroll över uppdateringar. Skriptet kan användas för att uppdatera noden för integration runtime med egen värd till den senaste versionen eller till en angiven högre version (nedgradering fungerar inte). Det accepterar ett argument för att ange versions nummer (exempel:-version 3.13.6942.1). När ingen version har angetts uppdaterar den alltid den egna IR-IR-enheten till den senaste versionen som finns i [nedladdningen](https://www.microsoft.com/download/details.aspx?id=39717).
    > [!NOTE]
    > Endast de senaste 3 versionerna kan anges. Vi rekommenderar att detta används för att uppdatera en befintlig nod till den senaste versionen. **det förutsätter att du har en registrerad egen IR-värd**. 

## <a name="usage-examples"></a>Användningsexempel

### <a name="for-automating-setup"></a>För automatisering av installations programmet
1. Hämta IR med egen värd [här](https://www.microsoft.com/download/details.aspx?id=39717). 
1. Ange sökvägen till den tidigare nedladdade SHIR MSI-filen (installations filen). Om sökvägen till exempel är *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi* kan du använda följande PowerShell-kommando rads exempel för den här uppgiften:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Ersätt [key] med autentiseringsnyckel för att registrera din IR.
    > Ersätt "username" med ditt användar namn.
    > Ange platsen för InstallGatewayOnLocalMachine.ps1-filen när du kör skriptet. I det här exemplet sparade vi det på Skriv bordet.

1. Om det finns en förinstallerad IR på egen hand på datorn avinstallerar skriptet det automatiskt och konfigurerar sedan ett nytt. Följande fönster visas: ![ Konfigurera integration runtime](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. När installationen och nyckel registreringen är klar kan du se *att installera Gateway* och *lyckas registrera Gateway* -resultat i din lokala PowerShell.
        [![Kör resultat för skript 1](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Automatisera manuella uppdateringar
Det här skriptet används för att uppdatera/installera + registrera den senaste integrerings körningen med egen värd. Skript körningen utför följande steg:
1. Kontrol lera aktuell IR-version med egen värd
2. Hämta den senaste versionen eller den angivna versionen från argument
3. Om det finns en nyare version än aktuell version:
    * Hämta IR-MSI med egen värd
    * uppgradera den

Du kan följa nedanstående kommando rads exempel för att använda det här skriptet:
* Hämta och installera den senaste gatewayen:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Ladda ned och installera gatewayen för den angivna versionen:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Om din aktuella version redan är den senaste, visas följande resultat, vilket innebär att ingen uppdatering krävs.   
    [![Kör resultat för skript 2](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)