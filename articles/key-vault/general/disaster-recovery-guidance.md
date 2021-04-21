---
title: Azure Key Vault tillgänglighet och redundans – Azure Key Vault | Microsoft Docs
description: Läs mer om Azure Key Vault tillgänglighet och redundans.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 3c5afc92044fcb109bedd38298b0b027ebeb437d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749698"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Tillgänglighet och redundans för Azure Key Vault

Azure Key Vault funktioner flera lager av redundans för att se till att dina nycklar och hemligheter förblir tillgängliga för ditt program även om enskilda komponenter i tjänsten misslyckas.

> [!NOTE]
> Den här guiden gäller för valv. Hanterade HSM-pooler använder en annan modell för hög tillgänglighet och haveriberedskap. Mer information [finns i Guide för haveriberedskap för hanterad HSM.](../managed-hsm/disaster-recovery-guide.md)

Innehållet i ditt nyckelvalv replikeras inom regionen och till en sekundär region minst 150 mil bort, men inom samma geografiska område för att upprätthålla hög hållbarhet för dina nycklar och hemligheter. Mer information om specifika regionpar finns i [Parkopplade Azure-regioner.](../../best-practices-availability-paired-regions.md) Undantaget till modellen med parkopplade regioner är Brasilien, södra, vilket endast tillåter alternativet att lagra data i Brasilien, södra. Brasilien, södra använder zonredundant lagring (ZRS) för att replikera data tre gånger inom den enskilda platsen/regionen. För AKV Premium används endast 2 av de 3 regionerna för att replikera data från HSM:erna.  

Om enskilda komponenter i nyckelvalvstjänsten misslyckas kan alternativa komponenter i regionsteget i skicka din begäran för att se till att funktionaliteten inte försämras. Du behöver inte vidta några åtgärder för att starta den här processen, den sker automatiskt och blir transparent för dig.

I de sällsynta fall då en hel Azure-region inte är tillgänglig dirigeras de begäranden som du gör för Azure Key Vault i den regionen *automatiskt*( misslyckades ) till en sekundär region förutom för regionen Brasilien, södra. När den primära regionen är tillgänglig igen dirigeras begäranden tillbaka *(misslyckades tillbaka)* till den primära regionen. Återigen behöver du inte vidta några åtgärder eftersom detta sker automatiskt.

I regionen Brasilien, södra måste du planera för återställning av dina Azure-nyckelvalv i ett regionfelscenario. Om du vill säkerhetskopiera och återställa ditt Azure-nyckelvalv till valfri region slutför du stegen som beskrivs i [Azure Key Vault säkerhetskopiering.](backup.md) 

Med den här designen för hög Azure Key Vault ingen stilleståndstid för underhållsaktiviteter.

Det finns några varningar att känna till:

* Vid en region redundans kan det ta några minuter för tjänsten att redundans. Begäranden som görs under den här tiden före redundans kan misslyckas.
* Om du använder en privat länk för att ansluta till nyckelvalvet kan det ta upp till 20 minuter innan anslutningen upprättas igen vid en redundans. 
* Under redundans är nyckelvalvet i skrivskyddsläge. Begäranden som stöds i det här läget är:
  * Lista certifikat
  * Hämta certifikat
  * Visa en lista över hemligheter
  * Hämta hemligheter
  * Visa en lista över nycklar
  * Hämta (egenskaper för) nycklar
  * Kryptera
  * Avkryptera
  * Wrap
  * Packa upp
  * Verifiera
  * Tecken
  * Backup

* Under redundans kan du inte göra ändringar i nyckelvalvsegenskaper. Du kommer inte att kunna ändra åtkomstprinciper eller brandväggskonfigurationer och inställningar.

* När en redundans har växlat tillbaka är alla typer av begäranden (inklusive *läs- och* skrivbegäranden) tillgängliga.
