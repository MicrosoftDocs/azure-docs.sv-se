---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 07/08/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 61aa5ffcbab493109371067b1eb9d199a29cb852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100551731"
---
**Krav för konfiguration och process Server**


## <a name="hardware-requirements"></a>Maskinvarukrav

**Komponent** | **Krav** 
--- | ---
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disken, cache-disk för processerver och lagrings enhet för återställning efter fel 
Ledigt disk utrymme (bearbetnings serverns cacheminne) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB
 | 

## <a name="software-requirements"></a>Programvarukrav

**Komponent** | **Krav** 
--- | ---
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-*)
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Ingen befintlig standard webbplats <br> -Ingen befintlig webbplats/program som lyssnar på port 443 <br>-Aktivera  [Anonym autentisering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Aktivera [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -inställning 
FIPS (Federal Information Processing Standards) | Aktivera inte FIPS-läge
|

## <a name="network-requirements"></a>Nätverkskrav

**Komponent** | **Krav** 
--- | --- 
IP-adresstyp | Statisk 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 (om konfigurations servern är en virtuell VMware-dator)
 |
**Internet åtkomst**  (servern behöver åtkomst till följande URL: er, direkt eller via proxy):|
\*.backup.windowsazure.com | Används för replikerad data överföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagrings kontot som lagrar replikerade data. Du kan ange den speciella URL: en för ditt cache Storage-konto.
\*.hypervrecoverymanager.windowsazure.com | Används för hanterings åtgärder och samordning av replikering
https:\//login.microsoftonline.com | Används för hanterings åtgärder och samordning av replikering 
time.nist.gov | Används för att kontrol lera tidssynkronisering mellan system och global tid
time.windows.com | Används för att kontrol lera tidssynkronisering mellan system och global tid
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /login.live.com </li><li> https: \/ /Graph.Windows.net </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com (valfritt) </li><li> https: \/ /www.live.com </li><li> https: \/ /www.Microsoft.com </li></ul> | OVF-installationen behöver åtkomst till dessa ytterligare URL: er. De används för åtkomst kontroll och identitets hantering av Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | För att slutföra MySQL-nedladdning. </br> I några regioner kan hämtningen omdirigeras till CDN-URL: en. Se till att CDN-URL: en också godkänns, om det behövs.
|

> [!NOTE]
> Om du har [anslutningar för privata länkar](../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) till Site Recovery valv behöver du inte någon ytterligare Internet åtkomst för konfigurations servern. Ett undantag till detta är när du konfigurerar CS-datorn med hjälp av en områdesmall-mall behöver du åtkomst till följande URL: er över och över privat länk åtkomst – https://management.azure.com https://www.live.com och https://www.microsoft.com . Om du inte vill tillåta åtkomst till dessa URL: er måste du konfigurera CS med enhetligt installations program.

## <a name="required-software"></a>Programvara som krävs

**Komponent** | **Krav** 
--- | ---
VMware vSphere PowerCLI | Krävs inte för version 9,14 och högre
MYSQL | MySQL ska installeras. Du kan installera manuellt, eller så kan Site Recovery installera det. (Se [Konfigurera inställningar](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) för mer information)
|

## <a name="sizing-and-capacity-requirements"></a>Storleks-och kapacitets krav

I följande tabell sammanfattas kapacitets kraven för konfigurations servern. Om du replikerar flera virtuella VMware-datorer bör du gå igenom [kapacitets planerings överväganden](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) och köra [Distributionshanteraren för Azure Site Recovery-verktyget](../articles/site-recovery/site-recovery-deployment-planner.md).


**Processor** | **Minne** | **Cachelagra disk** | **Data ändrings takt** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 Sockets * 4 kärnor \@ 2,5 GHz | 16 GB | 300 GB | 500 GB eller mindre | < 100-datorer
12 virtuella processorer<br/><br/> 2 SOCKS * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 till 150-datorer
16 virtuella processorer<br/><br/> 2 SOCKS * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200-datorer
|