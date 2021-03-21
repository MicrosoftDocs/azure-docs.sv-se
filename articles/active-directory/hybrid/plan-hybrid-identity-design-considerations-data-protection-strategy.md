---
title: Hybrid Identity design – data skydds strategi Azure | Microsoft Docs
description: Du definierar data skydds strategin för din hybrid identitets lösning för att uppfylla de affärs krav som du har definierat.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703860"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiera data skydds strategi för din hybrid identitets lösning
I den här uppgiften definierar du data skydds strategin för din hybrid identitets lösning som uppfyller de affärs krav som du definierade i:

* [Fastställa krav för data skydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Fastställa åtkomstkontrollkrav](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Fastställa krav för incidenthantering](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definiera alternativ för data skydd
Som förklaras i [fastställa krav för katalog synkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)kan Microsoft Azure AD synkroniseras med din lokala Active Directory Domain Services (AD DS). Med den här integrationen kan organisationer använda Azure AD för att verifiera användarnas autentiseringsuppgifter när de försöker komma åt företagets resurser. Du kan göra detta för båda scenarierna: data i vila lokalt och i molnet. Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst.

När autentiseringen har autentiserats läses User Principal Name (UPN) från autentiseringstoken. Sedan fastställer auktoriserings systemet den replikerade partitionen och behållaren som motsvarar användarens domän. Information om användarens existens, aktiverade tillstånd och roll hjälper auktoriserings systemet att avgöra om åtkomsten till mål klienten har behörighet för användaren i den sessionen. Vissa behöriga åtgärder (särskilt skapa användare och lösen ords återställning) skapa en Gransknings logg som en klient administratör använder för att hantera efterlevnad eller undersökningar av efterlevnad.

Det är inte alltid möjligt att flytta data från ditt lokala data Center till Azure Storage via en Internet anslutning på grund av data volym, bandbredds tillgänglighet eller andra överväganden. [Tjänsten Azure Storage import/export](../../import-export/storage-import-export-service.md) innehåller ett maskinvarubaserade alternativ för att placera/hämta stora mängder data i Blob Storage. Med den kan du skicka [BitLocker-krypterade](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) hård diskar direkt till ett Azure-datacenter där moln operatörer laddar upp innehållet till ditt lagrings konto, eller så kan de Ladda ned dina Azure-data till dina enheter för att komma tillbaka till dig. Endast krypterade diskar accepteras för den här processen (med en BitLocker-nyckel som genererats av själva tjänsten under jobb konfigurationen). BitLocker-nyckeln tillhandahålls till Azure separat, vilket ger en delning med out-of-band-nyckel.

Eftersom data som överförs kan ske i olika scenarier, är det också viktigt att veta att Microsoft Azure använder [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) för att isolera trafik trafik från varandra, som använder åtgärder som värd-och gäst brand väggar, IP-paketfiltrering, Port blockering och https-slutpunkter. De flesta av Azures interna kommunikation, inklusive infrastruktur-till-infrastruktur och infrastruktur till kund (lokalt), krypteras också. Ett annat viktigt scenario är kommunikationen i Azure-datacentren. Microsoft hanterar nätverk för att säkerställa att ingen virtuell dator kan personifiera eller eavesdrop på IP-adressen för en annan. TLS/SSL används vid åtkomst till Azure Storage-eller SQL-databaser eller vid anslutning till Cloud Services. I det här fallet ansvarar kund administratören för att erhålla ett TLS/SSL-certifikat och distribuera det till klient infrastrukturen. Data trafik som flyttas mellan Virtual Machines i samma distribution eller mellan klienter i en enda distribution via Microsoft Azure Virtual Network kan skyddas via krypterade kommunikations protokoll som HTTPS, SSL/TLS, eller andra.

Beroende på hur du besvarade frågorna i [fastställa data skydds krav](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)bör du kunna avgöra hur du vill skydda dina data och hur hybrid identitets lösningen kan hjälpa dig med den processen. I följande tabell visas de alternativ som stöds av Azure och som är tillgängliga för varje data skydds scenario.

| Alternativ för data skydd | I vila i molnet | I vila lokalt | Under överföring |
| --- | --- | --- | --- |
| BitLocker-diskkryptering |X |X | |
| SQL Server att kryptera databaser |X |X | |
| VM-till-VM-kryptering | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Läs mer om [funktionen](https://azure.microsoft.com/support/trust-center/services/) för att få mer information om de certifieringar som varje Azure-tjänst är kompatibel med i [Microsoft Azure säkerhets Center](https://azure.microsoft.com/support/trust-center/) .
> Eftersom alternativen för data skydd använder en Multilayer-metod, gäller inte jämförelsen mellan dessa alternativ för den här uppgiften. Se till att du utnyttjar alla alternativ som är tillgängliga för varje data tillstånd.
>
>

## <a name="define-content-management-options"></a>Definiera alternativ för innehålls hantering

En fördel med att använda Azure AD för att hantera en hybrid identitets infrastruktur är att processen är helt transparent från användarens perspektiv. Användaren försöker få åtkomst till en delad resurs, resursen kräver autentisering, användaren måste skicka en autentiseringsbegäran till Azure AD för att kunna hämta token och få åtkomst till resursen. Hela processen sker i bakgrunden, utan användar interaktion. 

Organisationer som är viktiga för data sekretess kräver vanligt vis data klassificering för lösningen. Om den aktuella lokala infrastrukturen redan använder data klassificering, är det möjligt att använda Azure AD som huvud databas för användarens identitet. Ett vanligt verktyg som används lokalt för data klassificering kallas för [data klassificerings verktyg](/previous-versions/tn-archive/hh204743(v=technet.10)) för Windows Server 2012 R2. Det här verktyget kan hjälpa till att identifiera, klassificera och skydda data på fil servrar i ditt privata moln. Du kan också använda [automatisk fil klassificering](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) i Windows Server 2012 för att utföra den här uppgiften.

Om din organisation inte har data klassificering på plats men behöver skydda känsliga filer utan att lägga till nya servrar lokalt, kan de använda Microsoft [Azure Rights Management-tjänsten](/azure/information-protection/what-is-azure-rms).  Azure RMS använder krypterings-, identitets-och Auktoriseringsprinciper för att skydda dina filer och e-postmeddelanden, och det fungerar på flera enheter – telefoner, surfplattor och datorer. Eftersom Azure RMS är en moln tjänst behöver du inte uttryckligen konfigurera förtroenden med andra organisationer innan du kan dela skyddat innehåll med dem. Om de redan har en Microsoft 365 eller en Azure AD-katalog, stöds samarbete mellan organisationer automatiskt. Du kan också synkronisera bara de katalogattribut som Azure RMS behöver ha stöd för en gemensam identitet för dina lokala Active Directory konton genom att använda Azure Active Directory Synchronization Services (Azure AD Sync) eller Azure AD Connect.

En viktig del av innehålls hanteringen är att förstå vem som har åtkomst till vilken resurs, och därför är en omfattande loggnings funktion viktig för identitets hanterings lösningen. Azure AD tillhandahåller logg över 30 dagar inklusive:

* Ändringar i roll medlemskap (t. ex. användaren har lagts till i rollen som global administratör)
* Uppdateringar av autentiseringsuppgifter (t. ex. lösen ords ändringar)
* Domän hantering (t. ex. att verifiera en anpassad domän, ta bort en domän)
* Lägga till eller ta bort program
* Användar hantering (t. ex. att lägga till, ta bort, uppdatera en användare)
* Lägga till eller ta bort licenser

> [!NOTE]
> Läs [Microsoft Azure säkerhet och granskning logg hantering](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) om du vill veta mer om loggnings funktioner i Azure.
> Beroende på hur du besvarade frågorna i [fastställa krav för innehålls hantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)bör du kunna avgöra hur du vill att innehållet ska hanteras i din hybrid identitets lösning. Även om alla alternativ som visas i tabell 6 kan integreras med Azure AD är det viktigt att definiera vilket som passar dina affärs behov bättre.
>
>

| Alternativ för innehålls hantering | Fördelar | Nackdelar |
| --- | --- | --- |
| Centraliserad lokal (Active Directory Rights Management Server) |Fullständig kontroll över Server infrastrukturen som ansvarar för att klassificera data <br> Inbyggd funktion i Windows Server, inget behov av extra licens eller prenumeration <br> Kan integreras med Azure AD i ett hybrid scenario <br> Stöder IRM-funktioner (Information Rights Management) i Microsoft Online Services, till exempel Exchange Online och SharePoint Online, samt Microsoft 365 <br> Stöder lokala Microsoft Server-produkter, till exempel Exchange Server, SharePoint Server och fil servrar som kör Windows Server och fil klassificerings infrastruktur (FCI). |Högre underhåll (Fortsätt med uppdateringar, konfiguration och potentiella uppgraderingar), eftersom den äger servern <br> Kräver en server infrastruktur lokalt<br> Utnyttjar inte Azure-funktionerna internt |
| Centraliserad i molnet (Azure RMS) |Enklare att hantera jämfört med den lokala lösningen <br> Kan integreras med AD DS i ett hybrid scenario <br>  Fullständigt integrerat med Azure AD <br> Kräver inte en lokal server för att distribuera tjänsten <br> Stöder lokala Microsoft Server-produkter, till exempel Exchange Server, SharePoint, server och fil servrar som kör Windows Server och fil klassificering, infrastruktur (FCI) <br> DEN kan ha fullständig kontroll över sin klients nyckel med BYOK-kapacitet. |Din organisation måste ha en moln prenumeration som stöder RMS <br> Din organisation måste ha en Azure AD-katalog för att stöda användarautentisering för RMS |
| Hybrid (Azure RMS integrerad med lokal Active Directory Rights Management-Server) |I det här scenariot ackumuleras fördelarna med både centraliserad lokal och i molnet. |Din organisation måste ha en moln prenumeration som stöder RMS <br> Din organisation måste ha en Azure AD-katalog för att stöda användarautentisering för RMS, <br> Kräver en anslutning mellan Azures moln tjänst och lokal infrastruktur |

## <a name="define-access-control-options"></a>Definiera alternativ för åtkomst kontroll
Genom att använda funktionerna för autentisering, auktorisering och åtkomst kontroll som är tillgängliga i Azure AD kan du göra det möjligt för företaget att använda en central identitets lagrings plats samtidigt som användare och partner kan använda enkel inloggning (SSO) som visas i följande bild:

![centraliserad hantering](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Centraliserad hantering och fullständig integrering med andra kataloger

Azure Active Directory ger enkel inloggning till tusentals SaaS-program och lokala webb program. Se [listan över Azure Active Directory Federations kompatibilitet: tredjeparts identitets leverantörer som kan användas för att implementera enkel inloggnings](how-to-connect-fed-compatibility.md) artikel för mer information om den SSO-tredje part som har testats av Microsoft. Den här funktionen gör det möjligt för organisationer att implementera en rad olika B2B-scenarier samtidigt som du har kontroll över identitets-och åtkomst hantering. Men under den B2B-design processen är det viktigt att förstå autentiseringsmetoden som används av partnern och validera om den här metoden stöds av Azure. För närvarande stöds följande metoder av Azure AD:

* Security Assertion Markup Language (SAML)
* Arbeta
* Kerberos
* Token
* Certifikat

> [!NOTE]
> Läs [Azure Active Directory autentiseringsprotokoll](/previous-versions/azure/dn151124(v=azure.100)) för att få mer information om varje protokoll och dess funktioner i Azure.
>
>

Med hjälp av Azure AD-supporten kan mobila företags program använda samma enkla Mobile Services-autentisering för att låta anställda logga in på sina mobil program med sina företags Active Directory autentiseringsuppgifter. Med den här funktionen stöds Azure AD som en identitetsprovider i Mobile Services tillsammans med andra identitets leverantörer som redan stöds (som omfattar Microsoft-konton, Facebook-ID, Google ID och Twitter-ID). Om de lokala apparna använder användarens autentiseringsuppgifter som finns på företagets AD DS bör åtkomsten från partner och användare som kommer från molnet vara transparent. Du kan hantera användarens villkorliga åtkomst kontroll till (molnbaserad) webb program, webb-API, Microsoft Cloud Services, SaaS-program från tredje part och interna (mobila) klient program, samt ha fördelarna med säkerhet, granskning och rapportering på ett och samma ställe. Vi rekommenderar dock att du validerar implementeringen i en miljö som inte är en produktions miljö eller ett begränsat antal användare.

> [!TIP]
> Det är viktigt att nämna att Azure AD inte har grupprincip som AD DS har. Du behöver en hanterings lösning för mobila enheter, till exempel [Microsoft Intune](/mem/intune/), för att kunna tillämpa principen för enheter.
>
>

När användaren har autentiserats med Azure AD är det viktigt att utvärdera åtkomst nivån som användaren har. Den åtkomst nivå som användaren har över en resurs kan variera. Även om Azure AD kan lägga till ett extra säkerhets lager genom att kontrol lera åtkomsten till vissa resurser, bör du tänka på att själva resursen också kan ha sin egen åtkomst kontrol lista separat, till exempel åtkomst kontrollen för filer som finns på en fil server. I följande bild sammanfattas de nivåer av åtkomst kontroll som du kan ha i ett hybrid scenario:

![åtkomst kontroll](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Varje interaktion i diagrammet som förtecknas i bild X representerar ett scenario för åtkomst kontroll som kan omfattas av Azure AD. Nedan har du en beskrivning av varje scenario:

1. Villkorlig åtkomst till program som finns lokalt: du kan använda registrerade enheter med åtkomst principer för program som har kon figurer ATS för att använda AD FS med Windows Server 2012 R2.

2. Access Control till Azure Portal: Azure låter dig också kontrol lera åtkomsten till portalen med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC)). Den här metoden gör det möjligt för företaget att begränsa antalet åtgärder som en enskild person kan göra i Azure Portal. Genom att använda Azure RBAC för att kontrol lera åtkomsten till portalen kan IT-administratörer Delegera åtkomst med hjälp av följande metoder för åtkomst hantering:

   - Gruppbaserad roll tilldelning: du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja de befintliga investeringar som din organisation har gjort i verktyg och processer för att hantera grupper. Du kan också använda funktionen delegerad grupp hantering i Azure AD Premium.
   - Använd inbyggda roller i Azure: du kan använda tre roller – ägare, deltagare och läsare för att säkerställa att användare och grupper har behörighet att bara utföra de uppgifter som de behöver för att utföra sina jobb.
   -  Detaljerad åtkomst till resurser: du kan tilldela roller till användare och grupper för en viss prenumeration, resurs grupp eller en enskild Azure-resurs, till exempel en webbplats eller databas. På så sätt kan du se till att användarna har åtkomst till alla resurser som de behöver och ingen åtkomst till resurser som de inte behöver hantera.

   > [!NOTE]
   > Om du skapar program och vill anpassa åtkomst kontrollen för dem, är det också möjligt att använda Azure AD-programroller för auktorisering. Gå igenom detta [webapp-RoleClaims-dotNet-exempel](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) på hur du skapar din app för att använda den här funktionen.


3. Villkorlig åtkomst för Microsoft 365 program med Microsoft Intune: IT-administratörer kan etablera enhets principer för villkorlig åtkomst för att skydda företags resurser, samtidigt som informations anställda på kompatibla enheter får åtkomst till tjänsterna. 
  
4. Villkorlig åtkomst för SaaS-appar: med [den här funktionen](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) kan du konfigurera åtkomst regler per program för Multi-Factor Authentication och möjlighet att blockera åtkomst för användare som inte är i ett betrott nätverk. Du kan använda Multi-Factor Authentication-regler för alla användare som är tilldelade till programmet eller bara för användare inom angivna säkerhets grupper. Användare kan undantas från kravet på Multi-Factor Authentication om de ansluter till programmet från en IP-adress som finns i i organisationens nätverk.

Eftersom alternativen för åtkomst kontroll använder en Multilayer-metod, gäller inte jämförelsen mellan dessa alternativ för den här uppgiften. Se till att du använder alla alternativ som är tillgängliga för varje scenario som kräver att du styr åtkomsten till dina resurser.

## <a name="define-incident-response-options"></a>Definiera alternativ för incident svar
Azure AD kan hjälpa IT att identifiera potentiella säkerhets risker i miljön genom att övervaka användarens aktivitet. DEN kan använda Azure AD-åtkomst och användnings rapporter för att få insyn i integriteten och säkerheten i din organisations katalog. Med den här informationen kan en IT-administratör bättre avgöra var möjliga säkerhets risker kan vara så att de kan planera för att minska riskerna.  [Azure AD Premium prenumerationen](../fundamentals/active-directory-get-started-premium.md) har en uppsättning säkerhets rapporter som kan användas för att hämta den här informationen. [Azure AD-rapporter](../reports-monitoring/overview-reports.md) kategoriseras enligt följande:

* **Avvikelse rapporter**: innehåller inloggnings händelser som påträffades som avvikande. Målet är att du ska känna till sådan aktivitet och gör att du kan bestämma om en händelse är misstänkt.
* **Rapport om integrerade program**: ger insikter om hur moln program används i din organisation. Azure Active Directory erbjuder integrering med tusentals moln program.
* **Fel rapporter**: ange fel som kan uppstå när du konfigurerar konton till externa program.
* **Användarspecifika rapporter**: Visa enhets-/inloggnings aktivitets data för en enskild användare.
* **Aktivitets loggar**: innehåller en lista över alla granskade händelser under de senaste 24 timmarna, senaste 7 dagarna eller de senaste 30 dagarna, samt ändringar i grupp aktiviteten, återställning av lösen ord och registrerings aktivitet.

> [!TIP]
> En annan rapport som också kan hjälpa till med incident hanterings teamet som arbetar på ett ärende är [användaren med läckta autentiseringsuppgifter](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) . Den här rapporten visar alla matchningar mellan de läckta autentiseringsuppgifterna och din klient.
>


Andra viktiga inbyggda rapporter i Azure AD som kan användas vid undersökning av incident svar och är:

* **Aktivitet för lösen ords återställning**: ge administratören en insikt om hur aktivt lösen ords återställning används i organisationen.
* **Registrerings aktivitet för lösen ords återställning**: ger insikter om vilka användare som har registrerat sina metoder för lösen ords återställning och vilka metoder de har valt.
* **Grupp aktivitet**: innehåller en historik över ändringar i gruppen (t. ex. användare som lagts till eller tagits bort) som initierades i åtkomst panelen.

Förutom kärn rapporterings funktionen i Azure AD Premium som du kan använda under en undersökning av incident svar, kan den också dra nytta av gransknings rapporten för att få information som:

* Ändringar i roll medlemskap (till exempel användare som har lagts till i rollen global administratör)
* Uppdateringar av autentiseringsuppgifter (till exempel lösen ords ändringar)
* Domän hantering (till exempel verifiera en anpassad domän, ta bort en domän)
* Lägga till eller ta bort program
* Användar hantering (till exempel lägga till, ta bort, uppdatera en användare)
* Lägga till eller ta bort licenser

Eftersom alternativen för incident svar använder en Multilayer-metod, gäller inte jämförelsen mellan dessa alternativ för den här uppgiften. Se till att du använder alla alternativ som är tillgängliga för varje scenario som kräver att du använder Azure AD repor ting-funktioner som en del av ditt företags incident svars process.

## <a name="next-steps"></a>Nästa steg
[Fastställa uppgifter för Hybrid identitets hantering](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Se även
[Översikt över design överväganden](plan-hybrid-identity-design-considerations-overview.md)