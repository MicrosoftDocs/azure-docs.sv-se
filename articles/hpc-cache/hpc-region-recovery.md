---
title: Regional redundans och återställning av redundans med Azure HPC cache
description: Tekniker för att tillhandahålla redundans för haveri beredskap med Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 9159807f55ae52393b8fccec339fcc94c3e4ebb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87061374"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Använd flera cacheminnen för regional redundansväxling

Varje Azure HPC cache-instans körs inom en viss prenumeration och i en region. Det innebär att ditt cache-arbetsflöde kanske inte kan störas om regionen har ett fullständigt avbrott.

I den här artikeln beskrivs en strategi för att minska risken för arbets avbrott genom att använda en andra region för cachelagring av redundans.

Nyckeln använder Server dels lagring som kan nås från flera regioner. Lagringen kan antingen vara ett lokalt NAS-system med lämpligt DNS-stöd eller Azure Blob Storage som finns i en annan region än cachen.

När arbets flödet fortsätter i din primära region, sparas data i den långsiktiga lagringen utanför regionen. Om cache-regionen blir otillgänglig kan du skapa en dubblett av Azure HPC-instansen i en sekundär region, ansluta till samma lagrings utrymme och återuppta arbetet från det nya cacheminnet.

## <a name="planning-for-regional-failover"></a>Planera för regional redundans

Följ dessa steg om du vill konfigurera ett cacheminne som är för berett för möjlig redundans:

1. Se till att Server dels lagringen är tillgänglig i en andra region.
1. När du planerar att skapa den primära cache-instansen bör du också förbereda för att replikera den här installations processen i den andra regionen. Ta med följande objekt:

   1. Virtuellt nätverk och en under näts struktur
   1. Cache-kapacitet
   1. Lagrings mål information, namn och sökvägar för namn områden
   1. Information om klient datorer, om de finns i samma region som cachen
   1. Monterings kommando för användning av cache-klienter

   > [!NOTE]
   > Azure HPC-cache kan skapas via programmering, antingen via en [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) eller genom direkt åtkomst till dess API. Kontakta Azure HPC-teamet för mer information.

## <a name="failover-example"></a>Exempel på redundans

Anta till exempel att du vill hitta din Azure HPC-cache i Azures region USA, östra. Den kommer att få åtkomst till data som lagras i ditt lokala data Center.

Du kan använda en cache i regionen USA, västra 2 som en säkerhets kopia av redundans.

När du skapar cachen i östra USA förbereder du en andra cache för distribution i USA, västra 2. Du kan automatisera förberedelsen genom att använda skript eller mallar.

Om det finns ett lands omfattande haveri i östra USA skapar du cachen som du har för berett i regionen USA, västra 2.

När cachen har skapats lägger du till lagrings mål som pekar på samma lokala data lager och använder samma aggregerade namn rymds sökvägar som den gamla cachens lagrings mål.

Om de ursprungliga klienterna påverkas skapar du nya klienter i regionen USA, västra 2, för användning med det nya cacheminnet.

Alla klienter måste montera det nya cacheminnet, även om klienterna inte påverkades av det här regions avbrottet. Den nya cachen har olika monterings adresser från den gamla.

## <a name="learn-more"></a>Läs mer

Azure Application Architecture-guiden innehåller mer information om hur du [återställer från ett brett tjänst avbrott](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
