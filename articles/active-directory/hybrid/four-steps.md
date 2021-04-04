---
title: Fyra steg till en stark Identity Foundation – Azure AD
description: I det här avsnittet beskrivs fyra steg som hybrid identitet kunder kan ta för att skapa en stark identitets grund.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795f5ede382e561ee810e54e1f8897c5d806e8b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94412382"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Fyra steg till en stark identitets grund med Azure Active Directory

Hantering av åtkomst till appar och data kan inte längre förlita sig på traditionella nätverks säkerhets gränser, t. ex. perimeternätverk och brand väggar på grund av snabb flyttning av appar till molnet. Nu måste organisationer ha förtroende för sin identitets lösning för att kontrol lera vem och vad som har åtkomst till organisationens appar och data. Fler organisationer gör det möjligt för anställda att använda sina egna enheter för att arbeta och använda sina enheter från valfri plats de kan ansluta till Internet. Att se till att enheterna är kompatibla och att säkra har blivit viktiga överväganden i den identitets lösning som en organisation väljer att implementera. I dagens digitala arbets plats [är identitet det primära kontroll planet](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) för alla organisationer som flyttar till molnet.

Vid införandet av en Azure Active Directory (Azure AD) hybrid identitets lösning får organisationer till gång till Premium-funktioner som låser upp produktiviteten genom automatisering, delegering, självbetjäning och funktioner för enkel inloggning. Det gör det möjligt för dina anställda att få åtkomst till företagets resurser från var de än behöver göra sitt arbete samtidigt som IT-teamet kan styra den åtkomsten genom att se till att rätt personer har rätt åtkomst till rätt resurser för att upprätta säker produktivitet.

Den här check listan med bästa praxis hjälper dig att snabbt distribuera rekommenderade åtgärder för att bygga en *stark* identitets bas i din organisation:

* Anslut till appar enkelt
* Upprätta en identitet för varje användare automatiskt
* Ge dina användare säker säkerhet
* Operationalisera dina insikter

## <a name="step-1---connect-to-apps-easily"></a>Steg 1 – Anslut till appar enkelt

Genom att ansluta dina appar med Azure AD kan du förbättra produktiviteten och säkerheten för slutanvändare genom att aktivera enkel inloggning (SSO) och användar etablering. Genom att hantera dina appar på en enda plats, Azure AD, kan du minimera den administrativa belastningen och få en enda kontroll punkt för dina säkerhets-och efterlevnadsprinciper.

I det här avsnittet beskrivs alternativen för att hantera användar åtkomst till appar, Aktivera säker fjärråtkomst till interna appar och fördelarna med att migrera dina appar till Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Gör appar tillgängliga för dina användare sömlöst

Med Azure AD kan administratörer [lägga till program](../manage-apps/add-application-portal.md) i galleriet för företags program i [Azure Portal](https://portal.azure.com/). Genom att lägga till program i galleriet för företags program blir det enklare för dig att konfigurera program att använda Azure AD som identitets leverantör. Du kan också hantera användar åtkomst till programmet med principer för villkorlig åtkomst och konfigurera enkel inloggning (SSO) till program så att användarna inte behöver ange sina lösen ord flera gånger och automatiskt signeras till både lokala och molnbaserade program.

När program har lagts till i Azure AD-galleriet kan användarna se appar som har tilldelats dem och söka efter och begära andra appar efter behov. Azure AD tillhandahåller [flera metoder](../manage-apps/end-user-experiences.md) för användare att komma åt sina appar:

* Åtkomst panel/Mina appar
* Microsoft 365-appstartprogram
* Direkt inloggning till federerade appar
* Direkta inloggnings länkar

Mer information om användar åtkomst till appar finns i **steg 3 – ge användarna** i den här artikeln.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrera appar från Active Directory Federation Services (AD FS) till Azure AD

Migrering av enkel inloggnings konfiguration från Active Directory Federation Services (AD FS) (ADFS) till Azure AD ger ytterligare funktioner för säkerhet, en mer konsekvent hantering och samarbete. För bästa resultat rekommenderar vi att du migrerar dina appar från AD FS till Azure AD. Att samla in din programautentisering och auktorisering till Azure AD ger dig följande fördelar:

* Hantera kostnader
* Hanterings risk
* Ökad produktivitet
* Hantera efterlevnad och styrning

Mer information finns i [migrera dina program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper) whitepaper.

### <a name="enable-secure-remote-access-to-apps"></a>Aktivera säker fjärråtkomst till appar

[Azure AD-programproxy](../manage-apps/what-is-application-proxy.md) ger en enkel lösning för organisationer för att publicera lokala appar till molnet för fjärran vändare som behöver åtkomst till interna appar på ett säkert sätt. Efter en enkel inloggning till Azure AD kan användare komma åt både molnet och lokala program via externa URL: er eller en intern applikations Portal.

Azure AD-programproxy erbjuder följande fördelar:

* Utöka Azure AD till lokala resurser
  * Säkerhet och skydd i moln skala
  * Funktioner som villkorlig åtkomst och Multi-Factor Authentication som är lätta att aktivera
* Inga komponenter i perimeternätverket, till exempel VPN-och traditionella omvända proxy-lösningar
* Inga inkommande anslutningar krävs
* Enkel inloggning (SSO) över enheter, resurser och appar i molnet och lokalt
* Gör det möjligt för slutanvändarna att vara produktiva när och var som helst

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Upptäck skugg-IT med Microsoft Cloud App Security

I moderna företag är IT-avdelningar ofta inte medvetna om alla moln program som används av användarna för att utföra sitt arbete. När IT-administratörer tillfrågas om hur många molnappar som de tror att deras anställda använder, i genomsnitt blir det 30 eller 40. I verkligheten är genomsnittet över 1 000 separata appar som används av anställda i din organisation. 80% av anställda använder icke-sanktionerade appar som ingen har granskat och kanske inte är kompatibla med dina principer för säkerhet och efterlevnad.

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) kan hjälpa dig att identifiera användbara appar som är populära med användare som det kan sanktionera och lägga till i galleriet för företags program, så att användarna får till gång till funktioner som SSO och villkorlig åtkomst.

<em>"**Cloud App Security** hjälper oss att se till att våra personer på rätt sätt använder våra moln-och SaaS-program, på ett sätt som stöder de grundläggande säkerhets principerna som skyddar Accenture."</em> --- [John Blasi, hantering av regissör, informations säkerhet, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Förutom att upptäcka skugg-IT kan MCAS också fastställa risk nivån för appar, förhindra obehörig åtkomst till företags data, möjliga data läckage och andra säkerhets risker i programmen.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Steg 2 – upprätta en identitet för varje användare automatiskt

Genom att använda lokala och molnbaserade kataloger tillsammans i en Azure AD hybrid identitets lösning kan du återanvända din befintliga lokala Active Directory investering genom att tillhandahålla dina befintliga identiteter i molnet. Lösningen synkroniserar lokala identiteter med Azure AD, medan den lokala Active Directory körs med befintliga styrnings lösningar som den primära källan till sanningen för identiteter. Microsofts Azure AD hybrid Identity-lösning omfattar lokala och molnbaserade funktioner, vilket skapar en gemensam användar identitet för autentisering och auktorisering till alla resurser, oavsett var de befinner sig.

Att integrera dina lokala kataloger med Azure AD gör användarna mer produktiva och förhindrar att användare använder flera konton i appar och tjänster genom att tillhandahålla en gemensam identitet för åtkomst till både moln resurser och lokala resurser. Att använda flera konton är en smärta för slutanvändare och det är lika. Från ett slut användar perspektiv, med flera konton, innebär det att du måste komma ihåg flera lösen ord. För att undvika detta kan många användare återanvända samma lösen ord för varje konto, vilket är felaktigt i ett säkerhets perspektiv. Från ett IT-perspektiv leder åter användning ofta till fler lösen ords återställningar och support kostnader tillsammans med slutanvändarens klagomål.

Azure AD Connect är det verktyg som används för att synkronisera dina lokala identiteter till Azure AD, som sedan kan användas för att få åtkomst till moln program. När identiteterna är i Azure AD kan de etablera till SaaS-program som Salesforce eller Concur.

I det här avsnittet listas rekommendationer för att tillhandahålla hög tillgänglighet, modern autentisering för molnet och för att minska ditt lokala utrymme.

> [!NOTE]
> Om du vill veta mer om Azure AD Connect, se [Vad är Azure AD Connect Sync?](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Konfigurera en uppsamlings Server för Azure AD Connect och håll den uppdaterad

Azure AD Connect spelar upp en nyckel roll i etablerings processen. Om synkroniseringstjänsten försätts offline av någon anledning, uppdateras inte ändringar i lokalt i molnet och orsakar åtkomst problem till användarna. Det är viktigt att definiera en strategi för växling vid fel som gör det möjligt för administratörer att snabbt återuppta synkronisering när Sync-servern är offline.

För att tillhandahålla hög tillgänglighet i händelse av att den primära Azure AD Connect servern är offline rekommenderar vi att du distribuerar en separat [mellanlagrings Server](./how-to-connect-sync-staging-server.md) för Azure AD Connect. Genom att distribuera en server kan administratören "befordra" mellanlagrings servern till produktion med en enkel konfigurations växel. Med en standby-server som kon figurer ATS i mellanlagrings läge kan du också testa och distribuera nya konfigurations ändringar och införa en ny server om du inaktiverar den gamla.

> [!TIP]
> Azure AD Connect uppdateras regelbundet. Därför rekommenderar vi starkt att du bevarar den här mellanlagrings servern för att kunna dra nytta av prestanda förbättringar, fel korrigeringar och nya funktioner som varje ny version ger.

### <a name="enable-cloud-authentication"></a>Aktivera molnbaserad autentisering

Organisationer med lokala Active Directory bör utöka sin katalog till Azure AD med Azure AD Connect och konfigurera lämplig autentiseringsmetod. Att [välja rätt autentiseringsmetod](./choose-ad-authn.md) för din organisation är det första steget i resan att flytta appar till molnet. Det är en viktig komponent eftersom den styr åtkomst till alla moln data och resurser.

Den enklaste och rekommenderade metoden för att aktivera molnbaserad autentisering för lokala katalog objekt i Azure AD är att aktivera [hash-synkronisering av lösen ord](./how-to-connect-password-hash-synchronization.md) (PHS). Vissa organisationer kan också överväga att aktivera [direktautentisering](./how-to-connect-pta-quick-start.md) (PTA).

Oavsett om du väljer PHS eller PTA kan du inte glömma att aktivera [sömlös enkel inloggning](./how-to-connect-sso.md) så att användarna kan komma åt molnappar utan att ständigt ange sitt användar namn och lösen ord i appen när de använder Windows 7-och 8-enheter i företags nätverket. Utan enkel inloggning måste användare komma ihåg programspecifika lösen ord och logga in på varje program. På samma sätt måste IT-personal skapa och uppdatera användar konton för varje program, till exempel Microsoft 365, ruta och Salesforce. Användarna måste komma ihåg sina lösen ord, samt ägna tid åt att logga in på varje program. Att tillhandahålla en standardiserad mekanism för enkel inloggning till hela företaget är avgörande för bästa användar upplevelse, minskning av risk, rapportering och styrning.

För organisationer som redan använder AD FS eller en annan lokal autentiseringsprovider kan du flytta till Azure AD som identitets leverantör kan minska komplexiteten och förbättra tillgängligheten. Om du inte har speciella användnings fall för att använda Federation rekommenderar vi att du migrerar från federerad autentisering till antingen PHS och sömlös SSO eller PTA och sömlös SSO för att dra nytta av fördelarna med ett reducerat lokalt utrymme och den flexibilitet som molnet erbjuder med förbättrade användar upplevelser. Mer information finns i [Migrera från Federation till hash-synkronisering av lösen ord för Azure Active Directory](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Aktivera automatisk avetablering av konton

Att aktivera automatisk etablering och avetablering för dina program är den bästa strategin för att reglera identiteternas livs längd i flera system. Azure AD stöder [automatiserad, principbaserad etablering och avetablering](../app-provisioning/configure-automatic-user-provisioning-portal.md) av användar konton till en rad populära SaaS-program, till exempel ServiceNow och Salesforce, och andra som implementerar [scim 2,0-protokollet](../app-provisioning/use-scim-to-provision-users-and-groups.md). Till skillnad från traditionella etablerings lösningar, som kräver anpassad kod eller manuell överföring av CSV-filer, finns etablerings tjänsten i molnet och innehåller för hands integrerade anslutningar som kan konfigureras och hanteras med hjälp av Azure Portal. En viktig fördel med automatisk avetablering är att den hjälper till att skydda din organisation genom att genast ta bort användarens identiteter från viktiga SaaS-appar när de lämnar organisationen.

Mer information om automatisk etablering av användar konton och hur det fungerar finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>Steg 3 – ge dina användare säker säkerhet

I dagens digitala arbets plats är det viktigt att balansera säkerheten med produktivitet. Slutanvändare skickar dock ofta tillbaka säkerhets åtgärder som saktar produktiviteten och kommer åt molnappar. För att hjälpa dig att åtgärda detta tillhandahåller Azure AD självbetjänings funktioner som gör det möjligt för användarna att vara produktiva och minimera administrativa kostnader.

I det här avsnittet listas rekommendationer för att ta bort friktion från din organisation genom att ge användarna möjlighet att använda återstående vigilant.

### <a name="enable-self-service-password-reset-for-all-users"></a>Aktivera Self-Service lösen ords återställning för alla användare

Azures självbetjäning för [återställning av lösen ord](../authentication/tutorial-enable-sspr.md) (SSPR) erbjuder ett enkelt sätt för IT-administratörer att tillåta att användare återställer och låser upp sina lösen ord eller konton utan Administratörs åtgärder. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk.

Som standard låser Azure AD upp konton när en lösen ords återställning utförs. Men när du aktiverar Azure AD Connect [-integrering lokalt](../authentication/concept-sspr-howitworks.md#on-premises-integration)kan du också välja att separera de två åtgärderna, vilket gör att användarna kan låsa upp kontot utan att behöva återställa lösen ordet.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Se till att alla användare är registrerade för MFA och SSPR

Azure ger rapporter som du och din organisation kan använda för att se till att användare registreras för MFA-och SSPR. Användare som inte har registrerat kan behöva bli sammanutbildade i processen.

Rapporten MFA [-inloggningar](../authentication/howto-mfa-reporting.md) innehåller information om MFA-användning och ger dig insikter om hur MFA arbetar i din organisation. Att ha till gång till inloggnings aktivitet (och granskningar och risk identifieringar) för Azure AD är avgörande för fel sökning, användnings analys och data utredning undersökningar.

På samma sätt kan du använda [rapporten för lösen ords hantering med självbetjäning](../authentication/howto-sspr-reporting.md) för att fastställa vem som har (eller inte har) registrerat för SSPR.

### <a name="self-service-app-management"></a>Hantering av självbetjänings program

Innan användarna kan identifiera program från sina åtkomst paneler måste du aktivera självbetjänings [program åtkomst](../manage-apps/access-panel-manage-self-service-access.md) till alla program som du vill ge användare möjlighet att själv identifiera och begära åtkomst till. Åtkomst till självbetjänings program är ett bra sätt att ge användare möjlighet att identifiera program och eventuellt tillåta affärs gruppen att godkänna åtkomst till dessa program. Du kan tillåta att affärs gruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare för [lösen ord Single-Sign på program](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app) direkt från sina åtkomst paneler.

### <a name="self-service-group-management"></a>Självbetjäning, grupphantering

Att tilldela användare till program mappas bäst när du använder grupper, eftersom de ger stor flexibilitet och möjlighet att hantera i skala:

* Attribut-baserad med dynamiskt grupp medlemskap
* Delegera till app-ägare

Azure AD ger möjlighet att hantera åtkomst till resurser med hjälp av säkerhets grupper och Microsoft 365 grupper. Dessa grupper kan hanteras av en grupp ägare som kan godkänna eller avvisa medlemskaps begär Anden och delegera kontrollen av grupp medlemskap. Den här funktionen är känd som självbetjänings [grupp hantering](../enterprise-users/groups-self-service-management.md)och sparar tid genom att tillåta grupp ägare som inte har tilldelats en administrativ roll för att skapa och hantera grupper utan att behöva förlita sig på att administratörer hanterar sina begär Anden.

## <a name="step-4---operationalize-your-insights"></a>Steg 4 – operationalisera dina insikter

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en effektiv strategi för att se till att användarna är produktiva och att organisationen är säker. Säkerhets loggar och-rapporter kan hjälpa till att besvara frågor som:

* Använder du det du betalar för?
* Finns det några misstänkta eller skadliga händer i min klient organisation?
* Vem har påverkat under en säkerhets incident?

Säkerhets loggar och-rapporter ger dig en elektronisk post med misstänkta aktiviteter och hjälper dig att identifiera mönster som kan indikera försök till eller lyckad extern inträngande av nätverket och interna attacker. Du kan använda granskning för att övervaka användar aktivitet, dokumentera regelefterlevnad, utföra kriminal tekniska analys med mera. Aviseringar ger meddelanden om säkerhets händelser.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Tilldela minst privilegierade administratörs roller för åtgärder

När du funderar på hur du använder åtgärder finns det ett par olika administrations nivåer att tänka på. På den första nivån placeras administrations belastningen på dina globala administratörer. Använd alltid rollen global administratör, kan vara lämplig för mindre företag. Men för större organisationer med support personal och administratörer som ansvarar för vissa uppgifter, kan det vara en säkerhets risk att tilldela rollen global administratör eftersom den ger användarna möjlighet att hantera uppgifter som är över och utöver vad de ska kunna utföra.

I detta fall bör du överväga nästa nivå av administration. Med hjälp av Azure AD kan du ange slutanvändare som "begränsade administratörer" som kan hantera uppgifter i roller med lägre privilegier. Du kan till exempel tilldela supportavdelningen rollen som [säkerhets läsare](../roles/permissions-reference.md#security-reader) för att ge dem möjlighet att hantera säkerhetsrelaterade funktioner med skrivskyddad åtkomst. Eller så kanske det är klokt att tilldela rollen som [Administratörs](../roles/permissions-reference.md#authentication-administrator) roll till enskilda användare för att ge dem möjlighet att återställa autentiseringsuppgifter som inte är lösen ord eller läsa och konfigurera Azure Service Health.

Mer information finns i [Administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Övervaka hybrid komponenter (Azure AD Connect Sync, AD FS) med Azure AD Connect Health

Azure AD Connect och AD FS är kritiska komponenter som potentiellt kan bryta livs cykel hantering och autentisering och leda till avbrott. Därför bör du Distribuera Azure AD Connect Health för övervakning och rapportering av dessa komponenter.

Om du vill veta mer går du till läsa [övervakare AD FS använder Azure AD Connect Health](./how-to-connect-health-adfs.md).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Använd Azure Monitor för att samla in data loggar för analys

[Azure Monitor](../../azure-monitor/overview.md) är en enhetlig övervaknings Portal för alla Azure AD-loggar, som ger djupgående insikter, avancerad analys och smart Machine Learning. Med Azure Monitor kan du använda mått och loggar i portalen och via API: er för att få bättre insyn i dina resursers tillstånd och prestanda. Det möjliggör ett enda fönster med glas upplevelse i portalen samtidigt som du kan aktivera en mängd produkt integreringar via API: er och data export alternativ som stöder traditionella SIEM-system från tredje part. Azure Monitor ger dig också möjlighet att konfigurera aviserings regler för att bli meddelad eller vidta automatiska åtgärder på problem som påverkar dina resurser.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Skapa anpassade instrument paneler för ledarskap och dag till dag

Organisationer som inte har någon SIEM-lösning kan hämta [Power BIs innehålls paketet](../reports-monitoring/howto-use-azure-monitor-workbooks.md) för Azure AD. Power BI innehålls paketet innehåller färdiga rapporter som hjälper dig att förstå hur dina användare använder och använder Azure AD-funktioner, vilket gör att du kan få insikter om alla aktiviteter i din katalog. Du kan också skapa en egen [anpassad instrument panel](/power-bi/service-dashboards) och dela med ditt ledarskaps team för att rapportera om dagliga aktiviteter. Instrument paneler är ett bra sätt att övervaka din verksamhet och se alla dina viktigaste mått på ett ögonblick. Visualiseringarna på en instrumentpanel kan komma från en underliggande datauppsättning eller flera, eller från en underliggande rapport eller flera. En instrumentpanel kombinerar lokala och molndata, vilket ger en samlad vy oavsett var dessa data finns.

![Power BI anpassad instrument panel](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Förstå dina support samtals driv rutiner

När du implementerar en hybrid identitets lösning som beskrivs i den här artikeln bör du i slut ändan minska dina support samtal. Vanliga problem, t. ex. glömt lösen ord och konto utelåsningar, begränsas genom implementering av återställning av lösen ord för självbetjäning i Azure, samtidigt som du aktiverar självbetjäning för program åtkomst kan användarna själv identifiera och begära åtkomst till program utan att behöva använda IT-personalen.

Om du inte ser en minskning av support samtalen rekommenderar vi att du analyserar dina support samtals driv rutiner i ett försök att bekräfta om SSPR eller självbetjänings program åtkomst har kon figurer ATS korrekt eller om det finns andra nya problem som kan åtgärdas systematiskt.

*"I vår process för digital omvandling krävde vi en tillförlitlig identitets-och åtkomst hanterings leverantör för att under lätta sömlöst säkrare integrering mellan USA, partners och moln tjänst leverantörer, för ett effektivt eko system. Azure AD var det bästa alternativet som erbjuder oss de funktioner och den synlighet som vi behövde för att upptäcka och reagera på risker. "* --- [Yazan Almasri, global information Security Director, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Övervaka användningen av appar för att driva insikter

Förutom att upptäcka skugg-IT kan du övervaka användningen av appar i din organisation med hjälp av [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) kan hjälpa din organisation när du flyttar för att dra full nytta av löftet med moln program. Det kan hjälpa dig att hålla kontrollen över dina till gångar genom förbättrad insyn i aktivitet och öka skyddet av kritiska data i moln program. Att övervaka användningen av appar i din organisation med hjälp av MCAS kan hjälpa dig att besvara följande frågor:

* Vilka ej sanktionerade appar är anställda som använder för att lagra data i?
* Var och när är känsliga data lagrade i molnet?
* Vem kommer åt känsliga data i molnet?

*"Med Cloud App Security kan vi snabbt upptäcka avvikelser och vidta åtgärder."* --- [Eric LePenske, Senior Manager, informations säkerhet, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Sammanfattning

Det finns många aspekter som krävs för att implementera en hybrid identitets lösning, men den här check listan i fyra steg hjälper dig att snabbt uppnå en identitets infrastruktur som gör det möjligt för användare att bli mer produktiva och säkra.

* Anslut till appar enkelt
* Upprätta en identitet för varje användare automatiskt
* Ge dina användare säker säkerhet
* Operationalisera dina insikter

Vi hoppas att det här dokumentet är en bra översikt över att upprätta en stark identitets grund för din organisation.

## <a name="identity-checklist"></a>Identitets check lista

Vi rekommenderar att du skriver ut följande check lista som referens när du påbörjar resan till en mer solid Identity Foundation i din organisation.

### <a name="today"></a>Today

|Klart?|Objekt|
|:-|:-|
||Lösen ords återställning via självbetjäning (SSPR) för en grupp|
||Övervaka hybrid komponenter med Azure AD Connect Health|
||Tilldela minst privilegierade administratörs roller för åtgärden|
||Upptäck skugg-IT med Microsoft Cloud App Security|
||Använd Azure Monitor för att samla in data loggar för analys|

### <a name="next-two-weeks"></a>Kommande två veckorna

|Klart?|Objekt|
|:-|:-|
||Gör en app tillgänglig för dina användare|
||Pilot Azure AD-etablering för en SaaS app som du väljer|
||Konfigurera en mellanlagrings Server för Azure AD Connect och håll den uppdaterad|
||Starta migrering av appar från ADFS till Azure AD|
||Skapa anpassade instrument paneler för ledarskap och dag till dag|

### <a name="next-month"></a>Nästa månad

|Klart?|Objekt|
|:-|:-|
||Övervaka användningen av appar för att driva insikter|
||Pilot säker fjärråtkomst till appar|
||Se till att alla användare är registrerade för MFA och SSPR|
||Aktivera molnbaserad autentisering|

### <a name="next-three-months"></a>Kommande tre månaderna

|Klart?|Objekt|
|:-|:-|
||Aktivera hantering av självbetjänings program|
||Aktivera grupp hantering via självbetjäning|
||Övervaka användningen av appar för att driva insikter|
||Förstå dina support samtals driv rutiner|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan öka din säkra position med funktionerna i Azure Active Directory och i den här check listan i fem steg – [fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md).

Lär dig hur identitets funktionerna i Azure AD kan hjälpa dig att påskynda över gången till moln styrnings hantering genom att tillhandahålla lösningar och funktioner som gör att organisationer snabbt kan använda och flytta mer av sin identitets hantering från traditionella lokala system till Azure AD – [hur Azure AD levererar hantering av moln styrning för lokala arbets belastningar](./cloud-governed-management-for-on-premises.md).