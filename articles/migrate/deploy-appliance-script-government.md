---
title: Konfigurera en Azure Migrate-apparat i Azure Government
description: Lär dig hur du konfigurerar en Azure Migrate-apparat i Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775212"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurera en installation i Azure Government 

Följ den här artikeln för att distribuera en [Azure Migrate](./migrate-appliance-architecture.md) -enhet för servrar i VMware-miljö, servrar på Hyper-V och fysiska servrar i ett Azure Government moln. Du kan köra ett skript för att skapa installationen och kontrol lera att den kan ansluta till Azure. Om du vill konfigurera en installation i det offentliga molnet följer du [den här artikeln](deploy-appliance-script.md).


> [!NOTE]
> Alternativet för att distribuera en installation med hjälp av en mall (för servrar i VMware-miljö och Hyper-V-miljö) stöds inte i Azure Government.


## <a name="prerequisites"></a>Förutsättningar

Skriptet konfigurerar Azure Migrate-enheten på en befintlig fysisk server eller på en virtualiserad Server.

- Servern som fungerar som installations program måste köra Windows Server 2016 med 32 GB minne, åtta virtuella processorer, runt 80 GB disk lagring och en extern virtuell växel. Den kräver en statisk eller dynamisk IP-adress och till gång till Internet.
- Innan du distribuerar installationen bör du gå igenom detaljerade installations krav för [servrar i VMware](migrate-appliance.md#appliance---vmware), [på Hyper-V](migrate-appliance.md#appliance---hyper-v)och [fysiska servrar](migrate-appliance.md#appliance---physical).
- Kör inte skriptet på en befintlig Azure Migrate-apparat.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurera enheten för VMware

Om du vill konfigurera en installation för VMware laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med projektet.

### <a name="download-the-script"></a>Hämta skriptet

1. I **mål för migrering**  >  **fönster, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och bedömning**, klicka på **identifiera**.
2. I **identifiera Server**  >  **är servrarna virtualiserade?**, Välj **Ja, med VMware vSphere hypervisor**.
3. Klicka på **Ladda ned** för att ladda ned den zippade filen.

### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett administratörs kommando fönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Kontrol lera den senaste versionen av produkten och hash-värdet:

    **Integritetsalgoritm** | **Ladda ned** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


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

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en server med en befintlig Azure Migrate-apparat.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** enligt följande:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för [myndighets moln](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera enheten för Hyper-V

Om du vill konfigurera enheten för Hyper-V laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med projektet.

### <a name="download-the-script"></a>Hämta skriptet

1.  I **mål för migrering**  >  **fönster, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och bedömning**, klicka på **identifiera**.
2.  I **Discover-servrar**  >  **är servrarna virtualiserade?** väljer du **Ja, med Hyper-V**.
3.  Klicka på **Ladda ned** för att ladda ned den zippade filen. 


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett administratörs kommando fönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Kontrol lera den senaste versionen av produkten och hash-värdet:

    **Scenario** | **Ladda ned** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

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

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en server som kör en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** enligt följande: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för [myndighets moln](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurera enheten för fysiska servrar

Om du vill konfigurera en installation för VMware laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med projektet.

### <a name="download-the-script"></a>Hämta skriptet

1. I **mål för migrering**  >  **fönster, Linux-och SQL-servrar**  >  **Azure Migrate: identifiering och bedömning**, klicka på **identifiera**.
2. I **Discover-servrar**  >  **är servrarna virtualiserade?** väljer du **inte virtualiserad/övrigt**.
3. Klicka på **Ladda ned** för att ladda ned den zippade filen.

### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett administratörs kommando fönster på de servrar som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Kontrol lera den senaste versionen av produkten och hash-värdet:

    **Scenario** | **Hämta** _ | _ *Hash-värde**
    --- | --- | ---
    Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

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

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en server som kör en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** enligt följande:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för [myndighets moln](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Nästa steg

När du har distribuerat enheten måste du konfigurera den för första gången och registrera den med projektet.

- Konfigurera enheten för [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Konfigurera enheten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Konfigurera enheten för [fysiska servrar](how-to-set-up-appliance-physical.md).
