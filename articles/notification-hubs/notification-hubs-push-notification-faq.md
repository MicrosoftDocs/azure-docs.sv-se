---
title: Vanliga frågor och svar om Azure Notification Hubs | Microsoft Docs
description: Vanliga frågor och svar om att utforma och implementera lösningar på Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: push-meddelande, push-meddelanden, iOS push-meddelanden, Android push-meddelanden, iOS-push, Android-push
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 0f79402956148c566bc34faa88e10895657883c2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591747"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Push-meddelanden med Azure Notification Hubs: vanliga frågor och svar

## <a name="general"></a>Allmänt

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Vad är resurs strukturen för Notification Hubs?

Azure Notification Hubs har två resurs nivåer: hubbar och namn områden. En hubb är en enda push-resurs som kan lagra plattforms oberoende push-information för en app. Ett namn område är en samling hubbar i en region. Rekommenderad mappning matchar en namnrymd med en app. I ett namn område kan du ha ett produktions nav som fungerar med din webbapp, ett test nav som fungerar med din testapp och så vidare.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Vad är pris modellen för Notification Hubs?

Du hittar den senaste pris informationen på sidan [Notification Hubs prissättning] . Notification Hubs faktureras på namn områdes nivå. (Information om definitionen av ett namn område finns i "Vad är resurs strukturen för Notification Hubs?") Notification Hubs har tre nivåer:

* **Kostnads fri**: den här nivån är en lämplig utgångs punkt för att utforska push-funktionerna. Det rekommenderas inte för produktions program. Du får 500 enheter och 1 000 000 push-meddelanden per namnrymd per månad, utan garanti för service nivå avtal (SLA).
* **Basic**: den här nivån (eller standard nivån) rekommenderas för mindre produktions program. Du får 200 000 enheter och 10 000 000 push-meddelanden per namnrymd per månad som en bas linje.
* **Standard**: den här nivån rekommenderas för medel stora och stora produktions program. Du får 10 000 000 enheter och 10 000 000 push-meddelanden per namnrymd per månad som en bas linje. Innehåller omfattande telemetri (ytterligare information om push-status har angetts).

Standard-nivå funktioner:

* **Avancerad telemetri**: du kan använda Notification Hubs per meddelande-telemetri för att spåra eventuella push-begäranden och plattformsspecifikt meddelandesystem feedback för fel sökning.
* **Flera innehavare**: du kan arbeta med plattformsspecifikt meddelandesystem autentiseringsuppgifter på en namn områdes nivå. Med det här alternativet kan du enkelt dela klienter i hubbar inom samma namnrymd.
* **Schemalagd push**: du kan schemalägga meddelanden så att de skickas när som helst.
* **Mass åtgärder**: aktiverar registreringar av export/import-funktioner som beskrivs i dokument för [export/import av registrering] .

### <a name="what-is-the-notification-hubs-sla"></a>Vad är service avtal för Notification Hubs?

För Basic-och Standard Notification Hubs-nivåer kan korrekt konfigurerade program skicka push-meddelanden eller utföra registrerings hanterings åtgärder minst 99,9 procent av tiden. Om du vill veta mer om service avtalet går du till sidan [Notification Hubs service avtal](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Eftersom push-meddelanden är beroende av plattforms meddelande system från tredje part, t. ex. Apple Push Notification Service (APN) och Googles Firebase Cloud Messaging (FCM), finns det ingen SLA-garanti för leverans av dessa meddelanden. När Notification Hubs skickar batcharna till plattforms meddelande system (garanterat service avtal) är det ansvaret för plattforms aviserings systemen att leverera push-meddelanden (Inget SLA-garanti).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Hur gör jag för att uppgradera eller nedgradera mitt nav eller namn område till en annan nivå?

Gå till **[Azure Portal]**  >  **Notification Hubs namnrum** eller **Notification Hubs**. Välj den resurs som du vill uppdatera och gå till **pris nivån**. Observera följande krav:

* Den uppdaterade pris nivån gäller för *alla* hubbar i namn området som du arbetar med.
* Om antalet enheter överskrider gränsen för den nivå som du degraderar till, måste du ta bort enheter innan du degraderar.

## <a name="design-and-development"></a>Design och utveckling

### <a name="which-server-side-platforms-do-you-support"></a>Vilka plattform för Server sidan har du stöd för?

Server-SDK: er är tillgängliga för .NET, Java, Node.js, PHP och python. Notification Hubs-API: er baseras på REST-gränssnitt, så du kan arbeta direkt med REST-API: er om du använder olika plattformar eller inte vill ha något extra beroende. Mer information finns på sidan [Notification HUBS REST-API: er] .

### <a name="which-client-platforms-do-you-support"></a>Vilka klient plattformar stöder du?

Push-meddelanden stöds för [iOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin [iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) och [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)och [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Mer information finns på sidan om [Notification Hubs komma igång självstudier](ios-sdk-get-started.md) .

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Stöder du SMS, e-post eller webb meddelanden?

Notification Hubs skickar meddelanden till enheter som kör Mobile Apps. Den innehåller inga e-post-eller SMS-funktioner. Notification Hubs tillhandahåller inte heller en webb läsar tjänst för push-meddelanden i webbläsaren. Kunder kan implementera den här funktionen med hjälp av Signalerare ovanpå de plattformar som stöds på Server sidan. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hur många enheter kan jag använda om jag skickar push-meddelanden via Notification Hubs?

Mer information om antalet enheter som stöds finns på sidan med [Notification Hubs priser] .

Om du behöver stöd för fler än 10 000 000 registrerade enheter måste du partitionera enheterna över flera namn områden.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hur många push-meddelanden kan jag skicka ut?

Beroende på den valda nivån skalar Azure Notification Hubs automatiskt upp baserat på antalet meddelanden som flödar genom systemet.

> [!NOTE]
> Den totala förbruknings kostnaden kan öka beroende på hur många push-meddelanden som skickas. Se till att du är medveten om nivå gränserna som beskrivs på sidan [Notification Hubs prissättning] .

Våra kunder använder Notification Hubs för att skicka miljon tals push-meddelanden varje dag. Du behöver inte göra något särskilt för att skala räckvidden för dina push-meddelanden så länge du använder Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Hur lång tid tar det för skickade push-meddelanden för att få åtkomst till enheten?

I ett scenario med normal användning, där den inkommande belastningen är konsekvent och även, kan Azure Notification Hubs bearbeta minst *1 000 000 push-meddelande skickar en minut*. Hastigheten kan variera beroende på antalet taggar, typen av inkommande sändningar och andra externa faktorer.

Under den uppskattade leverans tiden beräknar tjänsten målen per plattform och skickar meddelanden till Push Notification Service (PNS) baserat på registrerade taggar eller tagg uttryck. PNS är ansvaret för att skicka meddelanden till enheten.

PNS garanterar inte några service avtal för att leverera meddelanden. De flesta push-meddelanden levereras dock till mål enheter inom några minuter (vanligt vis inom 10 minuter) från den tid de skickas till Notification Hubs. Det kan ta längre tid att få aviseringar.

> [!NOTE]
> Azure Notification Hubs har en princip för att ta bort alla push-meddelanden som inte levereras till PNS inom 30 minuter. Fördröjningen kan inträffa av flera orsaker, men oftast på grund av att PNS begränsar ditt program.

### <a name="is-there-any-latency-guarantee"></a>Garanterar svars tiden?

På grund av typen av push-meddelanden (de levereras av en extern, plattformsspecifik PNS) finns det ingen latens garanti. Oftast levereras merparten av push-meddelanden inom några minuter.

### <a name="where-does-azure-notification-hubs-store-data"></a>Var lagrar Azure Notification Hubs data?

Azure Notification Hubs lagrar kund registrerings data i den region som valts av kunden. Notification Hubs ger katastrof återställnings täckning för metadata (Notification Hubs namn, anslutnings sträng och annan viktig information). För alla regioner förutom Brasilien, södra och Sydostasien, finns säkerhets kopian av säkerhets kopian i en annan region (vanligt vis i den Azure-kopplade regionen). För södra Brasilien och Sydostasien regioner lagras säkerhets kopior i samma region för att tillgodose placering-krav för dessa regioner.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Vad måste jag tänka på när jag skapar en lösning med namn områden och Notification Hub?

#### <a name="mobile-appenvironment"></a>Mobilapp/miljö

* Använd en Notification Hub per mobilapp per miljö.
* I ett scenario med flera innehavare bör varje klient ha en separat hubb.
* Dela aldrig samma Notification Hub för produktions-och test miljöer. Den här metoden kan orsaka problem när du skickar meddelanden. (Apple erbjuder begränsat antal-och produktions-push-slutpunkter, var och en med separata autentiseringsuppgifter.)
* Som standard kan du skicka test meddelanden till dina registrerade enheter via Azure Portal eller den integrerade Azure-komponenten i Visual Studio. Tröskelvärdet är inställt på 10 enheter som väljs slumpmässigt från registrerings poolen.

> [!NOTE]
> Om navet ursprungligen konfigurerades med ett certifikat från Apple Sandbox och sedan konfigurerades om för att använda ett Apple-produktionsområde, är de ursprungliga tokens ogiltiga. Ogiltiga token orsakar att push-meddelanden inte fungerar. Separera dina produktions-och test miljöer och Använd olika hubbar för olika miljöer.

#### <a name="pns-credentials"></a>PNS-autentiseringsuppgifter

När en mobilapp registreras med en plattforms utvecklings portal (till exempel Apple eller Google), skickas ett app-ID och säkerhetstoken. Appens Server del innehåller dessa tokens till plattformens PNS så att push-meddelanden kan skickas till enheter. Säkerhetstoken kan vara i form av certifikat (till exempel Apple iOS eller Windows Phone) eller säkerhets nycklar (till exempel Google Android eller Windows). De måste konfigureras i Notification Hub. Konfigurationen utförs vanligt vis på meddelande-Hub-nivån, men den kan också göras på namn områdes nivå i ett scenario med flera innehavare.

#### <a name="namespaces"></a>Namnrymder

Namn områden kan användas för gruppering av distribution. De kan också användas för att representera alla meddelande hubbar för alla klienter i samma app i ett scenario med flera innehavare.

#### <a name="geo-distribution"></a>Geo-distribution

Geo-distribution är inte alltid kritiskt i scenarier med push-meddelanden. Olika PNSes (till exempel APN eller FCM) som levererar push-meddelanden till enheter är inte jämnt distribuerade.

Om du har ett program som används globalt kan du skapa hubbar i olika namn områden med hjälp av tjänsten Notification Hubs i olika Azure-regioner runtom i världen.

> [!NOTE]
> Vi rekommenderar inte detta avtal eftersom det ökar din hanterings kostnad, särskilt för registreringar. Det bör endast göras om det finns ett explicit behov.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Ska jag göra-registreringar från appens Server del eller direkt via klient enheter?

Registreringar från appens Server del är användbara när du måste autentisera klienter innan du skapar registreringen. De är också användbara när du har taggar som måste skapas eller ändras av appens Server del baserat på appens logik. Mer information finns i rikt linjer för [registrerings vägledning] och [Server dels registrering 2] sidor.

### <a name="what-is-the-push-notification-delivery-security-model"></a>Vad är leverans säkerhets modellen för push-meddelanden?

Azure Notification Hubs använder en signatur baserad säkerhets modell för [delad åtkomst](../storage/common/storage-sas-overview.md). Du kan använda token för signaturer för delad åtkomst på rot namn områdes nivån eller på den detaljerade nivån för Notification Hub. Token för signaturer för delad åtkomst kan anges till att följa olika auktoriseringsregler, till exempel för att skicka meddelande behörigheter eller för att lyssna efter aviserings behörigheter. Mer information finns i dokumentet om [Notification Hubs säkerhets modell] .

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hur ska jag hantera känslig nytto Last i push-meddelanden?

Alla meddelanden levereras till mål enheter av plattformens PNS. När ett meddelande skickas till Azure Notification Hubs bearbetas det och skickas till respektive PNS.

Alla anslutningar, från avsändaren till Azure-Notification Hubs till PNS, använder HTTPS.

> [!NOTE]
> Azure Notification Hubs loggar inte nytto lasten för meddelanden.

Om du vill skicka känsliga nytto laster rekommenderar vi att du använder ett säkert push-mönster. Avsändaren skickar ett ping-meddelande med en meddelande identifierare till enheten utan den känsliga nytto lasten. När appen på enheten tar emot nytto lasten anropar appen ett säkert API direkt för att hämta information om meddelandet. En guide om hur du implementerar det här mönstret finns på sidan [Notification Hubs säker push-självstudie] .

## <a name="operations"></a>Operations

### <a name="what-support-is-provided-for-disaster-recovery"></a>Vilken support tillhandahålls för haveri beredskap?

Vi tillhandahåller katastrof återställnings täckning för metadata i vårt slut (Notification Hubs namn, anslutnings sträng och annan viktig information). När ett katastrof återställnings scenario utlöses är registrerings data det *enda segmentet* i Notification Hubs-infrastrukturen som förloras. Du måste implementera en lösning för att fylla i dessa data i din nya hubb efter återställning:

1. Skapa en sekundär meddelande hubb i ett annat data Center. Vi rekommenderar att du skapar en från början för att skydda dig från en katastrof återställnings händelse som kan påverka hanterings funktionerna. Du kan också skapa en vid tidpunkten för katastrof återställnings händelsen.

2. Se till att den sekundära Notification-hubben synkroniseras med den primära Notification-hubben med något av följande alternativ:

   * Använd en app-server del som samtidigt skapar och uppdaterar installationer i båda Notification Hub. Med installationer kan du ange en egen unik enhets identifierare, vilket gör den lämpligare för replikeringsrelationen. Mer information finns i den här [exempel koden](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Använd en app-backend som hämtar en vanlig dumpning av registreringar från den primära Notification Hub som en säkerhets kopia. Den kan sedan utföra en Mass infogning i den sekundära meddelande hubben.

Den sekundära Notification-hubben kan få slut på installationer/registreringar som har upphört att gälla. När push-meddelandet görs till en utgångs referens rensar Notification Hubs automatiskt den associerade installations-/registrerings posten baserat på svaret från PNS-servern. Om du vill rensa utgångna poster från en sekundär meddelande hubb lägger du till anpassad logik som bearbetar feedback från varje sändning. Sedan väljer du att installera/registrera i den sekundära Notification Hub.

Om du inte har en server del, när appen startar på mål enheter, utför de en ny registrering i den sekundära Notification Hub. Slutligen kommer den sekundära meddelande hubben att alla aktiva enheter har registrerats.

Det kommer att finnas en tids period när enheter med appar som inte är öppna får aviseringar.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Är alla mina data lagrade i krypterad form?

Azure Notification Hubs krypterar alla kunddata i vila med undantag av registrerings märken. Därför bör du inte lagra personliga eller konfidentiella data med hjälp av taggar.

### <a name="is-there-audit-log-capability"></a>Finns det funktioner för gransknings logg?

Ja. Alla Notification Hubs hanterings åtgärder uppdaterar Azure aktivitets loggen som visas i [Azure Portal]. Azure aktivitets loggen ger insikter om de åtgärder som utförs på resurser i dina prenumerationer. Med hjälp av aktivitets loggen kan du bestämma vad, vem och när för Skriv åtgärder (skicka, skicka och ta bort) som gjorts för resurserna i din prenumeration. Du kan också förstå status för åtgärder och andra relevanta egenskaper. Ändå. Aktivitets loggen innehåller inte Läs-(GET)-åtgärd.

## <a name="monitoring-and-troubleshooting"></a>Övervaka och felsöka

### <a name="what-troubleshooting-capabilities-are-available"></a>Vilka fel söknings funktioner är tillgängliga?

Azure Notification Hubs innehåller flera funktioner för fel sökning, särskilt för det vanligaste scenariot med ignorerade meddelanden. Mer information finns i [Notification Hubs fel söknings] White Paper.

### <a name="what-telemetry-features-are-available"></a>Vilka funktioner för telemetri är tillgängliga?

Med Azure Notification Hubs kan du Visa telemetridata i [Azure Portal]. Information om måtten finns på sidan [Notification Hubs mått] .

Du kan också program mässigt komma åt Mät värden. Mer information finns i följande artiklar:

- [Hämta Azure Monitor mått med .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). I det här exemplet används användar namn och lösen ord. Om du vill använda ett certifikat överbelastar du FromServicePrincipal-metoden för att tillhandahålla ett certifikat som visas i [det här exemplet](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Hämta mått och aktivitets loggar för en resurs](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Genomgång av REST-API:et för Azure Monitoring](../azure-monitor/essentials/rest-api-walkthrough.md)

> [!NOTE]
> Lyckade meddelanden innebär bara att push-meddelanden har levererats till den externa PNS (till exempel APN för iOS och macOS eller FCM för Android-enheter). Det åligger PNS att leverera meddelanden till mål enheter. Normalt visar PNS inte leverans måtten för tredje part.  

[Azure-portalen]: https://portal.azure.com
[Notification Hubs priser]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Notification Hubs REST-API: er]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Vägledning för Server dels registrering]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Guide för registrering av Server del 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Notification Hubs säkerhets modell]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Själv studie kurs om Notification Hubs säker push]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Notification Hubs fel sökning]: ./notification-hubs-push-notification-fixer.md
[Notification Hubs mått]: ../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Export/import av registreringar]: ./export-modify-registrations-bulk.md
[Azure-portalen]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
