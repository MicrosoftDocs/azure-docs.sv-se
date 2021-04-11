---
title: Distribuera StorSimple Enhetshanteraren-tjänsten i Azure | Microsoft Docs
description: Lär dig mer om de steg som krävs för att skapa, ta bort, migrera och hantera tjänst registrerings nyckeln.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076572"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Distribuera StorSimple Enhetshanteraren-tjänsten för enheter med StorSimple 8000-serien

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda den för att hantera alla enheter som är anslutna till StorSimple Enhetshanteraren-tjänsten från en enda central plats, vilket minimerar administrations belastningen.

I den här självstudien beskrivs de steg som krävs för att skapa, ta bort, migrera och hantera tjänst registrerings nyckeln. Informationen i den här artikeln gäller endast för enheter med StorSimple 8000-serien. Mer information om virtuella StorSimple-matriser finns i [distribuera en StorSimple Enhetshanteraren-tjänst för din virtuella StorSimple-matris](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure Portal stöder enheter som kör uppdatering 5,0 eller senare. Om enheten inte är uppdaterad installerar du uppdatering 5 omedelbart. Mer information finns i [installera uppdatering 5](storsimple-8000-install-update-5.md). 
> - Om du använder en StorSimple Cloud Appliance (8010/8020) kan du inte uppdatera en moln installation. Använd den senaste versionen av program vara för att skapa en ny moln installation med uppdatering 5,0 och sedan redundansväxla till den nya moln installationen som skapats. 
> - Alla enheter som kör uppdatering 4,0 eller tidigare får färre hanterings funktioner. 

## <a name="create-a-service"></a>Skapa en tjänst
Om du vill skapa en StorSimple-Enhetshanteraren tjänst måste du ha:

* En prenumeration med en Enterprise-avtal
* Ett aktivt Microsoft Azure lagrings konto
* Den fakturerings information som används för åtkomst hantering

Endast prenumerationer med en Enterprise-avtal tillåts. Du kan också välja att generera ett standard lagrings konto när du skapar tjänsten.

En enskild tjänst kan hantera flera enheter. En enhet kan dock inte omfatta flera tjänster. Ett stort företag kan ha flera tjänst instanser för att arbeta med olika prenumerationer, organisationer eller till och med distributions platser. 

> [!NOTE]
> Du behöver separata instanser av StorSimple Enhetshanteraren-tjänsten för att hantera StorSimple 8000-seriens enheter och StorSimple virtuella matriser.

Utför följande steg för att skapa en tjänst.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Följande attribut finns för varje StorSimple Enhetshanteraren-tjänst:

* **Namn** – det namn som har tilldelats till din StorSimple Enhetshanteraren-tjänst när den skapades. **Det går inte att ändra tjänst namnet när tjänsten har skapats. Detta gäller även för andra entiteter, till exempel enheter, volymer, volym behållare och säkerhets kopierings principer som inte kan byta namn på Azure Portal.**
* **Status** – tjänstens status, som kan vara **aktiv**, **skapas** eller **online**.
* **Plats** – den geografiska plats där StorSimple-enheten kommer att distribueras.
* **Prenumeration** – den fakturerings prenumeration som är associerad med din tjänst.

## <a name="delete-a-service"></a>Ta bort en tjänst

Innan du tar bort en tjänst ser du till att inga anslutna enheter använder den. Om tjänsten används inaktiverar du de anslutna enheterna. Åtgärden inaktivera kommer att påverka anslutningen mellan enheten och tjänsten, men behåll enhets data i molnet.

> [!IMPORTANT]
> När en tjänst har tagits bort går det inte att ångra åtgärden. Alla enheter som använder tjänsten måste återställas till fabriks inställningarna innan den kan användas med en annan tjänst. I det här scenariot förloras lokala data på enheten och konfigurationen.

Utför följande steg för att ta bort en tjänst.

### <a name="to-delete-a-service"></a>Ta bort en tjänst

1. Sök efter den tjänst som du vill ta bort. Klicka på ikonen **resurser** och mata in lämpliga villkor att söka. I Sök resultaten klickar du på den tjänst som du vill ta bort.

    ![Sök tjänst som ska tas bort](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Då går du till bladet StorSimple Enhetshanteraren-tjänst. Klicka på **Ta bort**.

    ![Ta bort tjänst](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klicka på **Ja** i bekräftelse meddelandet. Det kan ta några minuter för tjänsten att tas bort.

    ![Bekräfta borttagning](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering

När du har skapat en tjänst måste du registrera din StorSimple-enhet med tjänsten. För att registrera din första StorSimple-enhet behöver du tjänst registrerings nyckeln. Om du vill registrera ytterligare enheter med en befintlig StorSimple-tjänst behöver du både registrerings nyckeln och krypterings nyckeln för tjänst data (som genereras på den första enheten under registreringen). Mer information om krypterings nyckeln för tjänst data finns i [StorSimple Security](storsimple-8000-security.md). Du kan hämta registrerings nyckeln genom att komma åt **nycklar** på ditt StorSimple Enhetshanteraren-blad.

Utför följande steg för att hämta tjänst registrerings nyckeln.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Behåll tjänst registrerings nyckeln på en säker plats. Du behöver den här nyckeln, samt krypterings nyckeln för tjänst data, för att registrera ytterligare enheter med tjänsten. När du har hämtat tjänst registrerings nyckeln måste du konfigurera enheten via Windows PowerShell för StorSimple-gränssnittet.

Mer information om hur du använder den här registrerings nyckeln finns i [steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Återskapa tjänst registrerings nyckeln
Du måste återskapa en tjänst registrerings nyckel om du måste utföra nyckel rotation eller om listan över tjänst administratörer har ändrats. När du återskapar nyckeln används den nya nyckeln bara för att registrera efterföljande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en tjänst registrerings nyckel.

### <a name="to-regenerate-the-service-registration-key"></a>Återskapa tjänst registrerings nyckeln
1. I bladet **StorSimple Enhetshanteraren** går du till **hanterings &gt;** **nycklar**.
    
    ![Gå till bladet nycklar](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Klicka på **Återskapa** på bladet **nycklar** .

    ![Klicka på återskapa](./media/storsimple-8000-manage-service/regenregkey3.png)
3. I bladet **Återskapa tjänst registrerings nyckel** granskar du den åtgärd som krävs när nycklarna återskapas. Alla efterföljande enheter som är registrerade med den här tjänsten använder den nya registrerings nyckeln. Klicka på **Återskapa** för att bekräfta. Du får ett meddelande när återskapandet har slutförts.

    ![Bekräfta återskapande](./media/storsimple-8000-manage-service/regenregkey4.png)

4. En ny tjänst registrerings nyckel kommer att visas.

5. Kopiera den här nyckeln och spara den för att registrera nya enheter med tjänsten.



## <a name="change-the-service-data-encryption-key"></a>Ändra krypterings nyckeln för tjänst data
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Åtgärder som stöds på enheter som kör versioner före uppdateringen 5,0
I Azure Portal stöds endast de StorSimple-enheter som kör uppdatering 5,0 och högre. Enheter som kör äldre versioner har begränsat stöd. När du har migrerat till Azure Portal kan du använda följande tabell för att förstå vilka åtgärder som stöds på enheter som kör tidigare versioner än uppdateringen 5,0.

| Åtgärd                                                                                                                       | Stöds      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrera en enhet                                                                                                               | Ja            |
| Konfigurera enhets inställningar som allmänt, nätverk och säkerhet                                                                | Ja            |
| Skanna, ladda ned och installera uppdateringar                                                                                             | Ja            |
| Inaktivera enhet                                                                                                               | Ja            |
| Ta bort enhet                                                                                                                   | Ja            |
| Skapa, ändra och ta bort en volym behållare                                                                                   | Inga             |
| Skapa, ändra och ta bort en volym                                                                                             | Inga             |
| Skapa, ändra och ta bort en princip för säkerhets kopiering                                                                                      | Inga             |
| Gör en manuell säkerhets kopiering                                                                                                            | Inga             |
| Gör en schemalagd säkerhets kopiering                                                                                                         | Inte tillämpligt |
| Återställ från en säkerhets kopian                                                                                                        | Inga             |
| Klona till en enhet som kör uppdatering 3,0 och senare <br> Käll enheten kör version före uppdatering 3,0.                                | Ja            |
| Klona till en enhet som kör versioner före uppdateringen 3,0                                                                          | Inga             |
| Redundans som käll enhet <br> (från en enhet som kör version före uppdatering 3,0 till en enhet som kör uppdatering 3,0 och senare)                                                               | Ja            |
| Redundansväxling som mål enhet <br> (till en enhet som kör program varu version före uppdatering 3,0)                                                                                   | Inga             |
| Rensa en avisering                                                                                                                  | Ja            |
| Visa säkerhets kopierings principer, säkerhets kopierings katalog, volymer, volym behållare, övervaknings diagram, jobb och aviseringar som skapats i den klassiska portalen | Ja            |
| Aktivera och inaktivera enhets styrenheter                                                                                              | Ja            |


## <a name="next-steps"></a>Nästa steg
* Läs mer om [distributions processen för StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Läs mer om hur [du hanterar ditt StorSimple-lagrings konto](storsimple-8000-manage-storage-accounts.md).
* Lär dig mer om hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).
