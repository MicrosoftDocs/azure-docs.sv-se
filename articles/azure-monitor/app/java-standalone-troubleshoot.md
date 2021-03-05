---
title: Felsöka Azure Monitor Application Insights för Java
description: Lär dig hur du felsöker Java-agenten för Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 45f45e71546909b71c71c66303c1459edd74548f
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199619"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Fel söknings guide: Azure Monitor Application Insights för Java

I den här artikeln tar vi upp några vanliga problem som kan uppstå vid instrumentering av ett Java-program med hjälp av Java-agenten för Application Insights. Vi beskriver också stegen för att lösa problemen. Application Insights är en funktion i tjänsten Azure Monitor Platform.

## <a name="check-the-self-diagnostic-log-file"></a>Kontrol lera logg filen för självdiagnostik

Som standard skapar Java 3,0-agenten för Application Insights en loggfil med namnet `applicationinsights.log` i samma katalog som innehåller `applicationinsights-agent-3.0.2.jar` filen.

Den här logg filen är den första platsen där du kan söka efter tips till eventuella problem som kan uppstå.

## <a name="jvm-fails-to-start"></a>JVM kan inte startas

Om JVM inte kan starta med "Det gick inte att öppna ZIP-filen eller JAR-manifestet", kan du försöka hämta om agentens jar-fil på nytt, eftersom den kan ha skadats under fil överföringen.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Uppgradera från Application Insights Java 2. x SDK

Om du redan använder Application Insights Java 2. x SDK i programmet kan du fortsätta att använda det. Java 3,0-agenten kommer att identifiera den. Mer information finns i [Uppgradera från Java 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Uppgradera från Application Insights Java 3,0 Preview

Om du uppgraderar från Java 3,0 Preview-agenten granskar du alla [konfigurations alternativ](./java-standalone-config.md) noggrant. JSON-strukturen har ändrats helt i 3,0 General Availability-versionen (GA).

Dessa ändringar omfattar:

-  Konfigurations filens namn har ändrats från `ApplicationInsights.json` till `applicationinsights.json` .
-  `instrumentationSettings`Noden finns inte längre. Allt innehåll i `instrumentationSettings` flyttas till rot nivån. 
-  Konfigurations noder som `sampling` ,, `jmxMetrics` `instrumentation` och `heartbeat` flyttas ut från `preview` till rot nivån.

## <a name="some-logging-is-not-auto-collected"></a>Vissa loggningar samlas inte in automatiskt

Loggning registreras endast om det stämmer överens med det konfigurerade tröskelvärdet för loggnings ramverk, och den andra uppfyller även det Application Insights konfigurerade tröskelvärdet.

Det bästa sättet att veta om ett visst loggnings uttryck uppfyller det konfigurerade tröskelvärdet för loggnings ramverk är att bekräfta att det visas i din normala program logg (t. ex. fil eller konsol).

Observera också att om ett undantags objekt skickas till loggen visas logg meddelandet (och undantags objekt information) i Azure Portal under `exceptions` tabellen i stället för `traces` tabellen.

Se den [automatiskt insamlade loggnings konfigurationen](./java-standalone-config.md#auto-collected-logging) för mer information.

## <a name="import-ssl-certificates"></a>Importera SSL-certifikat

Det här avsnittet hjälper dig att felsöka och eventuellt åtgärda undantag som rör SSL-certifikat när du använder Java-agenten.

Det finns två olika sökvägar nedan för att lösa problemet:
* Om du använder en standard-Java-nyckel lagring
* Om du använder ett anpassat Java-attributarkiv

Om du inte är säker på vilken sökväg du ska följa, kontrollerar du om du har ett JVM-argument `-Djavax.net.ssl.trustStore=...` .
Om du _inte_ har ett sådant JVM-argument använder du förmodligen Java-standardarkivet som standard.
Om du _har ett_ sådant JVM-argument använder du förmodligen en anpassad nyckel lagring och JVM-arg kommer att peka dig på din anpassade nyckel lagring.

### <a name="if-using-the-default-java-keystore"></a>Om du använder standard-Java-nyckel arkivet:

Normalt har Java-standardarkivet redan alla certifikat utfärdarens rot certifikat. Det kan dock finnas vissa undantag, till exempel om inmatnings slut punkt certifikatet kan vara signerat av ett annat rot certifikat. Vi rekommenderar därför följande tre steg för att lösa problemet:

1.  Kontrol lera att rot certifikatet som användes för att signera Application Insights-slutpunkten redan finns i standard arkivet. Certifikaten för betrodda certifikat utfärdare lagras som standard i `$JAVA_HOME/jre/lib/security/cacerts` . Om du vill visa en lista över certifikat i ett Java-nyckel Arkiv använder du följande kommando:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    Du kan omdirigera utdata till en temporär fil som detta (kommer att vara lätt att söka senare)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. När du har listan över certifikat, följer du de här [stegen](#steps-to-download-ssl-certificate) för att ladda ned rot certifikatet som användes för att signera Application Insights-slutpunkten.

    När du har hämtat certifikatet genererar du en SHA-1-hash på certifikatet med kommandot nedan:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Kopiera SHA-1-värdet och kontrol lera om det här värdet finns i filen "temp.txt" som du sparade tidigare.  Om du inte kan hitta SHA-1-värdet i den temporära filen anger det att det nedladdade rot certifikatet saknas i standard-Java-auktoriseringsarkivet.


3. Importera rot certifikatet till standard-Java-auktoriseringsarkivet med följande kommando:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    I det här fallet kommer det att
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Om du använder ett anpassat Java-nyckel Arkiv:

Om du använder en anpassad Java-nyckel lagring kan du behöva importera Application Insights slut punkt (er) rot-SSL-certifikat till den.
Vi rekommenderar följande två steg för att lösa det här problemet:
1. Följ de här [stegen](#steps-to-download-ssl-certificate) för att ladda ned rot certifikatet från Application Insights slut punkten.
2. Använd följande kommando för att importera rot-SSL-certifikatet till det anpassade Java-nyckel arkivet:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Steg för att hämta SSL-certifikat

1.  Öppna din favorit webbläsare och gå till `IngestionEndpoint` URL: en som finns i anslutnings strängen som används för att instrumentera ditt program.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Skärm bild som visar en Application Insights anslutnings sträng." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Välj ikonen **Visa plats information** (lås) i webbläsaren och välj sedan alternativet **certifikat** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Skärm bild av alternativet certifikat i plats information." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  I stället för att hämta ' löv ' certifikatet bör du ladda ned rot certifikatet som det visas nedan. Senare måste du klicka på "certifikat Sök väg" – > väljer rot certifikatet – > Klicka på Visa certifikat. Då öppnas en ny certifikat-meny och du kan hämta certifikatet från den nya menyn.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Skärm bild av hur du väljer rot certifikatet." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Gå till fliken **information** och välj **Kopiera till fil**.
5.  Välj **Nästa** -knappen, Välj **Base-64-kodad X. 509 (. CER-** format och välj sedan **Nästa** igen.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Skärm bild av guiden Exportera certifikat med ett format valt." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Ange den fil där du vill spara SSL-certifikatet. Välj sedan **Nästa**  >  **Slutför**. Du bör se meddelandet "exporten lyckades".

> [!WARNING]
> Du måste upprepa de här stegen för att få det nya certifikatet innan det aktuella certifikatet upphör att gälla. Du kan hitta information om förfallo datum på fliken **information** i dialog rutan **certifikat** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Skärm bild som visar information om SSL-certifikat." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
