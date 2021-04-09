---
title: Vad är säker Poäng för identitet? -Azure Active Directory
description: Hur du kan använda identitetens säkra Poäng för att förbättra säkerhets position i din katalog
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: guptashi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23832d9f1205105f1f9711cdf3260b74ee4a9bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952273"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Vad är identitetssäkerhetspoäng i Azure Active Directory?

Hur säker är din Azure AD-klientorganisation? Om du inte vet hur du ska svara på den här frågan förklarar den här artikeln hur du kan övervaka och förbättra din position för identiteter med säkerhet.

## <a name="what-is-an-identity-secure-score"></a>Vad är identitetssäkerhetspoäng?

De säkraste poängen i identiteten är procent andel som fungerar som en indikator för hur justerad du är med i Microsofts rekommendationer om bästa praxis för säkerhet. Varje förbättrings åtgärd i säkra Poäng för identiteter skräddarsys efter din speciella konfiguration.  

![Säkerhetspoäng](./media/identity-secure-score/identity-secure-score-overview.png)

Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

Du kan komma åt poängen och tillhörande information på instrumentpanelen för identitetssäkerhetspoäng. På den här instrumentpanelen hittar du:

- Din identitet säkra Poäng
- Ett jämförelse diagram som visar hur din identitet säkrar poängen jämförs med andra klienter i samma bransch och liknande storlek
- Ett trend diagram som visar hur din identitet säkra poäng har ändrats med tiden
- En lista över möjliga förbättringar

Med hjälp av följande förbättringsåtgärder kan du:

- Förbättra din säkerhets position och dina Poäng
- Dra nytta av de funktioner som är tillgängliga för din organisation som en del av dina identitets investeringar

## <a name="how-do-i-get-my-secure-score"></a>Hur får jag mina säkerhetspoäng?

De säkraste poängen för identiteter finns i alla utgåvor av Azure AD. Organisationer kan komma åt sina identitets säkra Poäng från **Azure Portal**  >  **Azure Active Directory**  >  **säkerhets**  >  **identitet säkra Poäng**.

## <a name="how-does-it-work"></a>Hur fungerar det?

Var 48 timme tittar Azure på din säkerhetskonfiguration och jämför dina inställningar med de rekommenderade metoderna. Baserat på resultatet av den här utvärderingen beräknas en ny Poäng för din katalog. Det är möjligt att din säkerhets konfiguration inte är helt justerad med den bästa övnings vägledningen och att förbättrings åtgärderna endast delvis uppfylls. I dessa scenarion tilldelas du bara en del av det högsta antalet poäng som är tillgängliga för kontrollen.

Varje rekommendation mäts baserat på din Azure AD-konfiguration. Om du använder produkter från tredje part för att aktivera en rekommendation för bästa praxis kan du ange den här konfigurationen i inställningarna för en förbättrings åtgärd. Du kan också välja att ställa in rekommendationer som ska ignoreras om de inte gäller för din miljö. En ignorerad rekommendation bidrar inte till beräkningen av dina poäng.

![Ignorera eller markera åtgärd som täcks av tredje part](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Hur hjälper det här mig?

Med säkerhetspoängen kan du:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

## <a name="what-you-should-know"></a>Det här bör du veta

### <a name="who-can-use-the-identity-secure-score"></a>Vilka kan använda identitetssäkerhetspoängen?

Identitetssäkerhetspoängen kan användas av följande roller:

- Global administratör
- Säkerhetsadministratör
- Säkerhetsläsare

### <a name="how-are-controls-scored"></a>Hur kommer kontrollerna att poängas?

Kontrollerna kan betygas på två sätt. Vissa är i ett binärt läge – du får 100% av poängen om du har den funktion eller inställning som kon figurer ATS utifrån vår rekommendation. Andra Poäng beräknas som en procent andel av den totala konfigurationen. Om du till exempel får ett max värde på 10,71% om du skyddar alla användare med MFA och du bara har 5 av 100 användare som är skyddade, får du en del Poäng kring 0,53% (5 skyddade/100 totalt * 10,71% max = 0,53% partiella Poäng).

### <a name="what-does-not-scored-mean"></a>Vad betyder [Not Scored] ([Inte poängsatt])?

Åtgärder märkta med [Not Scored] ([Inte poängsatt]) är sådana du kan utföra i organisationen men som inte poängsätts eftersom de inte är anslutna till verktyget (än!). Du kan alltså fortfarande förbättra din säkerhet men du får inga poäng för de åtgärderna just nu.

### <a name="how-often-is-my-score-updated"></a>Hur ofta uppdateras mina poäng?

Poängen beräknas en gång per dag (kring 01:00 PST). Om du gör en ändring av en åtgärd som mäts uppdateras poängen automatiskt nästa dag. Det tar upp till 48 timmar innan en ändring återspeglas i dina poäng.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mina poäng har ändrats. Hur får jag reda på varför?

Gå till [Microsoft 365 Security Center](https://security.microsoft.com/), där du hittar dina fullständiga Microsoft Secure-poäng. Du kan enkelt se alla ändringar av dina säkra poäng genom att granska de djupgående ändringarna på fliken Historik.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Är det säkra Poäng måttet min risk för att få en överträdelse?

Det korta svaret är nej. De säkra poängen uttrycker inte ett absolut mått på hur troligt du är på att få en överträdelse. De anger i vilken utsträckning du har implementerat funktioner som kan motverka risken för intrång. Ingen tjänst kan garantera att du inte kommer att bli komprometterad och att de säkra poängen inte bör tolkas som en garanti på något sätt.

### <a name="how-should-i-interpret-my-score"></a>Hur ska jag tolka mina poäng?

Poängen har förbättrats för att konfigurera rekommenderade säkerhetsfunktioner eller utföra säkerhetsrelaterade uppgifter (t. ex. läsning av rapporter). Vissa åtgärder poängsätts när de är delvis slutförda, som att aktivera multifaktorautentisering (MFA) för dina användare. Dina säkra poäng är direkt representativa för Microsofts säkerhets tjänster som du använder. Kom ihåg att säkerheten måste bal anse ras med användbarhet. Alla säkerhetskontroller har en komponent med användarpåverkan. Kontroller med låg användarpåverkan bör ha lite eller ingen effekt på användarnas dagliga åtgärder.

Om du vill se din resultat historik går du till [Microsoft 365 Security Center](https://security.microsoft.com/) och läser igenom dina totala säkra Microsoft-poäng. Du kan granska ändringar av den övergripande säkra poängen genom att klicka på Visa historik. Välj ett specifikt datum för att se vilka kontroller som hade aktiverats för den dagen och vilka poäng du har fått för varje kontroll.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>Hur relaterar identiteten för den säkra poängen till Microsoft 365 säkra Poäng?

[Microsofts säkra Poäng](/office365/securitycompliance/microsoft-secure-score) innehåller fem skilda kontroll-och Poäng kategorier:

- Identitet
- Data
- Enheter
- Infrastruktur
- Appar

De säkraste poängen i identiteten representerar identitets delen av Microsofts säkra poäng. Den här överlappningen innebär att dina rekommendationer för identitetens säkra poäng och identitets poängen i Microsoft är desamma.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Microsofts säkra Poäng](/office365/securitycompliance/microsoft-secure-score)