---
title: Felsök problem med Azure NFS-filresursen – Azure Files
description: Felsök problem med Azure NFS-filresursen.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574473"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Felsöka fil resurser i Azure NFS

Den här artikeln innehåller några vanliga problem som rör Azure NFS-filresurser. Den ger potentiella orsaker och lösningar när dessa problem uppstår.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" misslyckades: Ogiltigt argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Orsak 1: idmapping har inte inaktiverats
Azure Files tillåter inte alfanumeriskt UID/GID. Så idmapping måste vara inaktiverat. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Orsak 2: idmapping har inaktiverats, men aktive ras igen efter att felaktigt fil-/katalog namn har påträffats
Även om idmapping har inaktiverats korrekt, åsidosätts inställningarna för att inaktivera idmapping i vissa fall. När Azure Files till exempel påträffar ett felaktigt fil namn skickar den tillbaka ett fel. När du ser den här felkoden väljer NFS v 4,1 Linux-klienten att återaktivera idmapping och framtida begär Anden skickas igen med alfanumeriskt UID/GID. En lista med tecken som inte stöds på Azure Files finns i den här [artikeln](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Kolon är ett tecken som inte stöds. 

### <a name="workaround"></a>Lösning
Kontrol lera att idmapping har inaktiverats och att inget återaktiverar det och gör sedan följande:

- Demontera resursen
- Inaktivera ID-mappning med # ECHO Y >/sys/module/NFS/Parameters/nfs4_disable_idmapping
- Montera delningen
- Om du kör rsync kör du rsync med argumentet "– numeric-ID" från katalogen som inte har något felaktigt katalog-/fil namn.

## <a name="unable-to-create-an-nfs-share"></a>Det gick inte att skapa en NFS-resurs

### <a name="cause-1-subscription-is-not-enabled"></a>Orsak 1: prenumerationen är inte aktive rad

Din prenumeration har kanske inte registrerats för för hands versionen av Azure Files NFS. Du måste köra ytterligare en cmdletarna från antingen Cloud Shell eller en lokal Terminal för att aktivera funktionen.

> [!NOTE]
> Du kan behöva vänta upp till 30 minuter innan registreringen är klar.


#### <a name="solution"></a>Lösning

Använd följande skript för att registrera funktionen och resurs leverantören, Ersätt `<yourSubscriptionIDHere>` innan du kör skriptet:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Orsak 2: inställningar för lagrings konto som inte stöds

NFS är endast tillgängligt på lagrings konton med följande konfiguration:

- Nivå – Premium
- Konto Natura-FileStorage
- Regioner – [lista över regioner som stöds](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Lösning

Följ anvisningarna i vår artikel: [så här skapar du en NFS-resurs](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Orsak 3: lagrings kontot skapades innan registreringen slutfördes

För att ett lagrings konto ska kunna använda funktionen måste det skapas när prenumerationen har slutfört registreringen för NFS. Det kan ta upp till 30 minuter innan registreringen är klar.

#### <a name="solution"></a>Lösning

När registreringen är klar följer du anvisningarna i vår artikel: [så här skapar du en NFS-resurs](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Det går inte att ansluta till eller montera en fil resurs i Azure NFS

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Orsak 1: begäran kommer från en klient i ett ej betrott nätverk/ej betrodda IP-adresser

Till skillnad från SMB har NFS inte användar-baserad autentisering. Autentiseringen för en resurs baseras på nätverks säkerhets regelns konfiguration. På grund av detta måste du antingen använda tjänstens slut punkt eller privata slut punkter för att säkerställa att endast säkra anslutningar upprättas till din NFS-resurs. För att få åtkomst till resurser lokalt, förutom privata slut punkter, måste du konfigurera VPN eller ExpressRoute. IP-adresser som läggs till i lagrings kontots lista över tillåtna för brand väggen ignoreras. Du måste använda någon av följande metoder för att konfigurera åtkomst till en NFS-resurs:


- [Tjänst slut punkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Används av den offentliga slut punkten
    - Endast tillgängligt i samma region.
    - VNet-peering ger inte åtkomst till din resurs.
    - Varje virtuellt nätverk eller undernät måste läggas till separat i listan över tillåtna.
    - För lokal åtkomst kan tjänst slut punkter användas med ExpressRoute-, punkt-till-plats-och plats-till-plats-VPN, men vi rekommenderar att du använder privat slut punkt eftersom det är säkrare.

Följande diagram visar anslutningar med hjälp av offentliga slut punkter.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagram över anslutning till offentliga slut punkter." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Privat slutpunkt](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Åtkomst är säkrare än tjänstens slut punkt.
    - Åtkomst till NFS-resurs via privat länk är tillgänglig i och utanför lagrings kontots Azure-region (över flera regioner lokalt)
    - Virtuell nätverks-peering med virtuella nätverk som finns i den privata slut punkten ger NFS-resurs åtkomst till klienter i peer-kopplade virtuella nätverk.
    - Privata slut punkter kan användas med ExpressRoute-, punkt-till-plats-och plats-till-plats-VPN.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagram över anslutning till privat slut punkt." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Orsak 2: säker överföring krävs har Aktiver ATS

Double Encryption stöds inte för NFS-resurser än. Azure tillhandahåller ett krypterings lager för alla data som överförs mellan Azure-datacenter med hjälp av MACSec. NFS-resurser kan bara nås från betrodda virtuella nätverk och via VPN-tunnlar. Ingen ytterligare kryptering av transport skikt är tillgängligt på NFS-resurser.

#### <a name="solution"></a>Lösning

Inaktivera säker överföring som krävs i ditt lagrings kontos konfigurations blad.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Skärm bild som visar bladet lagrings konto konfiguration och inaktiverar säker överföring krävs.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Orsak 3: NFS-vanligt paket har inte installerats
Innan du kör monterings kommandot installerar du paketet genom att köra det distribution kommandot nedan.

Kontrol lera om NFS-paketet är installerat genom att köra: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Lösning

Om paketet inte är installerat installerar du paketet på din distribution.

##### <a name="ubuntu-or-debian"></a>Ubuntu eller Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Använd DNF Package Manager: `sudo dnf install nfs-common` .

Äldre versioner av Red Hat Enterprise Linux och CentOS använder yum Package Manager: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Använd zypper Package Manager: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Orsak 4: brand vägg blockerar port 2049

NFS-protokollet kommunicerar med servern via port 2049, kontrol lera att porten är öppen för lagrings kontot (NFS-servern).

#### <a name="solution"></a>Lösning

Kontrol lera att port 2049 är öppen på klienten genom att köra följande kommando: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Om porten inte är öppen öppnar du den.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>LS (list Files) visar felaktigt/inkonsekventa resultat

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Orsak: inkonsekvens mellan cachelagrade värden och metadata för Server filen när fil referensen är öppen
Ibland visar kommandot "List Files" en storlek som inte är noll som förväntat och i det allra följande List fils kommandot visas i stället storlek 0 eller en mycket gammal tidstämpel. Detta är ett känt problem på grund av Inkonsekvent cachelagring av fil-metadata värden när filen är öppen. Du kan använda någon av följande lösningar för att lösa problemet:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Lösning 1: Använd WC-c i stället för ls-l för att hämta fil storlek.
Att använda WC-c hämtar alltid det senaste värdet från servern och har inte någon inkonsekvens.

#### <a name="workaround-2-use-noac-mount-flag"></a>Lösning 2: Använd flaggan "NOAC" (Mount)
Montera om fil systemet med hjälp av flaggan "NOAC" med monterings kommandot. Alla metadata-värden från servern hämtas alltid. Det kan finnas mindre prestanda för alla metadata-åtgärder om den här lösningen används.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.
