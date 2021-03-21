---
title: Migrera data på StorSimple 5000-7000-serien till 8000 Series-enhet | Microsoft Docs
description: Lär dig mer om hur du migrerar data på StorSimple 5000-7000-serien till 8000-seriens enhet och vilka krav som gäller för migreringsprocessen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428174"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrera data från StorSimple 5000-7000-serien till 8000-serien het

> [!IMPORTANT]
> - Den 31 juli 2019 kommer StorSimple 5000/7000-serien att uppnå status för slut på support (EOS). Vi rekommenderar att StorSimple 5000/7000-seriens kunder migrerar till något av de alternativ som beskrivs i dokumentet.
> - Migreringen är för närvarande en åtgärd som kan åtgärdas. Om du planerar att migrera data från din StorSimple 5000-7000-serien till en enhet med 8000-serien måste du schemalägga migreringen med Microsoft Support. Microsoft Support aktive ras din prenumeration för migrering. Mer information finns i så här [öppnar du ett support ärende](storsimple-8000-contact-microsoft-support.md).
> - När du har överfört tjänstbegäran kan det ta några veckor att köra migreringsprocessen och starta migreringen.
> - Innan du kontaktar Microsoft Support måste du granska och slutföra de [migrerings krav](#migration-prerequisites) som anges i artikeln.

## <a name="overview"></a>Översikt

Den här artikeln beskriver migreringen som gör att StorSimple 5000-7000-serien kan migrera sina data till fysiska enheter i StorSimple 8000-serien eller en 8010/8020-moln installation. Den här artikeln länkar också till en nedladdnings bar steg-för-steg-beskrivning av de steg som krävs för att migrera data från en äldre enhet 5000-7000-serien till en fysisk eller moln installation på 8000-serien.

Den här artikeln gäller för både den lokala 8000-serien och StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Migrerings funktion jämfört med migrering på värd Sidan

Du kan flytta dina data med hjälp av funktionen migrering eller genom att utföra en migrering på värd sidan. I det här avsnittet beskrivs de olika metoderna, inklusive för-och nack delar. Använd den här informationen för att ta reda på vilken metod du vill använda för att migrera dina data.

Funktionen migrering simulerar en haveri beredskap (DR) från 7000/5000-serien till 8000-serien. Med den här funktionen kan du migrera data från 5000/7000-serien till 8000 Series-format på Azure. Migreringsprocessen initieras med hjälp av StorSimple Migration Tool. Verktyget startar nedladdningen och konverteringen av metadata för säkerhets kopiering på 8000 serie enheten och använder sedan den senaste säkerhets kopieringen för att exponera volymerna på enheten.

| Fördelar                                                                                                                                     |Nackdelar                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Migreringsprocessen bevarar historiken för säkerhets kopieringar som togs i 5000/7000-serien.                                               | När användarna försöker komma åt data laddar migreringen ned data från Azure så att kostnaderna för data hämtning hämtas.                                     |
| Inga data migreras på värd sidan.                                                                                                     | Processen behöver avbrott mellan starten av säkerhets kopieringen och den senaste säkerhets kopian i 8000-serien (kan beräknas med hjälp av migrations verktyget). |
| Den här processen bevarar alla principer, bandbredds-mallar, kryptering och andra inställningar på 8000 serie enheter.                      | Användar åtkomsten kommer bara att återställa de data som användaren har åtkomst till och kommer inte att göra om hela data uppsättningen.                                                  |
| Den här processen kräver ytterligare tid för att konvertera alla äldre säkerhets kopieringar i Azure som utförs asynkront utan att produktionen påverkas | Migrering kan bara göras på en moln konfigurations nivå.  Enskilda volymer i en moln konfiguration kan inte migreras separat                       |

Med en migrering på värd sidan kan du konfigurera 8000-serien oberoende av varandra och kopiera data från 5000/7000-seriens enhet till 8000-serien het. Detta motsvarar migrering av data från en lagrings enhet till en annan. En rad olika verktyg, till exempel Diskboss, Robocopy används för att kopiera data.

| Fördelar                                                                                                                      |Nackdelar                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Migrering kan närmas i ett stegvist sätt på volym-till-volym.                                               | Tidigare säkerhets kopior (tas i 5000/7000-serien) är inte tillgängliga på enheten för 8000-serien.                                                                                                       |
| Möjliggör konsolidering av data till ett lagrings konto i Azure.                                                       | Den första säkerhets kopieringen till molnet på 8000-serien tar längre tid eftersom alla data på 8000-serien måste säkerhets kopie ras till Azure.                                                                     |
| Efter en lyckad migrering är alla data lokalt på enheten. Det finns ingen fördröjning vid åtkomst till data. | Användningen av Azure Storage ökar tills data tas bort från 5000/7000-enheten.                                                                                                        |
|                                                                                                                           | Om enheten för 7000/5000-serien har en stor mängd data, måste den här informationen laddas ned från Azure, vilket kommer att debiteras för kostnader och fördröjningar som rör hämtning av data från Azure |

Den här artikeln fokuserar bara på migrering från 5000/7000 till 8000-serien het. Mer information om migrering på värd sidan finns i [migrering från andra lagrings enheter](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Krav för migrering

Här är kraven för migrering för din äldre 5000-eller 7000-serie enhet och 8000-seriens StorSimple-enhet.

> [!IMPORTANT]
> Granska och slutför migrerings kraven innan du File a service-begäran med Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>För 5000/7000-serien het (källa)

Innan du påbörjar migreringen kontrollerar du att:

* Du har en käll enhet för 5000-eller 7000-serien. enheten kan vara aktiv eller inaktiv.

    > [!IMPORTANT]
    > Vi rekommenderar att du har seriell åtkomst till den här enheten under hela migreringsprocessen. Om det finns några enhets problem kan seriell åtkomst hjälpa till med fel sökning.

* Käll enheten 5000-eller 7000-serien kör program version v 2.1.1.518 eller senare. Tidigare versioner stöds inte.
* Kontrol lera vilken version av din 5000-eller 7000-serie som körs genom att titta i det övre högra hörnet av ditt webb gränssnitt. Detta bör visa den program varu version som enheten kör. För migrering ska din 5000-eller 7000-serie köra v-2.1.1.518.

    ![Kontrol lera program varu versionen på en äldre enhet](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Om din Live-enhet inte kör v 2.1.1.518 eller senare, måste du uppgradera systemet till den minimala versionen som krävs. Du kan behöva arbeta med Microsoft Support för att hjälpa dig att utföra uppgraderingen.
    * Om du kör v-2.1.1.518 går du till webb gränssnitt för att se om det finns några meddelanden om problem med återställning av registret. Om det inte gick att återställa registret kör du registret Restore. Du kan behöva arbeta med Microsoft Support för att hjälpa dig att återställa ditt register.
    * Om du har en enhet som inte körde v-2.1.1.518 utför du en redundansväxling till en ersättnings enhet som kör v-2.1.1.518. Detaljerade anvisningar finns i DR för din StorSimple-enhet för 5000/7000-serien.
    * Säkerhetskopiera data för enheten genom att ta en ögonblicks bild av molnet.
    * Sök efter andra aktiva säkerhets kopierings jobb som körs på käll enheten. Detta inkluderar jobben på värden för StorSimple Data Protection-konsolen. Vänta tills de aktuella jobben har slutförts.


### <a name="for-the-8000-series-physical-device-target"></a>För fysisk enhet på 8000-serien (mål)

Innan du påbörjar migreringen kontrollerar du att:

* Din mål 8000 serie enhet är registrerad och körs. Mer information finns i [distribuera din StorSimple-enhet med StorSimple Manager-tjänsten](storsimple-8000-deployment-walkthrough-u2.md).
* Din enhet på 8000-serien har den senaste StorSimple 8000-serien uppdatering 5 installerad och kör 6.3.9600.17845 eller senare. Om enheten inte har de senaste uppdateringarna installerade måste du installera de senaste uppdateringarna innan du kan fortsätta med migreringen. Mer information finns i så här [installerar du den senaste uppdateringen på din enhet för 8000-serien](storsimple-8000-install-update-5.md).
* Din Azure-prenumeration är aktive rad för migrering. Om din prenumeration inte är aktive rad kontaktar du Microsoft Support för att aktivera prenumerationen för migrering.

### <a name="for-the-80108020-cloud-appliance-target"></a>För moln installationen 8010/8020 (mål)

Innan du påbörjar migreringen, se till att:

* Mål moln installationen är registrerad och körs. Mer information finns i [distribuera och hantera StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* Din moln installation kör den senaste versionen av StorSimple 8000-serien uppdatering 5 6.3.9600.17845. Om din moln installation inte kör uppdatering 5 skapar du en ny uppdatering 5-moln installation innan du fortsätter med migreringen. Mer information finns i så här [skapar du en 8010/8020-molnbaserad utrustning](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>För datorn som kör StorSimple Migration Tool

StorSimple Migration Tool är ett UI-baserat verktyg som gör att du kan migrera data från en StorSimple 5000-7000-serie till en enhet med 8000-serien. Om du vill installera migrations verktyget StorSimple använder du en dator som uppfyller följande krav.

Datorn är ansluten till Internet och:

* Kör följande operativ system
    * Windows 10.
    * Windows Server 2012 R2 (eller senare) för att installera StorSimple-Migreringsverktyg.
* Har .NET 4.5.2 installerat.
* Har minst 5 GB ledigt utrymme för att installera och använda verktyget.

> [!TIP]
> Om din StorSimple-enhet är ansluten till en Windows Server-värd kan du installera migreringsverktyget på Windows Server-värddatorn.

#### <a name="to-install-storsimple-migration-tool"></a>Installera StorSimple Migration Tool

Utför följande steg för att installera StorSimple Migration Tool på datorn.

1. Kopiera mappen _StorSimple8000SeriesMigrationTool_ till din Windows-dator. Kontrol lera att enheten där program varan har kopierats har tillräckligt med utrymme.

    Öppna konfigurations filen för verktyget _StorSimple8000SeriesMigrationTool.exe.config_ i mappen. Här är kodfragmentet i filen.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Redigera värdena som motsvarar nycklarna och Ersätt med:

    * `UserName` – Användar namn att logga in på Azure Portal.
    * `SubscriptionName and SubscriptionId` – Namn och ID för din Azure-prenumeration. Klicka på **Egenskaper** under **allmänt** på StorSimple Enhetshanteraren tjänstens landnings sida. Kopiera prenumerations namnet och prenumerations-ID: t som är associerat med din tjänst.
    * `ResourceName` – Namnet på din StorSimple Enhetshanteraren-tjänst i Azure Portal. Visas också under tjänst egenskaper.
    * `ResourceGroup` – Namnet på den resurs grupp som är kopplad till din StorSimple Enhetshanteraren-tjänst i Azure Portal. Visas också under tjänst egenskaper.
    ![Kontrol lera tjänst egenskaperna för mål enheten](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure Active Directory klient-ID i Azure Portal. Logga in på Microsoft Azure som administratör. Klicka på **Azure Active Directory** i Microsoft Azure-portalen. Klicka på **Egenskaper** under **Hantera**. Klient-ID visas i rutan **katalog-ID** .
    ![Kontrol lera klient-ID för Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Spara ändringarna som gjorts i konfigurations filen.
4.  Kör _StorSimple8000SeriesMigrationTool.exe_ för att starta verktyget. När du uppmanas att ange autentiseringsuppgifter anger du de autentiseringsuppgifter som är associerade med din prenumeration i Azure Portal. 
5.  Användar gränssnittet för migrations verktyget för StorSimple visas.
  

## <a name="next-steps"></a>Nästa steg
Hämta steg-för-steg-instruktioner om hur du [migrerar data från en StorSimple 5000-7000-serie till en enhet med 8000-serien](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf).
