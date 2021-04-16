---
title: Tillbakaskrivning av lokala lösenord med självbetjäning av lösenordsåterställning – Azure Active Directory
description: Lär dig hur lösenordsändrings- eller återställningshändelser i Azure Active Directory kan skrivas tillbaka till en lokal katalogmiljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8a84da331568d36b6f6910054fdb2aea76f490
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530327"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hur fungerar tillbakaskrivning av lösenordsåterställning via självbetjäning i Azure Active Directory?

Azure Active Directory (Azure AD) med självbetjäning av lösenordsåterställning (SSPR) låter användare återställa sina lösenord i molnet, men de flesta företag har också en miljö för lokal Active Directory Domain Services (AD DS) där deras användare finns. Tillbakaskrivning av lösenord är en funktion som är aktiverad [med Azure AD Connect](../hybrid/whatis-hybrid-identity.md) som gör att lösenordsändringar i molnet kan skrivas tillbaka till en befintlig lokal katalog i realtid. När användarna i den här konfigurationen ändrar eller återställer sina lösenord med hjälp av SSPR i molnet, skrivs även de uppdaterade lösenorden tillbaka till den lokala AD DS-miljön

> [!IMPORTANT]
> Den här konceptuella artikeln förklarar för en administratör hur tillbakaskrivning av lösenordsåterställning via självbetjäning fungerar. Om du är en slutanvändare som redan har registrerats för lösenordsåterställning via självbetjäning och behöver komma tillbaka till ditt konto går du till https://aka.ms/sspr .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösenord kan du kontakta supporten för ytterligare hjälp.

Tillbakaskrivning av lösenord stöds i miljöer som använder följande hybrididentitetsmodeller:

* [Synkronisering av lösenordshashar](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Direktautentisering](../hybrid/how-to-connect-pta.md)
* [Konfigurera Active Directory Federation Services (AD FS)](../hybrid/how-to-connect-fed-management.md)

Tillbakaskrivning av lösenord innehåller följande funktioner:

* **Tillämpning av lokal Active Directory Domain Services (AD DS)-lösenordsprinciper:** När en användare återställer sitt lösenord kontrolleras det för att säkerställa att det uppfyller din lokala AD DS-princip innan användaren checkar in den i katalogen. Den här granskningen omfattar kontroll av historik, komplexitet, ålder, lösenordsfilter och andra lösenordsbegränsningar som du definierar i AD DS.
* **Feedback utan fördröjning:** Tillbakaskrivning av lösenord är en synkron åtgärd. Användarna meddelas omedelbart om deras lösenord inte uppfyller principen eller inte kan återställas eller ändras av någon anledning.
* **Stöder lösenordsändringar** från åtkomstpanelen och Microsoft 365: När federerade användare eller synkroniserade lösenordshashar kommer för att ändra sina utgångna eller icke-utgångna lösenord skrivs dessa lösenord tillbaka till AD DS.
* **Stöder tillbakaskrivning** av lösenord när en administratör återställer dem från Azure Portal: När en administratör återställer en användares lösenord i [Azure Portal](https://portal.azure.com)skrivs lösenordet tillbaka till den lokala platsen om användaren är federerad eller lösenordshashar synkroniserad. Den här funktionen stöds för närvarande inte i Office-administratörsportalen.
* **Kräver inga inkommande brandväggsregler: Tillbakaskrivning** av lösenord använder ett relä Azure Service Bus som en underliggande kommunikationskanal. All kommunikation är utgående via port 443.

> [!NOTE]
> Administratörskonton som finns i skyddade grupper i lokala AD kan användas med tillbakaskrivning av lösenord. Administratörer kan ändra sina lösenord i molnet men kan inte använda lösenordsåterställning för att återställa ett bortglömt lösenord. Mer information om skyddade grupper finns i [Skyddade konton och grupper i AD DS.](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)

Kom igång med SSPR-tillbakaskrivning genom att slutföra följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera tillbakaskrivning av lösenord via självbetjäning (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Så fungerar tillbakaskrivning av lösenord

När en federerad användare eller en lösenordshashar synkroniserad användare försöker återställa eller ändra sitt lösenord i molnet, sker följande åtgärder:

1. En kontroll utförs för att se vilken typ av lösenord användaren har. Om lösenordet hanteras lokalt:
   * En kontroll utförs för att se om tillbakaskrivningstjänsten är igång. Om det är det kan användaren fortsätta.
   * Om tillbakaskrivningstjänsten är nere informeras användaren om att lösenordet inte kan återställas just nu.
1. Därefter skickar användaren lämpliga autentiseringsportar och når sidan **Återställ** lösenord.
1. Användaren väljer ett nytt lösenord och bekräftar det.
1. När användaren väljer **Skicka** krypteras lösenordet i klartext med en symmetrisk nyckel som skapades under installationen av tillbakaskrivningen.
1. Det krypterade lösenordet ingår i en nyttolast som skickas via en HTTPS-kanal till ditt klientspecifika Service Bus Relay (som konfigureras åt dig under installationen av tillbakaskrivning). Det här reläet skyddas av ett slumpmässigt genererat lösenord som endast den lokala installationen känner till.
1. När meddelandet når Service Bus aktiveras slutpunkten för lösenordsåterställning automatiskt och ser att den har en väntande återställningsbegäran.
1. Tjänsten söker sedan efter användaren med hjälp av molnankarattributet. För att den här sökning ska lyckas måste följande villkor vara uppfyllda:

   * Användarobjektet måste finnas i AD DS-anslutningsutrymmet.
   * Användarobjektet måste vara länkat till motsvarande metaversumobjekt (MV).
   * Användarobjektet måste vara länkat till motsvarande Azure AD-anslutningsobjekt.
   * Länken från AD DS-anslutningsobjektet till MV måste ha `Microsoft.InfromADUserAccountEnabled.xxx` synkroniseringsregeln på länken.

   När anropet kommer från molnet använder synkroniseringsmotorn **attributet cloudAnchor** för att leta upp Azure AD-anslutningsappens utrymmesobjekt. Den följer sedan länken tillbaka till MV-objektet och följer sedan länken tillbaka till AD DS-objektet. Eftersom det kan finnas flera AD DS-objekt (flera skogar) för samma användare förlitar sig synkroniseringsmotorn på länken för `Microsoft.InfromADUserAccountEnabled.xxx` att välja rätt.

1. När användarkontot har hittats görs ett försök att återställa lösenordet direkt i lämplig AD DS-skog.
1. Om åtgärden med lösenordsuppsättningen lyckas får användaren ett information om att lösenordet har ändrats.

   > [!NOTE]
   > Om användarens lösenordshashar synkroniseras med Azure AD med hjälp av synkronisering av lösenordshashar finns det en risk att den lokala lösenordsprincipen är svagare än lösenordsprincipen i molnet. I det här fallet tillämpas den lokala principen. Den här principen säkerställer att din lokala princip tillämpas i molnet, oavsett om du använder synkronisering av lösenordshashar eller federation för att tillhandahålla enkel inloggning.

1. Om lösenordsuppsättningen misslyckas uppmanas användaren att försöka igen. Åtgärden kan misslyckas på grund av följande orsaker:
    * Tjänsten låg nere.
    * Det lösenord som de valde uppfyller inte organisationens principer.
    * Det går inte att hitta användaren i den lokala AD DS-miljön.

   Felmeddelandena ger vägledning till användarna så att de kan försöka lösa problemet utan att administratören behöver göra något.

## <a name="password-writeback-security"></a>Säkerhet för tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en mycket säker tjänst. För att säkerställa att din information skyddas aktiveras en säkerhetsmodell med fyra nivåer på följande sätt:

* **Klientspecifikt Service Bus Relay**
   * När du ställer in tjänsten konfigureras ett klientspecifikt Service Bus Relay som skyddas av ett slumpmässigt genererat starkt lösenord som Microsoft aldrig har åtkomst till.
* **Låst, kryptografiskt stark, lösenordskrypteringsnyckel**
   * När Service Bus Relay har skapats skapas en stark symmetrisk nyckel som används för att kryptera lösenordet när det kommer över kabeln. Den här nyckeln finns bara i företagets hemliga lagring i molnet, som är kraftigt låst och granskad, precis som andra lösenord i katalogen.
* **Branschstandard Transport Layer Security (TLS)**
   1. När en lösenordsåterställning eller ändring utförs i molnet krypteras lösenordet i klartext med din offentliga nyckel.
   1. Det krypterade lösenordet placeras i ett HTTPS-meddelande som skickas via en krypterad kanal med hjälp av Microsoft TLS/SSL-certifikat till Service Bus Relay.
   1. När meddelandet tas emot i Service Bus aktiveras din lokala agent och autentiseras till Service Bus med hjälp av det starka lösenord som genererades tidigare.
   1. Den lokala agenten hämtar det krypterade meddelandet och dekrypterar det med hjälp av den privata nyckeln.
   1. Den lokala agenten försöker ange lösenordet via AD DS SetPassword-API:et. Det här steget är det som gör det möjligt att framttvingande av din lokala lösenordsprincip för AD DS (till exempel komplexitet, ålder, historik och filter) i molnet.
* **Förfalloprinciper för meddelanden**
   * Om meddelandet finns i Service Bus på grund av att din lokala tjänst är ur drift, går den ut på grund av en tidsfördr dess slut och tas bort efter flera minuter. Time out och borttagning av meddelandet ökar säkerheten ytterligare.

### <a name="password-writeback-encryption-details"></a>Krypteringsinformation för tillbakaskrivning av lösenord

När en användare har skickat en lösenordsåterställning går återställningsbegäran igenom flera krypteringssteg innan den tas emot i din lokala miljö. Dessa krypteringssteg säkerställer maximal tillförlitlighet och säkerhet för tjänsten. De beskrivs på följande sätt:

1. Lösenordskryptering med **2 048-bitars RSA-nyckel:** När en användare skickar ett lösenord som ska skrivas tillbaka till den lokala platsen krypteras själva det skickade lösenordet med en 2 048-bitars RSA-nyckel.
1. **Kryptering på paketnivå med AES-GCM:** Hela paketet, lösenordet plus nödvändiga metadata, krypteras med hjälp av AES-GCM. Den här krypteringen förhindrar att alla med direkt åtkomst till den underliggande Service Bus-kanalen visar eller manipulerar innehållet.
1. **All kommunikation sker via TLS/SSL:** All kommunikation med Service Bus sker i en SSL/TLS-kanal. Den här krypteringen skyddar innehållet från obehöriga tredje parter.
1. **Automatisk nyckelrover** var sjätte månad: Alla nycklar återställs var sjätte månad, eller varje gång tillbakaskrivning av lösenord inaktiveras och sedan återaktiveras på Azure AD Connect, för att säkerställa maximal säkerhet och säkerhet för tjänsten.

### <a name="password-writeback-bandwidth-usage"></a>Bandbreddsanvändning för tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en tjänst med låg bandbredd som endast skickar begäranden tillbaka till den lokala agenten under följande omständigheter:

* Två meddelanden skickas när funktionen är aktiverad eller inaktiverad via Azure AD Connect.
* Ett meddelande skickas en gång var femte minut som ett tjänst pulsslag så länge tjänsten körs.
* Två meddelanden skickas varje gång ett nytt lösenord skickas:
   * Det första meddelandet är en begäran om att utföra åtgärden.
   * Det andra meddelandet innehåller resultatet av åtgärden och skickas under följande omständigheter:
      * Varje gång ett nytt lösenord skickas under en självbetjäning av användarens lösenordsåterställning.
      * Varje gång ett nytt lösenord skickas under en ändring av användarlösenord.
      * Varje gång ett nytt lösenord skickas under en administratörsinitierad återställning av användarlösenord (endast från Azure-administratörsportalerna).

#### <a name="message-size-and-bandwidth-considerations"></a>Överväganden för meddelandestorlek och bandbredd

Storleken på vart och ett av de meddelanden som beskrevs tidigare är vanligtvis under 1 kB. Även under extrema belastningar förbrukar själva tjänsten för tillbakaskrivning av lösenord några kilobitar per sekund bandbredd. Eftersom varje meddelande skickas i realtid, endast när det krävs av en lösenordsuppdateringsåtgärd, och eftersom meddelandestorleken är så liten, är bandbreddsanvändningen för tillbakaskrivningsfunktionerna för liten för att ha en mätbar inverkan.

## <a name="supported-writeback-operations"></a>Tillbakaskrivningsåtgärder som stöds

Lösenord skrivs tillbaka i alla följande situationer:

* **Slutanvändaråtgärder som stöds**
   * Valfri självbetjäning för lösenordsändring av slutanvändare.
   * Alla självbetjäning tvingar fram ändring av lösenord, till exempel förfallotid för lösenord.
   * Alla självbetjäning av lösenordsåterställning för slutanvändare som kommer från portalen för [lösenordsåterställning.](https://passwordreset.microsoftonline.com)

* **Administratörsåtgärder som stöds**
   * Valfri självbetjäning för lösenordsändring av administratör.
   * En administratörs självbetjäning tvingar fram ändring av lösenordsåtgärd, till exempel förfallotid för lösenord.
   * Alla självbetjäning av lösenordsåterställning för administratörer som kommer från portalen [för lösenordsåterställning.](https://passwordreset.microsoftonline.com)
   * Alla administratörsinitierade lösenordsåterställning för slutanvändare från [Azure Portal](https://portal.azure.com).
   * Alla administratörsinitierade lösenordsåterställning för slutanvändare från [Microsoft Graph API.](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http)

## <a name="unsupported-writeback-operations"></a>Tillbakaskrivningsåtgärder som inte stöds

Lösenord skrivs inte tillbaka i någon av följande situationer:

* **Slutanvändaråtgärder som inte stöds**
   * Slutanvändare återställer sina egna lösenord med hjälp av PowerShell version 1, version 2 eller Microsoft Graph API.
* **Administratörsåtgärder som inte stöds**
   * Alla administratörsinitierade lösenordsåterställning för slutanvändare från PowerShell version 1, version 2 eller Microsoft Graph-API:et [(Microsoft Graph-API:et](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http) stöds).
   * Alla administratörsinitierade lösenordsåterställning för slutanvändare från [Administrationscenter för Microsoft 365](https://admin.microsoft.com).
   * Administratörer kan inte använda verktyget för lösenordsåterställning för att återställa sina egna lösenord för tillbakaskrivning av lösenord.

> [!WARNING]
> Användning av kryssrutan "Användaren måste ändra lösenord vid nästa inloggning" i lokala AD DS-administrationsverktyg som Active Directory - användare och datorer eller Active Directory Administrationscenter stöds som en förhandsgranskningsfunktion i Azure AD Connect. Mer information finns i Implementera [synkronisering av lösenordshashar Azure AD Connect synkronisering.](../hybrid/how-to-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Nästa steg

Kom igång med tillbakaskrivning av SSPR genom att slutföra följande självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera tillbakaskrivning av lösenord via självbetjäning (SSPR)](./tutorial-enable-sspr-writeback.md)