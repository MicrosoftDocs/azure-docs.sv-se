---
title: Allmänna rikt linjer för företags säkerhet i Azure HDInsight
description: Några metod tips som gör det enklare att Enterprise Security Package distribution och hantering.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933565"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Allmän information och rikt linjer för företags säkerhet i Azure HDInsight

När du distribuerar ett säkert HDInsight-kluster finns det några metod tips som gör distributionen och kluster hanteringen enklare. En del allmän information och rikt linjer beskrivs här.

## <a name="use-of-secure-cluster"></a>Användning av säkra kluster

### <a name="recommended"></a>Rekommenderas

* Klustret kommer att användas av flera användare samtidigt.
* Användare har olika åtkomst nivåer till samma data.

### <a name="not-necessary"></a>Inte nödvändigt

* Du kommer bara att köra automatiserade jobb (som ett enda användar konto), vilket är ett standard kluster tillräckligt.
* Du kan importera data med hjälp av ett standard kluster och använda samma lagrings konto i ett annat säkert kluster där användarna kan köra analys jobb.

## <a name="use-of-local-account"></a>Användning av lokalt konto

* Om du använder ett delat användar konto eller ett lokalt konto är det svårt att identifiera vem som använde kontot för att ändra konfigurationen eller tjänsten.
* Att använda lokala konton är problematiskt när användare inte längre är en del av organisationen.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Principer

* Som standard använder Ranger **neka** som princip.

* När data åtkomst görs via en tjänst där auktorisering har Aktiver ATS:
  * Plugin-programmet för Range-autentisering anropas och tilldelas kontexten för begäran.
  * Ranger tillämpar de principer som kon figurer ATS för tjänsten. Om Ranger-principerna inte fungerar uppskjuts åtkomst kontrollen till fil systemet. Vissa tjänster som MapReduce kontrollerar bara om filen/mappen ägs av samma användare som skickar begäran. Tjänster som Hive, kontrol lera antingen ägarskaps matchning eller lämpliga fil Systems behörigheter ( `rwx` ).

* För Hive, förutom att ha behörighet att skapa/uppdatera/ta bort, bör användaren ha `rwx` behörighet till katalogen på lagrings platsen och alla under kataloger.

* Principer kan tillämpas på grupper (hellre) i stället för individer.

* Range-auktoriseraren utvärderar alla Ranger-principer för tjänsten för varje begäran. Den här utvärderingen kan påverka hur lång tid det tar att acceptera jobbet eller frågan.

### <a name="storage-access"></a>Lagrings åtkomst

* Om lagrings typen är WASB är ingen OAuth-token inblandad.
* Om Ranger har utfört auktoriseringen sker lagrings åtkomsten med hjälp av den hanterade identiteten.
* Om Ranger inte utförde någon auktorisering sker lagrings åtkomsten med hjälp av användarens OAuth-token.

### <a name="hierarchical-name-space"></a>Hierarkiskt namn område

När hierarkiskt namn område inte är aktiverat:

* Det finns inga ärvda behörigheter.
* Endast fil Systems behörighet som fungerar är **lagrings data xxxx** Azure Role som ska tilldelas användaren direkt i Azure Portal.

### <a name="default-hdfs-permissions"></a>Standard HDFS-behörigheter

