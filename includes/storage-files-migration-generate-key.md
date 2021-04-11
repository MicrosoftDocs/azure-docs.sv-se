---
title: ta med fil
description: ta med fil
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075627"
---
Krypterings nycklar för tjänst data används för att kryptera konfidentiella kunddata, t. ex. autentiseringsuppgifter för lagrings konto, som skickas från din StorSimple Manager-tjänst till StorSimple-enheten. Du måste ändra nycklarna regelbundet om din IT-organisation har en nyckel rotations princip på lagrings enheterna. Nyckel ändrings processen kan skilja sig åt beroende på om det finns en enskild enhet eller flera enheter som hanteras av StorSimple Managers tjänsten. Mer information finns på [StorSimple Security och Data Protection](../articles/storsimple/storsimple-8000-security.md).

Att ändra krypterings nyckeln för tjänst data är en tre stegs process:

1. Använd Windows PowerShell-skript för Azure Resource Manager för att auktorisera en enhet för att ändra krypterings nyckeln för tjänst data.
2. Initiera ändringen av tjänst data krypterings nyckeln med hjälp av Windows PowerShell för StorSimple.
3. Om du har mer än en StorSimple-enhet uppdaterar du tjänst data krypterings nyckeln på de andra enheterna.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Steg 1: Använd Windows PowerShell-skript för att auktorisera en enhet att ändra krypterings nyckeln för tjänst data
Normalt begär enhets administratören att tjänst administratören ger en enhet behörighet att ändra krypterings nycklar för tjänst data. Tjänst administratören kommer sedan att auktorisera enheten för att ändra nyckeln.

Det här steget utförs med det Azure Resource Manager baserade skriptet. Tjänst administratören kan välja en enhet som är behörig att auktoriseras. Enheten har sedan behörighet att starta ändrings processen för tjänst data krypterings nyckel. 

Om du vill ha mer information om hur du använder skriptet går du till [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Vilka enheter kan auktoriseras för att ändra krypterings nycklar för tjänst data?
En enhet måste uppfylla följande kriterier innan den kan auktoriseras för att initiera ändringar av tjänst data krypterings nyckel:

* Enheten måste vara online för att kunna bli berättigad till ändrings behörighet för tjänst data krypterings nyckel.
* Du kan auktorisera samma enhet igen efter 30 minuter om nyckel ändringen inte har initierats.
* Du kan auktorisera en annan enhet, förutsatt att nyckel ändringen inte har initierats av den tidigare auktoriserade enheten. När den nya enheten har auktoriserats kan den gamla enheten inte påbörja ändringen.
* Du kan inte auktorisera en enhet när förnyelsen av tjänst data krypterings nyckeln pågår.
* Du kan auktorisera en enhet när några av enheterna som är registrerade i tjänsten har registrerats via krypteringen medan andra inte har det. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Steg 2: Använd Windows PowerShell för StorSimple för att initiera ändringen av tjänst data krypterings nyckeln
Det här steget utförs i Windows PowerShell för StorSimple-gränssnittet på den auktoriserade StorSimple-enheten.

> [!NOTE]
> Inga åtgärder kan utföras i Azure Portal av din StorSimple Manager-tjänst förrän nyckel förnyelsen har slutförts.


Om du använder enhetens serie konsol för att ansluta till Windows PowerShell-gränssnittet utför du följande steg.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Så här initierar du ändringen av tjänst data krypterings nyckeln
1. Välj alternativ 1 för att logga in med fullständig åtkomst.
2. Skriv följande i kommandotolken:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. När cmdleten har slutförts visas en ny krypterings nyckel för tjänst data. Kopiera och spara nyckeln för användning i steg 3 i den här processen. Den här nyckeln används för att uppdatera alla återstående enheter som är registrerade i StorSimple Manager-tjänsten.
   
   > [!NOTE]
   > Den här processen måste initieras inom fyra timmar med att auktorisera en StorSimple-enhet.
   > 
   > 
   
   Den nya nyckeln skickas sedan till tjänsten som ska skickas till alla enheter som är registrerade i tjänsten. En avisering visas sedan på instrument panelen för tjänsten. Tjänsten inaktiverar alla åtgärder på de registrerade enheterna och enhets administratören måste då uppdatera krypterings nyckeln för tjänst data på de andra enheterna. I/o (värdar som skickar data till molnet) avbryts dock inte.
   
   Om du har en enda enhet som är registrerad för tjänsten slutförs förnyelse processen och du kan hoppa över nästa steg. Om du har flera enheter registrerade för tjänsten går du vidare till steg 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Steg 3: uppdatera krypterings nyckeln för tjänst data på andra StorSimple-enheter
De här stegen måste utföras i Windows PowerShell-gränssnittet på din StorSimple-enhet om du har flera enheter registrerade i StorSimple Manager-tjänsten. Den nyckel som du hämtade i steg 2 måste användas för att uppdatera alla återstående StorSimple-enheter som är registrerade i StorSimple Manager-tjänsten.

Utför följande steg för att uppdatera tjänst data krypteringen på enheten.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Så här uppdaterar du krypterings nyckeln för tjänst data på fysiska enheter
1. Använd Windows PowerShell för StorSimple för att ansluta till-konsolen. Välj alternativ 1 för att logga in med fullständig åtkomst.
2. Skriv följande i kommando tolken:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Ange krypterings nyckeln för tjänst data som du hämtade i [steg 2: använd Windows PowerShell för StorSimple för att initiera ändringen av tjänst data krypterings nyckeln](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Så här uppdaterar du krypterings nyckeln för tjänst data på alla 8010/8020-moln enheter
1. Hämta och konfigurera [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-skript. 
2. Öppna PowerShell och skriv följande i kommando tolken:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Det här skriptet ser till att krypterings nyckeln för tjänst data är inställd på alla 8010/8020-moln enheter under enhets hanteraren.