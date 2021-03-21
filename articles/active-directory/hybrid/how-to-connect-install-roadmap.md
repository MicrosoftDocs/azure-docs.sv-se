---
title: Installationsplan för Azure AD Connect och Azure AD Connect Health. | Microsoft Docs
description: Det här dokumentet innehåller en översikt över installationsalternativ och sökvägar för att installera Azure AD Connect och Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae575aa6544a174a70eb8ea4749566e8660280e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94873275"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Installationsplan för Azure AD Connect och Azure AD Connect Health

## <a name="install-azure-ad-connect"></a>Installera Azure AD Connect

> [!IMPORTANT]
> Microsoft stöder inte ändring eller hantering av Azure AD Connect-synkronisering utöver de åtgärder som är formellt dokumenterade. Någon av dessa åtgärder kan resultera i ett inkonsekvent eller tillstånd som inte stöds för Azure AD Connect Sync. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

Du kan ladda ned Azure AD Connect från [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).

| Lösning | Scenario |
| --- | --- |
| Innan du börjar – [Maskinvara och krav](how-to-connect-install-prerequisites.md) |<li>Steg som du måste utföra innan du börjar installera Azure AD Connect.</li> |
| [Standardinställningar](how-to-connect-install-express.md) |<li>Om du har en AD med enda skog är detta det rekommenderade alternativet.</li> <li>Användaren loggar in med samma lösenord med hjälp av lösenordssynkronisering.</li> |
| [Anpassade inställningar](how-to-connect-install-custom.md) |<li>Används när du har flera skogar. Har stöd för många lokala [topologier](plan-connect-topologies.md).</li> <li>Anpassa ditt inloggningsalternativ, till exempel direktautentisering, AD FS för federation eller använd en tredje parts identitetsprovider.</li> <li>Anpassa synkroniseringsfunktioner, t.ex. filtrering och tillbakaskrivning.</li> |
| [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Används när du har en befintlig DirSync-server som redan körs.</li> |
| [Uppgradera från Azure AD Sync eller Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Det finns flera olika metoder beroende på dina preferenser.</li> |

[Efter installationen](how-to-connect-post-installation.md) bör du kontrollera att allt fungerar som förväntat och tilldela licenser till användarna.

### <a name="next-steps-to-install-azure-ad-connect"></a>Nästa steg för att installera Azure AD Connect
|Avsnitt |Länk|  
| --- | --- |
|Ladda ned Azure AD Connect | [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](./how-to-connect-install-express.md)|
|Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](./how-to-connect-install-custom.md)|
|Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Efter installationen | [Verifiera installationen och tilldela licenser](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Mer information om installationen av Azure AD Connect
Det är bra att vara förberedd om det skulle uppstå [driftproblem](./how-to-connect-sync-staging-server.md). Du kanske vill ha en reservserver som du enkelt kan redundansväxla till i händelse av ett [allvarligt fel](how-to-connect-sync-staging-server.md#disaster-recovery). Om du ofta kommer att göra konfigurationsändringar bör du planera för ett server i [mellanlagringsläge](how-to-connect-sync-staging-server.md).

|Avsnitt |Länk|  
| --- | --- |
|Topologier som stöds | [Topologier för Azure AD Connect](plan-connect-topologies.md)|
|Designbegrepp | [Designbegrepp för Azure AD Connect](plan-connect-design-concepts.md)|
|Konton som används för installation | [Mer information om Azure AD Connect-autentiseringsuppgifter och -behörigheter](reference-connect-accounts-permissions.md)|
|Driftplanering | [Azure AD Connect-synkronisering: Driftåtgärder och saker att tänka på](./how-to-connect-sync-staging-server.md)|
|Alternativ för användarinloggning | [Alternativ för Azure AD Connect användar inloggning](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurera synkroniseringsfunktioner
Azure AD Connect har flera funktioner som du kan aktivera om du vill eller som är aktiverade som standard. Vissa funktioner kan ibland kräva ytterligare konfiguration i vissa scenarier och topologier.

[Filtrering](how-to-connect-sync-configure-filtering.md) används när du vill begränsa vilka objekt som synkroniseras till Azure AD. Som standard synkroniseras alla användare, kontakter, grupper och Windows 10-datorer. Du kan ändra filtreringen baserat på domäner, organisationsenheter eller attribut.

[Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md) synkroniserar lösenordshashen i Active Directory med Azure AD. Slutanvändare kan använda samma lösenord lokalt och i molnet men hanterar det endast på en plats. Eftersom din lokala Active Directory används som auktoritet kan du också använda en egen lösenordsprincip.

Med [tillbakaskrivning av lösenord](../authentication/tutorial-enable-sspr.md) kan dina användare ändra och återställa sina lösenord i molnet och tillämpa din lokala lösenordsprincip.

Med [tillbakaskrivning av enheter](how-to-connect-device-writeback.md) kan en enhet som är registrerad i Azure AD skrivas tillbaka till lokala Active Directory så att den kan användas för villkorlig åtkomst.

Funktionen [förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) är aktiverad som standard och skyddar din molnkatalog mot flera samtidiga borttagningar. Som standard tillåts 500 borttagningar per körning. Du kan ändra den här inställningen beroende på storleken på din organisation.

Funktionen [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) är aktiverad som standard för installationer med standardinställningar och säkerställer att Azure AD Connect alltid är uppdaterat med den senaste versionen.

### <a name="next-steps-to-configure-sync-features"></a>Nästa steg för att konfigurera synkroniseringsfunktioner
|Avsnitt |Länk|  
| --- | --- |
|Konfigurera filtrering | [Azure AD Connect-synkronisering: Konfigurera filtrering](how-to-connect-sync-configure-filtering.md)|
|Synkronisering av lösenordshash | [Hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)|
|Direktautentisering | [Direktautentisering](how-to-connect-pta.md)
|Tillbakaskrivning av lösenord | [Komma igång med lösen ords hantering](../authentication/tutorial-enable-sspr.md)|
|Tillbakaskrivning av enheter | [Aktivera tillbakaskrivning av enheter i Azure AD Connect](how-to-connect-device-writeback.md)|
|Förhindra oavsiktliga borttagningar | [Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|Automatisk uppgradering | [Azure AD Connect: Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Anpassa synkroniseringen av Azure AD Connect
Azure AD Connect-synkroniseringsverktyget levereras med en standardkonfiguration som är avsedd att fungera för de flesta kunder och topologier. Men det finns alltid situationer då standardkonfigurationen inte fungerar och måste justeras. Du kan göra ändringarna som beskrivs i det här avsnittet och i länkade avsnitt.

Om du inte har arbetat med en synkroniseringstopologi förut är det bäst att börja med att lära sig grunderna och de termer som används. Mer information finns i [Tekniska begrepp](how-to-connect-sync-technical-concepts.md). Azure AD Connect är en utveckling av MIIS2003, ILM2007 och FIM2010. Även om vissa saker är identiska, har mycket ändrats.

[Standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md) förutsätter att det kan finnas mer än en skog i konfigurationen. I dessa topologier kan ett användarobjekt representeras som en kontakt i en annan skog. Användaren kan också ha en länkad postlåda i en annan resursskog. Standardkonfigurationens funktionssätt beskrivs i [användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md).

Konfigurationsmodellen i synkroniseringsverktyget kallas för [deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). De avancerade attributflödena använder [funktioner](reference-connect-sync-functions-reference.md) för att uttrycka attributtransformationer. Du kan se och granska hela konfigurationen med hjälp av verktyg som medföljer Azure AD Connect. Om du behöver göra konfigurationsändringar ser du till att du följer [metodtipsen](how-to-connect-sync-best-practices-changing-default-configuration.md) så att det är lättare att integrera nya versioner.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Nästa steg för att anpassa Azure AD Connect-synkroniseringen
|Avsnitt |Länk|  
| --- | --- |
|Alla artiklar om Azure AD Connect-synkronisering | [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md)|
|Tekniska begrepp | [Azure AD Connect synkronisering: tekniska begrepp](how-to-connect-sync-technical-concepts.md)|
|Förstå standardkonfigurationen | [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md)|
|Förstå användare och kontakter | [Azure AD Connect-synkronisering: Förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md)|
|Deklarativ etablering | [Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Ändra standardkonfigurationen | [Metodtips för att ändra standardkonfigurationen](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurera federationsfunktioner

Azure AD Connect har flera funktioner som förenklar federering med Azure AD med hjälp av AD FS och hantering av federationsförtroendet. Azure AD Connect stöder AD FS på Windows Server 2012R2 eller senare.

[Uppdatera TLS/SSL-certifikatet för AD FS server gruppen](how-to-connect-fed-ssl-update.md) även om du inte använder Azure AD Connect för att hantera Federations förtroendet.

[Lägg till en AD FS-server](how-to-connect-fed-management.md#addadfsserver) i servergruppen för att utöka servergruppen efter behov.

[Reparera förtroendet](how-to-connect-fed-management.md#repairthetrust) med Azure AD med några enkla klick.

AD FS kan konfigureras att ge stöd för [flera domäner](how-to-connect-install-multiple-domains.md). Du kan till exempel ha flera toppdomäner som du behöver använda för federation.

Om ADFS-servern inte har konfigurerats att automatiskt uppdatera certifikat från Azure AD eller om du använder en annan lösning än en ADFS-lösning så meddelas du när du behöver [uppdatera certifikaten](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Nästa steg för att konfigurera federationsfunktioner
|Avsnitt |Länk|  
| --- | --- |
|Alla AD FS-artiklar | [Azure AD Connect och federation](how-to-connect-fed-whatis.md)|
|Konfigurera AD FS med underdomäner | [Stöd för flera domäner för federering med Azure AD](how-to-connect-install-multiple-domains.md)|
|Hantera AD FS-servergrupp | [Hantering och anpassning av AD FS med Azure AD Connect](how-to-connect-fed-management.md)|
|Uppdatera federationscertifikat manuellt | [Förnya Federations certifikat för Microsoft 365 och Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Komma igång med Azure AD Connect Health
Använd följande steg för att komma igång med Azure AD Connect Health:

1. [Hämta Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) eller [starta en utvärderings version](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Hämta och installera Azure AD Connect Health agenter](#download-and-install-azure-ad-connect-health-agent) på dina identitets servrar.
3. Visa Azure AD Connect Health instrument panelen på [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) .

> [!NOTE]
> Kom ihåg att innan du ser några data på Azure AD Connect Health-instrumentpanelen så måste du installera Azure AD Connect Health-agenterna på dina målservrar.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Ladda ned och installera Azure AD Connect Health-agenten
* Säkerställ att du [uppfyller kraven](how-to-connect-health-agent-install.md#requirements) för Azure AD Connect Health.
* Kom igång med Azure AD Connect Health för AD FS
    * [Hämta Azure AD Connect Health Agent för AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Se installationsinstruktionerna](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs).
* Kom igång med Azure AD Connect Health för synkronisering
    * [Hämta och installera den senaste versionen av Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). Hälsoagenten för synkronisering installeras som en del av installationen av Azure AD Connect (version 1.0.9125.0 eller senare).
* Kom igång med Azure AD Connect Health för AD DS
    * [Ladda ned Azure AD Connect Health Agent för AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Se installationsinstruktionerna](how-to-connect-health-agent-install.md#install-the-agent-for-azure-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health-portalen
På Azure AD Connect Health-portalen kan du visa varningar, övervaka prestanda och hitta användningsanalyser. URL:en https://aka.ms/aadconnecthealth tar dig till huvudbladet i Azure AD Connect Health. Tänk dig ett blad som ett fönster. På huvud bladet kan du se **Snabbstart**, tjänster inom Azure AD Connect Health och ytterligare konfigurations alternativ. Mer information finns i form av skärmbilden nedan tillsammans med kortfattade beskrivningar. När du har distribuerat agenterna identifierar hälsotjänsten automatiskt de tjänster som Azure AD Connect Health övervakar.

> [!NOTE]
> För mer information om licenser går du till [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md) eller [sidan med priser för Azure AD](https://aka.ms/aadpricing).
    
![Azure AD Connect Health-portalen](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Snabbstart**: När du väljer det här alternativet öppnas bladet **Snabbstart**. Du kan hämta Azure AD Connect Health-agenten genom att välja **Hämta verktyg**. Du kan också komma åt dokumentationen och ge feedback.
* **Azure Active Directory Connect (synkronisering)**: När du väljer det här alternativet visas de Azure AD Connect-servrar som Azure AD Connect Health övervakar för tillfället. Posten **Sync errors** (Synkroniseringsfel) visar grundläggande synkroniseringsfel för din första publicerade tjänst efter kategorier. När du väljer posten **Sync services** (Synkroniseringstjänster) öppnas ett blad som visar information om dina Azure AD Connect-servrar. Läs mer om funktionerna i [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md).
* **Active Directory Federation Services**: När du väljer det här alternativet visas alla AD FS-tjänster som Azure AD Connect Health övervakar för tillfället. När du väljer en instans visas information om tjänstinstansen på bladet som öppnas. Den här informationen innehåller en översikt, egenskaper, aviseringar, övervakning och användningsanalys. Läs mer om funktionerna i [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: När du väljer det här alternativet visas alla AD DS-skogar som Azure AD Connect Health övervakar för tillfället. När du väljer en skog visas information om skogen på bladet som öppnas. Denna information innehåller en översikt över viktig information, instrumentpanelerna Domänkontrollanter och Replikeringsstatus, aviseringar och övervakning. Läs mer om funktionerna i [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md).
* **Konfigurera**: Det här avsnittet innehåller alternativ för att aktivera eller inaktivera följande:

   - Den **automatiska uppdateringen** av den Azure AD Connect Health agenten till den senaste versionen: Azure AD Connect Health Agent uppdateras automatiskt när nya versioner är tillgängliga. Det här alternativet är aktiverat som standard.
   - **Åtkomst till data** från Azure AD-katalogen integritet endast av Microsoft i fel söknings syfte: om det här alternativet är aktiverat kan Microsoft komma åt samma data som visas av användaren. Den här informationen kan vara användbar för fel sökning och för att ge nödvändig hjälp. Det här alternativet är inaktiverat som standard
* **Role based access control (IAM)** (Rollbaserad åtkomstkontroll) är avsnittet för att hantera åtkomst till Connect Health-data i rollbasen. 

## <a name="next-steps"></a>Nästa steg

- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)|
- [Direktautentisering](how-to-connect-pta.md)
- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
- [Installera Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md) 
- [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md)