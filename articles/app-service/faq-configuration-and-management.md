---
title: Vanliga frågor om konfiguration
description: Få svar på vanliga frågor om konfigurations-och hanterings problem för Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 843e11e7f2df404a6bf1866da7b383ee9d1097de
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574782"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Vanliga frågor och svar om konfiguration och hantering för Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar om konfigurations-och hanterings problem för [Web Apps funktionen i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Finns det begränsningar som jag bör känna till om jag vill flytta App Service resurser?

Om du planerar att flytta App Service resurser till en ny resurs grupp eller prenumeration finns det några begränsningar som du bör känna till. Mer information finns i [App Service begränsningar](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hur gör jag för att använder du ett anpassat domän namn för min webbapp?

Svar på vanliga frågor om att använda ett anpassat domän namn med din Azure-webbapp finns i vår sju minuters video [Lägg till ett anpassat domän namn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Videon innehåller en genom gång av hur du lägger till ett anpassat domän namn. Den beskriver hur du använder din egen URL i stället för *. azurewebsites.net-URL: en med din App Service-webbapp. Du kan också se en detaljerad genom gång av [hur du mappar ett anpassat domän namn](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hur gör jag för att köper du en ny anpassad domän för min webbapp?

Information om hur du köper och konfigurerar en anpassad domän för din App Service-webbapp finns i [köpa och konfigurera ett anpassat domän namn i App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hur gör jag för att ladda upp och konfigurera ett befintligt TLS/SSL-certifikat för min webbapp?

Information om hur du överför och konfigurerar ett befintligt anpassat TLS/SSL-certifikat finns i [lägga till ett TLS/SSL-certifikat till din app service-app](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hur gör jag för att köpa och konfigurera ett nytt TLS/SSL-certifikat i Azure för mitt webb program?

Information om hur du köper och konfigurerar ett TLS/SSL-certifikat för din App Service-webbapp finns i [lägga till ett TLS/SSL-certifikat till din app service-app](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hur gör jag för att flyttar du Application Insights resurser?

Azure Application insikter har för närvarande inte stöd för flytt åtgärden. Om den ursprungliga resurs gruppen innehåller en Application Insights resurs kan du inte flytta den resursen. Om du tar med Application Insights resursen när du försöker flytta en App Service-app, Miss lyckas hela flyttnings åtgärden. Men Application Insights och App Service plan behöver inte finnas i samma resurs grupp som appen för att appen ska fungera korrekt.

Mer information finns i [App Service begränsningar](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Var hittar jag en väglednings check lista och lär dig mer om resurs flytt åtgärder?

[App Service begränsningar](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) visar hur du flyttar resurser till antingen en ny prenumeration eller till en ny resurs grupp i samma prenumeration. Du kan få information om check lista för resurs flyttning, se vilka tjänster som har stöd för flytt åtgärden och lär dig mer om App Service begränsningar och andra ämnen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hur gör jag för att ställa in serverns tidszon för min webbapp?

Så här ställer du in serverns tidszon för din webbapp:

1. I App Service prenumeration i Azure Portal går du till **program inställningar** -menyn.
2. Under **appinställningar** lägger du till den här inställningen:
    * Nyckel = WEBSITE_TIME_ZONE
    * Värde = *den tidszon du vill använda*
3. Välj **Spara**.

För de app Services som körs på Windows, se utdata från Windows- `tzutil /L` kommandot. Använd värdet från den andra raden i varje post. Exempel: "Tonga, normal tid". En del av dessa värden visas också i kolumnen **timezone** i [standard tids zoner](/windows-hardware/manufacture/desktop/default-time-zones).

Ange ett värde från [IANA TZ-databasen](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)för de app Services som körs på Linux. Till exempel: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Varför kan mina kontinuerliga WebJobs ibland inte fungera?

Som standard inaktive ras Web Apps om de är inaktiva under en angiven tids period. På så sätt kan systemet Spara resurser. I Basic-och standard-planer kan du aktivera inställningen **Always on** för att hålla webbappen inläst för hela tiden. Om din webbapp kör kontinuerliga WebJobs, bör du aktivera **Always on** eller också körs inte webbjobben på ett tillförlitligt sätt. Mer information finns i [skapa ett jobb som körs kontinuerligt](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hur gör jag för att hämta den utgående IP-adressen för min webbapp?

Hämta listan över utgående IP-adresser för din webbapp:

1. I Azure Portal går du till menyn **Egenskaper** på bladet webbapp.
2. Sök efter **utgående IP-adresser**.

Listan över utgående IP-adresser visas.

Information om hur du hämtar den utgående IP-adressen om din webbplats finns i en App Service-miljön finns i [utgående nätverks adresser](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hur gör jag för att hämta en reserverad eller dedikerad inkommande IP-adress för min webbapp?

Om du vill konfigurera en dedikerad eller reserverad IP-adress för inkommande anrop till din Azure App-webbplats, installerar och konfigurerar du ett IP-baserat TLS/SSL-certifikat.

Observera att om du vill använda en dedikerad eller reserverad IP-adress för inkommande samtal måste App Service plan finnas i en grundläggande eller högre tjänste plan.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan jag exportera mitt App Service certifikat för användning utanför Azure, till exempel för en webbplats som är värd för någon annan stans? 

Ja, du kan exportera dem för att använda utanför Azure. Mer information finns i [vanliga frågor och svar om App Service certifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan jag exportera mitt App Service certifikat som ska användas med andra Azure Cloud Services?

Portalen ger en förstklassig upplevelse för att distribuera ett App Service-certifikat via Azure Key Vault till App Service appar. Vi har dock fått förfrågningar från kunder om att använda dessa certifikat utanför App Services plattformen, till exempel med Azure Virtual Machines. Information om hur du skapar en lokal PFX-kopia av ditt App Service certifikat så att du kan använda certifikatet med andra Azure-resurser finns i [skapa en lokal PFX-kopia av ett app service certifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Mer information finns i [vanliga frågor och svar om App Service certifikat och anpassade domäner](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Varför visas meddelandet "delvis lyckades" när jag försöker säkerhetskopiera min webbapp?

En vanlig orsak till fel vid säkerhets kopiering är att vissa filer används av programmet. Filer som används är låsta när du utför säkerhets kopieringen. Detta förhindrar att de här filerna säkerhets kopie ras och kan resultera i statusen "delvis lyckades". Du kan eventuellt förhindra detta genom att utesluta filer från säkerhets kopierings processen. Du kan välja att bara säkerhetskopiera vad som behövs. Mer information finns i [säkerhetskopiera enbart de viktiga delarna av din webbplats med Azure Web Apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hur gör jag för att ta bort en rubrik från HTTP-svaret?

Om du vill ta bort huvudena från HTTP-svaret uppdaterar du platsens web.config-fil. Mer information finns i [ta bort standard server rubriker på dina Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Är App Service kompatibel med PCI standard 3,0 och 3,1?

För närvarande är Web Apps funktionen i Azure App Service kompatibel med PCI Data Security Standard (DSS) version 3,0 nivå 1. PCI DSS version 3,1 finns på vår översikt. Planering är redan igång för att anta att den senaste standarden kommer att fortsätta.

PCI DSS version 3,1-certifiering kräver att Transport Layer Security (TLS) 1,0 inaktive ras. Inaktive ring av TLS 1,0 är för närvarande inte ett alternativ för de flesta App Service-planer. Men om du använder App Service-miljön eller vill migrera din arbets belastning till App Service-miljön, kan du få bättre kontroll över din miljö. Det innebär att du inaktiverar TLS 1,0 genom att kontakta Azure-supporten. Inom en snar framtid planerar vi att göra dessa inställningar tillgängliga för användarna.

Mer information finns i [Microsoft Azure App Service webbappens kompatibilitet med PCI Standard 3,0 och 3,1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hur gör jag för att använder du mellanlagrings miljö och distributions platser?

När du distribuerar din webbapp till App Service i standard-och Premium App Service-planer kan du distribuera till en separat distributions plats i stället för till standard produktions platsen. Distributions fack är Live-webbappar som har sina egna värdnamn. Web App-innehåll och konfigurations element kan växlas mellan två distributions platser, inklusive produktions platsen.

Mer information om hur du använder distributions platser finns [i Konfigurera en mellanlagrings miljö i App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hur gör jag för att åtkomst och granska webb jobb loggar?

Så här granskar du webb jobb loggar:

1. Logga in på din **kudu-webbplats** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Välj webb jobbet.
3. Välj knappen **Växla utdata** .
4. Hämta utdatafilen genom att välja länken **Hämta** .
5. För enskilda körningar väljer du **individuell Invoke**.
6. Välj knappen **Växla utdata** .
7. Välj länken Hämta.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Jag försöker använda Hybridanslutningar med SQL Server. Varför visas meddelandet "system. OverflowException: aritmetisk åtgärd resulterade i ett spill"?

Om du använder Hybridanslutningar för att få åtkomst till SQL Server kan en Microsoft .NET uppdatering den 10 maj 2016 orsaka att anslutningar Miss lyckas. Du kanske ser det här meddelandet:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Lösning

Undantaget orsakades av ett problem med den Hybridanslutningshanteraren som har åtgärd ATS sedan. Se till att [uppdatera Hybridanslutningshanteraren](https://go.microsoft.com/fwlink/?LinkID=841308) för att lösa problemet.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hur gör jag för att lägga till en regel för att skapa en URL?

Om du vill lägga till en regel för en URL-omskrivning skapar du en web.config-fil med relevanta konfigurations poster i mappen **wwwroot** . Mer information finns i [Azure App Services: förstå URL-omskrivning](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hur gör jag för att kontroll av inkommande trafik till App Service?

På webbplats nivå har du två alternativ för att styra inkommande trafik till App Service:

* Aktivera dynamiska IP-begränsningar. Information om hur du aktiverar dynamiska IP-begränsningar finns i [IP-och domän begränsningar för Azure Websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Aktivera modulens säkerhet. Information om hur du aktiverar modulens säkerhet finns i [ModSecurity Web Application Firewall på Azure Websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Om du använder App Service-miljön kan du använda [Barracuda-brandväggen](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hur gör jag för att blockera portar i en App Service webbapp?

I den App Service delade klient miljön går det inte att blockera specifika portar på grund av infrastrukturens typ. TCP-portarna 4020, 4022 och 4024 kan också vara öppna för fel sökning i Visual Studio.

I App Service-miljön har du fullständig kontroll över inkommande och utgående trafik. Du kan använda nätverks säkerhets grupper för att begränsa eller blockera vissa portar. Mer information om App Service-miljön finns i [Introduktion av App Service-miljön](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hur gör jag för att fångar du en F12-spårning?

Du har två alternativ för att fånga en F12-spårning:

* F12 HTTP-spårning
* F12-konsolens utdata

### <a name="f12-http-trace"></a>F12 HTTP-spårning

1. Gå till din webbplats i Internet Explorer. Det är viktigt att logga in innan du gör nästa steg. Annars fångar F12-spårningen känsliga inloggnings data.
2. Tryck på F12.
3. Kontrol lera att fliken **nätverk** är markerad och välj sedan den gröna **uppspelnings** knappen.
4. Utför stegen för att återskapa problemet.
5. Välj knappen röd **stopp** .
6. Välj knappen **Spara** (disk ikon) och spara filen har (i Internet Explorer och Microsoft Edge) *eller* Högerklicka på filen har och välj sedan **Spara som har innehåll** (i Chrome).

### <a name="f12-console-output"></a>F12-konsolens utdata

1. Välj fliken **konsol** .
2. Välj fliken (**fel**, **Varning** eller **information**) för varje flik som innehåller fler än noll objekt. Om fliken inte är markerad är fliken nedtonad eller svart när du flyttar markören bort från den.
3. Högerklicka på meddelande ytan i fönstret och välj **Kopiera alla**.
4. Klistra in den kopierade texten i en fil och spara sedan filen.

Om du vill visa en har-fil kan du använda [visaren har visats](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Varför visas ett fel meddelande när jag försöker ansluta en App Service-webbapp till ett virtuellt nätverk som är anslutet till ExpressRoute?

Om du försöker ansluta en Azure-webbapp till ett virtuellt nätverk som är anslutet till Azure ExpressRoute Miss lyckas det. Följande meddelande visas: "gatewayen är inte en VPN-gateway."

För närvarande kan du inte ha punkt-till-plats-VPN-anslutningar till ett virtuellt nätverk som är anslutet till ExpressRoute. Det går inte att använda en punkt-till-plats-VPN och ExpressRoute för samma virtuella nätverk. Mer information finns i [ExpressRoute och begränsningar för plats-till-plats-VPN-anslutningar och begränsningar](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hur gör jag för att ansluta en App Service webbapp till ett virtuellt nätverk som har en statisk routning (principbaserad) Gateway?

För närvarande stöds inte att ansluta en App Service webbapp till ett virtuellt nätverk som har en statisk routning (principbaserad) Gateway. Om det virtuella mål nätverket redan finns måste det ha punkt-till-plats-VPN aktiverat, med en dynamisk routning-Gateway, innan den kan anslutas till en app. Om din gateway är inställd på statisk routning kan du inte aktivera en punkt-till-plats-VPN. 

Mer information finns i [integrera en app med ett virtuellt Azure-nätverk](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Varför kan jag bara skapa en App Service plan i mina App Service-miljön, trots att jag har två tillgängliga arbetare?

För att tillhandahålla fel tolerans kräver App Service-miljön att varje arbets pool måste ha minst en ytterligare beräknings resurs. Det går inte att tilldela den ytterligare beräknings resursen en arbets belastning.

Mer information finns i [så här skapar du en app service-miljön](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Varför visas tids gränser när jag försöker skapa en App Service-miljön?

Ibland går det inte att skapa en App Service-miljön. I så fall visas följande fel i aktivitets loggarna:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Lös detta genom att kontrol lera att inget av följande villkor är uppfyllda:
* Under nätet är för litet.
* Under nätet är inte tomt.
* ExpressRoute förhindrar nätverks anslutnings kraven för en App Service-miljön.
* En felaktig nätverks säkerhets grupp förhindrar nätverks anslutnings kraven för en App Service-miljön.
* Tvingad tunnel trafik är aktive rad.

Mer information finns i [vanliga frågor när du distribuerar (skapar) en ny Azure App Service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Varför kan jag inte ta bort mina App Service plan?

Du kan inte ta bort en App Service plan om App Service appar är associerade med App Service plan. Innan du tar bort en App Service plan tar du bort alla associerade App Services appar från App Service plan.

## <a name="how-do-i-schedule-a-webjob"></a>Hur gör jag för att schemalägga ett webb jobb?

Du kan skapa ett schemalagt webb jobb med hjälp av cron uttryck:

1. Skapa en Settings. job-fil.
2. I den här JSON-filen inkluderar du en schema egenskap med hjälp av ett cron-uttryck: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Mer information om schemalagda webbjobb finns i [skapa ett schemalagt webb jobb med hjälp av ett cron-uttryck](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hur gör jag för att utföra inträngande test för min App Service-app?

[Skicka en begäran](https://portal.msrc.microsoft.com/engage/pentest)för att utföra inträngande tester.

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hur gör jag för att konfigurera ett anpassat domän namn för en App Service webbapp som använder Traffic Manager?

Information om hur du använder ett anpassat domän namn med en App Service-app som använder Azure Traffic Manager för belastnings utjämning finns i [Konfigurera ett anpassat domän namn för en Azure-webbapp med Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mitt App Service Certificate har flaggats för bedrägeri. Hur gör jag för att lösa det?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Under domän verifieringen av ett App Service-certifikat Köp kan följande meddelande visas:

"Ditt certifikat har flaggats för möjlig bedrägerier. Begäran är för närvarande en granskning. Kontakta Azure-supporten om certifikatet inte blir användbart inom 24 timmar. "

När meddelandet visas kan det ta upp till 24 timmar innan den här processen för bedrägeri verifieringen har slutförts. Under den här tiden kan du fortsätta att se meddelandet.

Om ditt App Service certifikat fortsätter att visa det här meddelandet efter 24 timmar kör du följande PowerShell-skript. Skriptet kontaktar [certifikat leverantören](https://www.godaddy.com/) direkt för att lösa problemet.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hur fungerar autentisering och auktorisering i App Service?

Detaljerad dokumentation för autentisering och auktorisering i App Service finns i dokument för olika identifierings-providers inloggningar:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-konto](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hur gör jag för att omdirigera standard domänen *. azurewebsites.net till min Azure Web Apps anpassade domän?

När du skapar en ny webbplats genom att använda Web Apps i Azure, tilldelas *en standard plats*. azurewebsites.net-domän till din webbplats. Om du lägger till ett anpassat värdnamn på din webbplats och inte vill att användarna ska kunna komma åt din standard-azurewebsites.net-domän, kan du omdirigera standard-URL: en. Information om hur du omdirigerar all trafik från din webbplats standard domän till din anpassade domän finns i [dirigera om standard domänen till din anpassade domän i Azure Web Apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hur gör jag för att ta reda på vilken version av .NET-versionen som är installerad i App Service?

Det snabbaste sättet att hitta den version av Microsoft .NET som är installerad i App Service är att använda kudu-konsolen. Du kan komma åt kudu-konsolen från portalen eller genom att använda URL: en för din App Service-app. Detaljerade anvisningar finns i [bestämma den installerade .net-versionen i App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Varför fungerar inte autoskalning som förväntat?

Om den automatiska skalningen i Azure inte har skalats in eller skalas ut från webb program instansen som du förväntade, kan du köra i ett scenario där vi avsiktligt väljer att inte skala för att undvika en oändlig loop på grund av "växlar". Detta inträffar vanligt vis när det inte finns en lämplig marginal mellan tröskelvärdena för skalning och skalning. Information om hur du undviker "växlar" och hur du kan läsa mer om hur du använder bästa metoder för autoskalning finns i [metod tips för autoskalning](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Varför skalar autoskalning ibland bara delvis?

Autoskalning utlöses när måtten överskrider förkonfigurerade gränser. Ibland kanske du märker att kapaciteten bara delvis är ifylld jämfört med det du förväntade dig. Detta kan inträffa när antalet instanser som du vill ha inte är tillgängliga. I det scenariot fyller autoskalning delvis in med det tillgängliga antalet instanser. Autoskalning kör sedan ombalansa logiken för att få mer kapacitet. Den allokerar de återstående instanserna. Observera att det kan ta några minuter.

Om du inte ser det förväntade antalet instanser efter några minuter kan det bero på att den partiella påfyllning var tillräckligt för att ta Mät värdena inom gränserna. Eller så kanske autoskalning har skalats ned eftersom det nått den lägre Mät gränsen.

Skicka en supportbegäran om inget av dessa villkor gäller och problemet kvarstår.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hur gör jag för att aktivera HTTP-komprimering för mitt innehåll?

Om du vill aktivera komprimering både för statiska och dynamiska innehålls typer lägger du till följande kod på web.config-filen på program nivå:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Du kan också ange de angivna dynamiska och statiska MIME-typerna som du vill komprimera. Mer information finns i vårt svar på en forum fråga i [httpCompression-inställningar på en enkel Azure-webbplats](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hur gör jag för att migrera från en lokal miljö till App Service?

Om du vill migrera platser från Windows-och Linux-webbservrar till App Service kan du använda Azure App Service Migration Assistant. Migrations verktyget skapar webbappar och databaser i Azure efter behov och publicerar sedan innehållet. Mer information finns i [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Varför utfärdas mitt certifikat i 11 månader och inte ett helt år?

För certifikat som utfärdas efter 2020-09-01 är den maximala tiden nu 397 dagar. Certifikat som utfärdats innan 2020-09-01 har en maximal giltighetstid på 825 dagar innan de förnyas eller får en ny nyckel. Certifikat som förnyas efter 2020-09-01 påverkas av den här ändringen och användarna kan se en kortare giltighetstid för förnyade certifikat.
GoDaddy har implementerat en prenumerationstjänst som uppfyller de nya kraven samtidigt som befintliga kundcertifikat respekteras. Trettio dagar innan det nyutfärdade certifikatet upphör att gälla utfärdar tjänsten automatiskt ett andra certifikat som utökar varaktigheten till det ursprungliga förfallodatumet. App Service samarbetar med GoDaddy om att åtgärda den här ändringen och se till att våra kunders certifikat får den varaktighet de ska ha.
