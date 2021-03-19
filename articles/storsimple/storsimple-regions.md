---
title: Tillgänglighet för StorSimple-regionen
description: Förklarar de Azure-regioner där de olika StorSimple enhets modellerna är tillgängliga.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5efb87ad674474b2e21b86b5cdc48785753a51dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564159"
---
# <a name="available-regions-for-your-storsimple"></a>Tillgängliga regioner för din StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Azure-datacentren arbetar i flera geografiska områden runtom i världen för att uppfylla kundens krav på prestanda, krav och inställningar för data lokalisering. En Azure-geografi är ett definierat område i världen som innehåller minst en Azure-region. En Azure-region är ett område inom ett geografiskt område som innehåller ett eller flera data Center.

Att välja en Azure-region är mycket viktigt och valet av region påverkas av faktorer som data placering och suveränitet, tjänst tillgänglighet, prestanda, kostnad och redundans. För mer information om hur du väljer en region, gå till [vilken Azure-region passar mig?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

För StorSimple-lösning bestäms valet av region specifikt genom följande faktorer:

- Regioner där tjänsten StorSimple Enhetshanteraren är tillgänglig.
- De länder/regioner där StorSimple fysiska, moln eller virtuell enhet är tillgänglig.
- De regioner där de lagrings konton som lagrar StorSimple-data ska finnas för optimala prestanda.

I den här självstudien beskrivs regionens tillgänglighet för StorSimple Enhetshanteraren-tjänsten, de lokala fysiska och moln enheterna. Informationen i den här artikeln gäller för enheter med StorSimple 8000 och 1200-serien.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Regions tillgänglighet för StorSimple Enhetshanteraren-tjänsten

StorSimple Enhetshanteraren-tjänsten stöds för närvarande i 12 offentliga regioner och 2 Azure Government regioner.

Du definierar en region eller plats när du först skapar tjänsten StorSimple Enhetshanteraren. I allmänhet väljs en plats närmast den geografiska region där enheten distribueras. Men enheten och tjänsten kan också distribueras på olika platser.

Här är en lista över regioner där StorSimple Enhetshanteraren-tjänsten är tillgänglig för Azures offentliga moln och kan distribueras.

![Skärm bild som visar StorSimple-Enhetshanteraren med plats listan markerad.](./media/storsimple-region/storsimple-device-manager-service-regions.png)

För Azure Government Cloud är tjänsten StorSimple Enhetshanteraren tillgänglig i US Gov, Iowa och US Gov, Virginia Data Center.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Regions tillgänglighet för data som lagras i StorSimple

StorSimple-data lagras fysiskt i Azure Storage-konton och dessa konton är tillgängliga i alla Azure-regioner. När du skapar ett Azure Storage-konto väljs den primära platsen för lagrings kontot och den region där data finns.

När du först skapar en StorSimple Enhetshanteraren-tjänst och associerar ett lagrings konto med det kan din StorSimple Enhetshanteraren-tjänst och Azure-lagring finnas på två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat.

I allmänhet väljer du den närmaste regionen för din tjänst för ditt lagrings konto. Närmaste Microsoft Azure region kanske inte faktiskt är den region som har den lägsta svars tiden. Det är svars tiden som avgör nätverks tjänstens prestanda och därmed lösningens prestanda. Så om du väljer ett lagrings konto i en annan region är det viktigt att veta vad fördröjningarna är mellan tjänsten och den region som är kopplad till ditt lagrings konto.

Om du använder en StorSimple Cloud Appliance rekommenderar vi att tjänsten och det tillhör ande lagrings kontot finns i samma region. Lagrings konton i en annan region kan leda till dåliga prestanda.

## <a name="availability-of-storsimple-device"></a>Tillgänglighet för StorSimple-enhet

Beroende på modellen kan StorSimple-enheter vara tillgängliga i olika geografiska områden eller länder/regioner.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fysisk enhet (modell 8100/8600)

Om du använder en fysisk enhet på StorSimple 8100 eller 8600 är enheten tillgänglig i följande länder/regioner.

| #  | Land/region        | #  | Land/region     | #  | Land/region      | #  | Land/region             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australien             | 16 | Folkrepubliken Kinas särskilda administrativa region Hongkong      | 31 | Nya Zeeland         | 46 | Sydafrika               |
| 2  | Österrike               | 17 | Ungern            | 32 | Nigeria             | 47 | Sydkorea                |
| 3  | Bahrain               | 18 | Island            | 33 | Norge              | 48 | Spanien                      |
| 4  | Belgien               | 19 | Indien              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brasilien                | 20 | Indonesien          | 35 | Filippinerna         | 50 | Sverige                     |
| 6  | Kanada                | 21 | Irland            | 36 | Polen              | 51 | Schweiz                |
| 7  | Chile                 | 22 | Israel             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Colombia              | 23 | Italien              | 38 | Puerto Rico         | 53 | Thailand                   |
| 9  | Tjeckien        | 24 | Japan              | 39 | Qatar               | 54 | Turkiet                     |
| 10 | Danmark               | 25 | Kenya              | 40 | Rumänien             | 55 | Ukraina                    |
| 11 | Egypten                 | 26 | Kuwait             | 41 | Ryssland              | 56 | Förenade Arabemiraten       |
| 12 | Finland               | 27 | Folkrepubliken Kinas särskilda administrativa region Macao          | 42 | Saudiarabien        | 57 | Storbritannien             |
| 13 | Frankrike                | 28 | Malaysia           | 43 | Singapore           | 58 | USA              |
| 14 | Tyskland               | 29 | Mexico             | 44 | Slovakien            | 59 | Vietnam                    |
| 15 | Grekland                | 30 | Nederländerna        | 45 | Slovenien            | 60 | Kroatien                    |

Den här listan ändras när fler länder/regioner läggs till. Om du vill ha en uppdaterad lista över geografiska områden går du till bilaga för lagrings mat ris villkor i [produkt villkoren](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Microsoft kan leverera fysisk maskin vara och tillhandahålla ersättnings reserv delar för StorSimple till geografierna i föregående lista.

> [!IMPORTANT]
> Placera inte en fysisk StorSimple-enhet i en region där StorSimple inte stöds. Microsoft kommer inte att kunna leverera ersättnings delar till länder/regioner där StorSimple inte stöds.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modeller 8010/8020)

Om du använder en StorSimple Cloud Appliance 8010 eller 8020, stöds enheten och är tillgänglig i alla regioner där den underliggande virtuella datorn stöds. 8010 använder en _Standard_A3_ VM som stöds i alla Azure-regioner.

8020 använder Premium Storage och _Standard_DS3_ VM för att skapa en moln installation. 8020 stöds i Azure-regioner som stöder Premium Storage och _Standard_DS3_ virtuella Azure-datorer. Använd [listan](https://azure.microsoft.com/regions/services/) för att se om både **virtuella datorer > DS-serien** och **lagring > disklagring** är tillgängligt i din region.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple virtuell matris (modell 1200)

Om du använder en 1200-seriens StorSimple virtuella matris, stöds den virtuella disk avbildningen i alla Azure-regioner.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [prissättningen för olika StorSimple-modeller](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Läs mer om hur [du hanterar ditt StorSimple-lagrings konto](storsimple-8000-manage-storage-accounts.md).
* Lär dig mer om hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
