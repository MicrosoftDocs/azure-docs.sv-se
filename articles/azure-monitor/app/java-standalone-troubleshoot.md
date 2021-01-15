---
title: Felsöka Azure Monitor Application Insights för Java
description: Lär dig hur du felsöker Java-agenten för Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2876abd3749c9e56cef462e41b8268135f82cd12
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232224"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Fel söknings guide: Azure Monitor Application Insights för Java

I den här artikeln tar vi upp några vanliga problem som kan uppstå vid instrumentering av ett Java-program med hjälp av Java-agenten för Application Insights. Vi beskriver också stegen för att lösa problemen. Application Insights är en funktion i tjänsten Azure Monitor Platform.

## <a name="check-the-self-diagnostic-log-file"></a>Kontrol lera logg filen för självdiagnostik

Som standard skapar Java 3,0-agenten för Application Insights en loggfil med namnet `applicationinsights.log` i samma katalog som innehåller `applicationinsights-agent-3.0.1.jar` filen.

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

Se den [automatiskt insamlade loggnings konfigurationen](./java-standalone-config.md#auto-collected-logging) för mer information.

## <a name="import-ssl-certificates"></a>Importera SSL-certifikat

Om du använder standard-Java-standardarkivet kommer det redan att ha alla certifikat utfärdarens rot certifikat. Du behöver inte importera fler SSL-certifikat.

Om du använder en anpassad Java-nyckel lagring kan du behöva importera Application Insights slut punkt SSL-certifikat till den.

### <a name="key-terminology"></a>Nyckel terminologi
Ett nyckel *lager* är en lagrings plats för certifikat, offentliga nycklar och privata nycklar. Normalt har Java Development Kit-distributioner en körbar fil för att hantera dem: `keytool` .

Följande exempel är ett enkelt kommando för att importera ett SSL-certifikat till nyckel arkivet:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Steg för att hämta och lägga till ett SSL-certifikat

1.  Öppna din favorit webbläsare och gå till `IngestionEndpoint` URL: en som finns i anslutnings strängen som används för att instrumentera ditt program.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Skärm bild som visar en Application Insights anslutnings sträng.":::

2.  Välj ikonen **Visa plats information** (lås) i webbläsaren och välj sedan alternativet **certifikat** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Skärm bild av alternativet certifikat i plats information.":::

3.  Gå till fliken **information** och välj **Kopiera till fil**.
4.  Välj **Nästa** -knappen, Välj **Base-64-kodad X. 509 (. CER-** format och välj sedan **Nästa** igen.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Skärm bild av guiden Exportera certifikat med ett format valt.":::

5.  Ange den fil där du vill spara SSL-certifikatet. Välj sedan **Nästa**  >  **Slutför**. Du bör se meddelandet "exporten lyckades".
6.  När du har certifikatet är det dags att importera certifikatet till ett Java-nyckel arkiv. Använd [föregående kommando](#key-terminology) för att importera certifikat.

> [!WARNING]
> Du måste upprepa de här stegen för att få det nya certifikatet innan det aktuella certifikatet upphör att gälla. Du kan hitta information om förfallo datum på fliken **information** i dialog rutan **certifikat** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Skärm bild som visar information om SSL-certifikat.":::
