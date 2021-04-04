---
title: Tillbakaskrivning av lösen ord med självbetjäning för återställning av lösen ord – Azure Active Directory
description: Lär dig hur lösen ords ändring eller återställning av händelser i Azure Active Directory kan skrivas tillbaka till en lokal katalog miljö
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
ms.openlocfilehash: 53f416a23dbb47660097c41ada09c8c135434bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743657"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hur fungerar tillbakaskrivning av lösen ord för självbetjänings återställning i Azure Active Directory?

Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) gör det möjligt för användare att återställa sina lösen ord i molnet, men de flesta företag har också en lokal Active Directory Domain Services (AD DS)-miljö där deras användare finns. Tillbakaskrivning av lösen ord är en funktion som aktive ras med [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) som gör att lösen ords ändringar i molnet kan skrivas tillbaka till en befintlig lokal katalog i real tid. I den här konfigurationen, när användare ändrar eller återställer sina lösen ord med SSPR i molnet, skrivs även de uppdaterade lösen orden tillbaka till den lokala AD DS-miljön

> [!IMPORTANT]
> I den här konceptuella artikeln förklaras en administratör hur tillbakaskrivning av lösen ord för återställning av lösen ord fungerar. Om du är en slutanvändare som redan är registrerad för lösen ords återställning via självbetjäning och behöver gå tillbaka till ditt konto, går du till https://aka.ms/sspr .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

Tillbakaskrivning av lösen ord stöds i miljöer som använder följande hybrid identitets modeller:

* [Hash-synkronisering av lösen ord](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Direktautentisering](../hybrid/how-to-connect-pta.md)
* [Konfigurera Active Directory Federation Services (AD FS)](../hybrid/how-to-connect-fed-management.md)

Tillbakaskrivning av lösen ord innehåller följande funktioner:

* **Tillämpning av lösen ords principer för lokala Active Directory Domain Services (AD DS)**: när en användare återställer sitt lösen ord, kontrol leras att den uppfyller din lokala AD DS-princip innan den allokeras till den katalogen. Den här granskningen innefattar att kontrol lera historik, komplexitet, ålder, lösen ords filter och andra lösen ords begränsningar som du definierar i AD DS.
* **Noll – fördröjning i feedback**: tillbakaskrivning av lösen ord är en synkron åtgärd. Användare meddelas omedelbart om lösen ordet inte uppfyller principen eller inte kan återställas eller ändras av någon anledning.
* **Har stöd för lösen ords ändringar från åtkomst panelen och Microsoft 365**: om federerade eller icke-signerade hash-användare kommer att ändra sina lösen ord som har förfallit eller som inte har förfallit, skrivs lösen orden tillbaka till AD DS.
* **Har stöd för tillbakaskrivning av lösen ord när en administratör återställer dem från Azure Portal**: när en administratör återställer en användares lösen ord i [Azure Portal](https://portal.azure.com), skrivs lösen ordet tillbaka till lokalt. Den här funktionen stöds för närvarande inte i Office Admin-portalen.
* **Kräver inte några inkommande brand Väggs regler**: tillbakaskrivning av lösen ord använder ett Azure Service Bus relä som en underliggande kommunikations kanal. All kommunikation har utgående trafik via port 443.

> [!NOTE]
> Administratörs konton som finns i skyddade grupper i lokala AD kan användas med tillbakaskrivning av lösen ord. Administratörer kan ändra sitt lösen ord i molnet, men kan inte använda lösen ords återställning för att återställa ett bortglömt lösen ord. Mer information om skyddade grupper finns i [skyddade konton och grupper i AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Kom igång med SSPR tillbakaskrivning genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera tillbakaskrivning av lösen ord för självbetjäning (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Så fungerar tillbakaskrivning av lösenord

När en federerad eller lösenordsskyddad hash-användare försöker återställa eller ändra sitt lösen ord i molnet inträffar följande åtgärder:

1. En kontroll utförs för att se vilken typ av lösen ord som användaren har. Om lösen ordet hanteras lokalt:
   * En kontroll utförs för att se om tillbakaskrivning-tjänsten är igång och körs. Om det är det kan användaren fortsätta.
   * Om tillbakaskrivning-tjänsten inte är igång, informeras användaren om att lösen ordet inte kan återställas just nu.
1. Därefter skickar användaren lämpliga autentiseringsalgoritmer och når sidan **Återställ lösen ord** .
1. Användaren väljer ett nytt lösen ord och bekräftar det.
1. När användaren väljer **Skicka** krypteras lösen ordet i klartext med en symmetrisk nyckel som skapas under tillbakaskrivning-installationen.
1. Det krypterade lösen ordet ingår i en nytto last som skickas via en HTTPS-kanal till det klient-/regionsspecifika Service Bus-reläet (som har kon figurer ATS under installationen av tillbakaskrivning). Detta relä skyddas av ett slumpmässigt genererat lösen ord som bara en lokal installation känner till.
1. När meddelandet når Service Bus aktive ras slut punkten för lösen ords återställning automatiskt och ser att den har en återställnings förfrågan väntar.
1. Tjänsten söker sedan efter användaren med hjälp av molnets Anchor-attribut. För att sökningen ska lyckas måste följande villkor vara uppfyllda:

   * Användarobjektet måste finnas i AD DS Connector-utrymmet.
   * Användarobjektet måste vara länkat till motsvarande metaversum-objekt (MV).
   * Användarobjektet måste vara länkat till motsvarande Azure AD Connector-objekt.
   * Länken från AD DS Connector-objektet till MV måste ha synkroniseringsregeln `Microsoft.InfromADUserAccountEnabled.xxx` på länken.

   När anropet kommer från molnet använder Synkroniseringsmotorn attributet **cloudAnchor** för att leta upp Azure AD Connector Space-objektet. Sedan följer länken tillbaka till MV-objektet och följer sedan länken tillbaka till AD DS-objektet. Eftersom det kan finnas flera AD DS-objekt (flera skogar) för samma användare, är Synkroniseringsmotorn beroende av `Microsoft.InfromADUserAccountEnabled.xxx` länken för att välja rätt.

1. När användar kontot har hittats görs ett försök att återställa lösen ordet direkt i rätt AD DS-skog.
1. Om åtgärden för lösen ords uppsättning lyckas, uppmanas användaren att lösen ordet har ändrats.

   > [!NOTE]
   > Om användarens lösen ords-hash synkroniseras med Azure AD med hjälp av Lösenordssynkronisering, finns det en risk att den lokala lösen ords principen är svagare än moln lösen ords principen. I det här fallet tillämpas den lokala principen. Den här principen säkerställer att din lokala princip tillämpas i molnet, oavsett om du använder hash-synkronisering av lösen ord eller Federation för att tillhandahålla enkel inloggning.

1. Om lösen ords uppsättnings åtgärden Miss lyckas visas ett fel meddelande om att användaren försöker igen. Åtgärden kan Miss Miss av följande orsaker:
    * Tjänsten var avstängd.
    * Det lösen ord som de har valt uppfyller inte organisationens principer.
    * Det gick inte att hitta användaren i den lokala AD DS-miljön.

   Fel meddelandena ger vägledning för användarna så att de kan försöka lösa utan att administratören behöver göra något.

## <a name="password-writeback-security"></a>Säkerhet för tillbakaskrivning av lösen ord

Tillbakaskrivning av lösen ord är en mycket säker tjänst. För att se till att informationen är skyddad, aktive ras en säkerhets modell på fyra nivåer enligt följande:

* **Klient-Specific Service-Bus-relä**
   * När du konfigurerar tjänsten konfigureras klient organisationens Service Bus-relä som skyddas av ett slumpmässigt genererat starkt lösen ord som Microsoft aldrig har åtkomst till.
* **Låst, kryptografiskt stark kryptering nyckel för lösen ords kryptering**
   * När Service Bus-reläet har skapats skapas en stark symmetrisk nyckel som används för att kryptera lösen ordet när det kommer över kabeln. Den här nyckeln finns bara i företagets hemliga Arkiv i molnet, som är mycket låst och granskad, precis som andra lösen ord i katalogen.
* **Bransch standard Transport Layer Security (TLS)**
   1. När en lösen ords återställning eller ändrings åtgärd utförs i molnet krypteras lösen ordet i klartext med din offentliga nyckel.
   1. Det krypterade lösen ordet placeras i ett HTTPS-meddelande som skickas via en krypterad kanal genom att använda Microsoft TLS/SSL-certifikat till Service Bus-reläet.
   1. När meddelandet tas emot i Service Bus-tjänsten aktive ras och autentiseras den lokala agenten med hjälp av det starka lösen ord som genererades tidigare.
   1. Den lokala agenten hämtar det krypterade meddelandet och dekrypterar det med hjälp av den privata nyckeln.
   1. Den lokala agenten försöker ange lösen ordet via AD DS SetPassword-API: et. Det här steget är vad som tillåter att din lokala lösen ords princip för AD DS används (till exempel komplexitet, ålder, historik och filter) i molnet.
* **Principer för förfallo datum för meddelande**
   * Om meddelandet finns i Service Bus, eftersom den lokala tjänsten är nere, tids gränsen uppnåddes och tas bort efter några minuter. Timeout och borttagning av meddelandet ökar säkerheten ytterligare.

### <a name="password-writeback-encryption-details"></a>Krypterings information för tillbakaskrivning av lösen ord

När en användare har skickat en lösen ords återställning går återställnings förfrågan igenom flera krypterings steg innan den tas emot i din lokala miljö. Dessa krypterings steg säkerställer maximal tillförlitlighet och säkerhet för tjänsten. De beskrivs på följande sätt:

1. **Lösen ords kryptering med 2048-bitars RSA-nyckel**: när en användare skickar in ett lösen ord som ska skrivas tillbaka till lokalt, krypteras det skickade lösen ordet med en 2048-bitars RSA-nyckel.
1. **Kryptering på paket nivå med AES-GCM**: hela paketet, lösen ordet plus de metadata som krävs, krypteras med hjälp av AES-GCM. Den här krypteringen förhindrar att någon har direkt åtkomst till den underliggande Service Bus kanalen från att visa eller manipulera innehållet.
1. **All kommunikation sker över TLS/SSL**: all kommunikation med Service Bus sker i en SSL/TLS-kanal. Den här krypteringen skyddar innehållet från icke-auktoriserade tredje parter.
1. **Automatisk nyckel förnyelse var sjätte** månad: alla nycklar slås över var sjätte månad, eller varje gång en tillbakaskrivning av lösen ord inaktive ras och sedan aktive ras på Azure AD Connect, för att säkerställa maximal tjänst säkerhet och säkerhet.

### <a name="password-writeback-bandwidth-usage"></a>Bandbredds användning för tillbakaskrivning av lösen ord

Tillbakaskrivning av lösen ord är en tjänst med låg bandbredd som bara skickar förfrågningar tillbaka till den lokala agenten under följande omständigheter:

* Två meddelanden skickas när funktionen är aktive rad eller inaktive rad via Azure AD Connect.
* Ett meddelande skickas en gång var femte minut som en tjänst pulsslag så länge som tjänsten körs.
* Två meddelanden skickas varje gången ett nytt lösen ord skickas:
   * Det första meddelandet är en begäran att utföra åtgärden.
   * Det andra meddelandet innehåller resultatet av åtgärden och skickas i följande fall:
      * Varje gången skickas ett nytt lösen ord under en användares självbetjäning för återställning av lösen ord.
      * Varje gången ett nytt lösen ord skickas under en ändring av användar lösen ord.
      * Varje gången skickas ett nytt lösen ord under en administratörs initierad administratör för användar lösen ord (endast från Azures administrations portaler).

#### <a name="message-size-and-bandwidth-considerations"></a>Överväganden för meddelande storlek och bandbredd

Storleken på vart och ett av de meddelanden som beskrivs ovan är vanligt vis under 1 KB. Även under extrema inläsningar tar själva tjänsten för lösen ords tillbakaskrivning över några kilobit per sekund i bandbredden. Eftersom varje meddelande skickas i real tid, bara när det krävs av en lösen ords uppdaterings åtgärd och eftersom meddelande storleken är så liten, är bandbredds användningen för tillbakaskrivning för liten för att få en mätbar påverkan.

## <a name="supported-writeback-operations"></a>Tillbakaskrivning-åtgärder som stöds

Lösen ord skrivs tillbaka i alla följande situationer:

* **Slut användar åtgärder som stöds**
   * Valfri åtgärd för att ändra lösen ord för självbetjäning av egen användare.
   * Alla självbetjänings åtgärder för att ändra lösen ord för självbetjäning, till exempel förfallo datum för lösen ord.
   * Eventuell återställning av lösen ord för självbetjäning av användare som kommer från [portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com).

* **Administratörs åtgärder som stöds**
   * Valfri åtgärd för att ändra lösen ord för självbetjäning för administratörer.
   * Alla administratörs åtgärder för självbetjänings ändringar, till exempel lösen ordets giltighets tid.
   * Administratörs lösen ords återställning via självbetjäning som kommer från [portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com).
   * En administratör som initierat slut användar lösen ord Återställ från [Azure Portal](https://portal.azure.com).
   * All administratörs-initierad återställning av lösen ord för slutanvändare från [Microsoft Graph API beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Tillbakaskrivning-åtgärder som inte stöds

Lösen ord skrivs inte tillbaka i någon av följande situationer:

* **Slut användar åtgärder som inte stöds**
   * Alla slutanvändare återställer sina egna lösen ord med hjälp av PowerShell version 1, version 2 eller Microsoft Graph API.
* **Administratörs åtgärder som inte stöds**
   * All administratörs-initierad återställning av lösen ord för slutanvändare från PowerShell version 1, version 2 eller Microsoft Graph API ( [Microsoft Graph API beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) stöds).
   * All administratörs-initierad återställning av lösen ord för slutanvändare från [Microsoft 365 administrations Center](https://admin.microsoft.com).
   * Alla administratörer kan inte använda verktyget för återställning av lösen ord för att återställa sina egna lösen ord för tillbakaskrivning av lösen ord.

> [!WARNING]
> Om du använder kryss rutan "användaren måste byta lösen ord vid nästa inloggning" i lokala administrations verktyg för AD DS som Active Directory användare och datorer eller om Active Directory Administrationscenter stöds som en förhands gransknings funktion i Azure AD Connect. Mer information finns i [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nästa steg

Kom igång med SSPR tillbakaskrivning genom att följa de här självstudierna:

> [!div class="nextstepaction"]
> [Självstudie: Aktivera tillbakaskrivning av lösen ord för självbetjäning (SSPR)](./tutorial-enable-sspr-writeback.md)