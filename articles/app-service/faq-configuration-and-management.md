---
title: Vanliga frågor och svar om konfiguration
description: Få svar på vanliga frågor om konfigurations- och hanteringsproblem för Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4b6ceef837d985db004e9d925b554c54c287424
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834542"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Vanliga frågor och svar om konfiguration och hantering Web Apps i Azure

Den här artikeln innehåller svar på vanliga frågor och svar om konfigurations- och [hanteringsproblem för funktionen Web Apps i Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Finns det begränsningar som jag bör känna till om jag vill flytta App Service resurser?

Om du planerar att flytta App Service till en ny resursgrupp eller prenumeration finns det några begränsningar som du bör känna till. Mer information finns i [App Service begränsningar.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hur gör jag för att använda ett anpassat domännamn för min webbapp?

Svar på vanliga frågor om hur du använder ett anpassat domännamn med din Azure-webbapp finns i vår sjuminutersvideo [Lägg till ett anpassat domännamn](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Videon innehåller en genomgång av hur du lägger till ett anpassat domännamn. Den beskriver hur du använder din egen URL i stället för url:en *.azurewebsites.net med din App Service-webbapp. Du kan också se en detaljerad genomgång av [hur du mappar ett anpassat domännamn.](app-service-web-tutorial-custom-domain.md)


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hur gör jag för att köpa en ny anpassad domän för min webbapp?

Information om hur du köper och konfigurerar en anpassad domän för din App Service-webbapp finns i Köpa och konfigurera ett anpassat [domännamn i App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hur gör jag för att ladda upp och konfigurera ett befintligt TLS/SSL-certifikat för min webbapp?

Information om hur du laddar upp och ställer in ett befintligt anpassat TLS/SSL-certifikat finns i Lägga till [ett TLS/SSL-certifikat i din App Service app.](configure-ssl-certificate.md)


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hur gör jag för att köpa och konfigurera ett nytt TLS/SSL-certifikat i Azure för min webbapp?

Information om hur du köper och ställer in ett TLS/SSL-certifikat för din App Service-webbapp finns i Lägga till [ett TLS/SSL-certifikat i din App Service app.](configure-ssl-certificate.md)


## <a name="how-do-i-move-application-insights-resources"></a>Hur gör jag för att flytta Application Insights resurser?

För närvarande Azure Application Insights inte flyttåtgärden. Om den ursprungliga resursgruppen innehåller Application Insights resurs kan du inte flytta den resursen. Om du inkluderar Application Insights resurs när du försöker flytta en App Service-app misslyckas hela flyttåtgärden. Men Application Insights och App Service plan behöver inte finnas i samma resursgrupp som appen för att appen ska fungera korrekt.

Mer information finns i [App Service begränsningar.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Var hittar jag en checklista för vägledning och lär mig mer om åtgärder för resursflyttning?

[App Service begränsningar](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) visar hur du flyttar resurser till antingen en ny prenumeration eller till en ny resursgrupp i samma prenumeration. Du kan få information om checklistan för resursflyttning, lära dig vilka tjänster som stöder flyttåtgärden och lära dig mer om App Service begränsningar och andra ämnen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hur gör jag för att ange serverns tidszon för webbappen?

Så här anger du serverns tidszon för webbappen:

1. I Azure Portal går du App Service prenumerationen till menyn **Programinställningar.**
2. Lägg **till den här** inställningen under Appinställningar:
    * Nyckel = WEBSITE_TIME_ZONE
    * Värde = *Den tidszon som du vill använda*
3. Välj **Spara**.

Information om apptjänster som körs i Windows finns i utdata från `tzutil /L` Windows-kommandot. Använd värdet från den andra raden i varje post. Till exempel: "Tonga Standard Time". Vissa av dessa värden visas också i kolumnen **Tidszon i** [Standardtidszoner.](/windows-hardware/manufacture/desktop/default-time-zones)

För App Services som körs i Linux anger du ett värde från [IANA TZ-databasen](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Till exempel: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Varför misslyckas ibland mina kontinuerliga webbjobb?

Som standard tas webbappar bort om de är inaktiva under en viss tidsperiod. På så sätt kan systemet spara resurser. I Basic- och Standard-planer kan du aktivera **inställningen Alltid på** för att hålla webbappen inläst hela tiden. Om webbappen kör kontinuerliga webbjobb bör du aktivera **Always On**, annars kanske webbjobben inte körs på ett tillförlitligt sätt. Mer information finns i Skapa [ett webbjobb som körs kontinuerligt.](webjobs-create.md#CreateContinuous)

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hur gör jag för att hämta den utgående IP-adressen för min webbapp?

Så här hämtar du listan över utgående IP-adresser för din webbapp:

1. I Azure Portal går du till menyn Egenskaper på **bladet Webbapp.**
2. Sök efter **utgående IP-adresser**.

Listan över utgående IP-adresser visas.

Information om hur du hämtar den utgående IP-adressen om din webbplats finns i en App Service-miljön finns i [Utgående nätverksadresser.](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hur gör jag för att du en reserverad eller dedikerad inkommande IP-adress för min webbapp?

Om du vill konfigurera en dedikerad eller reserverad IP-adress för inkommande anrop till din Azure-appwebbplats installerar och konfigurerar du ett IP-baserat TLS/SSL-certifikat.

Observera att om du vill använda en dedikerad eller reserverad IP-adress för inkommande App Service plan måste din tjänstplan finnas i en Grundläggande eller högre tjänstplan.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan jag exportera mitt App Service certifikat för användning utanför Azure, till exempel för en webbplats som finns någon annanstans? 

Ja, du kan exportera dem för användning utanför Azure. Mer information finns i [Vanliga frågor och svar om App Service certifikat och anpassade domäner.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan jag exportera mitt App Service certifikat för användning med andra Azure-molntjänster?

Portalen ger en förstklassig upplevelse för att distribuera ett App Service certifikat via Azure Key Vault till App Service appar. Vi har dock tagit emot begäranden från kunder om att använda dessa certifikat utanför App Service-plattformen, till exempel med Azure Virtual Machines. Information om hur du skapar en lokal PFX-kopia av ditt App Service-certifikat så att du kan använda certifikatet med andra Azure-resurser finns i Skapa en lokal PFX-kopia av ett [App Service-certifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Mer information finns i Vanliga [frågor och svar om App Service certifikat och anpassade domäner.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Varför ser jag meddelandet "Lyckades delvis" när jag försöker backa upp min webbapp?

En vanlig orsak till säkerhetskopieringsfel är att vissa filer används av programmet. Filer som används låses när du utför säkerhetskopieringen. Detta förhindrar att de här filerna säkerhetskopieras och kan resultera i statusen "Delvis lyckades". Du kan eventuellt förhindra att detta inträffar genom att undanta filer från säkerhetskopieringsprocessen. Du kan välja att bara vilka resurser som behövs. Mer information finns i [Säkerhetskopiera bara viktiga delar av din webbplats med Azure-webbappar.](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hur gör jag för att du ta bort ett huvud från HTTP-svaret?

Om du vill ta bort huvudena från HTTP-svaret uppdaterar du webbplatsens web.config fil. Mer information finns i Ta [bort standardserverhuvuden på dina Azure-webbplatser.](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Är App Service kompatibel med PCI Standard 3.0 och 3.1?

För närvarande Web Apps funktionen i Azure App Service kompatibilitet med PCI Data Security Standard (DSS) version 3.0 Nivå 1. PCI DSS version 3.1 finns i vår översikt. Planeringen är redan på gång för hur implementeringen av den senaste standarden ska fortsätta.

PCI DSS certifiering för version 3.1 kräver inaktivering Transport Layer Security (TLS) 1.0. För närvarande är inaktivering av TLS 1.0 inte ett alternativ för de flesta App Service planer. Men om du använder App Service-miljön eller är beredd att migrera din arbetsbelastning till App Service-miljön kan du få större kontroll över din miljö. Detta innebär att TLS 1.0 inaktiveras genom att kontakta Azure Support. Inom en snar framtid planerar vi att göra dessa inställningar tillgängliga för användare.

Mer information finns i Microsoft Azure App Service [med PCI Standard 3.0 och 3.1.](https://support.microsoft.com/help/3124528)

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hur gör jag för att du mellanlagringsmiljön och distributionsfacken?

När du distribuerar webbappen till App Service i Standard- och Premium-App Service-planer kan du distribuera till ett separat distributionsfack i stället för till standardproduktionsplatsen. Distributionsfack är live-webbappar som har sina egna värdnamn. Webbappinnehåll och konfigurationselement kan växlas mellan två distributionsfack, inklusive produktionsplatsen.

Mer information om hur du använder distributionsfack finns i [Konfigurera en mellanlagringsmiljö i App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hur gör jag för att åtkomst till och granska webbjobbsloggar?

Så här granskar du webbjobbsloggar:

1. Logga in på **kudu-webbplatsen** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Välj Webbjobb.
3. Välj knappen **Växla utdata.**
4. Om du vill ladda ned utdatafilen väljer du **länken** Ladda ned.
5. För enskilda körningar väljer du **Individual Invoke (Individuell anropa).**
6. Välj knappen **Växla utdata.**
7. Välj nedladdningslänken.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Jag försöker använda hybridanslutningar med SQL Server. Varför visas meddelandet "System.OverflowException: Aritmetiska åtgärden resulterade i ett spill"?

Om du använder hybridanslutningar för SQL Server kan en Microsoft .NET uppdatering den 10 maj 2016 orsaka att anslutningarna misslyckas. Du kan se det här meddelandet:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Lösning

Undantaget orsakades av ett problem med Hybridanslutningshanteraren som har åtgärdats sedan dess. Se till att [uppdatera Hybridanslutningshanteraren](https://go.microsoft.com/fwlink/?LinkID=841308) för att lösa problemet.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hur gör jag för att lägga till en regel för URL-omskrivning?

Om du vill lägga till en url-omskrivningsregel skapar du en web.config-fil med relevanta konfigurationsposter i **wwwroot-mappen.** Mer information finns i [Azure App Services: Understanding URL rewrite](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hur gör jag för att styra inkommande trafik till App Service?

På platsnivå har du två alternativ för att styra inkommande trafik till App Service:

* Aktivera dynamiska IP-begränsningar. Information om hur du aktiverar dynamiska IP-begränsningar finns i [IP- och domänbegränsningar för Azure-webbplatser.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)
* Aktivera Modulsäkerhet. Information om hur du aktiverar modulsäkerhet finns i [ModSecurity web application firewall on Azure websites (ModSecurity-brandvägg för webbaserade program på Azure-webbplatser).](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)

Om du använder App Service-miljön kan du använda [Barracuda-brandväggen](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hur gör jag för att blockera portar i en App Service webbapp?

I den App Service delade klientmiljön går det inte att blockera specifika portar på grund av infrastrukturens natur. TCP-portarna 4020, 4022 och 4024 kan också vara öppna för Visual Studio fjärrfelsökning.

I App Service-miljön har du fullständig kontroll över inkommande och utgående trafik. Du kan använda nätverkssäkerhetsgrupper för att begränsa eller blockera specifika portar. Mer information om App Service-miljön finns i [Introduktion App Service-miljön](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hur gör jag för att du en F12-spårning?

Det finns två alternativ för att samla in en F12-spårning:

* F12 HTTP-spårning
* F12-konsolutdata

### <a name="f12-http-trace"></a>F12 HTTP-spårning

1. I Internet Explorer du till din webbplats. Det är viktigt att logga in innan du gör nästa steg. Annars samlar F12-spårningen in känsliga inloggningsdata.
2. Tryck på F12.
3. Kontrollera att **fliken Nätverk** är markerad och välj sedan den gröna **uppspelningsknappen.**
4. Gör de steg som återskapar problemet.
5. Välj den röda **stoppknappen.**
6. Välj knappen **Spara** (diskikonen) och spara HAR-filen (i Internet Explorer  och Microsoft Edge) eller högerklicka på HAR-filen och välj sedan Spara som **HAR** med innehåll (i Chrome).

### <a name="f12-console-output"></a>F12-konsolutdata

1. Välj **fliken** Konsol.
2. För varje flik som innehåller fler än noll objekt väljer du fliken (**Fel,** **Varning** eller **Information**). Om fliken inte är markerad är flikikonen grå eller svart när du flyttar markören bort från den.
3. Högerklicka i meddelandeområdet i fönstret och välj sedan **Kopiera alla**.
4. Klistra in den kopierade texten i en fil och spara sedan filen.

Om du vill visa en HAR-fil kan du använda [HAR-visningsprogrammet.](http://www.softwareishard.com/har/viewer/)

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Varför får jag ett felmeddelande när jag försöker ansluta en App Service till ett virtuellt nätverk som är anslutet till ExpressRoute?

Om du försöker ansluta en Azure-webbapp till ett virtuellt nätverk som är anslutet till Azure ExpressRoute misslyckas den. Följande meddelande visas: "Gateway är inte en VPN-gateway."

För närvarande kan du inte ha punkt-till-plats-VPN-anslutningar till ett virtuellt nätverk som är anslutet till ExpressRoute. Punkt-till-plats-VPN och ExpressRoute kan inte samexistera för samma virtuella nätverk. Mer information finns i ExpressRoute och gränser och begränsningar [för VPN-anslutningar](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)från plats till plats.

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hur gör jag för att ansluta en App Service-webbapp till ett virtuellt nätverk som har en gateway för statisk routning (principbaserad)

För närvarande stöds inte anslutning App Service en webbapp till ett virtuellt nätverk som har en gateway för statisk routning (principbaserad). Om det virtuella målnätverket redan finns måste det ha punkt-till-plats-VPN aktiverat, med en dynamisk routningsgateway, innan det kan anslutas till en app. Om din gateway är inställd på statisk routning kan du inte aktivera ett punkt-till-plats-VPN. 

Mer information finns i Integrera [en app med ett virtuellt Azure-nätverk.](web-sites-integrate-with-vnet.md)

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Varför kan jag i App Service-miljön skapa bara en App Service plan, även om jag har två tillgängliga arbetare?

För att tillhandahålla feltolerans App Service-miljön att varje arbetspool behöver minst en ytterligare beräkningsresurs. Den ytterligare beräkningsresursen kan inte tilldelas en arbetsbelastning.

Mer information finns i [Så här skapar du en App Service-miljön](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Varför visas tidsgränser när jag försöker skapa en App Service-miljön?

Ibland går det inte att skapa App Service-miljön en ny. I så fall visas följande fel i aktivitetsloggarna:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Lös detta genom att kontrollera att inget av följande villkor är sant:
* Undernätet är för litet.
* Undernätet är inte tomt.
* ExpressRoute förhindrar kraven på nätverksanslutning för en App Service-miljön.
* En felaktig nätverkssäkerhetsgrupp förhindrar kraven på nätverksanslutning för en App Service-miljön.
* Tvingad tunneling är aktiverat.

Mer information finns i [Vanliga problem när du distribuerar (skapar) en ny Azure App Service-miljön](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Varför kan jag inte ta bort min App Service plan?

Du kan inte ta bort en App Service plan om några App Service är associerade med App Service plan. Innan du tar bort App Service plan bör du ta bort alla App Service appar från App Service plan.

## <a name="how-do-i-schedule-a-webjob"></a>Hur gör jag för att schemalägga ett webbjobb?

Du kan skapa ett schemalagt webbjobb med hjälp av Cron-uttryck:

1. Skapa en settings.job-fil.
2. I den här JSON-filen inkluderar du en schemaegenskap med hjälp av ett Cron-uttryck: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Mer information om schemalagda webbjobb finns i Skapa ett [schemalagt webbjobb med hjälp av ett Cron-uttryck.](webjobs-create.md#CreateScheduledCRON)

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hur gör jag för att utföra intrångstester för min App Service app?

Skicka en begäran för att [utföra intrångstestning.](https://portal.msrc.microsoft.com/engage/pentest)

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hur gör jag för att konfigurera ett anpassat domännamn för en App Service-webbapp som använder Traffic Manager?

Information om hur du använder ett anpassat domännamn med en App Service-app som använder Azure Traffic Manager för belastningsutjämning finns i Konfigurera ett anpassat domännamn för en [Azure-webbapp med Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mitt App Service Certificate har flaggats för bedrägeri. Hur gör jag för att lösa det?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Under domänverifieringen av ett App Service certifikatinköp kan följande meddelande visas:

"Certifikatet har flaggats för eventuellt bedrägeri. Begäran granskas för närvarande. Om certifikatet inte kan användas inom 24 timmar kontaktar du Azure Support."

Som meddelandet antyder kan den här bedrägeriverifieringsprocessen ta upp till 24 timmar att slutföra. Under den här tiden fortsätter du att se meddelandet.

Om ditt App Service certifikat fortsätter att visa det här meddelandet efter 24 timmar kör du följande PowerShell-skript. Skriptet kontaktar [certifikatprovidern direkt](https://www.godaddy.com/) för att lösa problemet.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hur fungerar autentisering och auktorisering i App Service?

Detaljerad dokumentation för autentisering och auktorisering i App Service finns i dokumentationen för olika inloggningar för identifieringsprovider:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-konto](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hur gör jag för att omdirigerar du standarddomänen *.azurewebsites.net till min anpassade Azure-webbapps domän?

När du skapar en ny webbplats med Web Apps i Azure tilldelas en *standarddomän* för platsnamnet .azurewebsites.net till din plats. Om du lägger till ett anpassat värdnamn på webbplatsen och inte vill att användarna ska kunna komma åt din standarddomän *.azurewebsites.net kan du omdirigera standard-URL:en. Information om hur du omdirigerar all trafik från webbplatsens standarddomän till din anpassade domän finns i Omdirigera standarddomänen till din anpassade domän [i Azure Web Apps.](https://zainrizvi.io/blog/block-default-azure-websites-domain/)

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hur gör jag för att vilken version av .NET-versionen som är installerad i App Service?

Det snabbaste sättet att hitta den version av Microsoft .NET som är installerad i App Service är att använda Kudu-konsolen. Du kan komma åt Kudu-konsolen från portalen eller genom att använda URL:en för din App Service app. Detaljerade anvisningar finns i Fastställa [den installerade .NET-versionen i App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Varför fungerar inte autoskalning som förväntat?

Om Azure Autoscale inte har skalat in eller skalat ut webbappinstansen som förväntat kan du få problem med ett scenario där vi avsiktligt väljer att inte skala för att undvika en oändlig loop på grund av "flikande". Detta inträffar vanligtvis när det inte finns en tillräcklig marginal mellan tröskelvärdena för utskalning och inskalning. Information om hur du undviker att "byta" och läser om andra metodtips för autoskalning finns i [Metodtips för automatisk skalning.](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Varför skalas autoskalning ibland bara delvis?

Autoskalning utlöses när mått överskrider förkonfigurerade gränser. Ibland kanske du märker att kapaciteten bara är delvis fylld jämfört med vad du förväntade dig. Detta kan inträffa när antalet instanser som du vill ha inte är tillgängligt. I det scenariot fylls autoskalning delvis i med det tillgängliga antalet instanser. Autoskalning kör sedan ombalanseringslogiken för att få mer kapacitet. Den allokerar återstående instanser. Observera att det kan ta några minuter.

Om du inte ser det förväntade antalet instanser efter några minuter kan det vara på grund av att den partiella försöken var tillräckligt för att få måtten inom gränserna. Eller så kan autoskalningen ha skalats ned eftersom den nådde den nedre måttgränsen.

Om inget av dessa villkor gäller och problemet kvarstår skickar du en supportbegäran.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hur gör jag för att aktivera HTTP-komprimering för mitt innehåll?

Om du vill aktivera komprimering för både statiska och dynamiska innehållstyper lägger du till följande kod i web.config programnivå:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Du kan också ange de specifika dynamiska och statiska MIME-typer som du vill komprimera. Mer information finns i vårt svar på en forumfråga i [httpCompression-inställningar på en enkel Azure-webbplats.](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hur gör jag för att migrera från en lokal miljö till App Service?

Om du vill migrera webbplatser från Windows- och Linux-webbservrar App Service du använda Azure App Service Migration Assistant. Migreringsverktyget skapar webbappar och databaser i Azure efter behov och publicerar sedan innehållet. Mer information finns i [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Varför utfärdas mitt certifikat i 11 månader och inte ett helt år?

För certifikat som utfärdas efter 2020-09-01 är den maximala tiden nu 397 dagar. Certifikat som utfärdats innan 2020-09-01 har en maximal giltighetstid på 825 dagar innan de förnyas eller får en ny nyckel. Certifikat som förnyas efter 2020-09-01 påverkas av den här ändringen och användarna kan se en kortare giltighetstid för förnyade certifikat.
GoDaddy har implementerat en prenumerationstjänst som uppfyller de nya kraven samtidigt som befintliga kundcertifikat respekteras. Trettio dagar innan det nyutfärdade certifikatet upphör att gälla utfärdar tjänsten automatiskt ett andra certifikat som utökar varaktigheten till det ursprungliga förfallodatumet. App Service samarbetar med GoDaddy om att åtgärda den här ändringen och se till att våra kunders certifikat får den varaktighet de ska ha.
