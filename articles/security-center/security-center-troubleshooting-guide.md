---
title: Felsökningsguide för Azure Security Center | Microsoft Docs
description: Den här guiden är för IT-proffs, säkerhetsanalytiker och moln administratörer som behöver felsöka Azure Security Center relaterade problem.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 20241ad316da1c5d713617f3f371d02e2a4e6cc9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570820"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Felsökningsguide för Azure Security Center

Den här guiden riktar sig till de som arbetar med IT, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Security Center och behöver felsöka Security Center-relaterade problem.

Security Center använder Log Analytics-agenten för att samla in och lagra data. Mer information finns under [plattformsmigrering i Azure Security Center](./security-center-enable-data-collection.md). Informationen i den här artikeln representerar Security Center funktioner efter över gången till Log Analytics agenten.

## <a name="troubleshooting-guide"></a>Felsökningsguide

I den här guiden förklaras hur du felsöker Security Center-relaterade problem.

Aviserings typer:

* Virtual Machine Behavioral Analysis (VMBA)
* Nätverksanalys
* Analys av SQL Database och Azure Synapse Analytics
* Sammanhangsbaserad information

Beroende på aviseringstypen kan kunderna hämta den information som krävs för att undersöka aviseringen med hjälp av följande resurser:

* Säkerhetsloggarna i händelsevisaren för virtuell dator i Windows
* AuditD i Linux
* Azure-aktivitetsloggarna och aktivera diagnostikloggar på angreppsresursen.

Kunder kan dela feedback om aviseringens beskrivning och relevans. Gå till själva aviseringen och välj knappen **Hade du nytta av detta?**. Välj orsak och ange en kommentar för att förklara din feedback. Vi övervakar ständigt den här feedbackkanalen för att förbättra våra aviseringar.

## <a name="audit-log"></a>Granskningslogg

Den mesta felsökningen i Security Center kommer att ske genom att först granska [Granskningslogg](../azure-monitor/essentials/platform-logs-overview.md)-posterna för den felaktiga komponenten. Via granskningsloggarna kan du fastställa:

* Vilka åtgärder som har vidtagits
* Vem som initierade åtgärden
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Granskningsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) som utförs på dina resurser, men omfattar inte läsåtgärder (GET).

## <a name="log-analytics-agent"></a>Log Analytics-agent

Security Center använder Log Analytics agent – det här är samma agent som används av Azure Monitor tjänsten – för att samla in säkerhets data från dina virtuella Azure-datorer. När datainsamling är aktiverat och agenten är korrekt installerad i måldatorn, ska de här processerna köras:

* HealthService.exe

Om du öppnar tjänst hanterings konsolen (Services. msc) visas även Log Analytics Agent tjänsten som körs enligt nedan:

![Tjänster](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Om du vill se vilken version av agenten du har öppnar du **aktivitets hanteraren**, på fliken **processer** , letar upp **tjänsten Log Analytics agent** och högerklickar på den och klickar på **Egenskaper**. Filversionen visas på fliken **Information** enligt nedan:

![Fil](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Installations scenarier för Log Analytics agent

Det finns två installations scenarier som kan ge olika resultat när du installerar Log Analytics-agenten på datorn. Scenarier som stöds är:

* **Agent som installeras automatiskt av Security Center**: i det här scenariot kommer du att kunna visa aviseringarna på båda platser, Security Center och Loggsökning. Du får e-postaviseringar till den e-postadress som har kon figurer ATS i säkerhets principen för den prenumeration som resursen tillhör.

* **Agent installeras manuellt på en virtuell dator som finns i Azure**: i det här scenariot kan du, om du använder agenter som har hämtats och installerats manuellt före februari 2017, bara Visa aviseringarna i Security Center portal om du filtrerar på prenumerationen som arbets ytan tillhör. Om du filtrerar på prenumerationen som resursen tillhör visas inga aviseringar. Du får e-postaviseringar till den e-postadress som har kon figurer ATS i säkerhets principen för prenumerationen som arbets ytan tillhör.

> [!NOTE]
> Kontrollera att du laddar ned den senaste versionen av agenten för att undvika det andra scenariot.

## <a name="monitoring-agent-health-issues"></a>Övervaka problem med hälsotillstånd <a name="mon-agent"></a>

**Övervakningstillstånd** definierar anledningen till att Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk etablering. I följande tabell visas steg för värden, beskrivningar och lösningar för **övervakningstillstånd**.

| Övervakningstillstånd | Description | Lösningssteg |
|---|---|---|
| Väntande agentinstallation | Installationen av Log Analytics-agenten körs fortfarande.  Installationen kan ta upp till några timmar. | Vänta tills den automatiska installationen är slutförd. |
| Energisparläge inaktiverat | Den virtuella datorn har stoppats.  Log Analytics agenten kan bara installeras på en virtuell dator som kör. | Starta om den virtuella datorn. |
| Azure VM-agent saknas eller är ogiltig | Log Analytics agent har inte installerats ännu.  En giltig agent för virtuella Azure-datorer krävs för att Security Center ska installera tillägget. | Installera, installera om eller uppgradera agenten för virtuella Azure-datorer på den virtuella datorn. |
| VM-status inte är redo för installation  | Log Analytics agent har inte installerats ännu eftersom den virtuella datorn inte är redo för installation. Den virtuella datorn är inte redo för installation på grund av ett problem med VM-agenten eller VM-etableringen. | Kontrollera den virtuella datorns status. Återgå till **Virtual Machines** i portalen och välj den virtuella datorn för att få statusinformation. |
|Installationen misslyckades – allmänt fel | Log Analytics-agenten installerades men misslyckades på grund av ett fel. | [Installera tillägget manuellt](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) eller avinstallera tillägget så försöker Security Center att installera det igen. |
| Installationen misslyckades – den lokala agenten är redan installerad | Installationen av Log Analytics-agenten misslyckades. Security Center identifierat en lokal agent (Log Analytics eller System Center Operations Manager) som redan har installerats på den virtuella datorn. För att undvika konfiguration av flera värdar, där den virtuella datorn rapporterar till två separata arbets ytor, stoppades installationen av Log Analytics-agenten. | Det finns två sätt att lösa detta: [att installera tillägget manuellt](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) och ansluta det till önskad arbetsyta. Du kan även ange den önskade arbetsytan som standardarbetsyta och aktivera automatisk etablering av agenten.  Se [aktivera automatisk etablering](security-center-enable-data-collection.md). |
| Agenten kan inte ansluta till arbetsytan | Log Analytics-agenten installerades men misslyckades på grund av en nätverks anslutning.  Kontrollera att du har internetåtkomst eller att en giltig HTTP-proxy har konfigurerats för agenten. | Se felsöka nätverkskrav för övervakningsagenten. |
| Agenten ansluten till arbetsyta som saknas eller är okänd | Security Center identifierat att den Log Analyticss agenten som är installerad på den virtuella datorn är ansluten till en arbets yta som den inte har åtkomst till. | Det kan hända i två fall. Arbetsytan togs bort eller finns inte längre. Installera om agenten med rätt arbetsyta eller avinstallera agenten och tillåt att Security Center slutför sin automatiska etableringsinstallation. Det andra fallet är när arbetsytan är en del av en prenumeration som Security Center inte har behörighet till. Security Center kräver att prenumerationer tillåter att Microsoft Security Resource Provider får åtkomst till dem. Aktivera genom att registrera prenumerationen på Microsoft Security Resource Provider. Du kan göra det via API, PowerShell, portalen eller helt enkelt genom att filtrera prenumerationen i Security Centers **översiktsinstrumentpanel**. Mer information finns i [Resursproviders och resurstyper](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Agenten svarar inte eller saknar ID | Security Center kan inte hämta säkerhetsdata som genomsökts från den virtuella datorn, trots att agenten är installerad. | Agenten rapporterar inga data, inte heller pulsslag. Agenten kan vara skadad eller så är det något som blockerar trafiken. Eller så är agenten rapportering av data men saknar ett Azure-resurs-ID så det är omöjligt att matcha data till den virtuella Azure-datorn. För att felsöka Linux, se [fel söknings guide för Log Analytics agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Om du vill felsöka i Windows läser du [Felsökning av virtuella Windows-datorer](../virtual-machines/extensions/oms-windows.md#troubleshoot-and-support). |
| Agenten har inte installerats | Datainsamling är inaktiverat. | Aktivera data insamling i säkerhets principen eller installera Log Analytics agenten manuellt. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Felsöka nätverks krav för övervaknings agenten <a name="mon-network-req"></a>

Agenter att ansluta till och registrera med Security Center, måste de ha åtkomst till nätverksresurser, inklusive portnummer och URL: er för domänen.

* För proxyservrar måste du se till att lämpliga proxy serverresurser konfigureras i agentinställningarna. Mer information om att [ändra proxyinställningarna](../azure-monitor/agents/agent-windows.md) finns i den här artikeln.
* Om du använder en brandvägg för att begränsa åtkomsten till Internet, måste du konfigurera brandväggen att tillåta åtkomst till Log Analytics. Ingen åtgärd krävs i agentinställningarna.

I följande tabell visas resurser som krävs för kommunikation.

| Agentresurs | Portar | Kringgå HTTPS-kontroll |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Yes |
| *.oms.opinsights.azure.com | 443 | Yes |
| *.blob.core.windows.net | 443 | Yes |
| *.azure-automation.net | 443 | Yes |

Om du får problem med att komma igång med agenten, kan du hitta mer information i artikeln [Felsökning av problem med att komma igång med Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Felsökning om Endpoint Protection inte fungerar korrekt

Gästagenten är den överordnade processen av allt tillägget [Microsoft Antimalware](../security/fundamentals/antimalware.md) gör. När gästagentprocessen misslyckas kan även Microsoft Antimalware som körs som en underordnad process för gästagenten misslyckas.  I scenarier som dessa rekommenderas det att verifiera följande alternativ:

* Om den virtuella datorn som är målet är en anpassad avbildning och skaparen av den virtuella datorn aldrig installerade gästagenten.
* Om målet är en virtuell Linux-dator i stället för en virtuell Windows-dator så kommer installationen av Windows-versionen av tillägget mot skadlig kod att misslyckas. Linux-gästagenten har särskilda krav gällande OS-version och nödvändiga paket. Om de här kraven inte uppfylls kommer inte VM-agenten att fungera där heller.
* Om den virtuella datorn skapades med en äldre version av gästagenten. Om den var det bör du vara medveten om att vissa gamla agenter inte kan uppdateras automatiskt till nyare versioner och att detta kan leda till det här problemet. Använd alltid den senaste versionen av gästagenten om du skapar dina egna avbildningar.
* Vissa administrationsprogram från tredje part kan inaktivera gästagenten eller blockera åtkomst till vissa filsökvägar. Om du har ett program från tredje part installerad på den virtuella datorn bör du kontrollera så att agenten är med på undantagslistan.
* Vissa brandväggsinställningar och nätverkssäkerhetsgrupper (NSG) blockerar nätverkstrafik till och från gästagenten.
* Vissa åtkomstkontrollistor (ACL) förhindrar åtkomst till disken.
* Otillräckligt diskutrymme kan blockera gästagenten från att fungera korrekt.

Som standard är användargränssnittet för Microsoft Antimalware inaktiverat, se [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](/archive/blogs/azuresecurity/enabling-microsoft-antimalware-user-interface-post-deployment) (Aktivera användargränssnittet för Microsoft Antimalware på virtuella Azure Resource Manager-datorer efter distribution) för mer information om hur du aktiverar det om det behövs.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Felsöka problem med att läsa in instrumentpanelen

Om du har problem med att läsa in instrumentpanelen för Security Center ska du kontrollera att användaren som registrerar prenumerationen på Security Center (dvs. den första användaren som öppnade Security Center med prenumerationen) och användaren som vill aktivera datasamling är *ägare* eller *deltagare* i prenumerationen. Från det ögonblicket kan även användare som är *läsare* i prenumerationen se instrumentpanelen/aviseringar/rekommendationer/policy.

## <a name="contacting-microsoft-support"></a>Kontakta Microsoft Support

Vissa problem kan identifieras med hjälp av rikt linjerna i den här artikeln, andra du kan också hitta dokumenterade på Security Center offentliga [Microsoft Q&en sida](/answers/topics/azure-security-center.html). Men om du behöver ytterligare fel sökning kan du öppna en ny supportbegäran med hjälp av **Azure Portal** enligt nedan:

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Azure Security Center planerings-och drift guide](security-center-planning-and-operations-guide.md) – lär dig att planera och förstå design överväganden för att anta Azure Security Center.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig att övervaka hälso tillståndet för dina Azure-resurser
* [Hantera och åtgärda säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hur du hanterar och åtgärdar säkerhets aviseringar
* [Förstå säkerhetsaviseringar i Azure Security Center](./security-center-alerts-overview.md)
* [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)
* [Aviseringsverifiering i Azure Security Center](security-center-alert-validation.md)
* [E-postmeddelanden i Azure Security Center](security-center-provide-security-contact-details.md)
* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](./security-center-alerts-overview.md)
* [Övervaka partner lösningar med Azure Security Center](./security-center-partner-integration.md) – lär dig hur du övervakar dina partner lösningars hälso status.
* [Vanliga frågor och svar om Azure Security Center](faq-general.md) – Här finns vanliga frågor om tjänsten.
* [Azures säkerhets blogg](/archive/blogs/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure