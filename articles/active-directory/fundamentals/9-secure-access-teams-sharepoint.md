---
title: Skydda extern åtkomst till Microsoft Teams, SharePoint och OneDrive med Azure Active Directory
description: Säker åtkomst till Microsoft 365-tjänster som en del av din totala externa åtkomst säkerhet.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64a5bd409a29643c1a51630d6f029da7c90d29ad
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744108"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Säker extern åtkomst till Microsoft Teams, SharePoint och OneDrive för företag 

Microsoft Teams, SharePoint och OneDrive för företag är tre av de mest använda sätten att samar beta och dela innehåll med externa användare. Om metoderna "godkänt" är för begränsade kan användarna gå utanför godkända metoder genom att e-posta innehåll eller ställa in oskyddade externa processer och program, till exempel en personlig DropBox eller OneDrive. Målet är att balansera dina säkerhets behov med enkel samarbete.

Den här artikeln hjälper dig att fastställa och konfigurera externt samarbete för att möta dina affärs mål med Microsoft Teams och SharePoint.

## <a name="governance-begins-in-azure-active-directory"></a>Styrning börjar i Azure Active Directory

Delning i Microsoft 365 delvis styrs av [externa identiteter | Inställningar för externt samarbete](https://aad.portal.azure.com/) i Azure Active Directory (Azure AD). Om extern delning är inaktive rad eller begränsad i Azure AD, åsidosätts alla delnings inställningar som kon figurer ATS i Microsoft 365. Ett undantag till detta är att om Azure AD B2B-integration inte är aktive rad kan SharePoint och OneDrive konfigureras för att ge stöd för ad hoc-delning via eng ång slö sen ord (eng ång slö sen ord).

![Skärm bild av inställningar för externt samarbete](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Gästanvändaråtkomst

Det finns tre alternativ för åtkomst till gäst användare som styr vilka gäst användare som kan se när de har bjudits in. 

Om du vill förhindra gäst användare från att se information om andra gäst användare och kunna räkna upp grupp medlemskap väljer du gäst användare har begränsad åtkomst till egenskaper och medlemskap i katalog objekt.

### <a name="guest-invite-settings"></a>Inställningar för gäst inbjudning

De här inställningarna bestämmer vem som kan bjuda in gäster och hur dessa gäster kan bjudas in. De här inställningarna aktive ras endast om integrationen med B2B är aktive rad.

Vi rekommenderar att du aktiverar administratörer och användare i gäst Inbjudnings rollen kan bjuda in. Med den här inställningen kan du konfigurera kontrollerade samarbets processer, som i följande exempel.

* Team ägaren skickar en biljett som ska tilldelas rollen som gäst deltagare och

   * Blir ansvarig för alla inbjudningar till gästen.

   * Tillåter inte att användare läggs till i den underliggande SharePoint-tjänsten direkt

   * Kan vara konto för att utföra regelbundna åtkomst granskningar och återkalla åtkomsten efter behov.

* Central IT gör följande

   * Aktiverar extern delning genom att bevilja den begärda rollen när en utbildning slutförs.

   * Tilldelar Azure AD P2-licens till Microsoft 365 gruppens ägare för att aktivera åtkomst granskningar.
   * Skapar en Microsoft 365 grupp åtkomst granskning.

   * Bekräftar att åtkomst granskningar sker.

   * Tar bort användare som lagts till direkt till den underliggande SharePoint.

 Ange **Aktivera e-post med eng ång slö sen ord för gäster (för hands version) och aktivera självbetjänings inloggning för gäst via användar flöden** till **Ja**. Den här inställningen drar nytta av integreringen med inställningarna för externa samarbets funktioner i Azure AD.

### <a name="collaboration-restrictions"></a>Samarbetsbegränsningar

Det finns tre alternativ under samarbets begränsningar. Dina affärs krav avgör vilka du ska välja.

* **Tillåt att inbjudningar skickas till en domän** innebär att alla användare kan bli inbjudna att samar beta.

* **Neka inbjudningar till de angivna domänerna** innebär att alla användare utanför dem kan bjudas in att samar beta.

* **Tillåt endast inbjudningar till de angivna domänerna** innebär att alla användare utanför de angivna domänerna inte kan bjudas in. 

## <a name="govern-access-in-teams"></a>Regler för åtkomst i Teams

[Teamen skiljer sig mellan externa användare (någon utanför organisationen) och gäst användare (med gäst konton)](https://docs.microsoft.com/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH)). Du hanterar samarbets inställningen i [team administrations portalen](https://admin.teams.microsoft.com/company-wide-settings/external-communications) under Inställningar för hela organisationen. 

> [!NOTE]
> Inställningar för att samar beta med externa identiteter i Azure Active Directory kontrol lera de gällande behörigheterna. Du kan öka begränsningar i team, men inte minska dem från vad som anges i Azure AD.

* **Inställningar för extern åtkomst**. Som standard tillåter team extern åtkomst, vilket innebär att organisationen kan kommunicera med alla externa domäner. Om du vill begränsa eller tillåta vissa domäner bara för team kan du göra det här.

* **Gäst åtkomst**. Gäst åtkomst styr vilka gäst användare som kan göra i team.

Mer information om hur du hanterar extern åtkomst i team finns i följande resurser.

* [Hantera extern åtkomst i Microsoft Teams](https://docs.microsoft.com/microsoftteams/manage-external-access)

* [Microsoft 365 identitets modeller och Azure Active Directory](https://docs.microsoft.com/microsoft-365/enterprise/about-microsoft-365-identity?view=o365-worldwide)

* [Identitets modeller och autentisering för Microsoft Teams](https://docs.microsoft.com/MicrosoftTeams/identify-models-authentication)

* [Känslighets etiketter för Microsoft Teams](https://docs.microsoft.com/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Styra åtkomst i SharePoint och OneDrive

SharePoint-administratörer har många inställningar som är tillgängliga för samarbete. Inställningar för hela organisationen hanteras från administrations centret för SharePoint. Inställningar kan justeras för varje SharePoint-webbplats. Vi rekommenderar att dina inställningar för hela organisationen är tillräckligt höga säkerhets nivåer och att du ökar säkerheten på vissa platser efter behov. För ett projekt med hög risk kan du till exempel vilja begränsa användare till vissa domäner och inaktivera möjligheten för medlemmar att bjuda in gäster.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Integrera SharePoint och en enhet med Azure AD B2B

Som en del av din övergripande strategi för att styra det externa samarbetet rekommenderar vi att du [aktiverar för hands versionen av SharePoint och OneDrive-integrering med Azure AD B2B](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview) .

Azure AD B2B tillhandahåller autentisering och hantering av gäst användare. Med SharePoint och OneDrive-integrering används [Azure AD B2B eng ång slö sen ord](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode) för extern delning av filer, mappar, list objekt, dokument bibliotek och webbplatser. Den här funktionen ger en uppgraderad upplevelse från den befintliga [säkra externa delnings upplevelsen](https://docs.microsoft.com/sharepoint/what-s-new-in-sharing-in-targeted-release).

> [!NOTE]
> Om du aktiverar för hands versionen av Azure AD B2B-integrering, omfattas SharePoint och OneDrive-delning av inställningarna för organisations relationer i Azure AD, till exempel att **medlemmar kan bjuda in** och **gäster kan bjuda in**.

### <a name="sharing-policies"></a>Delnings principer

*Extern delning* kan ställas in för både SharePoint och OneDrive. OneDrive-begränsningar kan inte vara mer tillåtna än SharePoint-inställningarna.

 ![Skärm bild av inställningar för extern delning i SharePoint och OneDrive](media/secure-external-access/9-sharepoint-settings.png)

SharePoint-integrering med Azure AD B2B ändrar hur kontroller interagerar med konton.

* **Vem som helst**. Rekommenderas inte

   * Oavsett integrations status innebär aktivering av alla länkar att inga Azure-principer tillämpas när den här typen av länk används. 

   * Aktivera inte den här funktionen i ett scenario med styrt samarbete. 
   > [!NOTE]
   > Du kan hitta ett scenario där du måste aktivera den här inställningen för en viss plats. i så fall aktiverar du den här och anger den större begränsningen för enskilda platser.

* **Nya och befintliga gäster**. Rekommenderas om du har aktiverat integrering.

   * **Med Azure AD B2B-integrering** aktive rad kommer nya och befintliga gäster att ha ett Azure AD B2B-gäst konto som kan hanteras med Azure AD-principer.

   * **Utan Azure AD B2B-integrering** aktive rad kommer nya gäster inte att ha ett Azure AD B2B-konto skapat, och de kan inte hanteras från Azure AD. Huruvida befintliga gäster har ett Azure AD B2B-konto beror på hur gästen skapades.

* **Befintliga gäster**. Rekommenderas om du inte har aktiverat integrering.

   * Med det här alternativet kan användare bara dela med andra användare som redan finns i din katalog.

* **Endast personer i din organisation**. Rekommenderas inte när du behöver samar beta med externa användare.

   * Oavsett integrations status kommer användarna bara att kunna dela med användare i din organisation. 

* **Begränsa extern delning per domän**. Som standard tillåter SharePoint extern åtkomst, vilket innebär att delning tillåts med alla externa domäner. Om du vill begränsa eller tillåta vissa domäner bara för SharePoint kan du göra det här.

* **Tillåt endast användare i vissa säkerhets grupper att dela externt**.  Den här inställningen begränsar vem som kan dela innehåll i SharePoint och OneDrive, medan inställningen i Azure AD gäller för alla program. Att begränsa vem som kan dela kan vara användbart om du vill att användarna ska kunna ta en utbildning om delningen på ett säkert sätt, och sedan lägga till dem i en godkänd delnings säkerhets grupp. Om den här inställningen är markerad och användarna inte har möjlighet att få åtkomst till en "godkänd resurs" kan de i stället hitta ej godkända sätt att dela. 

* **Tillåt gäster att dela objekt som de inte äger**. Vi rekommenderar att du lämnar detta inaktiverat.

* **Personer som använder en verifierings kod måste autentiseras igen efter så här många dagar (Standardvärdet är 30)**. Vi rekommenderar att du aktiverar den här inställningen.

### <a name="access-controls"></a>Åtkomstkontroller

Inställningen åtkomst kontroller påverkar alla användare i din organisation. Eftersom du kanske inte kan kontrol lera om externa användare har kompatibla enheter, kommer vi inte att adressera dessa kontroller här. 

* **Utloggning av inaktiva sessioner**. Vi rekommenderar att du aktiverar den här kontrollen, vilket gör att du kan varna och logga ut användare på ohanterade enheter efter en tids inaktivitet. Du kan konfigurera tids perioden för inaktivitet och varningen. 

* **Nätverks plats**. Genom att ange den här kontrollen kan du bara tillåta åtkomst till formulär-IP-adresser som organisationen äger. I externa samarbets scenarier anger du detta endast om alla dina externa partners får åtkomst till resurser i nätverket, eller via VPN.

### <a name="file-and-folder-links"></a>Fil-och mapp länkar

I administrations centret för SharePoint kan du också ange hur fil-och mapp länkar delas. Du kan också konfigurera dessa inställningar för varje plats. 

 ![Skärm bild av inställningar för fil-och mapp-länk](media/secure-external-access/9-file-folder-links.png)

Om du har aktiverat integreringen med Azure AD B2B resulterar delning av filer och mappar med dem utanför organisationen till att en B2B-användare skapas när filer och mappar delas.

Vi rekommenderar att du ställer in standard länk typen till **endast personer i din organisation** och vilka standard behörigheter som ska **redige ras**. På så sätt ser du till att objekten delas i ett genomtänktt syfte. Du kan sedan anpassa den här inställningen för standard per webbplats som uppfyller specifika samarbets behov.

### <a name="anyone-links"></a>Alla länkar

Vi rekommenderar inte att du aktiverar alla länkar. Om du gör det rekommenderar vi att du anger ett förfallo datum och kan begränsa dem till att visa behörigheter. Om du väljer Visa endast behörigheter för filer eller mappar kommer användarna inte att kunna ändra alla länkar för att inkludera redigerings behörighet.

Om du vill veta mer om hur du styr extern åtkomst till SharePoint ser du följande:

* [Översikt över SharePoint extern delning](https://docs.microsoft.com/sharepoint/external-sharing-overview)

* [SharePoint och OneDrive-integrering med Azure AD B2B](https://docs.microsoft.com/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md) (du är här.)