* Som standard har användarna inte åtkomst till **/** mappen på HDFS (de måste finnas i rollen Storage BLOB-ägare för att åtkomsten ska lyckas).
* För uppsamlings katalogen för MapReduce och andra skapas en användarspecifik katalog som har `sticky _wx` behörigheter. Användare kan skapa filer och mappar under, men kan inte titta på andra objekt.

### <a name="url-auth"></a>URL-autentisering

Om URL-autentisering är aktiverat:

* Config kommer att innehålla vilka prefix som beskrivs i URL-autentiseringen (t `adl://` . ex.).
* Om åtkomsten är för denna URL, kontrollerar Ranger om användaren finns i listan över tillåtna.
* Ranger kontrollerar inte några detaljerade principer.

## <a name="resource-groups"></a>Resursgrupper

Använd en ny resurs grupp för varje kluster så att du kan skilja mellan kluster resurser.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG: er, brand väggar och intern Gateway

* Använd Nätverks säkerhets grupper (NSG: er) för att låsa virtuella nätverk.
* Använd brand väggen för att hantera principer för utgående åtkomst.
* Använd den interna gatewayen som inte är öppen för det offentliga Internet.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst.

### <a name="policies"></a>Principer

* Inaktivera principen för villkorlig åtkomst med hjälp av den IP-baserade principen. Detta kräver att tjänstens slut punkter aktive ras på den virtuella nätverk där klustren distribueras. Om du använder en extern tjänst för MFA (något annat än AAD) fungerar inte den IP-baserade principen

* `AllowCloudPasswordValidation` princip krävs för federerade användare. Eftersom HDInsight använder användar namn/lösen ord direkt för att hämta token från Azure AD måste den här principen aktive ras för alla federerade användare.

* Aktivera tjänst slut punkter om du behöver villkorlig åtkomst kringgå med betrodda IP-adresser.

### <a name="groups"></a>Grupper

* Distribuera alltid kluster med en grupp.
* Använd Azure AD för att hantera grupp medlemskap (enklare än att försöka hantera enskilda tjänster i klustret).

### <a name="user-accounts"></a>Användarkonton

* Använd ett unikt användar konto för varje scenario. Använd till exempel ett konto för import, Använd ett annat för fråga eller andra bearbetnings jobb.
* Använd gruppbaserade Ranger-principer i stället för enskilda principer.
* Ha en plan för hur du hanterar användare som inte ska ha åtkomst till kluster längre.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP (Lightweight Directory Access Protocol) och KERBEROS/NTLM-autentisering som är helt kompatibel med Windows Server Active Directory.

Azure AD DS krävs för att säkra kluster ska kunna ansluta till en domän.
HDInsight kan inte vara beroende av lokala domänkontrollanter eller anpassade domänkontrollanter, eftersom det introducerar för många fel punkter, delning av autentiseringsuppgifter, DNS-behörigheter och så vidare. Mer information finns i [vanliga frågor och svar om Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Azure AD DS-instans

* Skapa instansen med `.onmicrosoft.com domain` . På så sätt kommer det inte att finnas flera DNS-servrar som betjänar domänen.
* Skapa ett självsignerat certifikat för LDAPs och överför det till Azure AD DS.
* Använd ett peer-kopplat virtuellt nätverk för att distribuera kluster (om du har ett antal team som distribuerar HDInsight ESP-kluster, är detta användbart). Detta säkerställer att du inte behöver öppna portar (NSG: er) på det virtuella nätverket med domänkontrollanten.
* Konfigurera DNS för det virtuella nätverket korrekt (namnet på Azure AD DS-domänen bör matcha utan några värd fil poster).
* Om du begränsar utgående trafik ser du till att du har läst igenom [brand Väggs stödet i HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Egenskaper som har synkroniserats från Azure AD till Azure AD DS

* Azure AD Connect-synkroniseringar från lokala platser till Azure AD.
* Azure AD DS synkroniseras från Azure AD.

Azure AD DS synkroniserar objekt från Azure AD med jämna mellanrum. Azure AD DS-bladet på Azure Portal visar synkroniseringsstatus. Under varje stadium i synkroniseringen kan unika egenskaper komma in i konflikt och byta namn. Beakta egenskaps mappningen från Azure AD till Azure AD DS.

Mer information finns i [Azure AD userPrincipalName-ifyllning](../../active-directory/hybrid/plan-connect-userprincipalname.md)och [hur Azure AD DS-synkronisering fungerar](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Hash-synkronisering av lösen ord

* Lösen orden synkroniseras på olika sätt från andra objekt typer. Endast lösen ords-hashar som inte går att ångra synkroniseras i Azure AD och Azure AD DS
* Lokalt till Azure AD måste aktive ras via AD Connect
* Azure AD till Azure AD DS Sync är automatiskt (fördröjningar under 20 minuter).
* Lösenords-hashvärden synkroniseras bara när det finns ett ändrat lösen ord. När du aktiverar hash-synkronisering av lösen ord synkroniseras inte alla befintliga lösen ord automatiskt när de lagras oåterkalleligt. När du ändrar lösen ordet synkroniseras lösen ordets hash-värden.

### <a name="computer-objects-location"></a>Plats för dator objekt

Varje kluster är associerat med en enda ORGANISATIONSENHET. En intern användare tillhandahålls i ORGANISATIONSENHETen. Alla noder är domänanslutna i samma ORGANISATIONSENHET.

### <a name="active-directory-administrative-tools"></a>Active Directory administrations verktyg

Anvisningar om hur du installerar Active Directory administrations verktyg på en virtuell Windows Server-dator finns i [Installera hanterings verktyg](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="cluster-creation-fails-repeatedly"></a>Det går inte att skapa kluster upprepade gånger

Vanligaste orsaker:

* DNS-konfigurationen är inte korrekt, domän anslutning för klusternoder fungerar inte.
* NSG: er är för begränsade och förhindrar domän anslutning.
* Den hanterade identiteten har inte tillräcklig behörighet.
* Kluster namnet är inte unikt på de första sex tecknen (antingen med ett annat Live-kluster eller med ett borttaget kluster).

## <a name="authentication-setup-and-configuration"></a>Konfigurera autentisering och konfiguration

### <a name="user-principal-name-upn"></a>Användarens huvud namn (UPN)

* Använd gemener för alla tjänster – UPN är inte Skift läges känsliga i ESP-kluster, men
* UPN-prefixet ska matcha både SAMAccountName i Azure AD-DS. Matchning med e-postfältet är inte obligatoriskt.

### <a name="ldap-properties-in-ambari-configuration"></a>LDAP-egenskaper i Ambari-konfiguration

En fullständig lista över Ambari-egenskaper som påverkar konfigurationen av HDInsight-klustret finns i [installation av LDAP-autentisering i Ambari](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Nästa steg

* [Enterprise Security Package konfigurationer med Azure Active Directory Domain Services i HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Synkronisera Azure Active Directory användare till ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md).
