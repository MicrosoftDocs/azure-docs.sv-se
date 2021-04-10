---
title: Övervaka med webbtester med flera steg – Azure Application insikter
description: Konfigurera webb program med flera steg för att övervaka dina webb program med Azure Application insikter
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 1d3597eaf54c40fb1f986d822af0dd6b8c8a7b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719856"
---
# <a name="multi-step-web-tests"></a>Webbtester med flera steg

Du kan övervaka en inspelad sekvens av webb adresser och interaktioner med en webbplats via webbtester med flera steg. Den här artikeln vägleder dig genom processen för att skapa ett webb test med flera steg med Visual Studio Enterprise.

> [!NOTE]
> Webbtester med flera steg är beroende av Visual Studio webtest-filer. Det [meddelade](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) att Visual Studio 2019 är den senaste versionen med webbtest-funktioner. Det är viktigt att förstå att även om inga nya funktioner läggs till, så stöds inte webbtest-funktioner i Visual Studio 2019 och kommer fortfarande att stödjas under produktens support livs cykel. Azure Monitor produkt teamet har fått frågor om framtida tillgänglighets test för flera steg [här](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  
> </br>
> Webbtester med flera steg **stöds inte** i [Azure Government](../../azure-government/index.yml) molnet.


## <a name="pre-requisites"></a>Förutsättningar

* Visual Studio 2017 Enterprise eller senare.
* Webb prestanda och belastnings test verktyg i Visual Studio.

För att hitta de förberedande verktyg som krävs. Starta **Visual Studio Installer**  >  -fel sökning av **enskilda komponenter**  >  **och testa**  >  **verktyg för webb prestanda och belastnings testning**.

![Skärm bild av Visual Studio Installer-gränssnittet med enskilda komponenter som marker ATS med en kryss ruta bredvid objektet för webb prestanda och belastnings test verktyg](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Webbtester med flera steg har ytterligare kostnader som är kopplade till dem. Mer information finns i den [officiella pris guiden](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Spela in ett webb test för flera steg 

> [!WARNING]
> Vi rekommenderar inte att du använder flera stegs inspelningar. Inspelaren har utvecklats för statiska HTML-sidor med grundläggande interaktioner och ger ingen funktions upplevelse för moderna webb sidor.

Vägledning om hur du skapar Visual Studio-webbtester finns i den [officiella Visual studio 2019-dokumentationen](/visualstudio/test/how-to-create-a-web-service-test).

## <a name="upload-the-web-test"></a>Ladda upp webb testet

1. I Application Insights portal i fönstret tillgänglighet väljer du **skapa test**  >  **test typ**  >  **webb test för flera steg**.

2. Ange test platser, frekvens och aviserings parametrar.

### <a name="frequency--location"></a>Frekvens & plats

|Inställning| Förklaring
|----|----|----|
|**Test frekvens**| Anger hur ofta testet körs från varje test plats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Test platser**| Är platser där våra servrar skickar webb förfrågningar till din URL. Det **minsta antalet rekommenderade test platser är fem** för att försäkra dig om att du kan särskilja problem på din webbplats från nätverks problem. Du kan välja upp till 16 platser.

### <a name="success-criteria"></a>Lyckade kriterier

|Inställning| Förklaring
|----|----|----|
| **Timeout för test** |Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade status koden som räknas som lyckad. 200 är koden som anger att en normal webbsida har returnerats.|
| **Innehålls matchning** | En sträng, till exempel "Välkommen!" Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehålls matchning** |

### <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära real tid (för hands version)** | Vi rekommenderar att du använder aviseringar i nästan real tid. Konfigurationen av den här typen av avisering görs efter att ditt tillgänglighets test har skapats.  |
|**Tröskelvärde för aviserings plats**|Vi rekommenderar minst 3/5 platser. Den optimala relationen mellan aviserings platsens tröskelvärde och antalet test platser är **tröskelvärdet för aviserings platsens tröskel**  =  **antal test platser – 2, med minst fem test platser.**|

## <a name="configuration"></a>Konfiguration

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Att sätta igång tid och slumpmässiga tal i testet

Anta att du testar ett verktyg som hämtar tidsberoende data, till exempel aktier från ett externt flöde. När du spelar in webbtestet måste du ange specifika tider, men du anger dem som parametrar för testet, StartTime och EndTime.

![Skärm bild för min fantastisk Stock-app](./media/availability-multistep/app-insights-72webtest-parameters.png)

När du kör testet vill du att EndTime alltid ska vara den aktuella tiden, och StartTime 15 minuter tidigare.

Datum/tid-plugin-programmet för webb test ger dig möjlighet att hantera Parameterisera tider.

1. Lägg till ett plugin-program för webbtester för varje variabelt parametervärde som du behöver. I verktygsfältet Webbtest väljer du **Lägg till plugin-program för webbtest**.
    
    ![Lägg till plugin-program för webb test](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    I det här exemplet ska vi använda två instanser av plugin-programmet för datum och tid. En instans är för ”15 minuter tidigare” och en annan för ”nu”.

2. Öppna egenskaperna för varje plugin-program. Lägg till ett namn och ange att den aktuella tiden ska användas. För den ena anger du Lägg till minuter = -15.

    ![Kontext parametrar](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Du refererar till plugin-namn genom att använd {{plugin-name}} i parametrarna för webbtestet.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Ladda upp testet till portalen. De dynamiska värdena används i varje körning av testet.

### <a name="dealing-with-sign-in"></a>Hantera inloggning

Om användarna måste logga in i din app kan du välja mellan olika alternativ för att simulera inloggningen, så att du kan testa sidorna bakom inloggningen. Vilken metod du använder beror på vilken typ av säkerhet som tillhandahålls av appen.

I samtliga fall bör du skapa ett konto i ditt program som endast används för testning. Om möjligt begränsar du behörigheterna för det här testkontot så att webbtestningen inte påverkar riktiga användare.

**Enkelt användar namn och lösen ord** Spela in ett webbtest på vanligt sätt. Ta bort cookies först.

**SAML-autentisering**

|Egenskapsnamn| Beskrivning|
|----|-----|
| Målgrupps-URI | Målgrupps-URI för SAML-token.  Detta är URI: n för Access Control Service (ACS) – inklusive ACS-namnrymd och värd namn. |
| Certifikat lösen ord | Lösen ordet för klient certifikatet som ger åtkomst till den inbäddade privata nyckeln. |
| Klient certifikat  | Klient certifikatets värde med privat nyckel i Base64-kodat format. |
| Namn-ID | Namn identifieraren för token |
| Inte efter | TimeSpan som token ska vara giltigt för.  Standardvärdet är 5 minuter. |
| Inte före | TimeSpan för vilket en token som skapades tidigare är giltig (för att adressera tids skevar).  Standardvärdet är (negativt) 5 minuter. |
| Mål kontext parameter namn | Kontext parametern som tar emot den genererade kontrollen. |


**Klient hemlighet** Om din app har en inloggnings väg som omfattar en klient hemlighet använder du den vägen. Azure Active Directory (AAD) är ett exempel på en tjänst som erbjuder inloggning med klienthemligheter. I AAD är klienthemligheten appnyckeln.

Här är ett exempel på ett webbtest för en Azure-webbapp som använder en appnyckel:

![Exempel skärm bild](./media/availability-multistep/client-secret.png)

Hämta en token från AAD med hjälp av en klienthemlighet (AppKey).
Extrahera ägartoken från svaret.
Anropa API:et med hjälp av ägartoken i auktoriseringshuvudet.
Kontrol lera att webb testet är en faktisk klient, d.v.s. den har sin egen app i AAD, och använd dess clientId + app-nyckel. Din tjänst under testet har också sin egen app i AAD: appID-URI: n för den här appen avspeglas i webbtesten i resurs fältet.

### <a name="open-authentication"></a>Öppen autentisering
Ett exempel på öppen autentisering är inloggning med ett Microsoft- eller Google-konto. Många appar som använder OAuth erbjuder möjligheten att använda en klienthemlighet, så det första du bör göra är att ta reda på detta.

Om testet måste logga in med OAuth är den allmänna riktlinjen att:

Använda ett verktyg som Fiddler för att undersöka trafiken mellan webbläsaren, autentiseringswebbplatsen och din app.
Utföra två eller flera inloggningar med olika datorer eller webbläsare, eller med långa intervall (så att token upphör att gälla).
Genom att jämföra olika sessioner identifiera den token som skickas tillbaka från autentiseringswebbplatsen, som sedan skickas till din appserver efter inloggningen.
Spela in ett webbtest med hjälp av Visual Studio.
Parameterisera token genom att ange parametern när token returneras från autentiseraren och använda den i frågan till webbplatsen. (Visual Studio försöker parameterisera testet, men kan inte parameterisera token korrekt.)

## <a name="troubleshooting"></a>Felsökning

Dedikerad [fel söknings artikel](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Tillgänglighets varningar](availability-alerts.md)
* [Webb test för URL-ping](monitor-web-app-availability.md)
