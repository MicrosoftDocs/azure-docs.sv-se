---
title: Säkerhet för StorSimple 8000-serien | Microsoft Docs
description: Beskriver säkerhets-och sekretessfunktioner som skyddar dina StorSimple-tjänster, enheter och data lokalt och i molnet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 72bf252488878c8b6fabb2ea2d61658a2cd87e14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966215"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple säkerhet och data skydd

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Säkerhet är viktigt för alla som använder en ny teknik, särskilt när tekniken används med konfidentiell eller patentskyddad information. När du utvärderar olika tekniker måste du överväga ökade risker och kostnader för data skydd. Microsoft Azure StorSimple tillhandahåller både en säkerhets-och sekretess lösning för data skydd som hjälper till att säkerställa:

* **Konfidentialitet** – endast auktoriserade entiteter kan visa dina data.
* **Integritet** – endast auktoriserade entiteter kan ändra eller ta bort dina data.

Microsoft Azure StorSimple lösningen består av fyra huvud komponenter som interagerar med varandra:

* **StorSimple Enhetshanteraren tjänst som finns Microsoft Azure** – den hanterings tjänst som du använder för att konfigurera och etablera StorSimple-enheten.
* **StorSimple-enhet** – en fysisk enhet som är installerad i ditt data Center. Alla värdar och klienter som genererar data ansluter till StorSimple-enheten och enheten hanterar data och flyttar dem till Azure-molnet efter behov.
* **Klienter/värdar anslutna till enheten** – klienterna i din infrastruktur som ansluter till StorSimple-enheten och genererar data som behöver skyddas.
* **Molnlagring** – Platsen i Azure-molnet där data lagras.

I följande avsnitt beskrivs de StorSimple-säkerhetsfunktioner som hjälper dig att skydda var och en av dessa komponenter och de data som lagras på dem. Den innehåller också en lista med frågor som du kan ha om Microsoft Azure StorSimple säkerhet och motsvarande svar.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Enhetshanteraren tjänst skydd

Tjänsten StorSimple Enhetshanteraren är en hanterings tjänst som finns Microsoft Azure och används för att hantera alla StorSimple enheter som din organisation har införskaffat. Du kan komma åt StorSimple Enhetshanteraren-tjänsten genom att använda organisationens autentiseringsuppgifter för att logga in på Azure Portal via en webbläsare.

Åtkomst till StorSimple-Enhetshanteraren tjänsten kräver att din organisation har en Azure-prenumeration som innehåller StorSimple. Prenumerationen styr vilka funktioner du kan komma åt på Azure Portal. Om din organisation inte har någon Azure-prenumeration och du vill lära dig mer om dem, se [Registrera dig för Azure som en organisation](../active-directory/fundamentals/sign-up-organization.md).

