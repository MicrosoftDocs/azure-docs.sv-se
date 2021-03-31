---
title: Översikt över Red Hat Enterprise Linux avbildningar i Azure
description: Lär dig mer om Red Hat Enterprise Linux avbildningar i Microsoft Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 42e0788a25efa5124f24a77b48469d6ed8265dfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694690"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Översikt över Red Hat Enterprise Linux avbildningar

I den här artikeln beskrivs tillgängliga Red Hat Enterprise Linux-avbildningar (RHEL) i Azure Marketplace och principer kring deras namn och kvarhållning.

Information om Red Hat support-principer för alla versioner av RHEL finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata). Pris information finns i [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> RHEL-avbildningar som för närvarande är tillgängliga i Azure Marketplace stöder antingen en egen prenumeration (BYOS) eller licensierings modeller enligt principen betala per användning. Dynamisk växling mellan BYOS och licens enligt principen betala per användning kan göras via [Azure Hybrid Use Benefit](../../linux/azure-hybrid-benefit-linux.md).

>[!NOTE]
> Om du har problem som rör RHEL-avbildningar på Azure Marketplace kan du ge ett support ärende till Microsoft.

## <a name="view-images-available-in-azure"></a>Visa tillgängliga bilder i Azure

När du söker efter "Red Hat" i Azure Marketplace eller när du skapar en resurs i Azure Portal användar gränssnittet, visas bara en delmängd av alla tillgängliga RHEL-avbildningar. Du kan alltid hämta en fullständig uppsättning tillgängliga VM-avbildningar med hjälp av Azure CLI, PowerShell och API.

Om du vill se en fullständig uppsättning Red Hat-avbildningar i Azure kör du följande kommando:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Namngivnings konvention

VM-avbildningar i Azure ordnas efter utgivare, erbjudande, SKU och version. Kombinationen av utgivare: erbjudande: SKU: version är avbildningens URN och identifierar den avbildning som ska användas.

Till exempel `RedHat:RHEL:8-LVM:8.1.20200318` refererar till en RHEL 8,1 LVM-partitionerad avbildning som skapats den 18 mars 2020.

Ett exempel på hur du skapar en virtuell RHEL 8,1-dator visas här.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"Senaste" moniker

Azure-REST API tillåter användningen av monikern "nyaste" för versionen i stället för den aktuella versionen. Med "senaste" regleras den senaste tillgängliga avbildningen för den aktuella utgivaren, erbjudandet och SKU: n.

Till exempel `RedHat:RHEL:8-LVM:latest` refererar den senaste RHEL 8-familjen LVM-partitionerad avbildning tillgänglig.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> I allmänhet är jämförelsen av versioner att fastställa de senaste reglerna för [CompareTo-metoden](/dotnet/api/system.version.compareto#system_version_compareto_system_version_).
Denna bild versions jämförelse görs genom att jämföra värdena som ett [versions](/dotnet/api/system.version.-ctor) objekt, inte som en sträng.

## <a name="rhel-6-image-types"></a>RHEL 6-avbildnings typer

>[!NOTE]
> Från och med december 30 2020 har RHEL 6,10 infört livs längd (EOL). För fortsatt support aktiverar du ELS som en del av den utökade support fasen för livs cykeln, se [dokumentationen för Red Hat Els](./redhat-extended-lifecycle-support.md).

För RHEL 6. x-bilder visas bild typerna i följande tabell.

|Publisher | Erbjudande | SKU-värde | Version | Information
|----------|-------|-----------|---------|--------
|Redhat | RHEL | Lägre version (till exempel 6,9) | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 6.9.2018010506) | Alla standard RHEL 6. x-bilder följer denna konvention.
|Redhat | RHEL – BYOS | RHEL – raw69 | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 6.9.20181023) | Den här avbildningen är en RHEL 6,9 BYOS-avbildning.
|Redhat | RHEL | RHEL – SAP-APPS | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 6.8.2017053118) | Den här avbildningen är en avbildning av RHEL 6,8 för SAP-program. Det har rätt att komma åt SAP-programdatabaser och bas RHEL-lagringsplatser.
|Redhat | RHEL | RHEL – SAP-HANA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 6.7.2017053121) | Den här avbildningen är en RHEL 6,7 för SAP HANA avbildning. Det har rätt att komma åt SAP HANA-databaser och Base RHEL-databaser.

