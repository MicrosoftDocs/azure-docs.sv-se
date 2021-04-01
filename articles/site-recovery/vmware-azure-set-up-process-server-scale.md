---
title: Konfigurera en skalbar processerver under haveri beredskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar en skalbar processerver under haveri beredskap för virtuella VMware-datorer och fysiska servrar.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019162"
---
# <a name="scale-with-additional-process-servers"></a>Skala med ytterligare process servrar

När du replikerar virtuella VMware-datorer eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md), installeras som standard en processerver på konfigurations servern och används för att koordinera data överföringen mellan Site Recovery och din lokala infrastruktur. Du kan lägga till ytterligare fristående process servrar för att öka kapaciteten och skala ut distributionen av replikering. Den här artikeln beskriver hur du installerar en skalbar processerver.

## <a name="before-you-start"></a>Innan du börjar

### <a name="capacity-planning"></a>Kapacitetsplanering

Kontrol lera att du har utfört [kapacitets planering](site-recovery-plan-capacity-vmware.md) för VMware-replikering. Detta hjälper dig att identifiera hur och när du ska distribuera ytterligare process servrar.

Från 9,24-versionen läggs vägledningen till vid valet av processerver för nya replikeringar. Processervern markeras som felfri, varning och kritisk baserat på vissa kriterier. Om du vill förstå olika scenarier som kan påverka status för processervern granskar du [process Server aviseringarna](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Det finns inte stöd för att använda en klonad process Server komponent. Följ stegen i den här artikeln för varje PS-utskalning.

### <a name="sizing-requirements"></a>Storleks krav 

Kontrol lera storleks kraven som sammanfattas i tabellen. I allmänhet behöver du ytterligare process servrar för att hantera trafik volymen om du behöver skala distributionen till fler än 200 käll datorer, eller om du har en total daglig omsättnings taxa på mer än 2 TB.

| **Ytterligare processerver** | **Cachestorlek för cache** | **Data ändrings takt** | **Skyddade datorer** |
| --- | --- | --- | --- |
|4 virtuella processorer (2 Sockets * 2 kärnor \@ 2,5 GHz), 8 GB minne |300 GB |250 GB eller mindre |Replikera 85 eller färre datorer. |
|8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz), 12 GB minne |600 GB |250 GB till 1 TB |Replikera mellan 85-150 datorer. |
|12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz) 24 GB minne |1 TB |1 TB till 2 TB |Replikera mellan 150-225 datorer. |

Var varje skyddad käll dator har kon figurer ATS med 3 diskar på 100 GB vardera.

### <a name="prerequisites"></a>Förutsättningar

Kraven för den ytterligare processervern sammanfattas i följande tabell.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Ladda ned installations fil

Ladda ned installations filen för processervern enligt följande:

1. Logga in på Azure Portal och bläddra till Recovery Services-valvet.
2. Öppna **Site Recovery infrastruktur**  >  **VMware och**  >  **konfigurations servrar** för fysiska datorer (under för VMware & fysiska datorer).
3. Välj konfigurations servern för att öka detalj nivån i Server informationen. Klicka sedan på **+ processerver**.
4. I **Lägg till processerver**  >   **väljer du var du vill distribuera processervern** och väljer **distribuera en skalbar processerver lokalt**.

   ![Sidan Lägg till servrar](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klicka på **hämta Microsoft Azure Site Recovery enhetlig installation**. Detta laddar ned den senaste versionen av installations filen.

   > [!WARNING]
   > Installations versionen för processervern måste vara samma som, eller tidigare än, den konfigurations Server version som du kör. Ett enkelt sätt att se till att versionens kompatibilitet är att använda samma installations program, som du senast använde för att installera eller uppdatera konfigurations servern.

## <a name="install-from-the-ui"></a>Installera från användar gränssnittet

Installera på följande sätt. När du har konfigurerat servern migrerar du käll datorerna för att använda den.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Installera från kommando raden

Installera genom att köra följande kommando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Där kommando rads parametrarna är följande:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Exempel:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Skapa en fil med proxyinställningar

Om du behöver konfigurera en proxyserver tar ProxySettingsFilePath-parametern en fil som indata. Du kan skapa filen på följande sätt och skicka den som en ProxySettingsFilePath-parameter för indata.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer om att [Hantera inställningar för processervern](vmware-azure-manage-process-server.md)
