---
title: Lösen ords skydd i Azure AD – Azure Active Directory
description: Förbjuda svaga lösen ord i lokala Active Directory Domain Services miljöer med hjälp av lösen ords skydd i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743929"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Använd lokalt Azure AD-lösenord för Active Directory Domain Services

Lösen ords skyddet i Azure AD identifierar och blockerar kända svaga lösen ord och deras varianter, och kan också blockera ytterligare svaga termer som är speciella för din organisation. Lokal distribution av lösen ords skydd i Azure AD använder samma globala och anpassade listor över blockerade lösen ord som lagras i Azure AD, och utför samma kontroller för lokala lösen ords ändringar som i Azure AD för molnbaserade ändringar. Dessa kontroller utförs vid lösen ords ändringar och lösen ords återställnings händelser mot lokala Active Directory Domain Services-domänkontrollanter (AD DS).

## <a name="design-principles"></a>Designprinciper

Lösen ords skyddet i Azure AD har utformats med följande principer i åtanke:

* Domänkontrollanter (DCs) behöver aldrig kommunicera direkt med Internet.
* Inga nya nätverks portar öppnas i DCs.
* Inga ändringar av AD DS-schemat krävs. Program varan använder den befintliga AD DS- *behållaren* och schema objekt för *serviceConnectionPoint* .
* Ingen minsta AD DS-domän eller skogs funktions nivå (DFL/FFL) krävs.
* Program varan skapar eller kräver inte konton i AD DS-domäner som den skyddar.
* Användarens lösen ord för klartext lämnar aldrig domänkontrollanten, antingen under åtgärder för lösen ords validering eller vid någon annan tidpunkt.
* Program varan är inte beroende av andra funktioner i Azure AD. Till exempel är inte PHS (Azure AD Password hash Sync) relaterat till eller krävs för lösen ords skydd i Azure AD.
* Stegvis distribution stöds, men lösen ords principen tillämpas bara om domänkontrollantens agent (DC-agent) är installerad.

## <a name="incremental-deployment"></a>Stegvis distribution

Azure AD Password Protection stöder stegvis distribution över domänkontrollanter i en AD DS-domän. Det är viktigt att förstå vad det innebär i själva verket och vad kompromisserna är.

Azure AD Password Protection DC Agent-programvaran kan bara verifiera lösen ord när de installeras på en DOMÄNKONTROLLANT och endast för lösen ords ändringar som skickas till den DOMÄNKONTROLLANTen. Det går inte att styra vilka domänkontrollanter som väljs av Windows-klientdatorer för bearbetning av ändringar av användar lösen ord. För att garantera ett konsekvent beteende och för universellt skydd av lösen ords skydd i Azure AD måste DC-agenten vara installerad på alla domänkontrollanter i en domän.

Många organisationer vill noggrant testa lösen ords skyddet i Azure AD på en delmängd av deras DCs innan en fullständig distribution. För att stödja det här scenariot stöder Azure AD Password Protection delvis distribution. DC-agentens program vara på en specifik DOMÄNKONTROLLANT verifierar aktivt lösen ord även när andra domänkontrollanter i domänen inte har installerat DC-agenten. Partiella distributioner av den här typen är inte säkra och rekommenderas inte, förutom för test ändamål.

## <a name="architectural-diagram"></a>Arkitektur diagram

Det är viktigt att förstå de underliggande design-och funktions begreppen innan du distribuerar Azure AD Password Protection i en lokal AD DS-miljö. Följande diagram visar hur komponenterna i Azure AD Password Protection fungerar tillsammans:

![Så här fungerar Azure AD Password Protection-komponenter tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Proxy tjänsten Azure AD Password Protection körs på alla domänanslutna datorer i den aktuella AD DS-skogen. Tjänstens primära syfte är att vidarebefordra begär Anden om hämtning av lösen ords principer från DCs till Azure AD och sedan returnera svaren från Azure AD till DOMÄNKONTROLLANTen.
* Lösen ords filter-DLL: en för DC-agenten tar emot förfrågningar om användar lösen ord från operativ systemet. Filtret vidarebefordrar dem till DC-agenttjänsten som körs lokalt på DOMÄNKONTROLLANTen.
* DC-agenttjänsten för Azure AD Password Protection tar emot lösen ords verifierings begär Anden från DLL-filen med lösen ords filter för DC-agenten. DC-agenttjänsten bearbetar dem med hjälp av den aktuella (lokalt tillgängliga) lösen ords principen och returnerar resultatet av *pass* eller *misslyckande*.

## <a name="how-azure-ad-password-protection-works"></a>Så här fungerar Azure AD Password Protection

De lokala Azure AD-komponenterna för lösen ords skydd fungerar på följande sätt:

1. Varje Azure AD Password Protection proxy-tjänstinstans annonserar sig själv för domän kontrol Lanterna i skogen genom att skapa ett *serviceConnectionPoint* -objekt i Active Directory.

    Varje DC-agenttjänsten för lösen ords skydd i Azure AD skapar också ett *serviceConnectionPoint* -objekt i Active Directory. Det här objektet används främst för rapportering och diagnostik.

1. DC-agenttjänsten ansvarar för att initiera hämtningen av en ny lösen ords princip från Azure AD. Det första steget är att leta upp en Azure AD-proxy för lösen ords skydd genom att fråga skogen efter objekt i *serviceConnectionPoint* för proxy.

1. När en tillgänglig proxyvärd hittas skickar DC-agenten en begäran om att hämta en lösen ords princip till proxyservern. Proxy-tjänsten i sin tur skickar begäran till Azure AD och returnerar svaret till DC-agenttjänsten.

1. När DC-agenttjänsten tar emot en ny lösen ords princip från Azure AD, lagrar tjänsten principen i en särskild mapp i roten för *SYSVOL* -mappens resurs resurs. DC-agenttjänsten övervakar även den här mappen om nyare principer replikeras från andra DC agent-tjänster i domänen.

1. DC-agenttjänsten begär alltid en ny princip när tjänsten startas. När DC-agenttjänsten har startats kontrollerar den åldern för den aktuella lokalt tillgängliga principen varje timme. Om principen är äldre än en timme begär DC-agenten en ny princip från Azure AD via proxyservern, enligt beskrivningen ovan. Om den aktuella principen inte är äldre än en timme fortsätter DC-agenten att använda den principen.

1. När lösen ords ändrings händelser tas emot av en DOMÄNKONTROLLANT används den cachelagrade principen för att avgöra om det nya lösen ordet godkänns eller avvisas.

### <a name="key-considerations-and-features"></a>Viktiga överväganden och funktioner

* När en lösen ords princip för lösen ords skydd i Azure AD laddas ned, är den principen unik för en klient. Lösen ords principer är med andra ord alltid en kombination av den globala listan över blockerade lösen ord och per klient, anpassad lista över blockerade lösen ord.
* DC-agenten kommunicerar med proxyservern via RPC via TCP. Proxy-tjänsten lyssnar efter dessa anrop på en dynamisk eller statisk RPC-port, beroende på konfigurationen.
* DC-agenten lyssnar aldrig på en nätverks tillgänglig port.
* Proxy-tjänsten anropar aldrig DC-agenttjänsten.
* Proxy-tjänsten är tillstånds lös. De cachelagrar aldrig principer eller andra tillstånd som hämtats från Azure.
* DC-agenttjänsten använder alltid den senaste lokalt tillgängliga lösen ords principen för att utvärdera en användares lösen ord. Om ingen lösen ords princip är tillgänglig på den lokala DOMÄNKONTROLLANTen godkänns lösen ordet automatiskt. När detta inträffar loggas ett händelse meddelande som varnar administratören.
* Lösen ords skydd i Azure AD är inte en tillämpnings motor för real tids principer. Det kan finnas en fördröjning mellan när en konfigurations ändring av lösen ords principen görs i Azure AD och när ändringen når och tillämpas på alla domänkontrollanter.
* Azure AD Password Protection fungerar som ett tillägg till de befintliga lösen ords principerna för AD DS, inte en ersättning. Detta inkluderar eventuella andra tredjeparts lösen ords filter från tredje part som kan installeras. AD DS kräver alltid att alla lösen ords verifierings komponenter accepterar innan ett lösen ord accepteras.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Skog/klient bindning för Azure AD Password Protection

Distribution av lösen ords skydd i Azure AD i en AD DS-skog kräver registrering av den skogen med Azure AD. Varje proxy-tjänst som distribueras måste också registreras med Azure AD. Dessa skogar och proxy-registreringar är kopplade till en särskild Azure AD-klient, som identifieras implicit av de autentiseringsuppgifter som används under registreringen.

AD DS-skogen och alla distribuerade Proxy-tjänster i en skog måste registreras med samma klient organisation. Det finns inte stöd för att en AD DS-skog eller några Proxy-tjänster i skogen ska registreras på olika Azure AD-klienter. Symptom på en sådan mis-konfigurerad distribution är möjligheten att ladda ned lösen ords principer.

> [!NOTE]
> Kunder som har flera Azure AD-klienter måste därför välja en enskild klient för att registrera varje skog för lösen ords skydd i Azure AD.

## <a name="download"></a>Ladda ned

De två agent installations program som krävs för Azure AD Password Protection är tillgängliga från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Nästa steg

För att komma igång med att använda det lokala Azure AD-lösenordet, slutför du följande anvisningar:

> [!div class="nextstepaction"]
> [Distribuera lokalt Azure AD-lösenord för lösen ords skydd](howto-password-ban-bad-on-premises-deploy.md)
