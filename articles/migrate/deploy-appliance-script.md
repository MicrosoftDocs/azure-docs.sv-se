---
title: Konfigurera en Azure Migrate-apparat med ett skript
description: Lär dig hur du konfigurerar en Azure Migrate-apparat med ett skript
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: c78778f9152fd4c07fb9e550e562cfef858333c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786744"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurera en installation med ett skript

Följ den här artikeln om du vill skapa en [Azure Migrate-apparat](./migrate-appliance-architecture.md) för utvärdering/migrering av servrar i VMware och på Hyper-V. Du kan köra ett skript för att skapa en installation och kontrol lera att den kan ansluta till Azure. 

Du kan distribuera-installationen för servrar i VMware och på Hyper-V med hjälp av ett skript eller med hjälp av en mall som du hämtar från Azure Portal. Att använda ett skript är användbart om du inte kan skapa en installation med den nedladdade mallen.

- Om du vill använda en mall följer du självstudierna för [VMware](./tutorial-discover-vmware.md) eller [Hyper-V](./tutorial-discover-hyper-v.md).
- Om du vill konfigurera en installation för fysiska servrar kan du bara använda ett skript. Följ [den här artikeln](how-to-set-up-appliance-physical.md).
- Följ [den här artikeln](deploy-appliance-script-government.md)om du vill konfigurera en installation i ett Azure Government moln.

## <a name="prerequisites"></a>Förutsättningar

Skriptet konfigurerar Azure Migrate-installationen på en befintlig server.

- Servern som fungerar som installations program måste uppfylla följande krav på maskin vara och operativ system:

Scenario | Krav
--- | ---
VMware | Windows Server 2016, med 32 GB minne, åtta virtuella processorer, runt 80 GB disk lagring
Hyper-V | Windows Server 2016 med 16 GB minne, åtta virtuella processorer, cirka 80 GB disk lagring

- Servern behöver också en extern virtuell växel. Den kräver en statisk eller dynamisk IP-adress och till gång till Internet.
- Innan du distribuerar installationen bör du gå igenom detaljerade installations krav för [servrar i VMware](migrate-appliance.md#appliance---vmware) [på Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Kör inte skriptet på en befintlig Azure Migrate-apparat.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurera enheten för VMware

Om du vill konfigurera en installation för VMware laddar du ned den zippade filen med namnet AzureMigrateInstaller-Server-Public.zip antingen från portalen [eller härifrån och](https://go.microsoft.com/fwlink/?linkid=2140334)extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Project.

### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett administratörs kommando fönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Kontrol lera den senaste versionen och skriptet för det offentliga Azure-molnet:

    **Integritetsalgoritm** | **Ladda ned** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera enheten för Hyper-V

Om du vill konfigurera enheten för Hyper-V laddar du ned den zippade filen med namnet AzureMigrateInstaller-Server-Public.zip antingen från portalen [eller härifrån och](https://go.microsoft.com/fwlink/?linkid=2105112)extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Project.


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett administratörs kommando fönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Kontrol lera den senaste versionen och skriptet för det offentliga Azure-molnet:

    **Scenario** | **Ladda ned** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="next-steps"></a>Nästa steg

När du har distribuerat enheten måste du konfigurera den för första gången och registrera den med Project.

- Konfigurera enheten för [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Konfigurera enheten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).