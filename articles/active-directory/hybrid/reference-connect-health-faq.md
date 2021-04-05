---
title: Azure Active Directory Connect Health vanliga frågor och svar – Azure | Microsoft Docs
description: Detta vanliga frågor och svar om Azure AD Connect Health. Avsnittet ger svar på frågor om hur du använder tjänsten, inklusive faktureringsmodellen, funktioner, begränsningar och support.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368004"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Vanliga frågor och svar om Azure AD Connect Health
Den här artikeln innehåller svar på vanliga frågor och svar om Azure Active Directory (Azure AD) Connect Health. Vanliga frågor och svar om hur du använder tjänsten, inklusive fakturerings modellen, funktioner, begränsningar och support.

## <a name="general-questions"></a>Allmänna frågor
**F: jag hanterar flera Azure AD-kataloger. Hur gör jag för att växlar till den som har Azure Active Directory Premium?**

Om du vill växla mellan olika Azure AD-klienter väljer du det för tillfället inloggade **användar namnet** i det övre högra hörnet och väljer sedan lämpligt konto. Om kontot inte visas här väljer du **Logga ut** och använder sedan den globala administratörs behörigheten för den katalog som har Azure Active Directory Premium (P1 eller P2) som är aktive rad för att logga in.

**F: vilken version av identitets roller som stöds av Azure AD Connect Health?**

I följande tabell visas de roller och operativ system versioner som stöds.

