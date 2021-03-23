---
title: Krav för Azure HPC-cache
description: Krav för att använda Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 7d40dcf80d9ec566146bbe46bc2cb3c558584fcd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775773"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Krav för Azure HPC-cache

Innan du använder Azure Portal för att skapa en ny Azure HPC-cache kontrollerar du att din miljö uppfyller dessa krav.

## <a name="video-overviews"></a>Video översikter

Titta på dessa videor för en snabb översikt över systemets komponenter och vad de behöver för att arbeta tillsammans.

(Klicka på video bilden eller länken för att titta.)

* [Så här fungerar det](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) – förklarar hur Azure HPC-cache interagerar med lagring och klienter

  [![video miniatyr bild: Azure HPC-cache: hur det fungerar (Klicka för att besöka video sidan)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Krav – beskriver](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) kraven för NAS-lagring, Azure Blob Storage, nätverks åtkomst och klient åtkomst

  [![video miniatyr bild: Azure HPC-cache: förutsättningar (Klicka för att besöka video sidan)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Läs resten av den här artikeln för vissa rekommendationer.

## <a name="azure-subscription"></a>Azure-prenumeration

En betald prenumeration rekommenderas.

## <a name="network-infrastructure"></a>Nätverksinfrastruktur

Två nätverksrelaterade krav bör ställas in innan du kan använda din cache:

* Ett dedikerat undernät för Azure HPC cache-instansen
* DNS-stöd så att cachen kan komma åt lagring och andra resurser

### <a name="cache-subnet"></a>Cache-undernät

Azure HPC-cachen behöver ett dedikerat undernät med följande kvaliteter:

* Under nätet måste ha minst 64 IP-adresser tillgängliga.
* Under nätet kan inte vara värd för andra virtuella datorer, även för relaterade tjänster som klient datorer.
* Om du använder flera Azure HPC cache-instanser behöver var och en av sina egna undernät.

Det bästa sättet är att skapa ett nytt undernät för varje cache. Du kan skapa ett nytt virtuellt nätverk och undernät som en del av att skapa cachen.

### <a name="dns-access"></a>DNS-åtkomst

Cachen behöver DNS för att få åtkomst till resurser utanför det virtuella nätverket. Beroende på vilka resurser du använder kan du behöva konfigurera en anpassad DNS-server och konfigurera vidarebefordran mellan servern och Azure DNS servrar:

* För att få åtkomst till Azure Blob Storage-slutpunkter och andra interna resurser behöver du den Azure-baserade DNS-servern.
* För att komma åt lokal lagring måste du konfigurera en anpassad DNS-server som kan matcha dina lagrings-värdnamn. Du måste göra detta **innan** du skapar cachen.

Om du bara använder Blob Storage kan du använda standard-Azure-standardservern för din cache. Men om du behöver åtkomst till lagrings utrymme eller andra resurser utanför Azure bör du skapa en anpassad DNS-server och konfigurera den så att den vidarebefordrar eventuella Azure-/regionsspecifika matchnings begär anden till Azure DNS-servern.

Om du vill använda en anpassad DNS-server måste du utföra dessa installations steg innan du skapar din cache:

* Skapa det virtuella nätverk som ska vara värd för Azure HPC cache.
* Skapa DNS-servern.
* Lägg till DNS-servern i cachens virtuella nätverk.

  Följ dessa steg om du vill lägga till DNS-servern i det virtuella nätverket i Azure Portal:

  1. Öppna det virtuella nätverket i Azure Portal.
  1. Välj **DNS-servrar** på menyn **Inställningar** i sido rutan.
  1. Välj **anpassad**
  1. Ange DNS-serverns IP-adress i fältet.

En enkel DNS-server kan också användas för att belastningsutjämna klient anslutningar bland alla tillgängliga cache-monterings punkter.

Lär dig mer om Azure Virtual Networks och DNS-serverkonfigurationer i [namn matchning för resurser i virtuella Azure-nätverk](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Behörigheter

Kontrol lera de här behörighets kraven innan du börjar skapa din cache.

* Cache-instansen måste kunna skapa virtuella nätverks gränssnitt (NIC). Den användare som skapar cachen måste ha tillräcklig behörighet i prenumerationen för att skapa nätverkskort.

* Om du använder Blob Storage behöver Azure HPC cache auktorisering för att komma åt ditt lagrings konto. Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge cache åtkomst till Blob Storage. Två roller krävs: lagrings konto deltagare och data deltagare i Storage blob.

  Följ instruktionerna i [Lägg till lagrings mål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att lägga till rollerna.

## <a name="storage-infrastructure"></a>Lagrings infrastruktur
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

Cachen stöder Azure Blob-behållare, NFS-maskinvara för maskin varu lagring och NFS-monterade ADLS BLOB-behållare (för närvarande i för hands version). Lägg till lagrings mål när du har skapat cacheminnet.

Varje lagrings typ har särskilda krav.

### <a name="blob-storage-requirements"></a>Blob Storage-krav

Om du vill använda Azure Blob Storage med ditt cacheminne behöver du ett kompatibelt lagrings konto och antingen en tom BLOB-behållare eller en behållare som är ifylld med Azure HPC cache-formaterade data enligt beskrivningen i [Flytta data till Azure Blob Storage](hpc-cache-ingest.md).

> [!NOTE]
> Olika krav gäller för NFS-monterad blob-lagring. Mer information finns i [ADLS-NFS-lagrings kraven](#nfs-mounted-blob-adls-nfs-storage-requirements-preview) .

Skapa kontot innan du försöker lägga till ett lagrings mål. Du kan skapa en ny behållare när du lägger till målet.

Använd följande inställningar om du vill skapa ett kompatibelt lagrings konto:

* Prestanda: **standard**
* Konto typ: **StorageV2 (generell användning v2)**
* Replikering: **Lokalt Redundant lagring (LRS)**
* Åtkomst nivå (standard): **snabb**

Det är en bra idé att använda ett lagrings konto på samma plats som din cache.

Du måste också ge cache-programmet åtkomst till ditt Azure Storage-konto som anges i [behörigheter](#permissions)ovan. Följ proceduren i [Lägg till lagrings mål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att ge cachen de nödvändiga åtkomst rollerna. Om du inte är lagrings kontots ägare kan du låta ägaren göra detta steg.

### <a name="nfs-storage-requirements"></a>Krav för NFS-lagring
<!-- linked from configuration.md -->

Om du använder ett NFS-filsystem (till exempel ett lokalt maskinvaru-NAS-system) kontrollerar du att det uppfyller dessa krav. Du kan behöva arbeta med nätverks administratörer eller brand Väggs hanterare för ditt lagrings system (eller data Center) för att verifiera inställningarna.

> [!NOTE]
> Det gick inte att skapa lagrings mål om cachen har otillräcklig åtkomst till NFS-lagrings systemet.

Mer information finns i [Felsöka problem med NAS-konfiguration och NFS-lagring](troubleshoot-nas.md).

* **Nätverks anslutning:** Azure HPC-cachen behöver nätverks åtkomst med hög bandbredd mellan cache-undernät och NFS-systemets Data Center. [ExpressRoute](../expressroute/index.yml) eller liknande åtkomst rekommenderas. Om du använder en VPN-anslutning kan du behöva konfigurera den till dessutom foga ihop TCP MSS på 1350 för att se till att stora paket inte blockeras. Läs [storleks begränsningar för VPN-paket](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) för ytterligare hjälp vid fel sökning av VPN-inställningar.

* **Port åtkomst:** Cachen behöver åtkomst till vissa TCP/UDP-portar på lagrings systemet. Olika typer av lagrings enheter har olika port krav.

  Följ den här proceduren om du vill kontrol lera inställningarna för lagrings systemet.

  * Skicka ett `rpcinfo` kommando till lagrings systemet för att kontrol lera vilka portar som behövs. Kommandot nedan visar portarna och formaterar relevanta resultat i en tabell. (Använd systemets IP-adress i stället för *<storage_IP>* term.)

    Du kan skicka det här kommandot från valfri Linux-klient som har NFS-infrastruktur installerad. Om du använder en klient i klustrets undernät kan du också kontrol lera anslutningen mellan under nätet och lagrings systemet.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Se till att alla portar som returneras av ``rpcinfo`` frågan tillåter obegränsad trafik från Azure HPC-nätets undernät.

  * Om du inte kan använda `rpcinfo` kommandot kontrollerar du att dessa ofta använda portar tillåter inkommande och utgående trafik:

    | Protokoll | Port  | Tjänst  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | monterad   |
    | TCP/UDP  | 4047  | status   |

    Vissa system använder olika port nummer för dessa tjänster. Läs mer i dokumentationen för lagrings systemet.

  * Kontrol lera brand Väggs inställningarna så att de tillåter trafik på alla de portar som krävs. Se till att kontrol lera brand väggar som används i Azure samt lokala brand väggar i ditt data Center.

* **Rot åtkomst** (Läs/skriv): cachen ansluter till backend-systemet som användar-ID 0. Kontrol lera inställningarna på ditt lagrings system:
  
  * Aktivera `no_root_squash` . Med det här alternativet ser du till att fjärranslutna rot användare kan komma åt filer som ägs av roten.

  * Kontrol lera export principerna för att se till att de inte innehåller begränsningar för rot åtkomst från cachens undernät.

  * Om lagrings platsen har en export som är under kataloger till en annan export, se till att cachen har rot åtkomst till det lägsta segmentet i sökvägen. Läs [rot åtkomst för katalog Sök vägar](troubleshoot-nas.md#allow-root-access-on-directory-paths) i fel söknings artikeln NFS-lagrings mål för mer information.

* NFS-backend-lagring måste vara en kompatibel maskin-/program varu plattform. Kontakta Azure HPC-teamet för mer information.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>NFS-monterade blob-lagring (ADLS-NFS) lagrings krav (för hands version)

Azure HPC cache kan också använda en BLOB-behållare som är monterad med NFS-protokollet som ett lagrings mål.

> [!NOTE]
> NFS 3,0 protokoll stöd för Azure Blob Storage finns i offentlig för hands version. Tillgänglighet är begränsad och funktioner kan ändras mellan nu och när funktionen blir allmänt tillgänglig. Använd inte för hands versions teknik i produktions system.
>
> Läs mer om den här förhands gransknings funktionen i [NFS 3,0 protokoll stöd i Azure Blob Storage](../storage/blobs/network-file-system-protocol-support.md).

Kraven på lagrings kontot skiljer sig åt från ett ADLS-NFS-Blob Storage-mål och för ett standard-Blob Storage-mål. Följ instruktionerna i [montera Blob Storage med hjälp av Network File System (NFS) 3,0-protokollet](../storage/blobs/network-file-system-protocol-support-how-to.md) noggrant för att skapa och konfigurera det NFS-aktiverade lagrings kontot.

Detta är en allmän översikt över stegen. De här stegen kan ändras, så du kan alltid se [ADLS-NFS-instruktionerna](../storage/blobs/network-file-system-protocol-support-how-to.md) för den aktuella informationen.

1. Se till att de funktioner som du behöver är tillgängliga i de regioner där du planerar att arbeta.

1. Aktivera funktionen NFS-protokoll för din prenumeration. Gör detta *innan* du skapar lagrings kontot.

1. Skapa ett säkert virtuellt nätverk (VNet) för lagrings kontot. Du bör använda samma virtuella nätverk för ditt NFS-aktiverade lagrings konto och för Azure HPC-cachen. (Använd inte samma undernät som cachen.)

1. Skapa lagrings kontot.

   * I stället för att använda lagrings konto inställningarna för ett standard-Blob Storage-konto, följer du anvisningarna i [instruktions-dokumentet](../storage/blobs/network-file-system-protocol-support-how-to.md). Vilken typ av lagrings konto som stöds kan variera beroende på Azure-region.

   * I avsnittet **nätverk** väljer du en privat slut punkt i det säkra virtuella nätverk som du skapade (rekommenderas) eller så väljer du en offentlig slut punkt med begränsad åtkomst från det säkra virtuella nätverket.

   * Glöm inte att slutföra det **avancerade** avsnittet där du aktiverar NFS-åtkomst.

   * Ge cache-programmet åtkomst till ditt Azure Storage-konto som anges i [behörigheter](#permissions)ovan. Du kan göra detta första gången du skapar ett lagrings mål. Följ proceduren i [Lägg till lagrings mål](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) för att ge cachen de nödvändiga åtkomst rollerna.

     Om du inte är lagrings kontots ägare kan du låta ägaren göra detta steg.

## <a name="set-up-azure-cli-access-optional"></a>Konfigurera Azure CLI-åtkomst (valfritt)

Om du vill skapa eller hantera Azure HPC-cache från Azures kommando rads gränssnitt (Azure CLI) måste du installera CLI-programvaran och HPC-cache-tillägget. Följ instruktionerna i [Konfigurera Azure CLI för Azure HPC cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure HPC cache-instans](hpc-cache-create.md) från Azure Portal
