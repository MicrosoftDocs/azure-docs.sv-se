---
title: Red Hat Enterprise Linux avbildningar som är tillgängliga i Azure
description: Lär dig mer om Red Hat Enterprise Linux avbildningar i Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 0a4d2ef5b7f367130151fabda3f1d97b65605931
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676041"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux-avbildningar (RHEL) som är tillgängliga i Azure
Azure erbjuder en rad olika RHEL-avbildningar för olika användnings fall.

> [!NOTE]
> Alla RHEL-avbildningar är tillgängliga i Azures offentliga och Azure Government moln. De är inte tillgängliga i Azures moln i Kina.

## <a name="list-of-rhel-images"></a>Lista över RHEL-avbildningar
Det här är en lista över RHEL-avbildningar som är tillgängliga i Azure. Om inget annat anges är alla avbildningar LVM och är kopplade till vanliga RHEL-databaser (inte EUS, inte E4S). Följande bilder är för närvarande tillgängliga för allmänt bruk:

> [!NOTE]
> RAW-avbildningar skapas inte längre i förmån för LVM-partitionerade avbildningar. LVM ger flera fördelar jämfört med den äldre RAW-partitionen (icke-LVM), inklusive avsevärt mer flexibla alternativ för att ändra storlek på partitioner.

Erbjudande| SKU | Partitionering | Etablering | Kommentarer
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | OUTSPÄDD    | Linux-agent | Stöd för utökad livs cykel är tillgängligt från och med 1 december. [Mer information finns här.](redhat-extended-lifecycle-support.md)
|             | 6.8      | OUTSPÄDD    | Linux-agent | Stöd för utökad livs cykel är tillgängligt från och med 1 december. [Mer information finns här.](redhat-extended-lifecycle-support.md)
|             | 6,9      | OUTSPÄDD    | Linux-agent | Stöd för utökad livs cykel är tillgängligt från och med 1 december. [Mer information finns här.](redhat-extended-lifecycle-support.md)
|             | 6,10     | OUTSPÄDD    | Linux-agent | Stöd för utökad livs cykel är tillgängligt från och med 1 december. [Mer information finns här.](redhat-extended-lifecycle-support.md)
|             | 7 – RAW    | OUTSPÄDD    | Linux-agent | RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS).
|             | 7-LVM    | LVM    | Linux-agent | RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS). Om du letar efter en standard RHEL-avbildning som ska distribueras använder du den här uppsättningen avbildningar och/eller dess andra motsvarighet 2.
|             | 7lvm – Gen2| LVM    | Linux-agent | Generation 2, RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS). Om du letar efter en standard RHEL-avbildning som ska distribueras använder du den här uppsättningen avbildningar och/eller dess motsvarande generation 1.
|             | 7-RAW-CI | RAW-CI | init  | RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS).
|             | 7.2      | OUTSPÄDD    | Linux-agent |
|             | 7.3      | OUTSPÄDD    | Linux-agent |
|             | 7,4      | OUTSPÄDD    | Linux-agent | Anslutet till EUS-databaser som standard från april 2019.
|             | 74 – Gen2  | OUTSPÄDD    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 7.5      | OUTSPÄDD    | Linux-agent | Anslutet till EUS-databaser som standard från och med juni 2019.
|             | 75 – Gen2  | OUTSPÄDD    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 7,6      | OUTSPÄDD    | Linux-agent | Bifogad till EUS-databaser som standard från maj 2019.
|             | 76 – Gen2  | OUTSPÄDD    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 7,7      | LVM    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 77 – Gen2  | LVM    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 7,8      | LVM    | Linux-agent | Anslutet till vanliga databaser (EUS är inte tillgängliga för RHEL 7,8)
|             | 78 – Gen2  | LVM    | Linux-agent | Anslutet till vanliga databaser (EUS är inte tillgängliga för RHEL 7,8)
|             | 7,9      | LVM    | Linux-agent | Anslutet till vanliga databaser (EUS är inte tillgängliga för RHEL 7,9)
|             | 79 – Gen2  | LVM    | Linux-agent | Anslutet till vanliga databaser (EUS är inte tillgängliga för RHEL 7,9)
|             | 8-LVM    | LVM    | Linux-agent | RHEL 8. x-serien med bilder. Anslutet till vanliga databaser.
|             | 8-LVM-Gen2| LVM    | Linux-agent | Hyper-V generation 2-RHEL 8. x-serien med bilder. Anslutet till vanliga databaser.
|             | 8        | LVM    | Linux-agent | RHEL 8,0-avbildningar.
|             | 8-Gen2   | LVM    | Linux-agent | Hyper-V generation 2-RHEL 8,0-avbildningar.
|             | 8.1      | LVM    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 81gen2   | LVM    | Linux-agent | Hyper-V generation 2 – ansluten till EUS-databaser från och med november 2020.
|             | 8,1 – CI   | LVM    | Linux-agent | Bifogad till EUS-databaser från och med november 2020.
|             | 81 – CI-Gen2| LVM    | Linux-agent | Hyper-V generation 2 – ansluten till EUS-databaser från och med november 2020.
|             | 8,2      | LVM    | Linux-agent | Bifogad till EUS-databaser från och med november 2020.
|             | 82gen2   | LVM    | Linux-agent | Hyper-V generation 2 – ansluten till EUS-databaser från och med november 2020.
|             | 8.3   | LVM    | Linux-agent |  Anslutet till vanliga databaser (EUS är inte tillgängliga för RHEL 8,3)
|             | 83 – Gen2   | LVM    | Linux-agent |Hyper-V generation 2 – ansluten till vanliga databaser (EUS är inte tillgänglig för RHEL 8,3)
RHEL – SAP      | 7,4      | LVM    | Linux-agent | RHEL 7,4 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 74sap – Gen2| LVM    | Linux-agent | RHEL 7,4 för SAP HANA-och Business-appar. Avbildning av generation 2. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 7.5       | LVM    | Linux-agent | RHEL 7,5 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 75sap – Gen2| LVM    | Linux-agent | RHEL 7,5 för SAP HANA-och Business-appar. Avbildning av generation 2. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 7,6       | LVM    | Linux-agent | RHEL 7,6 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 76sap – Gen2| LVM    | Linux-agent | RHEL 7,6 för SAP HANA-och Business-appar. Avbildning av generation 2. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 7,7       | LVM    | Linux-agent | RHEL 7,7 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
RHEL – SAP-HANA (som ska tas bort i november 2020) | 6.7       | OUTSPÄDD    | Linux-agent | RHEL 6,7 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningarna. Den här avbildningen tas bort i november 2020. Mer information om Red Hats SAP Cloud-erbjudanden finns [här](https://access.redhat.com/articles/3751271).
|             | 7.2       | LVM    | Linux-agent | RHEL 7,2 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningarna. Den här avbildningen tas bort i november 2020. Mer information om Red Hats SAP Cloud-erbjudanden finns [här](https://access.redhat.com/articles/3751271).
|             | 7.3       | LVM    | Linux-agent | RHEL 7,3 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningarna. Den här avbildningen tas bort i november 2020. Mer information om Red Hats SAP Cloud-erbjudanden finns [här](https://access.redhat.com/articles/3751271).
RHEL – SAP-APPS | 6.8       | OUTSPÄDD    | Linux-agent | RHEL 6,8 för SAP Business Applications. Föråldrad till förmån för RHEL-SAP-avbildningarna.
|             | 7.3       | LVM    | Linux-agent | RHEL 7,3 för SAP Business Applications. Föråldrad till förmån för RHEL-SAP-avbildningarna.
|             | 7,4       | LVM    | Linux-agent | RHEL 7,4 för SAP Business Applications.
|             | 7,6       | LVM    | Linux-agent | RHEL 7,6 för SAP Business Applications.
|             | 7,7       | LVM    | Linux-agent | RHEL 7,7 för SAP Business Applications.
|             | 77 – Gen2       | LVM    | Linux-agent | RHEL 7,7 för SAP Business Applications. Generation 2-avbildning
|             | 8.1       | LVM    | Linux-agent | RHEL 8,1 för SAP Business Applications.
|             | 81 – Gen2      | LVM    | Linux-agent | RHEL 8,1 för SAP Business Applications. Avbildning av generation 2.
|             | 8,2       | LVM    | Linux-agent | RHEL 8,2 för SAP Business Applications.
|             | 82 – Gen2      | LVM    | Linux-agent | RHEL 8,2 för SAP Business Applications. Avbildning av generation 2.
RHEL-HA       | 7,4       | LVM    | Linux-agent | RHEL 7,4 med HA-tillägget. Debiterar ett Premium för HA-och RHEL ovanpå bas beräknings avgiften. Föråldrad till förmån för RHEL-SAP-HA-avbildningarna.
|             | 7.5       | LVM    | Linux-agent | RHEL 7,5 med HA-tillägget. Debiterar ett Premium för HA-och RHEL ovanpå bas beräknings avgiften. Föråldrad till förmån för RHEL-SAP-HA-avbildningarna.
|             | 7,6       | LVM    | Linux-agent | RHEL 7,6 med HA-tillägget. Debiterar ett Premium för HA-och RHEL ovanpå bas beräknings avgiften. Föråldrad till förmån för RHEL-SAP-HA-avbildningarna.
RHEL – SAP-HA   | 7,4          | LVM    | Linux-agent | RHEL 7,4 för SAP med HA och uppdaterings tjänster. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 74sapha – Gen2 | LVM    | Linux-agent | RHEL 7,4 för SAP med HA och uppdaterings tjänster. Avbildning av generation 2. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 7.5          | LVM    | Linux-agent | RHEL 7,5 för SAP med HA och uppdaterings tjänster. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 7,6          | LVM    | Linux-agent | RHEL 7,6 för SAP med HA och uppdaterings tjänster. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 76sapha – Gen2 | LVM    | Linux-agent | RHEL 7,6 för SAP med HA och uppdaterings tjänster. Avbildning av generation 2. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 7,7          | LVM    | Linux-agent | RHEL 7,7 för SAP med HA och uppdaterings tjänster. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 77sapha – Gen2 | LVM    | Linux-agent | RHEL 7,7 för SAP med HA och uppdaterings tjänster. Avbildning av generation 2. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 8.1          | LVM    | Linux-agent | RHEL 8,1 för SAP med HA och uppdaterings tjänster. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 81sapha – Gen2          | LVM    | Linux-agent | RHEL 8,1 för SAP med HA och uppdaterings tjänster. Generation 2-avbildningar kopplade till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 8,2          | LVM    | Linux-agent | RHEL 8,2 för SAP med HA och uppdaterings tjänster. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 82sapha – Gen2          | LVM    | Linux-agent | RHEL 8,2 för SAP med HA och uppdaterings tjänster. Generation 2-avbildningar kopplade till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
RHEL – BYOS     |RHEL – lvm74| LVM    | Linux-agent | RHEL 7,4 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm75| LVM    | Linux-agent | RHEL 7,5 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm76| LVM    | Linux-agent | RHEL 7,6 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL-lvm76-Gen2| LVM    | Linux-agent | RHEL 7,6 generation 2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm77| LVM    | Linux-agent | RHEL 7,7 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL-lvm77-Gen2| LVM    | Linux-agent | RHEL 7,7 generation 2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm78| LVM    | Linux-agent | RHEL 7,8 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL-lvm78-Gen2| LVM    | Linux-agent | RHEL 7,8 generation 2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm8 | LVM    | Linux-agent | RHEL 8,0 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL-lvm8-Gen2 | LVM    | Linux-agent | RHEL 8,0 generation 2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm81 | LVM    | Linux-agent | RHEL 8,1 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL-lvm81-Gen2 | LVM    | Linux-agent | RHEL 8,1 generation 2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm82 | LVM    | Linux-agent | RHEL 8,2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL-lvm82-Gen2 | LVM    | Linux-agent | RHEL 8,2 generation 2 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.

> [!NOTE]
> Produkt erbjudandet RHEL-SAP-HANA anses vara livs längd av Red Hat. Befintliga distributioner fortsätter att fungera normalt, men Red Hat rekommenderar att kunderna migrerar från RHEL-SAP-HANA-avbildningarna till RHEL-SAP-HA-avbildningarna som innehåller SAP HANA-lagringsplatser och HA-tillägget. Mer information om Red Hats SAP Cloud-erbjudanden finns [här](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Red Hat-avbildningarna i Azure](./redhat-images.md).
* Läs mer om den [Red Hat-uppdateringen av infrastrukturen](./redhat-rhui.md).
* Läs mer om [RHEL BYOS-erbjudandet](./byos.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