Eftersom tjänsten StorSimple Enhetshanteraren är värd för Azure, skyddas den av Azures säkerhetsfunktioner. Läs mer om säkerhetsfunktionerna i Microsoft Azure på [Microsoft Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>StorSimple enhets skydd

StorSimple-enheten är en lokal hybrid lagrings enhet som innehåller solid state-enheter (SSD) och hård diskar (HDD), tillsammans med redundanta styrenheter och funktioner för automatisk redundans. Styrenheterna hanterar lagrings nivåer, placerar data som används för närvarande (eller frekvent) på lokal lagring (i StorSimple-enheten eller lokala servrar) och flyttar mindre ofta använda data till molnet.

Endast auktoriserade StorSimple-enheter får ansluta till StorSimple Enhetshanteraren-tjänsten som du skapade i din Azure-prenumeration. För att auktorisera en enhet måste du registrera den med StorSimple-Enhetshanteraren tjänsten genom att tillhandahålla tjänst registrerings nyckeln. Tjänst registrerings nyckeln är en slumpmässig 128-bitars nyckel som genereras i Azure Portal.

![Tjänst registrerings nyckel](./media/storsimple-security/ServiceRegistrationKey.png)

Om du vill lära dig hur du hämtar en tjänst registrerings nyckel går du till [steg 2: Hämta tjänst registrerings nyckeln](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Tjänst registrerings nyckeln är en lång nyckel som innehåller 100 + tecken. Du kan kopiera nyckeln och spara den i en textfil på en säker plats så att du kan använda den för att ge ytterligare enheter vid behov. Om tjänst registrerings nyckeln försvinner när du har registrerat din första enhet kan du generera en ny nyckel från tjänsten StorSimple Enhetshanteraren. Detta påverkar inte användningen av befintliga enheter.

När en enhet har registrerats använder den token för att kommunicera med Microsoft Azure. Tjänst registrerings nyckeln används inte efter registrering av enheten.

> [!NOTE]
> Vi rekommenderar att du återskapar tjänst registrerings nyckeln efter varje användning.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Skydda din StorSimple-lösning via lösen ord

Lösen ord är en viktig aspekt av dator säkerhet och används i stor utsträckning i StorSimple-lösningen för att säkerställa att dina data endast är tillgängliga för behöriga användare. Med StorSimple kan du konfigurera följande lösen ord:

* StorSimple-enhetens administratörs lösen ord
* Challenge Handshake Authentication Protocol-eller CHAP-initierare och mål lösen ord
* Lösenordet för StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell för StorSimple och StorSimple-enhetens administratörs lösen ord

Windows PowerShell för StorSimple är ett kommando rads gränssnitt som du kan använda för att hantera StorSimple-enheten. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din enhet, konfigurera nätverks gränssnittet på enheten, installera vissa typer av uppdateringar, Felsöka enheten genom att komma åt support sessionen och ändra enhetens tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till serie konsolen på enheten eller genom att använda Windows PowerShell-fjärrkommunikation.

PowerShell-fjärrkommunikation kan göras via HTTPS eller HTTP. Om fjärrhantering över HTTPS har Aktiver ATS måste du ladda ned fjärrhanterings certifikatet från enheten och installera det på fjärrklienten. Mer information om PowerShell-fjärrkommunikation finns i fjärrans [luta till din StorSimple-enhet](storsimple-8000-remote-connect.md).

När du använder Windows PowerShell för StorSimple för att ansluta till enheten måste du ange enhetens administratörs lösen ord för att logga in på enheten.

![Enhetens administratörslösenord](./media/storsimple-security/DeviceAdminPW.png)

Tänk på följande rekommendationer:

* Fjärrhantering är inaktive rad som standard. Du kan använda tjänsten StorSimple Enhetshanteraren för att aktivera den. Som en säkerhets åtgärd bör fjärråtkomst bara aktive ras under den tids period som det verkligen behövs.
* Om du ändrar lösen ordet måste du meddela alla fjärråtkomst-användare så att de inte får oväntad anslutnings förlust.
* StorSimple-Enhetshanteraren tjänsten kan inte hämta befintliga lösen ord: den kan bara återställas. Vi rekommenderar att du lagrar alla lösen ord på en säker plats så att du inte behöver återställa ett lösen ord om det är glömt. Om du behöver återställa ett lösen ord måste du meddela alla användare innan du återställer det.

Du kan komma åt Windows PowerShell-gränssnittet genom att använda en seriell anslutning till enheten. Du kan också komma åt den via en fjärr anslutning genom att antingen använda HTTP eller HTTPS, vilket ger ytterligare säkerhet. HTTPS ger en högre säkerhets nivå än en seriell eller HTTP-anslutning. Men om du vill använda HTTPS måste du först installera ett certifikat på klient datorn som kommer att ha åtkomst till enheten. Du kan hämta Remote Access-certifikatet från sidan enhets konfiguration i StorSimple Enhetshanteraren-tjänsten. Om certifikatet för fjärråtkomst förloras måste du hämta ett nytt certifikat och sprida det till alla klienter som har behörighet att använda fjärrhantering.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Challenge Handshake Authentication Protocol-eller CHAP-initierare och mål lösen ord

CHAP är ett autentiseringsschema som används av StorSimple-enheten för att verifiera identiteten för fjärrklienter. Verifieringen baseras på ett delat lösen ord. CHAP kan vara enkelriktat (enkelriktat) eller ömsesidigt (dubbelriktat). Med enkelriktad CHAP autentiserar målet (StorSimple-enheten) en initierare (värd). Ömsesidig eller omvänd CHAP kräver att målet autentiserar initieraren och initieraren sedan målet. Din StorSimple kan konfigureras att använda någon av metoderna.

Tänk på följande när du konfigurerar CHAP:

* CHAP-användarnamnet måste innehålla färre än 233 tecken.
* CHAP-lösenordet måste vara mellan 12 och 16 tecken. Om du försöker använda ett längre användar namn eller lösen ord leder det till att autentiseringen Miss lyckas på Windows-värden.
* Du kan inte använda samma lösen ord för både CHAP-initieraren och CHAP-målet.
* När du har angett lösen ordet kan det ändras, men det går inte att hämta det. Om lösen ordet ändras måste du meddela alla fjärråtkomst-användare så att de kan ansluta till StorSimple-enheten.

Om du vill ha mer information om CHAP och hur du konfigurerar den för din StorSimple-lösning går du till [Konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Lösenordet för StorSimple Snapshot Manager

StorSimple Snapshot Manager är en snapin-modul för Microsoft Management Console (MMC) som använder volym grupper och Windows-tjänsten Volume Shadow Copy för att generera programkonsekventa säkerhets kopieringar. Dessutom kan du använda StorSimple Snapshot Manager för att skapa säkerhets kopierings scheman och klona eller återställa volymer.

När du konfigurerar en enhet att använda StorSimple-Snapshot Manager måste du ange lösen ordet för StorSimple Snapshot Manager. Det här lösen ordet anges först i Windows PowerShell för StorSimple under registreringen. Lösen ordet kan också ställas in och ändras från StorSimple Enhetshanteraren-tjänsten. Det här lösen ordet autentiserar enheten med StorSimple Snapshot Manager.

![Lösenordet för StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple Snapshot Manager lösen ordet måste innehålla mellan 14 och 15 tecken och måste innehålla 3 eller flera av en kombination av versaler, gemener, siffror och specialtecken. När du har angett StorSimple Snapshot Manager lösen ordet kan det ändras, men det går inte att hämta det. Om du ändrar lösen ordet ska du se till att meddela alla fjärran vändare.

Mer information om StorSimple Snapshot Manager finns i [StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Metodtips för lösenord

Vi rekommenderar att du använder följande rikt linjer för att säkerställa att StorSimple-lösenord är starkt och väl skyddade:

* Ändra lösen orden var tredje månad. Ändring av lösen ord tillämpas varje år.
* Använd starka lösen ord. Mer information finns i [skapa starkare lösen ord och skydda dem](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Använd alltid olika lösen ord för olika åtkomst mekanismer; vart och ett av de lösen ord som du anger måste vara unikt.
* Dela inte lösen ord med någon som inte har behörighet att komma åt StorSimple-enheten.
* Prata inte om ett lösen ord framför andra eller tips i formatet för ett lösen ord.
* Om du misstänker att ett konto eller lösen ord har komprometterats rapporterar du incidenten till din informations säkerhets avdelning.
* Behandla alla lösen ord som känslig, konfidentiell information. 

## <a name="storsimple-data-protection"></a>StorSimple data skydd

I det här avsnittet beskrivs StorSimple säkerhetsfunktioner som skyddar data i överförings-och lagrings data.

Som beskrivs i andra avsnitt används lösen ord för att auktorisera och autentisera användare innan de får åtkomst till din StorSimple-lösning. En annan säkerhets åtgärd är att skydda data från obehöriga användare medan den överförs mellan lagrings system och medan den lagras. I följande avsnitt beskrivs de data skydds funktioner som finns i StorSimple.

> [!NOTE]
> Deduplicering ger ytterligare skydd för data som lagras på StorSimple-enheten och i Microsoft Azure lagring. När data är deduplicerade lagras dataobjekten separat från de metadata som används för att mappa och komma åt dem: det finns ingen tillgänglig lagrings nivå kontext för att konstruera om data baserat på volym struktur, fil system eller fil namn.


## <a name="protect-data-flowing-through-the-service"></a>Skydda data som flödar genom tjänsten

Det främsta syftet med tjänsten StorSimple Enhetshanteraren är att hantera och konfigurera StorSimple-enheten. Tjänsten StorSimple Enhetshanteraren körs i Microsoft Azure. Du kan använda Azure Portal för att ange enhetens konfigurations data och sedan Microsoft Azure använder tjänsten StorSimple Enhetshanteraren för att skicka data till enheten. StorSimple använder ett system med asymmetriska nyckel par för att se till att en kompromiss i Azure-tjänsten inte leder till att lagrad information komprometteras.

![Data kryptering i flygning](./media/storsimple-security/DataEncryption.png)

Det asymmetriska nyckel systemet hjälper till att skydda de data som flödar via tjänsten på följande sätt:

1. Ett data krypterings certifikat som använder ett asymmetriskt offentligt och privat nyckel par genereras på enheten och används för att skydda data. Nycklarna skapas när den första enheten registreras.
2. Certifikat nycklarna för data kryptering exporteras till en personlig information Exchange-fil (. pfx) som skyddas av tjänst data krypterings nyckeln, som är en stark 128-bitars nyckel som skapas slumpmässigt av den första enheten under registreringen.
3. Den offentliga nyckeln för certifikatet görs säkert tillgänglig för StorSimple-Enhetshanteraren tjänsten och den privata nyckeln finns kvar på enheten.
4. Data som registrerar tjänsten krypteras med hjälp av den offentliga nyckeln och dekrypteras med hjälp av den privata nyckeln som lagras på enheten, vilket säkerställer att Azure-tjänsten inte kan dekryptera data som flödar till enheten.

Krypterings nyckeln för tjänst data genereras endast på den första enheten som är registrerad i tjänsten. Alla efterföljande enheter som har registrerats med tjänsten måste använda samma krypterings nyckel för tjänst data.

> [!IMPORTANT]
> Det är mycket viktigt att göra en kopia av krypterings nyckeln för tjänst data och spara den på en säker plats. En kopia av tjänst data krypterings nyckeln ska lagras på ett sådant sätt att den kan nås av en auktoriserad person och kan enkelt kommunicera till enhets administratören.
> 
> Om krypterings nyckeln för tjänst data förloras kan en support person från Microsoft hjälpa dig att hämta den förutsatt att du har minst en enhet i ett online-tillstånd. Vi rekommenderar att du ändrar krypterings nyckeln för tjänst data när den har hämtats.

Om du vill ändra krypterings nyckeln för tjänst data och motsvarande data krypterings certifikat följer du stegen i [ändra krypterings nyckeln för tjänst data för din StorSimple Enhetshanteraren-tjänst](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Att ändra krypterings nycklarna kräver att alla enheter uppdateras med den nya nyckeln. Därför rekommenderar vi att du ändrar nyckeln när alla enheter är online. Om enheterna är offline kan deras nycklar ändras vid en annan tidpunkt. Enheterna med inaktuella nycklar kommer fortfarande att kunna köra säkerhets kopieringar, men de kommer inte att kunna återställa data förrän nyckeln har uppdaterats.

Krypterings nyckeln för tjänst data och data krypterings certifikatet upphör inte att gälla. Vi rekommenderar dock att du ändrar krypterings nyckeln för tjänst data årligen för att hjälpa till att förhindra nyckel kompromisser.

## <a name="protect-data-at-rest"></a>Skydda data i vila

StorSimple-enheten hanterar data genom att lagra den på olika nivåer lokalt och i molnet, beroende på användnings frekvens. Alla värddatorer som är anslutna till enheten skickar data till enheten, som sedan flyttar data till molnet efter behov. Data överförs från enheten till molnet på ett säkert sätt via Internet. Varje enhet har ett iSCSI-mål som hämtar alla delade volymer på enheten. Alla data krypteras innan de skickas till moln lagring. 

![Krypterings nyckel för moln lagring](./media/storsimple-security/CloudStorageEncryption.png)

För att säkerställa säkerheten och integriteten för data som flyttas till molnet gör StorSimple att du kan definiera krypterings nycklar för moln lagring på följande sätt:

* Du anger krypterings nyckeln för moln lagring när du skapar en volym behållare. Nyckeln kan inte ändras eller läggas till senare.
* Alla volymer i en volym behållare delar samma krypterings nyckel. Om du vill ha en annan typ av kryptering för en särskild volym, rekommenderar vi att du skapar en ny volym behållare som är värd för volymen.
* När du anger en krypterings nyckel för moln lagring i StorSimple Enhetshanteraren tjänsten krypteras nyckeln med hjälp av den offentliga delen av tjänst data krypterings nyckeln och skickas sedan till enheten.
* Krypterings nyckeln för moln lagring lagras inte var som helst i tjänsten och är bara känd för enheten.
* Det är valfritt att ange en krypterings nyckel för moln lagring. Du kan skicka data som har krypterats på värden till enheten.

### <a name="additional-security-best-practices"></a>Ytterligare rekommenderade säkerhets metoder

* Dela trafik: isolera ditt iSCSI SAN-nätverk från användar trafik på ett företags nätverk genom att distribuera ett helt avgränsat nätverk och använda VLAN där fysisk isolering inte är ett alternativ. Ett dedikerat nätverk för iSCSI-lagring garanterar säkerheten och prestandan hos affärs kritiska data. Att blanda lagring och användar trafik över ett företags nätverk rekommenderas inte och kan öka fördröjningen och orsaka nätverks fel.
* För nätverks säkerhet på värd sidan använder du nätverks gränssnitt som stöder TCP/IP Offload Engine (TOE). TOE minskar CPU-belastningen genom att bearbeta TCP på nätverkskortet.

## <a name="protect-data-via-storage-accounts"></a>Skydda data via lagrings konton

Varje Microsoft Azure prenumeration kan skapa ett eller flera lagrings konton. (Ett lagrings konto ger ett unikt namn område för att arbeta med data som lagras i Azure-molnet.) Åtkomst till ett lagrings konto styrs av prenumerations-och åtkomst nycklarna som är associerade med lagrings kontot.

När du skapar ett lagrings konto genererar Microsoft Azure 2 512-bitars lagrings åtkomst nycklar som används för autentisering när StorSimple-enheten har åtkomst till lagrings kontot. Observera att endast en av dessa nycklar används. Den andra nyckeln lagras i reserv, så att du kan rotera nycklarna med jämna mellanrum. Om du vill rotera nycklar gör du den sekundära nyckeln aktiv och tar sedan bort den primära nyckeln. Du kan sedan skapa en ny nyckel för användning under nästa rotation. (Av säkerhets skäl kräver många data Center nyckel rotation.)

Vi rekommenderar att du följer dessa metod tips för nyckel rotation:

* Du bör rotera lagrings konto nycklar regelbundet för att se till att ditt lagrings konto inte kan nås av obehöriga användare.
* Med jämna mellanrum bör Azure-administratören ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagrings avsnittet i Azure Portal för att få direkt åtkomst till lagrings kontot.

## <a name="protect-data-via-encryption"></a>Skydda data via kryptering

StorSimple använder följande krypteringsalgoritmer för att skydda data som lagras i eller reser mellan komponenterna i din StorSimple-lösning.

| Integritetsalgoritm | Nyckellängd | Protokoll/program/kommentarer |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v 1.5 används av Azure Portal för att kryptera konfigurations data som skickas till enheten: till exempel lagrings konto uppgifter, StorSimple enhets konfiguration och krypterings nycklar för moln lagring. |
| AES |256 |AES med CBC används för att kryptera den offentliga delen av tjänst data krypterings nyckeln innan den skickas till Azure Portal från StorSimple-enheten. Den används också av StorSimple-enheten för att kryptera data innan data skickas till moln lagrings kontot. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance säkerhet

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple-Enhetshanteraren för både fysisk och virtuell serie samlar in personlig information i följande nyckel instanser:

- Varna användar inställningar där e-postadresser till användare har kon figurer ATS. Den här informationen kan visas och rensas av administratören. Detta gäller både StorSimple 8000-seriens enheter och StorSimple-virtuella matriser.
  * Om du vill visa och rensa inställningarna för StorSimple 8000-serien följer du stegen i [Visa och hantera StorSimple-aviseringar](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Om du vill visa och rensa inställningarna för virtuell StorSimple-matris följer du stegen i [Visa och hantera StorSimple-aviseringar](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Användare som har åtkomst till data som finns på resurserna. En lista över användare som kan komma åt resurs data visas och kan visas. Den här listan tas också bort när resurserna tas bort. Detta gäller endast för virtuella StorSimple-matriser.
  * Om du vill visa en lista över användare som har åtkomst till eller tar bort en resurs följer du stegen i [Hantera resurser på den virtuella StorSimple-matrisen](storsimple-virtual-array-manage-shares.md)

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Här följer några frågor och svar om säkerhets-och Microsoft Azure StorSimple.

**F:** Min tjänst har komprometterats. Vad ska vara nästa steg?

**A:** Du bör omedelbart ändra krypterings nyckeln för tjänst data och lagrings konto nycklarna för det lagrings konto som används för data skikts data. Anvisningar finns i:

* [Ändra krypterings nyckeln för tjänst data](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Nyckel rotation för lagrings konton](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**F:** Jag har en ny StorSimple-enhet som frågar efter tjänst registrerings nyckeln. Hur gör jag för att hämta det?

**A:** Den här nyckeln skapades när du först skapade tjänsten StorSimple Enhetshanteraren. När du använder tjänsten StorSimple Enhetshanteraren för att ansluta till enheten kan du använda snabb start sidan för tjänsten för att visa eller återskapa tjänst registrerings nyckeln. Att skapa en ny tjänst registrerings nyckel påverkar inte de befintliga registrerade enheterna. Anvisningar finns i:

* [Visa eller återskapa tjänst registrerings nyckeln](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**F:** Jag förlorade tjänst data krypterings nyckeln. Vad gör jag nu?

**A:** Kontakta Microsoft Support. De kan logga in på en support-session på enheten och hjälpa dig att hämta nyckeln (förutsatt att minst en enhet är online). Omedelbart efter att du har fått krypterings nyckeln för tjänst data bör du ändra den för att säkerställa att den nya nyckeln endast är känd för dig. Anvisningar finns i:

* [Ändra krypterings nyckeln för tjänst data](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**F:**  Jag auktoriserade en enhet för en tjänst data krypterings nyckel ändring, men startade inte nyckel ändrings processen. Vad ska jag göra?

**A:** Om tids gränsen har gått ut måste du auktorisera enheten för ändringen av tjänst data krypterings nyckeln och starta processen igen.

**F:**  Jag har ändrat krypterings nyckeln för tjänst data, men kunde inte uppdatera de andra enheterna inom 4 timmar. Måste jag börja om från början?

**A:** Tids perioden på fyra timmar är bara för att initiera ändringen. När du har startat uppdaterings processen på den auktoriserade StorSimple-enheten gäller auktoriseringen tills alla enheter har uppdaterats.

**F:** Vår StorSimple-administratör har lämnat företaget. Vad ska jag göra?

**A:** Ändra och Återställ lösen orden som tillåter åtkomst till StorSimple-enheten och ändra krypterings nyckeln för tjänst data för att säkerställa att den nya informationen inte är känd för obehöriga personer. Anvisningar finns i:

* [Använd tjänsten StorSimple Enhetshanteraren för att ändra dina StorSimple-lösenord](storsimple-8000-change-passwords.md)
* [Ändra krypterings nyckeln för tjänst data](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md)

**F:** Jag vill ange StorSimple-Snapshot Manager-lösenordet till en värd som ansluter till StorSimple-enheten, men lösen ordet är inte tillgängligt. Vad kan jag göra?

**A:** Om du har glömt lösen ordet bör du skapa ett nytt. Se sedan till att informera alla befintliga användare om att lösen ordet har ändrats och att de ska uppdatera sina klienter så att de använder det nya lösen ordet. Anvisningar finns i:

* [Ändra StorSimple Snapshot Manager-lösenordet](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autentisera en enhet](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**F:** Certifikatet för fjärråtkomst till Windows PowerShell för StorSimple har ändrats på enheten. Hur gör jag för att uppdatera mina fjärråtkomstklienter?

**A:** Du kan hämta det nya certifikatet från StorSimple Enhetshanteraren-tjänsten och sedan ange att det ska installeras i certifikat arkivet för fjärråtkomstklienterna. Anvisningar finns i:

* [Importera-certifikat-cmdlet](/powershell/module/pkiclient/import-certificate)

**F:** Är mina data skyddade om tjänsten StorSimple Enhetshanteraren komprometteras?

**A:** Tjänst konfigurations data krypteras alltid med den offentliga nyckeln när du visar den i en webbläsare. Eftersom tjänsten inte har åtkomst till den privata nyckeln kommer tjänsten inte att kunna se några data. Om tjänsten StorSimple Enhetshanteraren har komprometterats påverkas ingen påverkan eftersom det inte finns några nycklar lagrade i StorSimple Enhetshanteraren-tjänsten.

**F:** Kommer mina data att komprometteras om någon får åtkomst till data krypterings certifikatet?

**A:** Microsoft Azure lagrar kundens data krypterings nyckel (. pfx-fil) i ett krypterat format. Eftersom. pfx-filen är krypterad och StorSimple-tjänsten inte har krypterings nyckeln för tjänst data för att dekryptera. pfx-filen, kommer bara att få åtkomst till. pfx-filen kommer inte att exponera några hemligheter.

**F:** Vad händer om en myndighets enhet ber Microsoft om mina data?

**A:** Eftersom alla data är krypterade på tjänsten och den privata nyckeln behålls med enheten, måste myndighets enheten Be kunden om data.



## <a name="next-steps"></a>Nästa steg

[Distribuera din StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).