---
title: SaaS för SQL-app
description: Den här artikeln indexerar olika tidpunkter i 81 minuter-videon om SaaS DB-appens design, från antändnings konferensen den 11 oktober 2017. Du kan gå vidare till den del som intresserar dig. Minst tre mönster beskrivs. Azure-funktioner som fören klar utveckling och hantering beskrivs.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.custom: sqldbrb=1
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 8cceeef7e41e8fc5c48b4e67cdb1adf3aa4f02a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89483872"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video Indexed och kommenterad för SaaS-appen för flera innehavare med hjälp av Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln är ett kommenterat index i tids platserna för en 81-minuters video om SaaS-modeller eller mönster. I den här artikeln kan du hoppa över bakåt eller framåt i videon som delar. Videon förklarar de viktigaste design alternativen för ett databas program med flera innehavare på Azure SQL Database. Videon innehåller demonstrationer, genom gångar av hanterings kod och vid fler detaljer som informeras om erfarenhet än kan vara i vår skriftliga dokumentation.

Videon förstärkar informationen i vår skriftliga dokumentation, som finns på: 
- *Koncept:* [SaaS-databas för flera klient organisationer][saas-concept-design-patterns-563e]
- *Självstudier:* [Wingtip ticks SaaS-programmet][saas-how-welcome-wingtip-app-679t]

I videon och artiklarna beskrivs de olika faserna i att skapa ett program med flera innehavare på Azure SQL Database i molnet. Särskilda funktioner i Azure SQL Database gör det enklare att utveckla och implementera appar för flera klient organisationer som är både enklare att hantera och genomföra.

Vi uppdaterar regelbundet vår skriftliga dokumentation. Videon redige ras eller uppdateras inte, så så småningom kan mer information bli inaktuell.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekvens med 38 tids indexerade skärm bilder

I det här avsnittet indexeras tids platsen för 38-diskussioner i videon om 81 minuter. Varje tids index kommenteras med en skärm bild från videon och ibland med ytterligare information.

Varje tids index har formatet *h:mm: SS*. Till exempel, den andra indexerade tids platsen, som är märkt med **målenhet**, startar på den ungefärliga tids platsen för **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Komprimera länkar till platser för video index tid

Följande titlar är länkar till motsvarande kommenterade avsnitt längre fram i den här artikeln:

