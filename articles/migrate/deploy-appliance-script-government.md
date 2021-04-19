---
title: Konfigurera en Azure Migrate i Azure Government
description: Lär dig att konfigurera en Azure Migrate i Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714802"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurera en apparat i Azure Government 

Följ den här artikeln för [att Azure Migrate en](./migrate-appliance-architecture.md) installation för servrar i VMware-miljön, servrar på Hyper-V och fysiska servrar i ett Azure Government moln. Du kör ett skript för att skapa installationen och kontrollerar att den kan ansluta till Azure. Om du vill konfigurera en installation i det offentliga molnet följer du den [här artikeln.](deploy-appliance-script.md)


> [!NOTE]
> Alternativet att distribuera en installation med hjälp av en mall (för servrar i VMware-miljö och Hyper-V-miljö) stöds inte i Azure Government.


## <a name="prerequisites"></a>Förutsättningar

Skriptet ställer in Azure Migrate på en befintlig fysisk server eller på en virtualiserad server.

- Servern som ska fungera som installation måste köra Windows Server 2016, med 32 GB minne, åtta virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel. Det kräver en statisk eller dynamisk IP-adress. 
- Innan du distribuerar installationen bör du granska detaljerade installationskrav för servrar på [VMware](migrate-appliance.md#appliance---vmware), [på Hyper-V](migrate-appliance.md#appliance---hyper-v)och [fysiska servrar.](migrate-appliance.md#appliance---physical)
- Kör inte skriptet på en befintlig Azure Migrate enhet.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurera installationen för VMware

Om du vill konfigurera installationen för VMware laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installationens webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du installationen med projektet.

### <a name="download-the-script"></a>Ladda ned skriptet

1. I **Migreringsmål**  >  **för Windows, Linux och SQL-Azure Migrate:** Identifiering och  >  **utvärdering** klickar du **på Identifiera.**
2. I **Identifiera server** Är dina servrar  >  **virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor.**
3. Klicka **på Ladda** ned för att ladda ned den komprimerade filen.

### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den komprimerade filen är säker innan du distribuerar den.

1. Öppna ett administratörskommandofönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den komprimerade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Kontrollera den senaste versionen av installationen och hash-värdet:

    **Algoritm** | **Ladda ned** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Kör skriptet

Det här är vad skriptet gör:

- Installerar agenter och en webbapp.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Laddar ned och installerar en omskrivningsbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler enligt följande:
    - **Konfigurationsfiler:**%ProgramData%\Microsoft Azure\Config
    - **Loggfiler:**%ProgramData%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den komprimerade filen till en mapp på den server som ska vara värd för installationen. Kontrollera att du inte kör skriptet på en server med en befintlig Azure Migrate enhet.
2. Starta PowerShell på servern med administratörsbehörighet (förhöjd behörighet).
3. Ändra PowerShell-katalogen till mappen som innehåller det innehåll som extraheras från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**, enligt följande:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. När skriptet har slutförts startas installationens webbapp så att du kan konfigurera installationen. Om du stöter på problem granskar du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrollera att installationen kan ansluta till Azure-URL:er för [myndighetsmoln.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera installationen för Hyper-V

Om du vill konfigurera installationen för Hyper-V laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installationens webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du installationen med projektet.

### <a name="download-the-script"></a>Ladda ned skriptet

1.  I **Migreringsmål**  >  **för Windows, Linux och SQL-Azure Migrate:** Identifiering och  >  **utvärdering** klickar du **på Identifiera.**
2.  I **Identifiera servrar**  >  **Virtualiseras dina servrar?** väljer du **Ja, med Hyper-V**.
3.  Klicka **på Ladda** ned för att ladda ned den komprimerade filen. 


### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den komprimerade filen är säker innan du distribuerar den.

1. Öppna ett administratörskommandofönster på den server som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den komprimerade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Kontrollera den senaste versionen av installationen och hash-värdet:

    **Scenario** | **Ladda ned** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Kör skriptet

Det här är vad skriptet gör:

- Installerar agenter och en webbapp.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Laddar ned och installerar en omskrivningsbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler enligt följande:
    - **Konfigurationsfiler:**%ProgramData%\Microsoft Azure\Config
    - **Loggfiler:**%ProgramData%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den komprimerade filen till en mapp på den server som ska vara värd för installationen. Kontrollera att du inte kör skriptet på en server som kör en befintlig Azure Migrate enhet.
2. Starta PowerShell på servern med administratörsbehörighet (förhöjd behörighet).
3. Ändra PowerShell-katalogen till mappen som innehåller det innehåll som extraheras från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**, enligt följande: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. När skriptet har slutförts startas installationens webbapp så att du kan konfigurera installationen. Om det uppstår problem kan du granska skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrollera att installationen kan ansluta till Azure-URL:er för [myndighetsmoln.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurera installationen för fysiska servrar

Om du vill konfigurera installationen för VMware laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installationens webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du installationen med projektet.

### <a name="download-the-script"></a>Ladda ned skriptet

1. I **Migreringsmål**  >  **för Windows, Linux och SQL-Azure Migrate:** Identifiering och  >  **utvärdering** klickar du **på Identifiera.**
2. I **Identifiera servrar**  >  **Virtualiseras dina servrar?** väljer du Inte **virtualiserad/Övrigt.**
3. Klicka **på Ladda** ned för att ladda ned den komprimerade filen.

### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den komprimerade filen är säker innan du distribuerar den.

1. Öppna ett administratörskommandofönster på de servrar som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den komprimerade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Kontrollera den senaste versionen av installationen och hash-värdet:

    **Scenario** | **Ladda ned** _ | _ *Hash-värde**
    --- | --- | ---
    Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Kör skriptet

Det här är vad skriptet gör:

- Installerar agenter och en webbapp.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Laddar ned och installerar en omskrivningsbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler enligt följande:
    - **Konfigurationsfiler:**%ProgramData%\Microsoft Azure\Config
    - **Loggfiler:**%ProgramData%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den komprimerade filen till en mapp på den server som ska vara värd för installationen. Kontrollera att du inte kör skriptet på en server som kör en befintlig Azure Migrate enhet.
2. Starta PowerShell på servern med administratörsbehörighet (förhöjd behörighet).
3. Ändra PowerShell-katalogen till mappen som innehåller det innehåll som extraheras från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**, enligt följande:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. När skriptet har slutförts startas installationens webbapp så att du kan konfigurera installationen. Om det uppstår problem kan du granska skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrollera att installationen kan ansluta till Azure-URL:er för [myndighetsmoln.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Nästa steg

När du har distribuerat installationen måste du konfigurera den för första gången och registrera den med projektet.

- Konfigurera installationen för [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Konfigurera installationen för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Konfigurera installationen för [fysiska servrar](how-to-set-up-appliance-physical.md).
