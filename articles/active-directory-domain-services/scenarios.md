---
title: Vanliga distributions scenarier för Azure AD Domain Services | Microsoft Docs
description: Lär dig mer om några vanliga scenarier och användnings fall för Azure Active Directory Domain Services att tillhandahålla värde och uppfylla affärs behov.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619036"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Vanliga användnings fall och scenarier för Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP (Lightweight Directory Access Protocol) och Kerberos/NTLM-autentisering. Azure AD DS integreras med din befintliga Azure AD-klient, vilket gör det möjligt för användarna att logga in med sina befintliga autentiseringsuppgifter. Du kan använda dessa domän tjänster utan att behöva distribuera, hantera och korrigera domänkontrollanter i molnet, vilket ger en smidigare ökning och växling av lokala resurser till Azure.

Den här artikeln beskriver några vanliga affärs scenarier där Azure AD DS tillhandahåller värde och uppfyller dessa krav.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Vanliga sätt att tillhandahålla identitets lösningar i molnet

När du migrerar befintliga arbets belastningar till molnet kan katalog medveten program använda LDAP för Läs-eller skriv åtkomst till en lokal AD DS-katalog. Program som körs på Windows Server distribueras vanligt vis på domänanslutna virtuella datorer (VM) så att de kan hanteras på ett säkert sätt med hjälp av grupprincip. För att användarna ska kunna autentisera slutanvändare kan programmen också vara beroende av Windows-integrerad autentisering, t. ex. Kerberos eller NTLM-autentisering.

IT-administratörer använder ofta en av följande lösningar för att tillhandahålla en identitets tjänst till program som körs i Azure:

* Konfigurera en plats-till-plats-VPN-anslutning mellan arbets belastningar som körs i Azure och en lokal AD DS-miljö.
    * De lokala domän kontrol Lanterna tillhandahåller sedan autentisering via VPN-anslutningen.
* Skapa replik-domänkontrollanter med Azure Virtual Machines (VM) för att utöka AD DS-domänen/-skogen från en lokal plats.
    * Domän kontrol Lanterna som körs på virtuella Azure-datorer tillhandahåller autentisering och replikerar katalog information mellan den lokala AD DS-miljön.
* Distribuera en fristående AD DS-miljö i Azure med hjälp av domänkontrollanter som körs på virtuella Azure-datorer.
    * Domän kontrol Lanterna som körs på virtuella Azure-datorer tillhandahåller autentisering, men det finns ingen katalog information som replikeras från en lokal AD DS-miljö.

Med dessa metoder kan VPN-anslutningar till den lokala katalogen göra program sårbara för tillfälliga nätverks fel eller avbrott. Om du distribuerar domänkontrollanter med virtuella datorer i Azure måste IT-teamet hantera de virtuella datorerna, sedan säkra, korrigera, övervaka, säkerhetskopiera och felsöka.

Azure AD DS innehåller alternativ för behovet av att skapa VPN-anslutningar tillbaka till en lokal AD DS-miljö eller köra och hantera virtuella datorer i Azure för att tillhandahålla identitets tjänster. Som en hanterad tjänst minskar Azure AD DS komplexiteten för att skapa en integrerad identitets lösning för både hybrid miljöer och moln miljöer.

