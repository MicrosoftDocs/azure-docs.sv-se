---
title: Azure HPC Cache krav
description: Krav för att använda Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/14/2021
ms.author: v-erkel
ms.openlocfilehash: 3cddbba3dca64561d7e2b7b27715152a26a8c9e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717593"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Krav för Azure HPC Cache

Innan du använder Azure Portal för att skapa en Azure HPC Cache bör du kontrollera att din miljö uppfyller dessa krav.

## <a name="video-overviews"></a>Videoöversikter

Titta på dessa videor för en snabb översikt över systemets komponenter och vad de behöver för att fungera tillsammans.

(Klicka på videobilden eller länken som du vill titta på.)

* [Så här fungerar det](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) – Förklarar hur Azure HPC Cache interagerar med lagring och klienter

  [![videominiatyrbild: Azure HPC Cache: Så här fungerar det (klicka för att besöka videosidan)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Krav –](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) Beskriver kraven för NAS-lagring, Azure Blob Storage, nätverksåtkomst och klientåtkomst

  [![videominiatyrbild: Azure HPC Cache: Förutsättningar (klicka för att besöka videosidan)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Läs resten av den här artikeln för specifika rekommendationer.

## <a name="azure-subscription"></a>Azure-prenumeration

En betald prenumeration rekommenderas.

## <a name="network-infrastructure"></a>Nätverksinfrastruktur

Två nätverksrelaterade krav bör konfigureras innan du kan använda cacheminnet:

* Ett dedikerat undernät för Azure HPC Cache instansen
* DNS-stöd så att cachen kan komma åt lagring och andra resurser

### <a name="cache-subnet"></a>Cacheundernät

De Azure HPC Cache behöver ett dedikerat undernät med följande egenskaper:

* Undernätet måste ha minst 64 TILLGÄNGLIGA IP-adresser.
* Undernätet kan inte vara värd för andra virtuella datorer, även för relaterade tjänster som klientdatorer.
* Om du använder flera Azure HPC Cache instanser behöver var och en sitt eget undernät.

Det bästa sättet är att skapa ett nytt undernät för varje cache. Du kan skapa ett nytt virtuellt nätverk och undernät som en del av att skapa cachen.

### <a name="dns-access"></a>DNS-åtkomst

Cacheminnet behöver DNS för att komma åt resurser utanför det virtuella nätverket. Beroende på vilka resurser du använder kan du behöva konfigurera en anpassad DNS-server och konfigurera vidarebefordran mellan den servern och Azure DNS servrar:

* För att få åtkomst till Azure Blob Storage-slutpunkter och andra interna resurser behöver du den Azure-baserade DNS-servern.
* För att få åtkomst till lokal lagring måste du konfigurera en anpassad DNS-server som kan matcha dina lagringsvärdnamn. Du måste göra detta innan du skapar cachen.

Om du bara använder Blob Storage kan du använda standard-DNS-servern från Azure för din cache. Men om du behöver åtkomst till lagring eller andra resurser utanför Azure bör du skapa en anpassad DNS-server och konfigurera den så att den vidarebefordrar Azure-specifika lösningsbegäranden till Azure DNS servern.

Om du vill använda en anpassad DNS-server måste du göra följande konfigurationssteg innan du skapar din cache:

* Skapa det virtuella nätverk som ska vara värd för Azure HPC Cache.
* Skapa DNS-servern.
* Lägg till DNS-servern i cacheminnets virtuella nätverk.

  Följ dessa steg för att lägga till DNS-servern i det virtuella nätverket i Azure Portal:

  1. Öppna det virtuella nätverket i Azure Portal.
  1. Välj DNS-servrar på menyn Inställningar i sidopanelen.
  1. Välj Anpassad
  1. Ange DNS-serverns IP-adress i fältet .

En enkel DNS-server kan också användas för att belastningsutjämna klientanslutningar bland alla tillgängliga cachemonteringspunkter.

Läs mer om virtuella Azure-nätverk och DNS-serverkonfigurationer [i Namnmatchning för resurser i virtuella Azure-nätverk.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="permissions"></a>Behörigheter

Kontrollera dessa behörighetsrelaterade krav innan du börjar skapa cacheminnet.

* Cacheinstansen måste kunna skapa virtuella nätverksgränssnitt (NIC). Den användare som skapar cachen måste ha tillräcklig behörighet i prenumerationen för att kunna skapa nätverkskort.

* Om du använder Blob Storage Azure HPC Cache auktorisering för att få åtkomst till ditt lagringskonto. Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att ge cachen åtkomst till bloblagringen. Två roller krävs: Lagringskontodeltagare och Storage Blob Data-deltagare.

  Följ anvisningarna i Lägg [till lagringsmål för](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) att lägga till rollerna.

## <a name="storage-infrastructure"></a>Lagringsinfrastruktur
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

Cachen stöder Azure Blob-containrar, NFS-maskinvarulagringsexporter och NFS-monterade ADLS-blobcontainrar (för närvarande i förhandsversion). Lägg till lagringsmål när du har skapat cachen.

Varje lagringstyp har specifika krav.

### <a name="blob-storage-requirements"></a>Krav för Blob Storage

Om du vill använda Azure Blob Storage med ditt cacheminne behöver du ett kompatibelt lagringskonto och antingen en tom blobcontainer eller en container som är ifylld med Azure HPC Cache-formaterade data enligt beskrivningen i Flytta [data till Azure Blob Storage](hpc-cache-ingest.md).

> [!NOTE]
> Olika krav gäller för NFS-monterad bloblagring. Mer [information finns i Lagringskrav för ADLS-NFS.](#nfs-mounted-blob-adls-nfs-storage-requirements-preview)

Skapa kontot innan du försöker lägga till ett lagringsmål. Du kan skapa en ny container när du lägger till målet.

Om du vill skapa ett kompatibelt lagringskonto använder du någon av följande kombinationer:

| Prestanda | Typ | Replikering | Åtkomstnivå |
|--|--|--|--|
| Standard | StorageV2 (generell användning v2)| Lokalt redundant lagring (LRS) eller zonredundant lagring (ZRS) | Frekvent |
| Premium | Blockblobar | Lokalt redundant lagring (LRS) | Frekvent |

Lagringskontot måste vara tillgängligt från cachens privata undernät. Om ditt konto använder en privat slutpunkt eller en offentlig slutpunkt som är begränsad till specifika virtuella nätverk ser du till att aktivera åtkomst från cachens undernät. (En öppen offentlig slutpunkt rekommenderas inte.)

Det är en bra idé att använda ett lagringskonto i samma Azure-region som din cache.

Du måste också ge cacheprogrammet åtkomst till ditt Azure-lagringskonto enligt [behörigheterna](#permissions)ovan. Följ proceduren i Lägg [till lagringsmål för](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) att ge cachen de åtkomstroller som krävs. Om du inte är ägare till lagringskontot ska du be ägaren att göra det här steget.

### <a name="nfs-storage-requirements"></a>Krav för NFS-lagring
<!-- linked from configuration.md -->

Om du använder ett NFS-lagringssystem (till exempel ett lokalt NAS-maskinvarusystem) kontrollerar du att det uppfyller dessa krav. Du kan behöva arbeta med nätverksadministratörer eller brandväggshanterare för ditt lagringssystem (eller datacenter) för att verifiera de här inställningarna.

> [!NOTE]
> Det går inte att skapa lagringsmålet om cachen inte har tillräcklig åtkomst till NFS-lagringssystemet.

Mer information finns i Felsöka [PROBLEM med NAS-konfiguration och NFS-lagringsmål.](troubleshoot-nas.md)

* Nätverksanslutning: Azure HPC Cache behöver nätverksåtkomst med hög bandbredd mellan cacheundernätet och NFS-systemets datacenter. [ExpressRoute](../expressroute/index.yml) eller liknande åtkomst rekommenderas. Om du använder ett VPN kan du behöva konfigurera det för att slå ihop TCP MSS vid 1350 för att se till att stora paket inte blockeras. Läs [om storleksbegränsningar för VPN-paket](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) för ytterligare hjälp med felsökning av VPN-inställningar.

* Portåtkomst: Cachen behöver åtkomst till specifika TCP/UDP-portar i lagringssystemet. Olika typer av lagring har olika portkrav.

  Följ den här proceduren om du vill kontrollera inställningarna för ditt lagringssystem.

  * Utfärda ett `rpcinfo` kommando till lagringssystemet för att kontrollera de portar som behövs. Kommandot nedan visar portarna och formaterar relevanta resultat i en tabell. (Använd systemets IP-adress i stället för *<storage_IP>* termen.)

    Du kan utfärda det här kommandot från valfri Linux-klient som har NFS-infrastruktur installerad. Om du använder en klient i klustrets undernät kan det också hjälpa dig att verifiera anslutningen mellan undernätet och lagringssystemet.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Kontrollera att alla portar som returneras av frågan tillåter obegränsad ``rpcinfo`` trafik från Azure HPC Cache undernätet.

  * Om du inte kan använda kommandot `rpcinfo` kontrollerar du att dessa vanliga portar tillåter inkommande och utgående trafik:

    | Protokoll | Port  | Tjänst  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | monterat   |
    | TCP/UDP  | 4047  | status   |

    Vissa system använder olika portnummer för dessa tjänster – se lagringssystemets dokumentation för att vara säker.

  * Kontrollera brandväggsinställningarna för att se till att de tillåter trafik på alla dessa portar som krävs. Se till att kontrollera brandväggar som används i Azure och lokala brandväggar i ditt datacenter.

* Rotåtkomst (läsa/skriva): Cachen ansluter till backend-systemet som användar-ID 0. Kontrollera de här inställningarna i ditt lagringssystem:
  
  * Aktivera `no_root_squash` . Det här alternativet säkerställer att fjärrrotanvändaren kan komma åt filer som ägs av roten.

  * Kontrollera exportprinciperna för att se till att de inte innehåller begränsningar för rotåtkomst från cachens undernät.

  * Om lagringen har exporter som är underkataloger till en annan export kontrollerar du att cachen har rotåtkomst till sökvägens lägsta segment. Mer [information finns i Root access on directory paths](troubleshoot-nas.md#allow-root-access-on-directory-paths) (Rotåtkomst på katalogsökvägar) i felsökningsartikeln för NFS-lagringsmål.

* NFS-backend-lagring måste vara en kompatibel maskin-/programvaruplattform. Kontakta Azure HPC Cache för mer information.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Lagringskrav för NFS-monterad blob (ADLS-NFS) (FÖRHANDSVERSION)

Azure HPC Cache också använda en blobcontainer monterad med NFS-protokollet som lagringsmål.

> [!NOTE]
> Stöd för NFS 3.0-protokollet för Azure Blob Storage är i offentlig förhandsversion. Tillgängligheten är begränsad och funktionerna kan ändras från och med nu och när funktionen blir allmänt tillgänglig. Använd inte förhandsgranskningsteknik i produktionssystem.
>
> Läs mer om den här förhandsgranskningsfunktionen [i protokollstöd för NFS 3.0 i Azure Blob Storage.](../storage/blobs/network-file-system-protocol-support.md)

Kraven på lagringskonto skiljer sig för ett ADLS-NFS-bloblagringsmål och för ett standardlagringsmål för blobar. Följ anvisningarna i Montera Blob Storage med hjälp av [protokollet Network File System (NFS) 3.0](../storage/blobs/network-file-system-protocol-support-how-to.md) noggrant för att skapa och konfigurera det NFS-aktiverade lagringskontot.

Det här är en allmän översikt över stegen. De här stegen kan ändras, så läs alltid [ADLS-NFS-instruktionerna](../storage/blobs/network-file-system-protocol-support-how-to.md) för aktuell information.

1. Kontrollera att de funktioner du behöver är tillgängliga i de regioner där du planerar att arbeta.

1. Aktivera funktionen NFS-protokoll för din prenumeration. Gör detta *innan* du skapar lagringskontot.

1. Skapa ett säkert virtuellt nätverk (VNet) för lagringskontot. Du bör använda samma virtuella nätverk för ditt NFS-aktiverade lagringskonto och för ditt Azure HPC Cache. (Använd inte samma undernät som cachen.)

1. Skapa lagringskontot.

   * I stället för att använda inställningarna för lagringskontot för ett bloblagringskonto av standardnivå följer du anvisningarna i [instruktionsdokumentet](../storage/blobs/network-file-system-protocol-support-how-to.md). Vilken typ av lagringskonto som stöds kan variera beroende på Azure-region.

   * I avsnittet Nätverk väljer du en privat slutpunkt i det säkra virtuella nätverk som du skapade (rekommenderas) eller väljer en offentlig slutpunkt med begränsad åtkomst från det säkra virtuella nätverket.

   * Glöm inte att slutföra avsnittet Avancerat, där du aktiverar NFS-åtkomst.

   * Ge cacheprogrammet åtkomst till ditt Azure Storage-konto enligt vad som anges [i Behörigheter](#permissions)ovan. Det kan du göra första gången du skapar ett lagringsmål. Följ proceduren i Lägg [till lagringsmål för](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) att ge cachen de åtkomstroller som krävs.

     Om du inte är lagringskontoägare be ägaren att göra det här steget.

Läs mer om att använda ADLS-NFS-lagringsmål med Azure HPC Cache i [Använda NFS-monterad bloblagring med Azure HPC Cache](nfs-blob-considerations.md).

## <a name="set-up-azure-cli-access-optional"></a>Konfigurera Azure CLI-åtkomst (valfritt)

Om du vill skapa eller hantera Azure HPC Cache från Azure-kommandoradsgränssnittet (Azure CLI) måste du installera CLI-programvaran och hpc-cache-tillägget. Följ anvisningarna i [Konfigurera Azure CLI för Azure HPC Cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure HPC Cache-instans](hpc-cache-create.md) från Azure Portal