## <a name="rhel-7-image-types"></a>Bild typer för RHEL 7

För RHEL 7. x-bilder finns det några olika bild typer. I följande tabell visas de olika uppsättningar med avbildningar vi erbjuder. Om du vill se en fullständig lista använder du Azure CLI-kommandot `az vm image list --publisher redhat --all` .

>[!NOTE]
> Om inget annat anges är alla avbildningar LVM partitionerade och ansluta till vanliga RHEL-databaser. Det vill säga att databaserna inte har utökade uppdaterings stöd (EUS) och inte uppdaterar tjänster för SAP (E4S). Vi går vidare till att bara publicera LVM-partitionerade bilder men är öppna för feedback om det här beslutet. Mer information om stöd för utökad uppdatering och uppdaterings tjänster för SAP finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata).

|Publisher | Erbjudande | SKU-värde | Version | Information
|----------|-------|------------|---------|--------
|Redhat | RHEL | Lägre version (till exempel 7,6) | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019102813) | Avbildningar som publicerats före april 2019 är kopplade till standard RHEL-databaser. Avbildningar som publicerats efter 2019 april är kopplade till Red Hats EUS-databaser för att tillåta versions låsning av en speciell del version. Kunder som vill ha vanliga databaser bör använda de bilder som innehåller 7-LVM eller 7-RAW i SKU-värdet (information följer). RHEL 7,7 och senare bilder är LVM partitionerade. Alla andra bilder i den här kategorin är RAW-partitionerade.
|Redhat | RHEL | 7 – RAW | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019102813) | De här avbildningarna är RAW-partitionerade (till exempel inga logiska volymer har lagts till).
|Redhat | RHEL | 7-RAW-CI | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019072418) | De här avbildningarna är RAW-partitionerade (till exempel inga logiska volymer har lagts till) och använder Cloud-Init för etablering.
|Redhat | RHEL | 7-LVM | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019062414) | De här avbildningarna är LVM partitionerade.
|Redhat | RHEL – BYOS | RHEL – {LVM, RAW} | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.7.20190819) | Dessa bilder är RHEL 7 BYOS-avbildningar. De är inte kopplade till några databaser och debiterar inte RHEL Premium-avgiften. Om du är intresse rad av RHEL BYOS-avbildningar [begär du åtkomst](https://aka.ms/rhel-byos). SKU-värden slutar med den lägre versionen och anger om avbildningen är RAW eller LVM partitionerad. Till exempel indikerar ett SKU-värde för RHEL-lvm77 en LVM-partitionad RHEL 7,7-avbildning.
|Redhat | RHEL | RHEL – SAP | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019071300) | De här avbildningarna är RHEL för SAP-avbildningar. De har rätt att komma åt SAP HANA-och program databaser samt RHEL E4S-databaser. Faktureringen omfattar RHEL Premium och SAP Premium ovanpå bas beräknings avgiften.
|Redhat | RHEL | RHEL – SAP-HA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019062320) | De här avbildningarna är RHEL för SAP med bilder med hög tillgänglighet och uppdaterings tjänster. De har rätt att komma åt SAP HANA-och program Arkiv och databaser för hög tillgänglighet samt RHEL E4S-databaser. Faktureringen omfattar RHEL Premium, SAP Premium och hög tillgänglighet Premium ovanpå bas beräknings avgiften.
|Redhat | RHEL | RHEL-HA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.6.2019062019) | De här avbildningarna är RHEL-avbildningar som också är berättigade till åtkomst till hög tillgänglighet. De debiteras något extra ovanpå RHEL och bas beräknings avgiften på grund av Premium Premium-tillägget.
|Redhat | RHEL | RHEL – SAP-APPS | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.3.2017053118) | De här avbildningarna är inaktuella eftersom SAP-programmen och SAP HANA-databaserna kombinerades till SAP-databaserna. De här avbildningarna är RHEL för SAP-program. De har rätt att komma åt SAP-programdatabaser och bas RHEL-lagringsplatser.
|Redhat | RHEL | RHEL – SAP-HANA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 7.3.2018051421) | De här avbildningarna är inaktuella eftersom SAP-programmen och SAP HANA-databaserna kombinerades till SAP-databaserna. Dessa bilder är RHEL for SAP HANA bilder. De har rätt att komma åt SAP HANA-databaser och Base RHEL-databaser.

