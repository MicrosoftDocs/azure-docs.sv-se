---
title: Vanliga frågor och svar om Microsoft Azure Data Box | Microsoft Docs i data
description: Innehåller vanliga frågor och svar om Azure Data Box, en molnlösning som gör det möjligt att överföra stora mängder data till Azure.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/17/2020
ms.author: alkohli
ms.openlocfilehash: 87ec1f03a1f2294a4423e26129644eafcf7c915c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655483"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Vanliga frågor och svar

Med Microsoft Azure Data Box-hybridlösningen kan du skicka flera terabyte data till Azure på ett snabbt, prisvärt och tillförlitligt sätt med hjälp av en överföringsenhet. Här hittar du vanliga frågor och svar om hur du använder Data Box på Azure-portalen.

Frågor och svar är ordnade i följande kategorier:

- Om tjänsten
- Beställa en enhet
- Konfigurera och ansluta 
- Spåra status
- Kopiera data 
- Skicka en enhet
- Verifiera och ladda upp data 
- Spårbarhetssupport

## <a name="about-the-service"></a>Om tjänsten

### <a name="q-what-is-azure-data-box-service"></a>F. Vad är Azure Data Box-tjänsten? 
A.  Azure Data Box-tjänsten är utformad för offlineinmatning av data. Den här tjänsten hanterar en rad produkter som alla har skräddarsytts för dataöverföring för olika lagringskapaciteter. 

### <a name="q-what-is-azure-data-box"></a>F. Vad är Azure Data Box?
A. Azure Data Box ger en snabb, billig och säker överföring av terabyte data till Azure. Du beställer Data Box-enheten via Azure-portalen. Microsoft levererar en lagringsenhet med en användbar kapacitet på 80 TB via ett regionalt transportföretag. 

När enheten har tagits emot konfigurerar du den snabbt med hjälp av det lokala webbgränssnittet. Kopiera data från dina servrar till enheten eller från enheten till servrarna och skicka tillbaka enheten till Azure. För en import ordning överförs dina data automatiskt från enheten till Azure i Azure-datacentret. Hela processen spåras från slutpunkt till slutpunkt av Data Box-tjänsten på Azure-portalen.

