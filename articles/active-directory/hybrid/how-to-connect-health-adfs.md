---
title: Använda Azure AD Connect Health med AD FS | Microsoft Docs
description: Den här sidan handlar om Azure AD Connect Health och hur du övervakar den lokala AD FS-infrastrukturen.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
editor: curtand
ms.assetid: dc0e53d8-403e-462a-9543-164eaa7dd8b3
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fdf202cb9bcacee94c83578432f7a399f90a0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306284"
---
# <a name="monitor-ad-fs-using-azure-ad-connect-health"></a>Övervaka AD FS med Azure AD Connect Health
Följande dokumentation gäller specifikt för övervakningen av AD FS-infrastrukturen med Azure AD Connect Health. Information om övervakning Azure AD Connect (Sync) med Azure AD Connect Health finns i [använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md). Information om övervakning av Active Directory Domain Services med Azure AD Connect Health finns dessutom i [använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>AD FS-aviseringar
Avsnittet om Azure AD Connect Health-aviseringar innehåller en lista över aktiva aviseringar. För varje avisering finns relevant information, lösningssteg och länkar till relaterad dokumentation.

Om du dubbelklickar en aktiv eller åtgärdad avisering visas ett nytt blad med ytterligare information, samt de steg som du kan utföra för att åtgärda aviseringen liksom länkar till relevant dokumentation. Du kan också visa historiska data för tidigare åtgärdade aviseringar.

![Skärm bild som visar sidan med Azure AD Connect Health "aviseringar" med en avisering markerad och fönstret "varnings information" visas.](./media/how-to-connect-health-adfs/alert2.png)

## <a name="usage-analytics-for-ad-fs"></a>Användningsanalys för AD FS
Azure AD Connect Health-användningsanalys analyserar autentiseringstrafiken på dina federationsservrar. Om du dubbelklickar på kryssrutan för användningsanalys öppnas bladet för användningsanalys, som visar flera mått och grupperingar.

> [!NOTE]
> Om du ska kunna använda användningsanalys med AD FS måste AD FS-granskning vara aktiverat. Mer information finns i [Aktivera granskning för AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs).
>
>

![Skärm bild som visar sidan för Azure AD Connect Health "användnings analys".](./media/how-to-connect-health-adfs/report1.png)

Om du vill välja fler mått, ange ett tidsintervall eller ändra grupperingen högerklickar du på diagrammet med användningsanalysen och väljer Redigera diagram. Sedan kan du ange tidsintervallet, välja ett annat mått och ändra grupperingen. Du kan visa autentiseringstrafikens fördelning baserat på olika ”mått” och gruppera varje mått med relevanta ”gruppera efter”-parametrar som beskrivs i följande avsnitt:

**Mått: Totalt antal förfrågningar**: Totalt antal förfrågningar som bearbetas av AD FS-servrarna.

|Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| --- | --- |
| Alla | Visar det totala antalet begäranden som bearbetas av alla AD FS-servrar.|
| Program | Grupperar antalet förfrågningar baserat på den förlitande parten i fråga. Den här grupperingen är användbar för att förstå vilket program som tar emot en viss procentandel av den totala trafiken. |
|  Server |Grupperar antalet förfrågningar baserat på servern som bearbetade förfrågan. Den här grupperingen är användbar för att förstå belastningsfördelningen av den totala trafiken.
| Anslut till arbetsplats |Grupperar antalet förfrågningar baserat på om förfrågningarna kommer från arbetsplatsanslutna enheter (kända). Den här grupperingen är användbar för att förstå om dina resurser används med enheter som är okända för identitetsinfrastrukturen. |
|  Autentiseringsmetod | Grupperar förfrågningarna baserat på den autentiseringsmetod som används för autentisering. Den här grupperingen är användbar för att förstå den gemensamma autentiseringsmetoden som används för autentisering. Följande är möjliga autentiseringsmetoder <ol> <li>Windows-integrerad autentisering (Windows)</li> <li>Formulärbaserad autentisering (formulär)</li> <li>Enkel inloggning (SSO, Single Sign On)</li> <li>X509-certifikatautentisering (certifikat)</li> <br>Observera att en begäran räknas som en enkel inloggning (SSO) om federationsservrarna tar emot förfrågan med en SSO-cookie. I så fall, om cookien är giltig, uppmanas inte användaren att ange autentiseringsuppgifter och får sömlös åtkomst till programmet. Detta är vanligt om du har flera förlitande parter som skyddas av federationsservrar. |
| Nätverksplats | Grupperar förfrågningarna baserat på användarens nätverksplats. Platsen kan vara antingen intranät eller extranät. Den här grupperingen är användbar för att vet vilken procentandel av trafiken som kommer från intranätet och extranätet. |


**Mått: Totalt antal misslyckande begäranden**: Totalt antal misslyckade begäranden som bearbetas av federationstjänsten. (Det här måttet är bara tillgängligt på AD FS för Windows Server 2012 R2)

|Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| --- | --- |
| Feltyp | Visar antalet fel baserat på fördefinierade feltyper. Den här grupperingen är användbar för att förstå de vanliga typerna av fel. <ul><li>Felaktigt användarnamn eller lösenord: Fel som beror på ett felaktigt användarnamn eller lösenord.</li> <li>”Utelåsning från extranät”: Fel som beror på att förfrågningarna togs emot från en användare som var utelåst från extranätet </li><li> ”Lösenordet har upphört att gälla”: Fel som beror på att användaren loggar in med ett lösenord som upphört att gälla.</li><li>”Inaktiverat konto”: Fel som beror på att användaren loggar in med ett inaktiverat konto.</li><li>”Enhetsautentisering”: Fel som beror på att användaren inte kan autentiseras med enhetsautentisering.</li><li>”Autentisering med användarcertifikat”: Fel som beror på att användaren inte kan autentiseras på grund av ett ogiltigt certifikat.</li><li>”MFA”: Fel som beror på att användaren inte kan autentiseras med hjälp av Multi-Factor Authentication.</li><li>”Andra autentiseringsuppgifter”: ”Utfärdandeauktorisering”: Fel som beror på auktoriseringsproblem.</li><li>”Utfärdandedelegering”: Fel som beror på utfärdandedelegeringsproblem.</li><li>”Tokengodkännande”: Fel som beror på att ADFS avvisar token från en tredjeparts identitetsprovider.</li><li>”Protokoll”: Fel som beror på protokollproblem.</li><li>”Okänt”: Samla in allt. Andra fel som inte passar i de angivna kategorierna.</li> |
| Server | Grupperar felen baserat på servern. Denna gruppering är användbar för att förstå felfördelningen mellan servrar. En ojämn fördelning kan vara ett tecken på att en server har felaktigt tillstånd. |
| Nätverksplats | Grupperar felen baserat på förfrågningarnas nätverksplats (intranät eller extranät). Denna gruppering är användbar för att förstå vilken typ av förfrågningar som misslyckas. |
|  Program | Grupperar felen baserat på målprogrammet (förlitande part). Denna gruppering är användbar för att förstå vilket program som har flest antal fel. |

**Mått: Antal användare** – Det genomsnittliga antalet unika användare som aktivt autentiserar med AD FS

|Gruppera efter | Vad grupperingen betyder och varför den är användbar? |
| --- | --- |
|Alla |Detta mått visar genomsnittligt antal användare som använder federationstjänsten under det valda tidsintervallet. Användarna grupperas inte. <br>Det genomsnittliga antalet beror på det valda tidsintervallet. |
| Program |Grupperar det genomsnittliga antalet användare baserat på målprogrammet (förlitande part). Denna gruppering är användbar för att förstå hur många användare som använder olika program. |

## <a name="performance-monitoring-for-ad-fs"></a>Prestandaövervakning för AD FS
Azure AD Connect Health-prestandaövervakning tillhandahåller övervakningsinformation för mått. Om du markerar rutan Övervakning öppnas ett blad med detaljerad information om måtten.

![Skärm bild som visar sidan för Azure AD Connect Health prestanda övervakning.](./media/how-to-connect-health-adfs/perf1.png)

Om du väljer filteralternativet längst upp i bladet kan du filtrera efter server och visa måtten för enskilda servrar. Om du vill ändra måttet högerklickar du i övervakningsdiagrammet under övervakningsbladet och väljer Redigera diagram (eller klickar på Redigera diagram). Från det nya bladet som öppnas kan du sedan välja ytterligare mått i listrutan och ange ett tidsintervall som du vill visa prestandadata för.

## <a name="top-50-users-with-failed-usernamepassword-logins"></a>De 50 användarna med flest misslyckade inloggningar med användarnamn/lösenord
Ett av de vanligaste skälen till en misslyckad autentiseringsbegäran på en AD FS-server är en begäran med ogiltiga autentiseringsuppgifter, dvs. ett felaktigt användarnamn eller lösenord. Påverkar vanligtvis användare på grund av komplexa lösenord, glömt lösenord eller skrivfel.

Men det finns andra skäl till ett oväntat antal begäranden som hanteras av AD FS-servrar, exempelvis: ett program som lagrar användarens autentiseringsuppgifter i cacheminnet och autentiseringsuppgifterna upphör att gälla eller en obehörig användare som försöker logga in på ett konto med ett antal välkända lösenord. Dessa två exempel är giltiga skäl som kan leda till en ökning i begäranden.

Azure AD Connect Health för AD FS tillhandahåller en rapport över de 50 användarna med flest misslyckade inloggningsförsök på grund av ett ogiltigt användarnamn eller lösenord. Rapporten skapas genom att bearbeta granskningshändelserna som genereras av alla AD FS-servrar i servergrupperna.

![Skärm bild som visar avsnittet "rapporter" med antalet felaktiga lösen ords försök från de senaste 30 dagarna.](./media/how-to-connect-health-adfs/report1a.png)

I den här rapporten kan du enkelt få tillgång till följande typer av information:

* Totalt antal misslyckade begäranden med fel användarnamn/lösenord de senaste 30 dagarna
* Genomsnittligt antal användare som misslyckades med inloggningen på grund av ett felaktigt användarnamn/lösenord varje dag.

Om du klickar på den här delen kommer du till huvudbladet för rapporter som innehåller ytterligare information. Det här bladet innehåller ett diagram med trendinformation för att skapa en baslinje om begäranden med felaktigt användarnamn eller lösenord. Dessutom skapar den en lista med över upp 50 användare med antalet misslyckade försök under den gångna veckan. Observera att de 50 översta användarna från den senaste veckan kan hjälpa dig att identifiera ökningar i antalet felaktiga lösenord.  

Diagrammet innehåller följande information:

* Totalt antal misslyckade inloggningar på grund av ett felaktigt användarnamn/lösenord per dag.
* Totalt antal unika användare med misslyckade inloggningar per dag.
* Klientens IP-adress för senaste begäran

![Azure AD Connect Health-portalen](./media/how-to-connect-health-adfs/report3a.png)

Rapporten innehåller följande information:

| Rapportobjekt | Beskrivning |
| --- | --- |
| Användar-ID |Visar användar-ID:t som användes. Det här värdet är vad användaren har angett, vilket i vissa fall är fel användar-ID. |
| Misslyckade försök |Visar totalt antal misslyckade försök för det specifika användar-ID:t. Tabellen sorteras med flest antal misslyckade försök i fallande ordning. |
| Senaste fel |Visar tidsstämpeln då det senaste felet uppstod. |
| IP-adress för senaste fel |Visar klient-IP-adress från senaste felaktiga begäran. Om du ser flera IP-adresser i det här värdet kan det omfatta klientens IP-adress för vidarebefordran tillsammans med IP-adressen för användarens senaste begärandeförsök.  |

> [!NOTE]
> Den här rapporten uppdateras automatiskt var tolfte timme med den nya informationen som samlats in inom den tidsperioden. Därför kanske inloggningsförsök under de senaste 12 timmarna inte finns med i rapporten.

## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Riskfylld IP-rapport](how-to-connect-health-adfs-risky-ip.md)