## <a name="rhel-8-image-types"></a>RHEL 8-bildtyper

>[!NOTE]
> Red Hat rekommenderar att du använder grubby för att konfigurera kommando rads parametrar för kernel i RHEL 8 +. Mer information finns [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

Information om typer av RHEL 8-avbildningar finns nedan.

|Publisher | Erbjudande | SKU-värde | Version | Information
|----------|-------|------------|---------|--------
|Redhat | RHEL | 8 | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 8.0.20191023) | De här avbildningarna är RHEL 8 LVM-partitionerade avbildningar anslutna till Red Hat-standarddatabaser.
|Redhat | RHEL | 8-Gen2 | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 8.0.20191024) | De här avbildningarna är Hyper-V generation 2 RHEL 8 LVM-partitionerade avbildningar anslutna till Red Hat-standarddatabaser. Mer information om virtuella datorer i generation 2 i Azure finns i [stöd för virtuella datorer i generation 2 i Azure](../../generation-2.md).
|Redhat | RHEL | RHEL – SAP-APPS | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 8.1.2021012201) | De här avbildningarna är RHEL för SAP-program. De har rätt att komma åt SAP-programdatabaser och bas RHEL-lagringsplatser.
|Redhat | RHEL | RHEL – SAP-HA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (till exempel 8.1.2021010602) | De här avbildningarna är RHEL för SAP med bilder med hög tillgänglighet och uppdaterings tjänster. De har rätt att komma åt SAP-lösningar och program databaser och databaser för hög tillgänglighet samt RHEL E4S-databaser. Faktureringen omfattar RHEL Premium, SAP Premium och hög tillgänglighet Premium ovanpå bas beräknings avgiften.

## <a name="rhel-extended-support-add-ons"></a>RHEL Extended support-tillägg

### <a name="extended-life-cycle-support"></a>Stöd för utökad livs cykel

Det utökade ELS-tillägget (Life-Cycle support) är en valfri prenumeration som möjliggör viktiga och viktiga säkerhets korrigeringar för versioner som har nått livs längd (EOL). Mer information om RHEL-EUS finns i [Red Hat-dokumentationen](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support).

ELS är för närvarande endast tillgängligt för RHEL 6,10. För "betala per användning"-avbildningar kan ELS aktive ras genom att följa stegen i [Red Hat Els-dokumentationen](./redhat-extended-lifecycle-support.md).

Om du kör på en äldre version krävs en uppgradering till RHEL 6,10 innan du kan aktivera ELS.

### <a name="extended-update-support"></a>Stöd för utökad uppdatering

Från och med april 2019 är RHEL-avbildningar tillgängliga som är kopplade till EUS-databaserna som standard. Mer information om RHEL-EUS finns i [Red Hat-dokumentationen](https://access.redhat.com/articles/rhel-eus).

Det går att växla till EUS-databaser och stöds. Instruktioner för hur du byter virtuell dator till EUS och mer information om EUS support livs längd finns i [RHEL EUs och versions låsning RHEL VM](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS stöds inte på RHEL extra. Det innebär att om du installerar ett paket som vanligt vis är tillgängligt från RHEL tilläggs kanal kan du inte göra det på EUS. Mer information om den Red Hat extra produkt livs cykeln finns i [Red Hat Enterprise Linux extra livs cykel](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Skilj mellan vanliga och EUS bilder

Kunder som vill använda bilder som är kopplade till EUS-databaser bör använda RHEL-avbildningen som innehåller ett lägre versions nummer för RHEL i SKU: n.

Du kan till exempel se följande två RHEL 7,4-avbildningar som är tillgängliga.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

I det här fallet `RedHat:RHEL:7.6:7.6.2019102813` är som standard kopplad till EUs-databaser. SKU-värdet är 7,4. Och `RedHat:RHEL:7-LVM:7.6.2019062414` är kopplat till icke-EUs arkiv som standard. SKU-värdet är 7-LVM.

Om du vill använda vanliga (icke-EUS) databaser använder du en avbildning som inte innehåller ett lägre versions nummer i SKU: n.

#### <a name="rhel-images-with-eus"></a>RHEL-avbildningar med EUS

Informationen i följande tabell gäller för RHEL-avbildningar som är anslutna till EUS-databaser.

>[!NOTE]
> Vid tidpunkten för skrivning har endast RHEL 7,4 och senare lägre versioner stöd för EUS. EUS stöds inte längre för RHEL <= 7,3.
>
> Mer information om RHEL EUS-tillgänglighet finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata).

Delversion |Exempel på EUS-avbildning              |Status för EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Avbildningar som publicerats april 2019 och senare är EUS som standard.|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | Avbildningar som publicerats i juni 2019 och senare är EUS som standard. |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | Publicerade avbildningar maj 2019 och senare är EUS som standard. |
RHEL 8,0      |Ej tillämpligt                            | Ingen EUS är tillgänglig från Red Hat.                               |

### <a name="update-services-for-sap"></a>Uppdatera tjänster för SAP

De senaste RHEL för SAP-avbildningarna kommer att anslutas till uppdaterings tjänsterna för SAP Solutions-prenumerationer (E4S). Mer information om E4S finns i Red Hat- [dokumentationen](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions).

#### <a name="rhel-images-with-e4s"></a>RHEL-avbildningar med E4S

Avbildningar från följande erbjudanden som skapats efter december 2019 är anslutna till E4S-databaser:

* RHEL – SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL for SAP med hög tillgänglighet och uppdaterings tjänster)

## <a name="other-available-offers-and-skus"></a>Andra tillgängliga erbjudanden och SKU: er

Den fullständiga listan över tillgängliga erbjudanden och SKU: er kan innehålla ytterligare bilder utöver vad som anges i föregående tabell. Ett exempel är `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Dessa erbjudanden kan användas för att ge support för vissa Marketplace-lösningar. Eller så kan de publiceras för för hands versioner och test ändamål. De kan ändras eller tas bort när som helst utan varning. Använd dem inte om deras närvaro inte är offentligt dokumenterad av antingen Microsoft eller Red Hat.

## <a name="publishing-policy"></a>Publicerings princip

Microsoft och Red Hat-uppdaterings avbildningar när nya del versioner släpps, enligt vad som krävs för att åtgärda vissa vanliga sårbarheter och exponeringar (CVEs) eller för tillfälliga konfigurations ändringar eller uppdateringar. Vi strävar efter att ge uppdaterade avbildningar så snart som möjligt inom tre arbets dagar efter en lansering eller tillgänglighet av en CVE-korrigering.

Vi uppdaterar bara den aktuella del versionen i en specifik bild serie. I och med lanseringen av en nyare del version slutar vi att uppdatera den äldre lägre versionen. Med versionen av RHEL 7,6 uppdateras till exempel inte längre RHEL 7,5-avbildningar.

>[!NOTE]
> Aktiva virtuella Azure-datorer som tillhandahålls från RHEL-avbildningar med betala per användning är anslutna till Azure-RHUI och kan ta emot uppdateringar och korrigeringar så fort de släpps av Red Hat och replikeras till Azure-RHUI. Tids inställningen är vanligt vis mindre än 24 timmar efter den officiella versionen av Red Hat. De här virtuella datorerna kräver inte en ny publicerad avbildning för att hämta uppdateringar. Kunderna har fullständig kontroll över när uppdateringen ska initieras.

## <a name="image-retention-policy"></a>Bevarande princip för avbildning

Aktuell princip är att behålla alla tidigare publicerade avbildningar. Vi förbehåller oss rätten att ta bort avbildningar som är kända för att orsaka problem av något slag. Till exempel kan bilder med felaktiga konfigurationer på grund av efterföljande plattforms-eller komponent uppdateringar tas bort. Avbildningar som kan tas bort följer den aktuella Azure Marketplace-principen för att tillhandahålla meddelanden upp till 30 dagar innan avbildningen tas bort.

## <a name="next-steps"></a>Nästa steg

* Om du vill visa en fullständig lista över RHEL-avbildningar i Azure, se [Red Hat Enterprise Linux (RHEL)-avbildningar som är tillgängliga i Azure](./redhat-imagelist.md).
* Mer information om Azure Red Hat-uppdateringen finns i [Red Hat-uppdaterings infrastruktur för virtuella datorer på begäran RHEL i Azure](./redhat-rhui.md).
* Om du vill veta mer om erbjudandet om RHEL BYOS kan du Red Hat Enterprise Linux läsa mer [i få guld avbildningar för prenumerationer i Azure](./byos.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata).