- [1. **(start)** välkomst bild, 0:00:03](#anchor-image-wtip-min00001)
- [2. mål för sessionen, 0:03:11](#anchor-image-wtip-min00311)
- [3. agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. webbapp för flera innehavare, 0:05:05](#anchor-image-wtip-min00505)
- [5. app Web form i åtgärd, 0:05:55](#anchor-image-wtip-min00555)
- [6. per klient kostnad (skala, isolering, återställning), 0:09:31](#anchor-image-wtip-min00931)
- [7. databas modeller för flera klient organisationer:-och nack delar, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybrid modellen blandar fördelarna med MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. en-klient eller flera klienter:-och nack delar, 0:16:44](#anchor-image-wtip-min01644)
- [10. pooler är kostnads effektiva för oförutsedda arbets belastningar, 0:19:36](#anchor-image-wtip-min01936)
- [11. demo av Database-per-klient och hybrid ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live app-formulär som visar dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB och inte en DBA i syn, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB elastisk pool, exempel, 0:29:40](#anchor-image-wtip-min02940)
- [15. inlärning från MYOB och andra ISV: er, 0:31:36](#anchor-image-wtip-min03136)
- [16. mönster skapar E2E SaaS-scenario, 0:43:15](#anchor-image-wtip-min04315)
- [17. kanonisk hybrid SaaS-app för flera innehavare, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS exempel app, 0:48:10](#anchor-image-wtip-min04810)
- [19. scenarier och mönster som utforskas i självstudierna, 0:49:10](#anchor-image-wtip-min04910)
- [20. demo av självstudier och GitHub-lagringsplats 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub lagrings platsen Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. utforska mönstren, 0:56:20](#anchor-image-wtip-min05620)
- [23. etablering av klienter och onboarding, 0:57:44](#anchor-image-wtip-min05744)
- [24. etablering av klienter och program anslutning, 0:58:58](#anchor-image-wtip-min05858)
- [25. demonstration av hanterings skript för etablering av en enskild klient, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell för att etablera och katalogisera 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. hantera oväntade arbets belastningar för klienter, 1:04:36](#anchor-image-wtip-min10436)
- [29. övervakning av elastiska pooler, 1:06:39](#anchor-image-wtip-min10639)
- [30. läsa in generations-och prestanda övervakning, 1:09:42](#anchor-image-wtip-min10942)
- [31. schema hantering i skala, 1:10:33](#anchor-image-wtip-min11033)
- [32. distribuerad fråga över klient databaser, 1:12:21](#anchor-image-wtip-min11221)
- [33. demo av generering av biljett, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc-analys, 1:12:46](#anchor-image-wtip-min11246)
- [35. extrahera klient data till Azure Synapse Analytics, 1:16:32](#anchor-image-wtip-min11632)
- [36. diagram över dagliga försäljnings distribution, 1:16:48](#anchor-image-wtip-min11648)
- [37. Bryt upp och anropa åtgärden, 1:19:52](#anchor-image-wtip-min11952)
- [38. resurser för mer information, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Platser för kommenterad index tid i videon

Om du klickar på en skärm bild tas du till den exakta tids platsen i videon.


&nbsp; <a name="anchor-image-wtip-min00001"></a>
#### <a name="1-start-welcome-slide-00001"></a>1. *(start)* välkomst bild, 0:00:01

*Lära från MYOB: design mönster för SaaS-program på Azure SQL Database-BRK3120*

[![Välkomst bild][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Rubrik: inlärning från MYOB: design mönster för SaaS-program på Azure SQL Database
- Bill.Gibson@microsoft.com
- Huvud program hanterare, Azure SQL Database
- Microsoft antändning session BRK3120, Orlando, FL USA, oktober/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"></a>
#### <a name="2-session-objectives-00153"></a>2. mål för sessionen, 0:01:53
[![Mål för sessioner][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternativa modeller för appar för flera klienter, med-och nack delar.
- SaaS mönster för att minska kostnaderna för utveckling, hantering och resurser.
- Ett exempel på App +-skript.
- PaaS-funktioner och SaaS-mönster gör SQL Database en mycket skalbar och kostnads effektiv data plattform för SaaS i flera innehavare.


&nbsp; <a name="anchor-image-wtip-min00417"></a>
#### <a name="3-agenda-00409"></a>3. agenda, 0:04:09
[![Ordningen][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"></a>
#### <a name="4-multi-tenant-web-app-00500"></a>4. webbapp för flera innehavare, 0:05:00
[![Wingtip SaaS-app: webb program för flera innehavare][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"></a>
#### <a name="5-app-web-form-in-action-00539"></a>5. app Web form i åtgärd, 0:05:39
[![Webb formulär för app i praktiken][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"></a>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. per klient kostnad (skala, isolering, återställning), 0:06:58
[![Kostnad per klient, skala, isolering, återställning][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"></a>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. databas modeller för flera klient organisationer:-och nack delar, 0:09:52
[![Databas modeller för flera klient organisationer:-och nack delar][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"></a>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybrid modellen blandar fördelarna med MT/ST, 0:12:29
[![Hybrid modell överlappar fördelarna med MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"></a>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. en-klient eller flera klienter:-och nack delar, 0:13:11
[![En-klient eller flera klient organisationer:-och nack delar][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"></a>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. pooler är kostnads effektiva för oförutsedda arbets belastningar, 0:17:49
[![Pooler är kostnads effektiva för oförutsedda arbets belastningar][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"></a>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. demo av Database-per-klient och hybrid ST/MT, 0:19:59
[![Demo av databas per klient och hybrid ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"></a>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live app-formulär som visar dojo, 0:20:10
[![Live app-formuläret visar Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"></a>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB och inte en DBA i syn, 0:25:06
[![MYOB och inte en DBA i syn][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"></a>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB elastisk pool, exempel, 0:29:30
[![Exempel på MYOB elastisk pool][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"></a>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. inlärning från MYOB och andra ISV: er, 0:31:25
[![Lära sig från MYOB och andra ISV: er][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"></a>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. mönster skapar E2E SaaS-scenario, 0:31:42
[![Mönster i E2E SaaS-scenario][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"></a>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. kanonisk hybrid SaaS-app för flera innehavare, 0:46:04
[![Kanonisk hybrid SaaS-app för flera innehavare][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"></a>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS exempel app, 0:48:01
[![Exempel program för Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"></a>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. scenarier och mönster som utforskas i självstudierna, 0:49:00
[![Scenarier och mönster som utforskas i självstudierna][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"></a>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. demo av självstudier och GitHub-lagringsplats 0:50:12
[![Demo kurser och GitHub lagrings platsen][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"></a>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub lagrings platsen Microsoft/WingtipSaaS, 0:50:32
[![GitHub lagrings platsen Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"></a>
#### <a name="22-exploring-the-patterns-05615"></a>22. utforska mönstren, 0:56:15
[![Utforska mönstren][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"></a>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. etablering av klienter och onboarding, 0:56:19
[![Etablering av klienter och onboarding][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"></a>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. etablering av klienter och program anslutning, 0:57:52
[![Etablering av klienter och program anslutning][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"></a>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. demonstration av hanterings skript för etablering av en enskild klient, 0:59:36
[![Demo av hanterings skript för etablering av en enskild klient][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"></a>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell för att etablera och katalogisera 0:59:56
[![PowerShell för att etablera och katalogisera][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"></a>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"></a>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. hantera oväntade arbets belastningar för klienter, 1:03:34
[![Hantera oförutsägbara klient arbets belastningar][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"></a>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. övervakning av elastiska pooler, 1:06:32
[![Övervakning av elastisk pool][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"></a>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. läsa in generations-och prestanda övervakning, 1:09:37
[![Läsa in generering och prestanda övervakning][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"></a>
#### <a name="31-schema-management-at-scale-10940"></a>31. schema hantering i skala, 1:09:40
[![Schema hantering i skala][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"></a>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. distribuerad fråga över klient databaser, 1:11:18
[![Distribuerad fråga över klient databaser][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"></a>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. demo av generering av biljett, 1:12:28
[![Demo av skapande av biljett][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"></a>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc-analys, 1:12:35
[![SSMS adhoc-analys][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"></a>
#### <a name="35-extract-tenant-data-into-azure-synapse-analytics-11546"></a>35. extrahera klient data till Azure Synapse Analytics, 1:15:46
[![Extrahera klient data till Azure Synapse Analytics][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"></a>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. diagram över dagliga försäljnings distribution, 1:16:38
[![Diagram över dagliga Sälj distributioner][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"></a>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Bryt upp och anropa åtgärden, 1:17:43
[![Bryt upp och anropa åtgärd][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"></a>
#### <a name="38-resources-for-more-information-12035"></a>38. resurser för mer information, 1:20:35
[![Resurser för mer information][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogg inlägg, den 22 maj 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncept:* [SaaS-databas för flera klient organisationer][saas-concept-design-patterns-563e]

- *Självstudier:* [Wingtip ticks SaaS-programmet][saas-how-welcome-wingtip-app-679t]

- GitHub-lagringsplatser för varianter av Wingtip-biljetter SaaS:
    - [GitHub lagrings platsen för-fristående program modell][github-wingtip-standaloneapp].
    - [GitHub lagrings platsen för-DB per klient modell][github-wingtip-dbpertenant].
    - [GitHub lagrings platsen för-multi-TENANT DB-modell][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Nästa steg

- [Artikel om första självstudien][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Välkomst bild"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Mål för sessioner."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Ordningen."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS-app: webb program för flera innehavare"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Webb formulär för app i praktiken"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Kostnad per klient, skala, isolering, återställning"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databas modeller för flera klient organisationer:-och nack delar"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybrid modell överlappar fördelarna med MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "En-klient eller flera klient organisationer:-och nack delar"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pooler är kostnads effektiva för oförutsedda arbets belastningar"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demo av databas per klient och hybrid ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Live app-formuläret visar Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB och inte en DBA i syn"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Exempel på MYOB elastisk pool"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Lära sig från MYOB och andra ISV: er"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Mönster i E2E SaaS-scenario"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kanonisk hybrid SaaS-app för flera innehavare"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Exempel program för Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenarier och mönster som utforskas i självstudierna"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demo av självstudier och GitHub lagrings platsen"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub lagrings platsen Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Utforska mönstren"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Etablering av klienter och onboarding"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Etablering av klienter och program anslutning"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demo av hanterings skript för etablering av en enskild klient"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell för att etablera och katalogisera"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Hantera oförutsägbara klient arbets belastningar"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Övervakning av elastisk pool"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Läsa in generering och prestanda övervakning"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Schema hantering i skala"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Distribuerad fråga över klient databaser"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo av skapande av biljett"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS adhoc-analys"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrahera klient data till Azure Synapse Analytics"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Diagram över dagliga Sälj distributioner"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Bryt upp och anropa åtgärd"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Resurser för mer information"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