### <a name="q-when-should-i-use-data-box"></a>F. När ska jag använda Data Box?
A. Om du har 40-500 TB data som du vill överföra till eller från Azure kan du dra nytta av Data Box-enhet. För data storlekar < 40 TB använder du Data Box Disk och för data storlekar > 500 TB registrerar du dig för [data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>F. Hur mycket kostar Data Box?
A. Data Box-tjänsten är tillgänglig mot en nominell kostnad i tio dagar. Kostnaden för enheten visas när du väljer produktmodellen när du skapar din beställning på Azure-portalen. Standard Frakt kostnader och avgifter för Azure Storage gäller också. Export order följer en liknande pris modell som för import order, även om ytterligare utgående avgifter kan tillkomma. 

Mer information finns i [Azure Data Box priser](https://azure.microsoft.com/pricing/details/storage/databox/) och [utgående kostnader](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>F. Vad är den maximala mängden data som jag kan överföra samtidigt med Data Box?
A. Data Box har en råkapacitet på 100 TB och en användbar kapacitet på 80 TB. Du kan överföra upp till 80 TB data med Data Box. Om du vill överföra en större datamängd måste du beställa fler enheter.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>F. Hur kan jag kontrollera om Data Box är tillgänglig i min region? 
A.  Om du vill ha mer information om vilka länder/regioner som Data Box-enhet är tillgängliga går du till [region tillgänglighet](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>F. I vilka regioner kan jag lagra data med Data Box?
A. Data Box-enhet stöds för alla regioner i USA, västra Europa, norra Europa, Frankrike, Storbritannien, Japan, Australien och Kanada. Mer information finns i [region tillgänglighet](data-box-overview.md#region-availability).

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>F. Hur kan jag importera käll data på min plats i ett visst land till en Azure-region i ett annat land/region eller exportera data från en Azure-region i ett land till ett annat land/region?

Data Box-enhet stöder endast data inmatning eller utgående data inom samma land/region som målet och kommer inte att korsa några internationella gränser. Det enda undantaget är för beställningar i Europeiska unionen (EU), där data rutor kan levereras till och från ett EU-land/-region.

I import scenariot, om du till exempel hade de källdata i Kanada som du ville flytta till ett Azure västra USA-lagrings konto, kan du uppnå det på följande sätt:

1. Beställ Data Box-enhet i Kanada genom att välja ett lagrings konto i Kanada. Enheten levereras från ett Azure-datacenter i Kanada till leverans adressen (i Kanada) som angavs när ordern skapades.

2. När lokal data kopiering till Data Box-enhet görs, returnerar du enheten till Azure-datacentret i Kanada. De data som finns på Data Box-enhet laddas sedan upp till mål lagrings kontot i den Azure-region som du valde när du skapade ordern.

3. Du kan sedan använda ett verktyg som AzCopy för att kopiera data till ett lagrings konto i USA, västra. I det här steget debiteras standard [avgifterna](https://azure.microsoft.com/pricing/details/bandwidth/) för [lagring](https://azure.microsoft.com/pricing/details/storage/) och bandbredd som inte ingår i data Box-enhet fakturering.

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>F. Vem ska jag kontakta om jag stöter på problem med Data Box-enhet?
A. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md)om du stöter på problem med data Box-enhet.

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>F. Jag har förlorat mina Data Box-enhet. Utgår en avgift för borttappade enheter?
A. Ja. Det finns en avgift för en förlorad eller skadad enhet. Den här avgiften täcks av [prissättnings sidan](https://azure.microsoft.com/pricing/details/storage/databox/) och i [produkt villkoren](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Beställa en enhet

### <a name="q-how-do-i-get-data-box"></a>F. Hur skaffar jag Data Box? 
A.  Om du vill skaffa Azure Data Box loggar du in på Azure-portalen och skapar en Data Box-beställning. Ange din kontakt- och aviseringsinformation. När du har gjort beställningen skickas Data Box till dig inom tio dagar, beroende på tillgängligheten. Mer information finns på sidan [Beställa en Data Box](data-box-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>F. Jag kunde inte skapa en Data Box-enhet ordning i Azure Portal. Varför?
A. Om du inte kan skapa en Data Box-enhets ordning är det ett problem med prenumerations typen eller åtkomsten.

Kontrollera din prenumeration. Data Box är endast tillgängligt för prenumerationer med Enterprise-avtal (EA) eller leverantörer av molnlösningar (CSP). Om du inte har någon av dessa prenumerations typer kontaktar du Microsoft Support för att uppgradera prenumerationen.

Om du har en prenumerationstyp som stöds kontrollerar du din åtkomstnivå för prenumerationen. Du måste vara en Deltagare eller Ägare i prenumerationen för att kunna skapa en order.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>F. Hur lång tid tar det att skapa beställningar på data som överförs till Azure?

A. Följande beräknade produktions tider för varje fas i order bearbetningen ger dig en bra uppfattning om vad du kan förväntar dig.  

Dessa produktions tider är *uppskattningar*. Tiden för varje steg i order bearbetningen påverkas av belastningen på data centret, samtidiga beställningar och andra miljö villkor.

**Uppskattade produktions tider för en Data Box-enhet ordning:**

1. Beställ Data Box-enhet: några minuter från portalen
2. Enhets tilldelning och förberedelse: 1-2 arbets dagar, beroende på inventerings tillgänglighet och andra beställningar som väntar på att utföras
3. Frakt: 2–3 arbetsdagar
4. Data kopiering på kund webbplatsen: beror på data, storlek och antal filer
5. Returleverans: 2–3 arbetsdagar
6. Bearbetar enhet i Data Center: 1-2 arbets dagar, beroende på andra beställningar som väntar på bearbetning
7. Ladda upp data till Azure: börjar så snart bearbetningen är klar och enheten är ansluten. Uppladdnings tiden beror på data, storlek och antal filer.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>F. Jag har beställt ett par Data Box-enheter. Jag kan inte skapa några ytterligare beställningar. Varför?
A. Vi tillåter högst fem aktiva beställningar per prenumeration per handels gräns (kombination av land och region som valts). Om du vill beställa ytterligare en enhet kontaktar du Microsoft Support för att öka gränsen för din prenumeration.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>F. När jag försöker skapa en beställning får jag ett meddelande om att Data Box-tjänsten inte är tillgänglig. Vad betyder detta?
A. Tjänsten Data Box-enhet är inte tillgänglig för den kombination av land och region som du har valt. Om du ändrar den här kombinationen får du antagligen tillgång till Data Box-tjänsten. En lista över de regioner där tjänsten är tillgänglig finns på sidan för [regionstillgänglighet för Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>F. Jag skickade min Data Box-beställning för några dagar sedan. När får jag min Data Box?
A. När du gör en beställning kontrollerar vi om det finns någon tillgänglig enhet för din beställning. Om det finns en tillgänglig enhet skickar vi den inom tio dagar. Ibland kan det uppstå perioder med hög efterfrågan. I så fall kan din beställning placeras i kö. Du kan spåra statusförändringen på Azure-portalen. Om din beställning inte har slutförts på 90 dagar avbryts ordern automatiskt.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>F. Jag har fyllt upp mina Data Box-enhet med data och behöver beställa en annan. Går det att beställa dem snabbt?
A. Du kan klona din tidigare beställning. En kloning skapar samma ordning som tidigare och du kan redigera beställningsinformationen utan att behöva ange adress, kontaktuppgifter och aviseringsadress. Kloning tillåts bara för import order.

## <a name="configure-and-connect"></a>Konfigurera och ansluta

### <a name="q-how-do-i-unlock-the-data-box"></a>F. Hur låser jag upp min Data Box? 
A.  Gå till din Data Box-beställning på Azure-portalen och navigera till **Enhetsinformation**. Kopiera lösenordet för upplåsning. Använd det här lösenordet för att logga in i det lokala webbgränssnittet på din Data Box. Mer information finns i [självstudien om hur du packar upp och ansluter Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>F. Kan jag använda en Linux-värddator för att ansluta och kopiera data till Data Box?
A.  Ja. Du kan använda Data Box för att ansluta till SMB- och NFS-klienter. Mer information finns i listan över [operativsystem som stöds](data-box-system-requirements.md) för värddatorn.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>F. Min Data Box har skickats men jag vill avbryta beställningen. Varför kan jag inte trycka på knappen och avbryta?
A.  Du kan bara avbryta en Data Box-beställning innan ordern har bearbetats. När Data Box-beställningen har bearbetats går det inte att avbryta ordern. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>F. Kan jag ansluta en Data Box samtidigt till flera värddatorer för att överföra data?
A. Ja. Flera värddatorer kan ansluta till Data Box för att överföra data, och flera kopieringsjobb kan köras parallellt. Mer information finns i [Självstudie: Kopiera data till Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>F. Kan jag ansluta till båda 10 GbE-gränssnitten i Data Box för att överföra data?
A. Ja. Båda 10 GbE-gränssnitten kan anslutas i Data Box för samtidig datakopiering. Mer information om hur du kopierar data finns i [Självstudier: kopiera data till Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>F. Indikatorn för systemfel lyser på framsidan av datorn. Vad ska jag göra?
A. Det finns två LED-lampor under strömbrytaren framför en Data Box-enhet. Det understa ljuset är system Fels indikatorn, som anger om systemet är felfritt.

En indikator för system Fels indikatorn som är röd kan indikera något av följande problem:
- Fläkt haveri
- CPU-temperaturen är hög
- Moder kortets temperatur är hög
- ECC-fel (Dual on line memory module) (DIMM) fel anslutnings kod

Gör så här:
1. Kontrol lera om fläkten fungerar.
2. Flytta enheten till en plats med större luft flöde.

[Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md)om indikeringen system Fels indikator fortfarande är på.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>F. Jag kan inte komma åt Data Box-upplåsningslösenordet på Azure-portalen. Varför?
A. Om du inte kan komma åt lösen ordet för att låsa upp Azure Portal kontrollerar du behörigheterna för din prenumeration och ditt lagrings konto. Kontrollera att du har behörighet som deltagare eller ägare på resursgruppsnivå. Du måste ha minst Data Box-enhet operatörs behörighet för att se autentiseringsuppgifterna för åtkomst.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>F. Stöder Data Box portkonfiguration för kanalen? Vad gäller MPIO?
A. Vi stöder inte port kanal konfiguration, MPIO-konfiguration (Multipath i/o) eller vLAN-konfiguration på Data Box-enhet.

## <a name="track-status"></a>Spåra status

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>F. Hur spårar jag Data Box från tidpunkten då jag gjorde beställningen till returen av enheten? 
A.  Du kan spåra statusen för Data Box Disk-beställningen på Azure-portalen. När du skapar ordningen uppmanas du att ange ett e-postmeddelande. Om du har angett ett meddelande får du ett meddelande via e-post om alla status ändringar för ordern. Mer information om hur du [konfigurerar e-postaviseringar](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>F. Hur gör jag för att skicka tillbaka enheten? 
A.  Microsoft visar en adressetikett på e-bläckskärmen. Om frakt etiketten inte visas på E-Penn visningen går du till **översikt > Ladda ned leverans etikett**. Hämta och skriv ut etiketten, infoga etiketten i taggen rensa plast på enheten och ta bort enheten på transport platsen. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>F. Jag har fått ett e-postmeddelande om att enheten har kommit fram till Azure-datacentret. Hur tar jag reda på om dataöverföringen har startat?
A. Du kan gå till din Data Box-beställning på Azure-portalen och sedan till **Översikt**. Om dataöverföringen till Azure har börjat visas kopieringsprocessen i den högra rutan. 

## <a name="migrate-data"></a>Migrera data

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>F. Hur stor mängd data får plats på Data Box?  
A.  Data Box har en användbar lagringskapacitet på 80 TB. Du kan använda en enskild Data Box-enhet för datastorlekar från 40 TB till 80 TB. För större datastorlekar på upp till 500 TB kan du beställa flera Data Box-enheter. För datastorlekar över 500 TB registrerar du dig för Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>F. Vilken är den största blockblob- och sidblobstorleken som stöds av Data Box? 
A.  De maximala storlekarna regleras av begränsningarna för Azure Storage. Den maximala blockbloben är ungefär 4,768 TiB och den maximala sidblobstorleken är 8 TiB. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](../storage/blobs/scalability-targets.md).

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>F. Hur vet jag att mina data är säkra under överföringen? 
A. Flera säkerhetsfunktioner har implementerats för att säkerställa att din Data Box är säker under överföringen. Några exempel är säkra förslutningar, identifiering av maskin- och programvarumanipulering, lösenord för upplåsning av enheten. Mer information finns i [Säkerhet och dataskydd i Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>F. Hur gör jag för att kopiera data till Data Box? 
A.  Om du använder en SMB-klient kan du använda ett SMB-exemplar som `Robocopy` , `Diskboss` eller till och med Windows Utforskaren dra och släpp för att kopiera data till enheten. 

Om du använder en NFS-klient kan du använda [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) eller [Ultracopier](https://ultracopier.first-world.info/). 

Mer information finns i [Självstudie: Kopiera data till Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>F. Finns det några tips för att påskynda datakopieringen?
A.  Påskynda kopieringsprocessen:

- Använd flera dataströmmar. Med `Robocopy` kan du till exempel använda det multitrådade alternativet. Mer information om de exakta kommandon som används finns i [Självstudie: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md).
- Använda flera sessioner.
- I stället för att kopiera över en nätverks resurs (där nätverks hastigheter kan begränsa kopierings hastigheten) lagrar du data lokalt på den dator som Data Box-enhet är ansluten till.
- Analysera prestanda för den dator som används för att kopiera data. Hämta och Använd [ `Bluestop` FIO-verktyget](https://ci.appveyor.com/project/axboe/fio) för att mäta server maskin varans prestanda. Välj den senaste x86-eller x64-versionen, Välj fliken **artefakter** och ladda ned MSI.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>F. Kan jag använda flera lagringskonton med Data Box?
A.  Ja. Högst tio lagringskonton, GA-version (generell användning), klassisk eller bloblagring stöds med Data Box. Det går att använda både högfrekvent och lågfrekvent blob.


## <a name="ship-device"></a>Skicka en enhet

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>F. Enheten levererades, men enheten verkar vara skadad. Vad ska jag göra?
A. Om din enhet angivit skada eller om det finns belägg för manipulering, ska du inte använda enheten. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) och returnera enheten så snart som möjligt. Du kan också skapa en ny Data Box-beställning för en ersättningsenhet. I det här fallet debiteras du inte för ersättnings enheten.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>F. Kan jag hämta min Data Box-enhet beställningen själv? Kan jag returnera Data Box-enhet via en operatör som jag väljer?
A. Ja. Microsoft erbjuder även självhanterad leverans. När du placerar Data Box-enhets ordningen kan du välja alternativ för själv hantering av leverans. Mer information finns i [självhanterad leverans för data Box-enhet](data-box-portal-customer-managed-shipping.md).

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>F. Kommer mina Data Box-enhet enheter att korsa lands-/region gränser under leverans?
A. Alla Data Box-enhet enheter levereras inifrån samma land/region som deras destination och kommer inte att korsa några internationella gränser. Det enda undantaget är för beställningar i Europeiska unionen (EU), där enheterna kan leverera till och från valfritt EU-land/-region. Detta gäller både för Data Box-enhet och Data Box Heavy enheterna.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>F. Jag har beställt en Data Box-enhet i östra USA, men jag har fått en enhet som levererades från en plats i västra USA. Var ska jag återställa enheten?
A. Vi försöker hämta en Data Box-enhet-enhet till dig så snabbt som möjligt. Vi prioriterar leveransen från ett Data Center som är närmast din lagrings konto plats, men kommer att leverera en enhet från alla Azure-datacenter som har tillgängligt lager. Din Data Box-enhet ska returneras till samma plats som den levererades från som visas i leverans etiketten.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>F. Returadressetiketten visas inte på e-bläckskärmen. Vad ska jag göra?
A. Gör så här om E-Penn visningen inte visar etiketten för retur leverans:
- Ta bort den gamla adressetiketten och eventuella klistermärken från den tidigare leveransen.
- Gå till din beställning på Azure-portalen. Gå till **Översikt** och **Ladda ned adressetikett**. Mer information finns i [Ladda ned adressetikett](data-box-portal-admin.md#download-shipping-label).
- Skriv ut adressetiketten och sätt in den i plastfickan som är fäst på enheten. 
- Kontrollera att adressetiketten syns tydligt. 

### <a name="q-how-is-my-data-protected-during-transit"></a>F. Hur skyddade är mina data under transporten? 
A.  Under överföringen hjälper följande Data Box-funktioner till att skydda data.
 - Data Box-diskarna krypteras med AES 256-bitarskryptering. 
 - Enheten är låst och ett upplåsningslösenord krävs för att mata in eller komma åt data.
Mer information finns i avsnittet om [Data Box-säkerhetsfunktioner](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>F. Jag är färdig med att leverera för min import ordning och stänga av enheten. Kan jag fortfarande lägga till mer data i Data Box-enhet?
A. Ja. Du kan aktivera enheten och lägga till mer data. Du måste köra **Förbered för att skicka** igen när du har slutfört kopieringen.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>F. Jag fick enheten, men den startas inte. Hur skickar jag tillbaka enheten?
A. Om enheten inte startas går du till din beställning i Azure Portal. Hämta en leverans etikett och koppla den till enheten. Mer information finns i [Ladda ned adressetikett](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Verifiera och ladda upp

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>F. Hur snart kan jag få åtkomst till mina data i Azure när jag har skickat Data Box-enhet tillbaka? 
A.  När order statusen för **data kopiering** visas som **slutförd**, bör du kunna komma åt dina data direkt.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>F. Var i Azure finns mina data efter uppladdningen?
A.  När du kopierar data till Data Box-enhet, beroende på om data är Block-Blob eller Page BLOB eller Azure Files, överförs data till någon av följande sökvägar i ditt Azure Storage-konto:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternativt kan du gå till ditt Azure Storage-konto i Azure Portal och navigera därifrån.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>F. Jag har precis märkt att jag inte följde namngivningskraven för min container. Misslyckas dataöverföringen till Azure?
A.  Om behållar namnen har versaler, konverteras namnen automatiskt till gemener. Om namnen inte uppfyller kraven på andra sätt (specialtecken, andra språk och så vidare) misslyckas uppladdningen. För ytterligare vägledning om namngivning av resurser, behållare och filer, gå till:
- [Namnge och referera till resurser](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Konventioner för blockblobar och sidblobar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>F. Hur verifierar jag de data som jag har kopierat till Data Box?
A.  Dina data verifieras när datakopieringen är klar och du kör **Förbered för att skicka**. Data Box genererar en lista över filer och kontrollsummor för data under verifieringsprocessen. Du kan ladda ned fillistan och verifiera listan mot filerna i källdata. Om du vill ha mer information går du till [Förbered för att skicka](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>F. Vad händer med mina data när jag har returnerat Data Box-enhet?
A.  När datakopieringen till Azure är klar raderas alla data på Data Box på ett säkert sätt enligt riktlinjerna i NIST SP 800-88 Revision 1. Mer information finns i avsnittet om [radering av data från Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Granskningsrapport

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Hur hjälper Azure Data Box-tjänsten kunder att spåra och övervaka processen?
A.  Azure Data Box-tjänsten tillhandahåller rapporter som du kan använda för att spåra och dokumentera processen. Gransknings- och kopieringsloggarna är tillgängliga i ditt lagringskonto i Azure, och [du kan ladda ned orderhistoriken](data-box-portal-admin.md#download-order-history) på Azure-portalen när ordern har slutförts.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Vilken typ av rapportering är tillgänglig för spårbarhet och övervakning?
A.  Följande rapportering är tillgänglig för spårbarhet och övervakning:

- Transportlogistik från UPS.
- Loggning om start och resursåtkomst av användaren.
- STRUKTURLISTE-eller manifest fil med en 64-bitars cyclic redundancy check (CRC-64) eller kontroll summa för varje fil som har matats in i Data Box-enhet.
- Rapportering av filer som inte kunde laddas upp till Azure Storage-kontot.
- Sanering av Data Box-enheten (enligt NIST 800 88R1-standarder) efter att alla data har kopierats till ditt Azure Storage-konto.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Är transportföretagets spårningsloggar (från UPS) tillgängliga? 
A.  Transportföretagets spårningsloggar samlas in Data Box-orderhistoriken. Den här rapporten är tillgänglig när enheten har returnerats till Azure-datacentret och data på enhetsdiskarna har rensats. För omedelbar behov kan du också gå direkt till operatörens webbplats med order spårnings numret och hämta spårnings informationen.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Kan jag själv transportera Data Box till Azures datacenter? 
A.  Nej. Om du har valt Microsoft-hanterad leverans kan du inte transportera data. Azure-datacenter accepterar för närvarande inte leverans av Data Box-enhet från kunder eller från andra leverantörer än UPS.

Om du väljer själv hanterad leverans kan du hämta eller ta bort din Data Box-enhet från Azure-datacentret.


## <a name="next-steps"></a>Nästa steg

- Titta på [systemkraven för Data Box](data-box-system-requirements.md).
- Förstå [Data Box-begränsningarna](data-box-limits.md).
- Distribuera snabbt [Azure Data Box](data-box-quickstart-portal.md) i Azure-portalen.