---
title: Konfigurera en Azure Migrate med ett skript
description: Lär dig att konfigurera en Azure Migrate med ett skript
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714766"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurera en apparat med ett skript

Följ den här artikeln för att [Azure Migrate en](./migrate-appliance-architecture.md) programinstallation för utvärdering/migrering av servrar på VMware och Hyper-V. Du kör ett skript för att skapa en apparat och kontrollerar att den kan ansluta till Azure. 

Du kan distribuera installationen för servrar på VMware och På Hyper-V med hjälp av ett skript, eller med hjälp av en mall som du laddar ned från Azure Portal. Det är användbart att använda ett skript om du inte kan skapa en installation med hjälp av den hämtade mallen.

- Om du vill använda en mall följer du självstudierna [för VMware](./tutorial-discover-vmware.md) eller [Hyper-V.](./tutorial-discover-hyper-v.md)
- Om du vill konfigurera en installation för fysiska servrar kan du bara använda ett skript. Följ [den här artikeln.](how-to-set-up-appliance-physical.md)
- Om du vill konfigurera en apparat i ett Azure Government moln följer du den [här artikeln.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Förutsättningar

Skriptet uppsättningar Azure Migrate på en befintlig server.

- Servern som ska fungera som apparaten måste uppfylla följande maskinvaru- och operativsystemkrav:

Scenario | Krav
--- | ---
VMware | Windows Server 2016, med 32 GB minne, åtta virtuella processorer, cirka 80 GB disklagring
Hyper-V | Windows Server 2016, med 16 GB minne, åtta virtuella processorer, cirka 80 GB disklagring

- Servern behöver också en extern virtuell växel. Det krävs en statisk eller dynamisk IP-adress. 
- Innan du distribuerar installationen bör du granska detaljerade installationskrav [för servrar på VMware](migrate-appliance.md#appliance---vmware) [på Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Kör inte skriptet på en befintlig Azure Migrate enhet.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurera installationen för VMware

Om du vill konfigurera installationen för VMware laddar du ned den komprimerade filen AzureMigrateInstaller-Server-Public.zip antingen från portalen eller härifrån [och](https://go.microsoft.com/fwlink/?linkid=2140334)extraherar innehållet. Du kör PowerShell-skriptet för att starta installationens webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du installationen med projektet.

### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den komprimerade filen är säker innan du distribuerar den.

1. Öppna ett administratörskommandofönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den komprimerade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Kontrollera den senaste versionen av installationen och skriptet för det offentliga Azure-molnet:

    **Algoritm** | **Ladda ned** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>Kör skriptet

Det här är vad skriptet gör:

- Installerar agenter och en webbapp.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Laddar ned och installerar en omskrivningsbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler på följande sätt:
    - **Konfigurationsfiler:**%ProgramData%\Microsoft Azure\Config
    - **Loggfiler:**%ProgramData%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den komprimerade filen till en mapp på den server som ska vara värd för installationen. Kontrollera att du inte kör skriptet på en befintlig Azure Migrate enhet.
2. Starta PowerShell på servern med administratörsbehörighet (förhöjd behörighet).
3. Ändra PowerShell-katalogen till mappen som innehåller det innehåll som extraheras från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**, enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. När skriptet har slutförts startas installationens webbapp så att du kan konfigurera installationen. Om det uppstår problem kan du granska skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrollera att installationen kan ansluta till Azure-URL:er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera installationen för Hyper-V

Om du vill konfigurera installationen för Hyper-V laddar du ned den komprimerade filen AzureMigrateInstaller-Server-Public.zip antingen från portalen eller härifrån [och](https://go.microsoft.com/fwlink/?linkid=2105112)extraherar innehållet. Du kör PowerShell-skriptet för att starta installationens webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du installationen med projektet.


### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den komprimerade filen är säker innan du distribuerar den.

1. Öppna ett administratörskommandofönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den komprimerade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Kontrollera den senaste versionen av installationen och skriptet för det offentliga Azure-molnet:

    **Scenario** | **Ladda ned** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>Kör skriptet

Det här är vad skriptet gör:

- Installerar agenter och en webbapp.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Laddar ned och installerar en omskrivningsbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler på följande sätt:
    - **Konfigurationsfiler:**%ProgramData%\Microsoft Azure\Config
    - **Loggfiler:**%ProgramData%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den komprimerade filen till en mapp på den server som ska vara värd för installationen. Kontrollera att du inte kör skriptet på en befintlig Azure Migrate enhet.
2. Starta PowerShell på servern med administratörsbehörighet (förhöjd behörighet).
3. Ändra PowerShell-katalogen till mappen som innehåller det innehåll som extraheras från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**, enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. När skriptet har slutförts startas installationens webbapp så att du kan konfigurera installationen. Om du stöter på problem granskar du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrollera att installationen kan ansluta till Azure-URL:er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="next-steps"></a>Nästa steg

När du har distribuerat installationen måste du konfigurera den för första gången och registrera den med projektet.

- Konfigurera installationen för [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Konfigurera installationen för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).