|Roll| Operativ system/version|
|--|--|
|Active Directory Federation Services (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Version 1.0.9125 eller högre|
|Active Directory Domain Services (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Windows Server Core-installationer stöds inte.

Observera att de funktioner som tillhandahålls av tjänsten kan variera beroende på roll och operativ system. Med andra ord kanske inte alla funktioner är tillgängliga för alla versioner av operativ systemet. Mer information finns i funktions beskrivningarna.

**F: hur många licenser behöver jag för att övervaka min infrastruktur?**

* Den första Connect Health-agenten kräver minst en licens för Azure AD Premium (P1 eller P2).
* För alla ytterligare registrerade agenter krävs 25 ytterligare Azure AD Premium-licenser (P1 eller P2).
* Antalet agenter motsvarar det totala antalet agenter som har registrerats för alla övervakade roller (AD FS, Azure AD Connect och/eller AD DS).
* AAD Connect Health Licensing kräver inte att du tilldelar licensen till vissa användare. Du behöver bara ha det nödvändiga antalet giltiga licenser.

Licensierings information finns också på [prissättnings sidan för Azure AD](https://aka.ms/aadpricing).

Exempel:

| Registrerade agenter | Licenser som krävs | Exempel på övervaknings konfiguration |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect Server |
| 2 | 26| 1 Azure AD Connect Server och 1 domänkontrollant |
| 3 | 51 | 1 Active Directory Federation Services (AD FS) (AD FS) Server, 1 AD FS proxy och 1 domänkontrollant |
| 4 | 76 | 1 AD FS server, 1 AD FS proxy och 2 domänkontrollanter |
| 5 | 101 | 1 Azure AD Connect Server, 1 AD FS server, 1 AD FS proxy och 2 domänkontrollanter |

**F: stöder Azure AD Connect Health Azure Germany-molnet?**

Azure AD Connect Health stöds inte i Tyskland-molnet, förutom [rapport funktionen sync-fel](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Roller | Funktioner | Stöds i tyskt moln |
| ------ | --------------- | --- |
| Anslut hälsa för synkronisering | Övervakning/Insight/Alerts/analys | Inga |
|  | Fel rapport för synkronisering | Ja |
| Anslut hälsa för ADFS | Övervakning/Insight/Alerts/analys | Inga |
| Anslut hälsa för tillägg | Övervakning/Insight/Alerts/analys | Inga |

Konfigurera [installations kravet](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) enligt detta för att säkerställa att agent anslutningen för Connect Health för synkronisering är korrekt.

## <a name="installation-questions"></a>Installations frågor

**F: Vad är effekten av att installera Azure AD Connect Health-agenten på enskilda servrar?**

Effekten av att installera Microsoft Azure AD Connect Health Agent, AD FS, Webbprogramproxy, Azure AD Connect (Sync)-servrar, domän kontrol Lanterna är minimal med avseende på CPU, minnes användning, nätverks bandbredd och lagring.

Följande tal är en uppskattning:

* CPU-förbrukning: ~ 1-5% ökning.
* Minnes förbrukning: upp till 10% av det totala system minnet.

> [!NOTE]
> Om agenten inte kan kommunicera med Azure lagrar agenten data lokalt för den definierade Max gränsen. Agenten skriver över "cachelagrade" data på grund av "minst nyligen servad".
>
>

* Local buffer Storage för Azure AD Connect Health agenter: ~ 20 MB.
* För AD FS-servrar rekommenderar vi att du etablerar ett disk utrymme på 1 024 MB (1 GB) för AD FS gransknings kanal för Azure AD Connect Health agenter för att bearbeta alla gransknings data innan de skrivs över.

**F: kommer jag att behöva starta om mina servrar under installationen av Azure AD Connect Health agenterna?**

Nej. Installationen av agenterna kräver inte att du startar om servern. Installationen av vissa nödvändiga steg kan dock kräva en omstart av-servern.

På Windows Server 2008 R2 kräver till exempel installationen av .NET 4,5 Framework att servern startas om.

**F: fungerar Azure AD Connect Health via en genom strömnings-HTTP-proxy?**

Ja. För pågående åtgärder kan du konfigurera hälso agenten så att den använder en HTTP-proxy för att vidarebefordra utgående HTTP-begäranden.
Läs mer om hur du [konfigurerar HTTP-proxy för hälso agenter](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Om du behöver konfigurera en proxy under agent registreringen kan du behöva ändra proxyinställningarna i Internet Explorer i förväg.

1. Öppna Internet Explorer > **Inställningar**  >  **Internet alternativ**  >  **anslutningar**  >  **LAN-inställningar**.
2. Välj **Använd en proxyserver för ditt lokala nätverk**.
3. Välj **Avancerat** om du har olika proxy-portar för http och https/Secure.

**F: stöder Azure AD Connect Health grundläggande autentisering vid anslutning till HTTP-proxyservrar?**

Nej. För närvarande stöds inte en mekanism för att ange ett godtyckligt användar namn och lösen ord för grundläggande autentisering.

**F: vilka brand Väggs portar måste jag öppna för att Azure AD Connect Health agenten ska fungera?**

I [avsnittet krav](how-to-connect-health-agent-install.md#requirements) finns en lista över brand Väggs portar och andra anslutnings krav.

**F: Varför visas två servrar med samma namn i Azure AD Connect Health-portalen?**

När du tar bort en agent från en server tas inte servern bort automatiskt från Azure AD Connect Health-portalen. Om du manuellt tar bort en agent från en server eller tar bort själva servern, måste du ta bort Server posten manuellt från Azure AD Connect Health-portalen.

Du kan skapa en ny avbildning av en server eller skapa en ny server med samma information (till exempel dator namn). Om du inte har tagit bort den redan registrerade servern från Azure AD Connect Health-portalen och du har installerat agenten på den nya servern kan du se två poster med samma namn.

Ta i så fall bort posten som tillhör den äldre servern manuellt. Data för den här servern måste vara inaktuella.

**F: kan jag installera Azure AD Connect hälso agenten på Windows Server Core?**

Nej.  Det finns inte stöd för installation på Server Core.

## <a name="health-agent-registration-and-data-freshness"></a>Registrering och data aktualitet för hälso agenten

**F: Vad är vanliga orsaker till hälso agentens registrerings fel och hur felsöker jag problem?**

Hälso agenten kan inte registreras på grund av följande möjliga orsaker:

* Agenten kan inte kommunicera med nödvändiga slut punkter eftersom en brand vägg blockerar trafik. Detta är särskilt vanligt på Web Application Proxy-servrar. Kontrol lera att du har tillåtit utgående kommunikation till nödvändiga slut punkter och portar. Mer information finns i [avsnittet om krav](how-to-connect-health-agent-install.md#requirements) .
* Utgående kommunikation utsätts för en TLS-kontroll av nätverks lagret. Detta gör att certifikatet som agenten använder ersätts av inspektions servern/entiteten och att de steg som krävs för att slutföra Agent registreringen Miss lyckas.
* Användaren har inte åtkomst för att utföra registreringen av agenten. Globala administratörer har åtkomst som standard. Du kan använda [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) för att delegera åtkomst till andra användare.

**F: Jag får ett meddelande om att "Hälsotjänst data inte är aktuella." Hur gör jag för att felsöka problemet?**

Azure AD Connect Health genererar aviseringen när den inte tar emot alla data punkter från servern under de senaste två timmarna. [Läs mer](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Frågor om åtgärder
**F: behöver jag aktivera granskning på Web Application Proxy-servrar?**

Nej, granskning behöver inte vara aktive rad på webbappens proxyservrar.

**F: Hur kommer Azure AD Connect Health-aviseringar att lösas?**

Azure AD Connect Health aviseringar löses enligt ett lyckat tillstånd. Azure AD Connect Health agenter identifierar och rapporterar uppgångs villkoren till tjänsten regelbundet. Under vissa aviseringar är under tryckningen tidsbaserad. Om samma fel villkor inte observeras inom 72 timmar från genereringen av aviseringen, löses aviseringen automatiskt.

**F: Jag får ett meddelande om att "begäran om att testa autentisering (syntetisk transaktion) misslyckades med att hämta en token." Hur gör jag för att felsöka problemet?**

Azure AD Connect Health för AD FS genererar den här aviseringen när hälso agenten som är installerad på en AD FS server inte kan hämta en token som en del av en syntetisk transaktion som initieras av hälso agenten. Hälso agenten använder den lokala system kontexten och försöker hämta en token för en egen förlitande part. Detta är ett test för att se till att AD FS är i ett tillstånd för utfärdande av tokens.

Oftast Miss lyckas det här testet eftersom hälso agenten inte kan matcha AD FS server gruppens namn. Detta kan inträffa om AD FS-servrarna ligger bakom en utjämning av nätverks belastning och begäran initieras från en nod som ligger bakom belastningsutjämnaren (i stället för en vanlig klient som är framför belastningsutjämnaren). Detta kan åtgärdas genom att uppdatera filen "hosts" som finns under "C:\Windows\System32\drivers\etc" för att inkludera IP-adressen för AD FS-servern eller en loopback-IP-adress (127.0.0.1) för AD FS gruppens namn (t. ex. sts.contoso.com). Om du lägger till värd filen blir det kort för nätverks anropet, vilket innebär att hälso agenten kan hämta token.

**F: Jag fick ett e-postmeddelande som anger att mina datorer inte har uppdaterats för de senaste utpressnings angrepp. Varför fick jag det här e-postmeddelandet?**

Azure AD Connect Health tjänsten genomsöker alla datorer som den övervakar för att se till att de nödvändiga korrigeringarna har installerats. E-postmeddelandet skickades till klient organisationens administratörer om minst en dator inte hade de kritiska korrigeringarna. Följande logik användes för att göra den här beräkningen.
1. Hitta alla snabb korrigeringar som är installerade på datorn.
2. Kontrol lera om minst en av snabb korrigeringarna från den definierade listan finns.
3. Om ja, skyddas datorn. Om inte, är datorn utsatt för angrepp.

Du kan använda följande PowerShell-skript för att utföra den här kontrollen manuellt. Den implementerar ovanstående logik.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**F: Varför kan PowerShell-cmdleten <i>Get-MsolDirSyncProvisioningError</i> Visa mindre synkroniseringsfel i resultatet?**

<i>Get-MsolDirSyncProvisioningError</i> returnerar bara DirSync-etablerings fel. Förutom att Anslut hälso portalen visar även andra synkroniseringsfel, till exempel export fel. Detta är konsekvent med Azure AD Connect delta-resultatet. Läs mer om [Azure AD Connect synkroniseringsfel](./tshoot-connect-sync-errors.md).

**F: Varför genereras inte AD FS-granskningar?**

Använd PowerShell-cmdleten <i>Get-ADFSProperties-AuditLevel</i> för att säkerställa att gransknings loggar inte är inaktiverat. Läs mer om [AD FS gransknings loggar](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Observera att om det finns avancerade gransknings inställningar som skickas till ADFS-servern skrivs eventuella ändringar med auditpol.exe över (händelse om programmet som genereras inte har kon figurer ATS). I det här fallet ställer du in den lokala säkerhets principen för att logga program som skapats och lyckats.

**F: När blir agent certifikatet automatiskt förnyat innan det upphör att gälla?**
Agent certifieringen kommer att förnyas automatiskt **6 månader** innan dess förfallo datum. Om den inte förnyas bör du kontrol lera att agentens nätverks anslutning är stabil. Du kan även lösa problemet genom att starta om Agent tjänsterna eller uppdatera till den senaste versionen.



## <a name="related-links"></a>Relaterade länkar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent installation](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health åtgärder](how-to-connect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