> [!div class="nextstepaction"]
> [Jämför Azure AD DS med Azure AD och själv hanterad AD DS på virtuella Azure-datorer eller lokalt][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS för Hybrid organisationer

Många organisationer kör en hybrid infrastruktur som omfattar både moln-och lokala program arbets belastningar. Äldre program som migrerats till Azure som en del av en hiss och Shift-strategi kan använda traditionella LDAP-anslutningar för att tillhandahålla identitets information. För att stödja den här hybrid infrastrukturen kan identitets information från en lokal AD DS-miljö synkroniseras med en Azure AD-klient. Azure AD DS tillhandahåller sedan dessa äldre program i Azure med en identitets källa, utan att du behöver konfigurera och hantera program anslutningen tillbaka till lokala katalog tjänster.

Nu ska vi titta på ett exempel för Litware ' Corporation, en hybrid organisation som kör både lokala och Azure-resurser:

![Azure Active Directory Domain Services för en hybrid organisation som innehåller lokal synkronisering](./media/overview/synced-tenant.png)

* Program-och Server arbets belastningar som kräver domän tjänster distribueras i ett virtuellt nätverk i Azure.
    * Detta kan inkludera äldre program som migrerats till Azure som en del av en hiss-och Shift-strategi.
* För att synkronisera identitets information från sin lokala katalog till sin Azure AD-klient distribuerar Litware ' Corporation [Azure AD Connect][azure-ad-connect].
    * Identitets information som synkroniseras innehåller användar konton och grupp medlemskap.
* Litware ' IT-teamet möjliggör Azure AD DS för sin Azure AD-klient i detta eller ett peer-kopplat virtuellt nätverk.
* Program och virtuella datorer som distribueras i det virtuella Azure-nätverket kan sedan använda Azure AD DS-funktioner som domän anslutning, LDAP Read, LDAP-bindning, NTLM-och Kerberos-autentisering och grupprincip.

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det finns inte stöd för att installera Azure AD Connect i en hanterad domän för att synkronisera objekt tillbaka till Azure AD.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS för endast moln organisationer

En molnbaserad Azure AD-klient har ingen lokal identitets källa. Användar konton och grupp medlemskap skapas till exempel och hanteras direkt i Azure AD.

Nu ska vi titta på ett exempel för contoso, en molnbaserad organisation som använder Azure AD för identitet. Alla användar identiteter, deras autentiseringsuppgifter och grupp medlemskap skapas och hanteras i Azure AD. Det finns ingen ytterligare konfiguration av Azure AD Connect för att synkronisera identitets information från en lokal katalog.

![Azure Active Directory Domain Services för en organisation som endast är i molnet utan lokal synkronisering](./media/overview/cloud-only-tenant.png)

* Program-och Server arbets belastningar som kräver domän tjänster distribueras i ett virtuellt nätverk i Azure.
* Contosos IT-team möjliggör Azure AD DS för sin Azure AD-klient i detta eller ett peer-kopplat virtuellt nätverk.
* Program och virtuella datorer som distribueras i det virtuella Azure-nätverket kan sedan använda Azure AD DS-funktioner som domän anslutning, LDAP Read, LDAP-bindning, NTLM-och Kerberos-autentisering och grupprincip.

## <a name="secure-administration-of-azure-virtual-machines"></a>Säker administration av virtuella Azure-datorer

För att du ska kunna använda en enda uppsättning AD-autentiseringsuppgifter kan virtuella Azure-datorer (VM) anslutas till en hanterad Azure AD DS-domän. Den här metoden minskar problem med hantering av autentiseringsuppgifter, till exempel underhåll av lokala administratörs konton på varje virtuell dator eller separata konton och lösen ord mellan miljöer.

Virtuella datorer som är anslutna till en hanterad domän kan också administreras och skyddas med hjälp av grup princip. Nödvändiga säkerhets bas linjer kan tillämpas på virtuella datorer för att låsa dem i enlighet med företags säkerhets rikt linjerna. Du kan till exempel använda funktioner för hantering av grup princip för att begränsa vilka typer av program som kan startas på den virtuella datorn.

![Effektiv administration av virtuella Azure-datorer](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Nu ska vi titta på ett vanligt exempel scenario. När servrar och annan infrastruktur når sitt livs längd vill contoso flytta program som för närvarande finns lokalt i molnet. Deras aktuella IT-standardmandat som servrar som är värdar för företags program måste vara domänanslutna och hanterade med hjälp av grup princip.

Contosos IT-administratör föredrar domän anslutning till virtuella datorer som distribuerats i Azure för att förenkla administrationen eftersom användarna sedan kan logga in med sina företags uppgifter. När domänanslutna, kan virtuella datorer också konfigureras för att uppfylla de nödvändiga säkerhets bas linjerna med hjälp av grup princip objekt (GPO). Contoso föredrar inte att distribuera, övervaka och hantera sina egna domänkontrollanter i Azure.

Azure AD DS passar bra för det här användnings fallet. Med en hanterad domän kan du ansluta virtuella datorer till domän, använda en enda uppsättning autentiseringsuppgifter och tillämpa grup principer. Och eftersom det är en hanterad domän behöver du inte konfigurera och underhålla domän kontrol Lanterna själv.

### <a name="deployment-notes"></a>Distributions anteckningar

Följande distributions överväganden gäller för det här exemplet:

* Hanterade domäner använder en enda, platt organisationsenhet (OU) som standard. Alla domänanslutna virtuella datorer finns i en enda ORGANISATIONSENHET. Om du vill kan du skapa [anpassade organisationsenheter][custom-ou].
* Azure AD DS använder ett inbyggt grup princip objekt för behållare för användare och datorer. För ytterligare kontroll kan du [skapa anpassade grup princip objekt][create-gpo] och rikta dem mot anpassade organisationsenheter.
* Azure AD DS stöder det grundläggande AD-datorns objekt schema. Det går inte att utöka schemat för dator objekt.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Lyft och byta lokala program som använder autentisering med LDAP-bindning

Som ett exempel scenario har contoso ett lokalt program som har köpts från en ISV för många år sedan. Programmet är för närvarande i underhålls läge av ISV och vi begär ändringar i programmet är prohibitively dyra. Det här programmet har en webbaserad klient klient som samlar in användarautentiseringsuppgifter med ett webb formulär och sedan autentiserar användare genom att utföra en LDAP-bindning till den lokala AD DS-miljön.

![LDAP-bindning](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso vill migrera det här programmet till Azure. Programmet bör fortsätta att fungera som det är, utan några ändringar som behövs. Dessutom bör användare kunna autentisera med sina befintliga företags uppgifter och utan ytterligare utbildning. Den bör vara transparent för slutanvändare där programmet körs.

I det här scenariot låter Azure AD DS program utföra LDAP-bindningar som en del av autentiseringsprocessen. Äldre lokala program kan lyfta och byta till Azure och fortsätta att sömlöst autentisera användare utan någon förändring i konfigurationen eller användar upplevelsen.

### <a name="deployment-notes"></a>Distributions anteckningar

Följande distributions överväganden gäller för det här exemplet:

* Kontrol lera att programmet inte behöver ändra/skriva till katalogen. Det finns inte stöd för att skriva åtkomst till LDAP till en hanterad domän.
* Du kan inte ändra lösen ord direkt mot en hanterad domän. Slutanvändare kan ändra sina lösen ord antingen med hjälp av [funktionen för lösen ords ändring i Azure AD][sspr] eller mot den lokala katalogen. Dessa ändringar synkroniseras sedan automatiskt och är tillgängliga i den hanterade domänen.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Lyft och byta lokala program som använder LDAP Read för att få åtkomst till katalogen

Precis som i föregående exempel scenario antar vi att contoso har ett lokalt LOB-program (Line-of-Business) som utvecklades nästan för varje år sedan. Det här programmet är katalog medvetet och utformat för att använda LDAP för att läsa information/attribut om användare från AD DS. Programmet ändrar inte attribut eller skriver på annat sätt till katalogen.

Contoso vill migrera det här programmet till Azure och dra tillbaka den lokala maskin vara som är värd för det här programmet. Programmet kan inte skrivas om för att använda moderna katalog-API: er, till exempel REST-baserade Microsoft Graph-API. Ett alternativ för att lyfta och byta är att önska var programmet kan migreras för att köras i molnet, utan att ändra kod eller skriva om programmet.

För att hjälpa dig med det här scenariot låter Azure AD DS program utföra LDAP-läsningar mot den hanterade domänen för att hämta den attributinformation som krävs. Programmet behöver inte skrivas om, så en hiss-och-Shift i Azure låter användarna fortsätta att använda appen utan att göra en ändring i där den körs.

### <a name="deployment-notes"></a>Distributions anteckningar

Följande distributions överväganden gäller för det här exemplet:

* Kontrol lera att programmet inte behöver ändra/skriva till katalogen. Det finns inte stöd för att skriva åtkomst till LDAP till en hanterad domän.
* Kontrol lera att programmet inte behöver ett anpassat/utökat Active Directory schema. Schema utökningar stöds inte i Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrera en lokal tjänst eller ett daemon-program till Azure

Vissa program innehåller flera nivåer där en av nivåerna måste utföra autentiserade anrop till en backend-nivå, till exempel en databas. AD-tjänstekonton används ofta i dessa scenarier. När du lyfter och växlar program till Azure kan du med Azure AD DS fortsätta att använda tjänst konton på samma sätt. Du kan välja att använda samma tjänst konto som synkroniseras från din lokala katalog till Azure AD eller skapa en anpassad ORGANISATIONSENHET och sedan skapa ett separat tjänst konto i ORGANISATIONSENHETen. Med båda metoderna fortsätter program att fungera på samma sätt för att göra autentiserade anrop till andra nivåer och tjänster.

![Tjänst konto med WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

I det här exempel scenariot har contoso ett anpassat program för program varu valv som innehåller en front webb, en SQL Server och en server dels-FTP-server. Windows-integrerad autentisering med hjälp av tjänst konton autentiserar webb klient delen till FTP-servern. Webb klient delen är konfigurerad att köras som ett tjänst konto. Backend-servern är konfigurerad för att bevilja åtkomst från tjänst kontot för webb klient delen. Contoso vill inte distribuera och hantera sina egna virtuella domänkontrollanter i molnet för att flytta det här programmet till Azure.

I det här scenariot kan servrar som är värdar för webb klient delen, SQL Server och FTP-servern migreras till virtuella Azure-datorer och anslutas till en hanterad domän. De virtuella datorerna kan sedan använda samma tjänst konto i sina lokala kataloger för appens autentisering, som synkroniseras via Azure AD med hjälp av Azure AD Connect.

### <a name="deployment-notes"></a>Distributions anteckningar

Följande distributions överväganden gäller för det här exemplet:

* Kontrol lera att programmen använder ett användar namn och lösen ord för autentisering. Certifikat-eller smartkort-baserad autentisering stöds inte av Azure AD DS.
* Du kan inte ändra lösen ord direkt mot en hanterad domän. Slutanvändare kan ändra sina lösen ord antingen med hjälp av [funktionen för lösen ords ändring i Azure AD][sspr] eller mot den lokala katalogen. Dessa ändringar synkroniseras sedan automatiskt och är tillgängliga i den hanterade domänen.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server-distributioner för fjärr skrivbords tjänster i Azure

Du kan använda Azure AD DS för att tillhandahålla hanterade domän tjänster till fjärr skrivbords servrar som distribuerats i Azure.

Mer information om det här distributions scenariot finns i [så här integrerar du Azure AD Domain Services med din RDS-distribution][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Domänanslutna HDInsight-kluster

Du kan konfigurera ett Azure HDInsight-kluster som är anslutet till en hanterad domän med Apache Ranger aktiverat. Du kan skapa och använda Hive-principer via Apache Ranger och tillåta användare, till exempel data experter, att ansluta till Hive med hjälp av ODBC-baserade verktyg som Excel eller Tableau. Vi fortsätter att arbeta med att lägga till andra arbets belastningar, till exempel HBase, Spark och Storm för domänanslutna HDInsight.

Mer information om det här distributions scenariot finns i [så här konfigurerar du domänanslutna HDInsight-kluster][hdinsight]

## <a name="next-steps"></a>Nästa steg

Kom igång genom att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds