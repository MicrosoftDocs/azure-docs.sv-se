---
title: Förhindra att under domäner översätts med Azure DNS Ali Aset-poster och Azure App Service anpassad domän verifiering
description: Lär dig hur du undviker det vanliga hotet om hög allvarlighets grad för under domän överbelastning
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: c3a821156074727d02ab36cf88f3e998756b8cc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389458"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Förhindra Dangling DNS-poster och Undvik under domän övertag Ande

I den här artikeln beskrivs det vanliga säkerhetshot för under domän Övertagning och de steg du kan vidta för att undvika det.


## <a name="what-is-a-subdomain-takeover"></a>Vad är en under domän överköps?

Under domänens övertag Ande är ett vanligt hot mot hög allvarlighets grad för organisationer som regelbundet skapar och tar bort många resurser. En under domän överköps kan uppstå när du har en [DNS-post](../../dns/dns-zones-records.md#dns-records) som pekar på en Deetablerad Azure-resurs. Sådana DNS-poster kallas även "Dangling DNS"-poster. CNAME-poster är särskilt sårbara för det här hotet. Under domänens övertag ande gör det möjligt för skadliga aktörer att omdirigera trafik som är avsedd för en organisations domän till en plats som utför skadlig aktivitet.

Ett vanligt scenario för en under domän överköps:

1. **FLIKAR**

    1. Du etablerar en Azure-resurs med ett fullständigt kvalificerat domän namn (FQDN) för `app-contogreat-dev-001.azurewebsites.net` .

    1. Du tilldelar en CNAME-post i din DNS-zon med den under domän `greatapp.contoso.com` som dirigerar trafik till Azure-resursen.

1. **AVETABLERING**

    1. Azure-resursen har avetablerats eller tagits bort efter att den inte längre behövs. 
    
        I det här läget `greatapp.contoso.com` *bör* CNAME-posten tas bort från din DNS-zon. Om CNAME-posten inte tas bort annonseras den som en aktiv domän men dirigerar inte trafik till en aktiv Azure-resurs. Detta är definitionen av en DNS-post av typen "Dangling".

    1. Dangling-underdomänen, `greatapp.contoso.com` , är nu sårbar och kan tas över genom att tilldelas till en annan Azure-prenumerations resurs.

1. **ÖVERTAG Ande**

    1. Med hjälp av vanligt tillgängliga metoder och verktyg, identifierar en hot aktör Dangling-underdomänen.  

    1. Hot aktören etablerar en Azure-resurs med samma fullständiga domän namn för den resurs som du tidigare kontrollerat. I det här exemplet `app-contogreat-dev-001.azurewebsites.net` .

    1. Trafik som skickas till under domänen `greatapp.contoso.com` dirigeras nu till den skadliga aktörens resurs där de styr innehållet.



![Under domän överköps från en avetablerad webbplats](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Riskerna med under domäner överköps

När en DNS-post pekar på en resurs som inte är tillgänglig, bör posten tas bort från din DNS-zon. Om den inte har tagits bort är det en "Dangling DNS"-post och skapar möjligheten för under domän övertag Ande.

Dangling DNS-poster gör det möjligt för hot aktörer att ta kontroll över det associerade DNS-namnet för att vara värd för en skadlig webbplats eller tjänst. Skadliga sidor och tjänster i en organisations under domän kan resultera i följande:

- **Förlust av kontroll över innehållet i under domänen** -negativ press om organisationens oförmåga att skydda dess innehåll, samt varumärkes skada och förtroende förlust.

- **Cookie-fångst från misstänkta besökare** – det är vanligt att webbappar exponerar sessionscookies till under domäner (*. contoso.com), vilket innebär att alla under domäner kan komma åt dem. Hot aktörer kan använda under domän uppköp för att bygga en äkta utseende sida, lura obehöriga användare att besöka den och skörda sina cookies (även säkra cookies). En vanlig felbegrepp är att använda SSL-certifikat för att skydda din webbplats, och dina användares cookies, från en övertag Ande. En hot aktör kan dock använda den kapade under domänen som ska användas för och ta emot ett giltigt SSL-certifikat. Giltiga SSL-certifikat ger dem åtkomst till säkra cookies och kan öka den uppfattade giltighet på den skadliga webbplatsen ytterligare.

- **Phishing-kampanjer** – autentiska under domäner kan användas i phishing-kampanjer. Detta gäller för skadliga webbplatser och för MX-poster som gör det möjligt för hot aktör att ta emot e-post som är adresserade till en legitim under domän till ett säkert märke.

- **Ytterligare risker** – skadliga webbplatser kan användas för att eskalera till andra klassiska attacker som XSS, CSRF, CORS bypass och mer.



## <a name="identify-dangling-dns-entries"></a>Identifiera Dangling DNS-poster

Använd Microsofts GitHub PowerShell-verktyg ["Get-DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains)för att identifiera DNS-poster i din organisation som kan vara Dangling.

Med det här verktyget kan Azure-kunder lista alla domäner med en CNAME som är kopplad till en befintlig Azure-resurs som skapades på prenumerationer eller klienter.

Om dina CNAME finns i andra DNS-tjänster och pekar på Azure-resurser, anger du CNAME-filerna i en indatafil till verktyget.

Verktyget stöder de Azure-resurser som anges i följande tabell. Verktyget extraherar eller tar sig som indata, alla innehavarens CNAME.


| Tjänst                   | Typ                                        | FQDNproperty                               | Exempel                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | Microsoft. Network/frontdoors                | egenskaper. cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | Microsoft. Storage/storageaccounts           | Properties. blobar. blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | Microsoft. CDN/profiler/slut punkter            | egenskaper. hostName                        | `abc.azureedge.net`             |
| Offentliga IP-adresser       | Microsoft. Network/publicipaddresses         | Properties. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | Microsoft. Network/trafficmanagerprofiles    | Properties. dnsConfig. FQDN                  | `abc.trafficmanager.net`        |
| Azure Container-instans  | Microsoft. containerinstance/containergroups | egenskaper. ipAddress. FQDN                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | Microsoft. API Management/Service             | Properties. hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Azure App Service         | Microsoft. Web/Sites                         | egenskaper. defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service-platser | Microsoft. Web/Sites/lotss                   | egenskaper. defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Förutsättningar

Kör frågan som en användare som har:

- minst åtkomst nivå till Azure-prenumerationer
- Läs åtkomst till Azure Resource Graph

Om du är global administratör för din organisations klient kan du höja ditt konto så att det har åtkomst till alla dina organisations prenumerationer med hjälp av vägledningen i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md).


> [!TIP]
> Azure Resource Graph har begränsnings-och växlings gränser som du bör tänka på om du har en stor Azure-miljö. 
> 
> [Lär dig mer om att arbeta med stora data uppsättningar för Azure-resurser](../../governance/resource-graph/concepts/work-with-data.md).
> 
> Verktyget använder prenumerations batching för att undvika dessa begränsningar.

### <a name="run-the-script"></a>Kör skriptet

Läs mer om PowerShell-skriptet **Get-DanglingDnsRecords.ps1** och ladda ned det från GitHub: https://aka.ms/Get-DanglingDnsRecords .

## <a name="remediate-dangling-dns-entries"></a>Åtgärda Dangling DNS-poster 

Granska dina DNS-zoner och identifiera CNAME-poster som är Dangling eller har tagits över. Om under domäner är Dangling eller har tagits bort tar du bort de utsatta under domänerna och minimerar riskerna med följande steg:

1. Från DNS-zonen tar du bort alla CNAME-poster som pekar på FQDN för resurser som inte längre har tillhandahållits.

1. Om du vill aktivera trafik som ska dirigeras till resurser i din kontroll, etablera ytterligare resurser med de FQDN: er som anges i CNAME-posterna i Dangling-underdomänerna.

1. Granska program koden för referenser till specifika under domäner och uppdatera eventuella felaktiga eller inaktuella under domän referenser.

1. Undersök om kompromisser har inträffat och vidta åtgärder enligt organisationens svars procedurer för incidenter. Tips och metod tips för att undersöka det här problemet finns nedan.

    Om din program logik är sådan att hemligheter, till exempel OAuth-autentiseringsuppgifter, har skickats till under domänen Dangling eller om sekretess känslig information har skickats till Dangling-underdomänerna, kan dessa data ha exponerats för tredje part.

1. Ta reda på varför CNAME-posten inte togs bort från din DNS-zon när resursen avetablerats och vidta åtgärder för att se till att DNS-poster uppdateras korrekt när Azure-resurser avetableras i framtiden.


## <a name="prevent-dangling-dns-entries"></a>Förhindra Dangling DNS-poster

Att se till att din organisation har implementerat processer för att förhindra Dangling DNS-poster och den resulterande under domänens övertag Ande är en viktig del av ditt säkerhets program.

Vissa Azure-tjänster erbjuder funktioner som hjälper dig att skapa förebyggande åtgärder och beskrivs nedan. Andra metoder för att förhindra det här problemet måste upprättas genom organisationens bästa praxis eller standard operativa procedurer.

### <a name="enable-azure-defender-for-app-service"></a>Aktivera Azure Defender för App Service

I Azure Security Center s integrerade Cloud Worker Protection Platform (CWPP), Azure Defender erbjuder vi ett antal planer för att skydda dina resurser och arbets belastningar i Azure, hybrid och flera moln.

**Azure Defender for App Service** -planen innehåller Dangling DNS-identifiering. När den här planen är aktive rad får du säkerhets aviseringar om du inaktiverar en App Service webbplats men inte tar bort den anpassade domänen från din DNS-registrator.

Azure Defenders Dangling DNS-skydd är tillgängligt oavsett om dina domäner hanteras med Azure DNS eller en extern domän registrator och gäller för App Service på både Windows och Linux.

Lär dig mer om det här och andra fördelar med den här Azure Defender-planen i [Introduktion till Azure Defender för App Service](../../security-center/defender-for-app-service-introduction.md).

### <a name="use-azure-dns-alias-records"></a>Använd Azure DNS Ali Aset poster

Azure DNSs [Ali Asets](../../dns/dns-alias.md#scenarios) kan förhindra Dangling-referenser genom att koppla livs cykeln för en DNS-post med en Azure-resurs. Anta till exempel att du har en DNS-post som är kvalificerad som en aliasresurspost som pekar på en offentlig IP-adress eller en Traffic Manager-profil. Om du tar bort de underliggande resurserna blir DNS-Ali-posten en tom post uppsättning. Den borttagna resursen är inte längre referenser till den. Det är viktigt att Observera att det finns gränser för vad du kan skydda med Ali Aset. I dag är listan begränsad till:

- Azure Front Door
- Traffic Manager-profiler
- Slut punkter för Azure Content Delivery Network (CDN)
- Offentliga IP-adresser

Trots de begränsade tjänst erbjudandena idag rekommenderar vi att du använder Ali Asets för att skydda mot under domän övertag närhelst det är möjligt.

[Lär dig mer om funktionerna i Azure DNSs Ali Asets poster](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Använd Azure App Service anpassade domän verifieringen

När du skapar DNS-poster för Azure App Service skapar du en asuid. under domän TXT-post med domän verifierings-ID. Om det finns en sådan TXT-post kan ingen annan Azure-prenumeration verifiera den anpassade domänen som tar den över. 

Dessa poster hindrar inte någon från att skapa Azure App Service med samma namn som finns i din CNAME-post. Utan möjligheten att bevisa ägande av domän namnet kan inte hot aktörer ta emot trafik eller kontrol lera innehållet.

[Läs mer om hur du mappar ett befintligt anpassat DNS-namn till Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Bygg och automatisera processer för att minimera hotet

Det är ofta upp till utvecklare och drift team att köra rensnings processer för att undvika Dangling DNS-hot. Med hjälp av metoderna nedan kan du se till att din organisation inte har drabbats av det här hotet. 

- **Skapa procedurer för förebyggande åtgärder:**

    - Lär dina programutvecklare att omdirigera adresser när de tar bort resurser.

    - Lägg till "ta bort DNS-post" i listan över nödvändiga kontroller vid inaktive ring av en tjänst.

    - Lägg till [borttagnings lås](../../azure-resource-manager/management/lock-resources.md) på alla resurser som har en anpassad DNS-post. Ett borttagnings lås fungerar som en indikator att mappningen måste tas bort innan resursen avetableras. Mått som detta kan endast fungera när det kombineras med interna utbildnings program.

- **Skapa procedurer för identifiering:**

    - Granska dina DNS-poster regelbundet för att säkerställa att dina under domäner är mappade till Azure-resurser som:

        - Exist – fråga dina DNS-zoner efter resurser som pekar på Azure-underdomäner som *. azurewebsites.net eller *. cloudapp.azure.com (se [referens listan för Azure-domäner](azure-domains.md)).
        - Du äger – bekräfta att du äger alla resurser som dina DNS-under domäner är riktade till.

    - Underhålla en tjänst katalog för Azures fullständiga kvalificerade domän namn (FQDN) och program ägare. Skapa tjänst katalogen genom att köra följande skript i Azure Resource Graph. Det här skriptet Projects innehåller FQDN-slutpunktens information om de resurser som du har åtkomst till och matar ut dem i en CSV-fil. Om du har åtkomst till alla prenumerationer för din klient, tar skriptet hänsyn till alla prenumerationer som visas i följande exempel skript. Om du vill begränsa resultatet till en speciell uppsättning prenumerationer redigerar du skriptet som det visas.


- **Skapa procedurer för reparation:**
    - När Dangling DNS-poster hittas måste ditt team undersöka om kompromissen har inträffat.
    - Undersök varför adressen inte omdirigerades när resursen avbröts.
    - Ta bort DNS-posten om den inte längre används eller peka på rätt Azure-resurs (FQDN) som ägs av din organisation.
 

## <a name="next-steps"></a>Nästa steg

Mer information om relaterade tjänster och Azure-funktioner som du kan använda för att skydda dig mot under domän övertag ande finns på följande sidor.

- [Aktivera Azure Defender för App Service](../../security-center/defender-for-app-service-introduction.md) för att ta emot aviseringar när Dangling DNS-poster identifieras

- [Förhindra Dangling DNS-poster med Azure DNS](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Använd ett domän verifierings-ID när du lägger till anpassade domäner i Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Snabb start: kör din första resurs diagram fråga med hjälp av Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)
