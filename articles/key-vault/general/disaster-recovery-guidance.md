---
title: Azure Key Vault tillgänglighet och redundans – Azure Key Vault | Microsoft Docs
description: Läs mer om hur du Azure Key Vault tillgänglighet och redundans.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 27184e267bb0472dad6fc9176dfdeee68d5eae58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611828"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Tillgänglighet och redundans för Azure Key Vault

Azure Key Vault har flera lager av redundans för att se till att dina nycklar och hemligheter är tillgängliga för ditt program även om enskilda komponenter i tjänsten inte fungerar.

> [!NOTE]
> Den här guiden gäller för valv. Hanterade HSM-pooler använder en annan hög tillgänglighet och katastrof återställnings modell. Mer information finns i [Översikt över hanterad HSM-återställning](../managed-hsm/disaster-recovery-guide.md) .

Innehållet i ditt nyckel valv replikeras i regionen och till en sekundär region minst 150 mil, men inom samma geografiska område för att upprätthålla hög hållbarhet för dina nycklar och hemligheter. Mer information om vissa region par finns i [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md). Undantaget till de Parada regionerna är södra Brasilien, vilket innebär att endast möjligheten att förvara data som är bosatta i södra Brasilien. Södra Brasilien använder Zone redundant lagring (ZRS) för att replikera dina data tre gånger inom den enda platsen/regionen. För AKV Premium används bara 2 av de tre regionerna för att replikera data från HSM: s.  

Om enskilda komponenter i Key Vault-tjänsten kraschar kan alternativa komponenter i regions steget i hantera din begäran för att se till att det inte finns någon försämring av funktionaliteten. Du behöver inte vidta några åtgärder för att starta den här processen, det sker automatiskt och blir transparent för dig.

I sällsynta fall där en hel Azure-region inte är tillgänglig dirigeras de begär Anden som du skapar Azure Key Vault i den regionen automatiskt (*redundansväxlas*) till en sekundär region, förutom i den södra Brasilien regionen. När den primära regionen är tillgänglig igen dirigeras begär Anden tillbaka (*växlas tillbaka*) till den primära regionen. Du behöver inte vidta några åtgärder eftersom detta sker automatiskt.

I regionen Södra Brasilien måste du planera för återställning av dina Azure Key Vault i ett regions haveri scenario. Om du vill säkerhetskopiera och återställa ditt Azure Key Vault till en region som du väljer, slutför du stegen som beskrivs i [Azure Key Vault säkerhets kopiering](backup.md). 

Med den här designen för hög tillgänglighet behöver Azure Key Vault ingen stillestånds tid för underhålls aktiviteter.

Det finns några varningar som kan vara medvetna om:

* I händelse av en redundansväxling av en region kan det ta några minuter för tjänsten att redundansväxla. Begär Anden som görs under den här tiden innan redundansväxlingen kan Miss lyckas.
* Om du använder en privat länk för att ansluta till nyckel valvet kan det ta upp till 20 minuter innan anslutningen har återupprättats i händelse av en redundansväxling. 
* Under redundansväxlingen är ditt nyckel valv i skrivskyddat läge. Begär Anden som stöds i det här läget är:
  * Visa lista över certifikat
  * Hämta certifikat
  * Visa en lista över hemligheter
  * Hämta hemligheter
  * Lista nycklar
  * Hämta (egenskaper för) nycklar
  * Kryptera
  * Avkryptera
  * Flytta
  * Packa upp
  * Verifiera
  * Tecken
  * Backup

* Under redundansväxlingen kan du inte göra ändringar i Key Vault-egenskaperna. Du kan inte ändra åtkomst princip eller brand Väggs konfiguration och inställningar.

* När en redundansväxling har misslyckats igen är alla typer av förfrågningar (inklusive Läs- *och* Skriv förfrågningar) tillgängliga.
