---
title: Azure Migrate-replikeringsinstallation
description: Lär dig mer om Azure Migrate Replication-enhet för agent-baserad VMWare-migrering.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754104"
---
# <a name="replication-appliance"></a>Replikeringsfil

Den här artikeln beskriver den replikeringsprincip som används av [Azure Migrate:](migrate-services-overview.md#azure-migrate-server-migration-tool) Migreringsverktyg för server när du migrerar virtuella VMware-datorer, fysiska datorer och privata/offentliga virtuella moln till Azure med hjälp av en agent-baserad migrering. 


## <a name="overview"></a>Översikt

Replikeringen distribueras när du konfigurerar en agent-baserad migrering av virtuella VMware-datorer eller fysiska servrar. Den distribueras som en enda lokal dator, antingen som en virtuell VMware-dator eller en fysisk server. Den körs:

- **Replikeringsfil**: replikeringstjänsten samordnar kommunikationen och hanterar datareplikering för lokala virtuella VMware-datorer och fysiska servrar som replikeras till Azure.
- **Processerver**: processervern som installeras som standard på serverdatorn och gör följande:
    - **Gateway för replikering**: den fungerar som en gateway för replikering. Den tar emot replikeringsdata från datorer som har Aktiver ATS för replikering. Det optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar dem till Azure.
    - **Agent installations program**: utför en push-installation av mobilitets tjänsten. Tjänsten måste installeras och köras på alla lokala datorer som du vill replikera för migrering.

## <a name="appliance-deployment"></a>Distribution av utrustning

**Används för** | **Information**
--- |  ---
**VM-baserad migrering i VMware** | Du hämtar en embryo-mall från Azure Migrate hubben och importerar till vCenter Server för att skapa den virtuella datorn.
**VM-baserad migrering för fysisk dator** | Om du inte har en VMware-infrastruktur, eller om du inte kan skapa en virtuell VMware-dator med en ägg-mall, laddar du ned ett installations program från Azure Migrate hubben och kör det för att konfigurera installations programmet för datorn.

> [!NOTE]
> Om du distribuerar i Azure Government använder du installations filen för att distribuera replikerings enheten.

## <a name="appliance-requirements"></a>Krav för utrustning

När du ställer in replikeringen med hjälp av den ägg-mall som finns i Azure Migrate Hub, kör enheten Windows Server 2016 och uppfyller support kraven. Om du konfigurerar replikeringen manuellt på en fysisk server kontrollerar du att den uppfyller kraven.

**Komponent** | **Krav**
--- | ---
 | **VMware VM-apparat**
PowerCLI | [PowerCLI version 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) bör installeras om Replikeringshanteraren körs på en virtuell VMware-dator.
Typ av nätverkskort | VMXNET3 (om installationen är en virtuell VMware-dator)
 | **Maskinvaruinställningar**
Processorkärnor | 8
RAM | 16 GB
Antal diskar | Tre: OS-disken, cache-disken för processervern och lagrings enheten.
Ledigt disk utrymme (cache) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB
**Program varu inställningar** |
Operativsystem | Windows Server 2016 eller Windows Server 2012 R2
Licens | Enheten levereras med en utvärderings licens för Windows Server 2016, som är giltig i 180 dagar.<br/><br/> Om utvärderings perioden ligger nära förfallo datum, rekommenderar vi att du laddar ned och distribuerar en ny installation, eller att du aktiverar operativ Systems licensen för den virtuella dator enheten.
Nationella inställningar för operativsystem | Engelska (en-us)
TLS | TLS 1,2 ska vara aktiverat.
.NET Framework | .NET Framework 4,6 eller senare bör installeras på datorn (med stark kryptering aktiverat.
MySQL | MySQL bör installeras på enheten.<br/> MySQL ska installeras. Du kan installera manuellt, eller så kan Site Recovery installera det under installationen av produkten.
Andra appar | Kör inte andra appar på replikerings enheten.
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Ingen befintlig standard webbplats <br> -Ingen befintlig webbplats/program som lyssnar på port 443 <br>-Aktivera  [Anonym autentisering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Aktivera [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -inställning
**Nätverksinställningar** |
IP-adresstyp | Statisk
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)
Typ av nätverkskort | VMXNET3

## <a name="mysql-installation"></a>MySQL-installation 

MySQL måste vara installerat på datorn för replikerings enheten. Den kan installeras med någon av dessa metoder.

**Metod** | **Information**
--- | ---
Ladda ned och installera manuellt | Hämta MySQL-programmet & placera det i mappen C:\Temp\ASRSetup och installera manuellt.<br/> När du konfigurerar installationen av MySQL visas som redan installerad.
Utan nedladdning online | Placera programmet MySQL installations program i mappen C:\Temp\ASRSetup. När du installerar installationen och klickar för att ladda ned och installera MySQL använder installations programmet det installations program som du har lagt till.
Hämta och installera i Azure Migrate | När du installerar installationen och tillfrågas om MySQL väljer du **Ladda ned och installera**.

## <a name="url-access"></a>URL-åtkomst

Replication-enheten behöver åtkomst till dessa URL: er i det offentliga Azure-molnet.

**URL** | **Information**
--- | ---
\*.backup.windowsazure.com | Används för replikerad data överföring och samordning
\*.store.core.windows.net | Används för replikerad data överföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagrings kontot som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för hanterings åtgärder och samordning av replikering
https:\//management.azure.com | Används för hanterings åtgärder och samordning av replikering
*.services.visualstudio.com | Används för telemetri (är valfritt)
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /Graph.Windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.Microsoft.com  | Installations programmet behöver åtkomst till dessa URL: er. De används för åtkomst kontroll och identitets hantering genom Azure Active Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | För att slutföra MySQL-nedladdning. I några regioner kan hämtningen omdirigeras till CDN-URL: en. Se till att CDN-URL: en också tillåts om det behövs.


## <a name="azure-government-url-access"></a>Azure Government URL-åtkomst

Replication-enheten behöver åtkomst till dessa URL: er i Azure Government.

**URL** | **Information**
--- | ---
\*. backup.windowsazure.us | Används för replikerad data överföring och samordning
\*.store.core.windows.net | Används för replikerad data överföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagrings kontot som lagrar replikerade data
\*. hypervrecoverymanager.windowsazure.us | Används för hanterings åtgärder och samordning av replikering
https:\//management.usgovcloudapi.net | Används för hanterings åtgärder och samordning av replikering
*.services.visualstudio.com | Används för telemetri (är valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /Graph.Windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.Microsoft.com  | Installations programmet med ägg behöver åtkomst till dessa URL: er. De används för åtkomst kontroll och identitets hantering av Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | För att slutföra MySQL-nedladdning. I några regioner kan hämtningen omdirigeras till CDN-URL: en. Se till att CDN-URL: en också tillåts om det behövs.

## <a name="port-access"></a>Port åtkomst

**Enhet** | **Anslutning**
--- | ---
Virtuella datorer | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala replikerings enheten (konfigurations servern) på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
Replikeringsfil | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
Processerver | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.<br/> Som standard körs processervern på replikerings enheten.


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den inledande replikeringen till Azure Storage med den angivna replikeringsprincipen. 
2. Trafiken replikeras till offentliga slut punkter i Azure Storage via Internet. Det finns inte stöd för att replikera trafik över ett virtuellt privat nätverk (VPN) från plats till plats från en lokal plats till Azure.
3. När den inledande replikeringen har slutförts börjar delta-replikeringen. Spårade ändringar för en dator loggas.
4. Kommunikationen sker på följande sätt:
    - Virtuella datorer kommunicerar med replikerings enheten på port HTTPS 443 inkommande, för hantering av replikering.
    - Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (körs på replikerings enheten) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.
5. Replikeringsdata loggar första marken i ett cache Storage-konto i Azure. Dessa loggar bearbetas och data lagras på en Azure-hanterad disk.

![Diagrammet visar replikeringens arkitektur.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Installations program

Enheten uppgraderas manuellt från Azure Migrate Hub. Vi rekommenderar att du alltid kör den senaste versionen.

1. I Azure Migrate > servrar > Azure Migrate: Server utvärdering, infrastruktur servrar, klickar du på **konfigurations servrar**.
2. I **konfigurations servrar** visas en länk i **agent version** när det finns en ny version av Replikerings enheten. 
3. Ladda ned installations programmet till replikeringstjänsten och installera uppgraderingen. Installations programmet identifierar den version som är aktuell som körs på enheten.
 
## <a name="next-steps"></a>Nästa steg

- [Lär dig hur](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) du konfigurerar replikeringstjänsten för VM-baserad migrering av virtuella VMware-datorer.
- [Lär dig hur](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) du konfigurerar replikeringstjänsten för fysiska servrar.
