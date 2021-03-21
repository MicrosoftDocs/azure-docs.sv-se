---
title: Migrera StorSimple Enhetshanteraren lagrings konton, prenumerationer
description: Lär dig hur du migrerar prenumerationer, lagrings konton för StorSimple Enhetshanteraren-tjänsten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491528"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrera prenumerationer och lagringskonton som är associerade med StorSimple Device Manager-tjänsten

Du kan behöva flytta din StorSimple-tjänst till en ny registrering eller till en ny prenumeration. Dessa scenarier för migreringen är antingen konto ändringar eller data Center ändringar. Använd följande tabell för att förstå vilka av dessa scenarier som stöds, inklusive detaljerade steg för att flytta.

## <a name="account-changes"></a>Konto ändringar

| Kan du flytta...| Stöds| Driftstopp| Support process för Azure| Metod|
|-----|-----|-----|-----|-----|
| En hel prenumeration (inkluderar StorSimple-tjänst och lagrings konton) till en annan registrering? | Ja       | Inga       | **Registrerings överföring**<br>Använd<li>När du köper ett nytt Azure-åtagande enligt ett nytt avtal.</li><li>Du vill migrera alla konton och prenumerationer från den gamla registreringen till den nya. Detta inkluderar alla Azure-tjänster under den gamla prenumerationen.</li> | **Steg 1: öppna ett support ärende för Azure Enterprise operation.**<li>Gå till [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li> Välj **registrerings administration** och välj sedan **överför från en registrering till en ny registrering**.<br>**Steg 2: Ange den begärda informationen**<br>Omfattar<li>registrerings nummer för källa</li><li> registrerings nummer för mål</li><li>giltighets datum för överföring|
| StorSimple tjänsten från ett befintligt konto till en ny registrering?    | Ja       | Inga       | **Konto överföring**<br>Använd<li>När du inte vill ha en fullständig registrerings överföring.</li><li>Du vill bara flytta vissa konton till en ny registrering.</li>| **Steg 1: öppna ett support ärende för Azure Enterprise operation.**<li>Gå till [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li>Välj **registrerings administration** och välj sedan **överför ett EA-konto till en ny registrering**.<br>**Steg 2: Ange den begärda informationen**<br>Omfattar<li>registrerings nummer för källa</li><li> registrerings nummer för mål</li><li>giltighets datum för överföring|
| StorSimple tjänsten från en prenumeration till en annan prenumeration?      | Inga        |    Ja         | Ingen, manuell process|<li>Migrera data från StorSimple-enheten.</li><li>Utför en fabriks återställning av enheten. då tas alla lokala data på enheten bort.</li><li>Registrera enheten med den nya prenumerationen på en StorSimple Enhetshanteraren-tjänst.</li><li>Migrera data tillbaka till enheten.|
|Kan jag överföra ägarskapet för en Azure-prenumeration till en annan katalog? | Ja       | Inga       | Koppla en befintlig prenumeration till din Azure AD-katalog | Se [associera en befintlig prenumeration till din Azure AD-katalog](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Det kan ta upp till 10 minuter innan allt visas korrekt.|
| StorSimple enheten från en StorSimple Enhetshanteraren tjänst till en annan tjänst i en annan region?      | Inga        | Ja            | Ingen, manuell process |Samma som ovan.|
| Lagrings konto till en ny prenumeration eller resurs grupp?     | Ja        | Inga             |Flytta lagrings kontot till en annan prenumeration eller resurs grupp |Om lagrings kontots åtkomst nycklar uppdateras efter flytten måste användaren konfigurera åtkomst nycklarna manuellt för det migrerade lagrings kontot via tjänsten StorSimple Enhetshanteraren.|
| Klassiskt lagrings konto till ett Azure Resource Manager lagrings konto      | Ja        | Inga             |Migrera från klassisk till Azure Resource Manager |<li>Detaljerade anvisningar om hur du migrerar ett lagrings konto från klassisk till Azure Resource Manager finns i [Migrera ett klassiskt lagrings konto](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Om lagrings kontots åtkomst nycklar uppdateras efter migreringen måste användaren synkronisera åtkomst nycklarna för det migrerade lagrings kontot via tjänsten StorSimple Enhetshanteraren. Detta är för att se till att StorSimple-enheterna fungerar som de ska och att de ska kunna skikta primära/säkerhetskopierade data till Azure. Detaljerade anvisningar om hur du synkroniserar åtkomst nycklar finns i [rotations arbets flödet](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> Om det klassiska lagrings kontot har migrerats i ett StorSimple Cloud Appliance, men den underliggande virtuella datorn fortfarande är i klassiskt läge, bör installationen fungera korrekt. Om den underliggande virtuella datorn för moln installationen migreras, fungerar inte inaktivera-och ta bort-funktionerna.</li><li> Du måste skapa en ny StorSimple Cloud-utrustning i Azure Portal och sedan redundansväxla från de äldre moln enheterna. Du kan inte skapa en StorSimple Cloud Appliance i den nya Azure Portal med ett klassiskt lagrings konto, men de måste ha ett Azure Resource Manager lagrings konto. Mer information finns i [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Data Center ändringar

| Kan du flytta...| Stöds|Driftstopp| Support process för Azure| Metod|
|-----|-----|-----|-----|-----|
| En StorSimple-tjänst från ett Azure-datacenter till en annan? | Inga | Ja |Ingen, manuell process  |<li>Migrera data från StorSimple-enheten.</li><li>Utför en fabriks återställning av enheten. då tas alla lokala data på enheten bort.</li><li>Registrera enheten med den nya prenumerationen på en ny StorSimple Enhetshanteraren-tjänst.</li><li>Migrera data tillbaka till enheten.|
| Ett lagrings konto från ett Azure-datacenter till ett annat? | Inga |Ja  |Ingen, manuell process  | Samma som ovan.|

## <a name="next-steps"></a>Nästa steg

* [Distribuera StorSimple Enhetshanteraren-tjänsten](storsimple-8000-manage-service.md)
* [Distribuera StorSimple 8000-serien het i Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)