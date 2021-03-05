---
title: Certifikat rotation för Azure Database for PostgreSQL enskild server
description: Lär dig mer om kommande ändringar av rot certifikat ändringar som påverkar Azure Database for PostgreSQL enskild server
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 6dc687879eb646b4abd081b40bce292d20ff3186
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123997"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Förstå ändringarna i rot certifikat utfärdarens ändring för Azure Database for PostgreSQL enskild server

Azure Database for PostgreSQL en enda server slutförde rot certifikats ändringen den **15 februari 2021 (02/15/2021)** som en del av standard underhåll och säkerhets metoder. Den här artikeln innehåller mer information om ändringarna, de resurser som påverkas och de steg som krävs för att säkerställa att programmet upprätthåller anslutningen till databas servern.

## <a name="why-root-certificate-update-is-required"></a>Varför uppdatering av rot certifikat krävs?

Azure Database för PostgreSQL-användare kan bara använda det fördefinierade certifikatet för att ansluta till sin PostgreSQL-Server, som finns [här](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). [Certifikat utfärdare (ca) webbläsarens forum](https://cabforum.org/)   publicerade nyligen rapporter om flera certifikat som utfärdats av ca-leverantörer som icke-kompatibla.

Som enligt branschens krav på efterlevnad började CA-leverantörer återkallade CA-certifikat för icke-kompatibla ca: er, vilket kräver att servrar använder certifikat som utfärdats av kompatibla ca: er och signeras av CA-certifikat från dessa kompatibla certifikat utfärdare. Eftersom Azure Database for MySQL använde något av dessa icke-kompatibla certifikat behövde vi att rotera certifikatet till den kompatibla versionen för att minimera det potentiella hotet till MySQL-servrarna.

Det nya certifikatet distribueras och börjar användas 15 februari 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Vilken ändring utfördes den 15 februari 2021 (02/15/2021)?

Den 15 februari 2021 ersattes [rot certifikatet för BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) med en **kompatibel version** av samma [BaltimoreCyberTrustRoot-rotcertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) för att säkerställa att befintliga kunder inte behöver ändra något och det inte påverkar deras anslutningar till servern. Under den här ändringen har [BaltimoreCyberTrustRoot-rotcertifikatet](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **inte ersatts** med [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) och denna ändring uppskjuts för att tillåta mer tid för kunderna att göra ändringen.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Behöver jag göra några ändringar på klienten för att upprätthålla anslutningen?

Ingen ändring krävs på klient sidan. Om du har följt vår tidigare rekommendation nedan kan du fortfarande fortsätta att ansluta så länge **BaltimoreCyberTrustRoot-certifikatet inte har tagits bort** från det kombinerade CA-certifikatet. **Vi rekommenderar att inte ta bort BaltimoreCyberTrustRoot från ditt kombinerade CA-certifikat förrän ytterligare meddelande om att upprätthålla anslutningen.**

### <a name="previous-recommendation"></a>Föregående rekommendation

*   Hämta BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 rot certifikat utfärdare från länkarna nedan:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Generera ett kombinerat CA-certifikatarkiv med både **BaltimoreCyberTrustRoot** -och **DigiCertGlobalRootG2** -certifikat ingår.
    *   För Java-användare (PostgreSQL JDBC) som använder DefaultJavaSSLFactory kör du:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Ersätt sedan den ursprungliga nyckel lagrings filen med den nya som genererade:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "lösen ord");
        
    *   För .NET-användare (Npgsql) i Windows kontrollerar du att **Baltimore CyberTrust root** och **DigiCert global root G2** finns både i Windows certifikat Arkiv, betrodda rot certifikat utfärdare. Importera certifikatet som saknas om det inte finns några certifikat.

        ![Azure Database for PostgreSQL .net-certifikat](media/overview/netconnecter-cert.png)

    *   För .NET (Npgsql)-användare på Linux med hjälp av SSL_CERT_DIR kontrollerar du att **BaltimoreCyberTrustRoot** och **DigiCertGlobalRootG2** finns i katalogen som anges av SSL_CERT_DIR. Om det inte finns några certifikat, skapar du den certifikat fil som saknas.

    *   För andra PostgreSQL-klient användare kan du slå samman två CA-certifikatfiler som detta format nedan

        </br>-----BEGIN CERTIFICATE-----
 </br>(Rot-CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----SLUT CERTIFIKAT-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(Rot-CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----SLUT CERTIFIKAT-----

*   Ersätt den ursprungliga PEM-filen för rot certifikat utfärdaren med den kombinerade rot-CA-filen och starta om programmet/klienten.
*    I framtiden kan du efter det nya certifikatet som distribuerats på Server sidan Ändra CA PEM-filen till DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Ta inte bort eller ändra **Baltimore-certifikatet** förrän certifikat ändringen har gjorts. Vi kommer att skicka en kommunikation när ändringen har gjorts, och det är säkert att ta bort Baltimore-certifikatet. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Varför bytte BaltimoreCyberTrustRoot-certifikatet inte till DigiCertGlobalRootG2 under den här ändringen den 15 februari 2021?

Vi utvärderade kundens beredskap för den här ändringen och realiserade många kunder sökte efter ytterligare ledtid för att hantera den här ändringen. För att tillhandahålla mer ledtid till kunder för beredskap har vi beslutat att skjuta upp certifikat ändringen till DigiCertGlobalRootG2 under minst ett år och tillhandahålla tillräckligt med ledtid för kunderna och slutanvändarna. 

Våra rekommendationer till användare är, Använd de ovannämnda stegen för att skapa ett kombinerat certifikat och ansluta till servern, men ta inte bort BaltimoreCyberTrustRoot-certifikatet förrän vi skickar en kommunikation för att ta bort den. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Vad händer om vi har tagit bort BaltimoreCyberTrustRoot-certifikatet?

Du kommer att påbörjar anslutnings fel när du ansluter till din Azure Database for PostgreSQL-Server. Du måste konfigurera SSL med [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certifikatet igen för att upprätthålla anslutningen.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. om jag inte använder SSL/TLS måste jag fortfarande uppdatera rot certifikat utfärdaren?
Inga åtgärder krävs om du inte använder SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. om jag använder SSL/TLS måste jag starta om min databas server för att uppdatera rot certifikat utfärdaren?
Nej, du behöver inte starta om databas servern för att börja använda det nya certifikatet. Detta är en ändring på klient sidan och inkommande klient anslutningar måste använda det nya certifikatet för att säkerställa att de kan ansluta till databas servern.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Hur gör jag för att vet du om jag använder SSL/TLS med verifiering av rot certifikat?

Du kan identifiera om dina anslutningar verifierar rot certifikatet genom att granska anslutnings strängen.
-    Om din anslutnings sträng innehåller `sslmode=verify-ca` eller `sslmode=verify-full` måste du uppdatera certifikatet.
-    Om din anslutnings sträng innehåller `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` eller `sslmode=require` , behöver du inte uppdatera certifikat. 
-    Om anslutnings strängen inte anger sslmode behöver du inte uppdatera certifikat.

Om du använder en-klient som sammanfattar anslutnings strängen går du igenom klientens dokumentation för att ta reda på om den verifierar certifikat. Information om PostgreSQL-sslmode finns i avsnittet om [SSL-läge](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) i postgresql-dokumentationen.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. Vad är effekten om du använder App Service med Azure Database for PostgreSQL?
För Azure App Services, som ansluter till Azure Database for PostgreSQL, kan vi ha två möjliga scenarier och det beror på hur du använder SSL med ditt program.
*   Det nya certifikatet har lagts till App Service på plattforms nivå. Om du använder SSL-certifikat som finns på App Service plattform i ditt program krävs ingen åtgärd.
*   Om du uttryckligen inkluderar sökvägen till SSL-cert-filen i din kod måste du hämta det nya certifikatet och uppdatera koden för att använda det nya certifikatet. Ett användbart exempel på det här scenariot är när du använder anpassade behållare i App Service som delas i [App Service-dokumentationen](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. Vad är effekten om du använder Azure Kubernetes Services (AKS) med Azure Database for PostgreSQL?
Om du försöker ansluta till Azure Database for PostgreSQL med hjälp av Azure Kubernetes Services (AKS), liknar den ungefär åtkomst från en dedikerad kund värd miljö. Se stegen [här](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. Vad är effekten om du använder Azure Data Factory för att ansluta till Azure Database for PostgreSQL?
För att ansluta med hjälp av Azure Integration Runtime utnyttjar anslutningen certifikat i Windows certifikat Arkiv i Azure-värd miljön. Dessa certifikat är redan kompatibla med de nyligen tillämpade certifikaten och därför behövs ingen åtgärd.

För koppling med egen värd Integration Runtime där du uttryckligen inkluderar sökvägen till SSL-cert-filen i anslutnings strängen, måste du hämta det [nya certifikatet](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) och uppdatera anslutnings strängen för att använda den.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. behöver jag planera ett avbrott för databas server underhåll för den här ändringen?
Nej. Eftersom ändringen bara finns på klient sidan för att ansluta till databas servern, finns det inga underhålls avbrott som krävs för databas servern för den här ändringen.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. om jag skapar en ny server efter den 15 februari 2021 (02/15/2021) kommer jag att påverkas?
För servrar som skapats efter den 15 februari 2021 (02/15/2021) kommer du fortsätta att använda [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) för dina program för att ansluta med SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. hur ofta kommer Microsoft att uppdatera sina certifikat eller vad är förfallo principen?
Dessa certifikat som används av Azure Database for PostgreSQL tillhandahålls av betrodda certifikat utfärdare (CA). Detta innebär att stöd för dessa certifikat är knutet till stöd för dessa certifikat av CA. [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) -certifikatet är schemalagt att förfalla i 2025 så att Microsoft måste utföra en ändring av certifikatet innan förfallo datum. Även om det finns oförutsedda buggar i dessa fördefinierade certifikat, måste Microsoft göra certifikats rotationen på det tidigaste sätt som genomfördes den 15 februari 2021 för att säkerställa att tjänsten är säker och kompatibel hela tiden.

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. om jag använder Läs repliker behöver jag bara utföra den här uppdateringen på den primära servern eller Läs replikerna?
Eftersom den här uppdateringen är en ändring på klient sidan, om klienten som används för att läsa data från replik servern, måste du även tillämpa ändringarna för dessa klienter. 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. har vi en fråga på Server sidan för att kontrol lera om SSL används?
För att kontrol lera om du använder SSL-anslutning för att ansluta till servern, se [SSL-verifiering](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. finns det en åtgärd som behövs om jag redan har DigiCertGlobalRootG2 i min certifikat fil?
Nej. Ingen åtgärd krävs om certifikat filen redan har **DigiCertGlobalRootG2**.

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. Vad händer om du använder Docker-avbildning av PgBouncer sidvagn från Microsoft?
En ny Docker-avbildning som stöder både [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) och [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) publiceras [till nedan (](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) senaste taggen). Du kan hämta den här nya avbildningen för att undvika avbrott i anslutningen från den 15 februari 2021. 

### <a name="14-what-if-i-have-further-questions"></a>14. Vad händer om jag har fler frågor?
Om du har frågor kan du få svar från community-experter i [Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). [Kontakta oss](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com) om du har en Support plan och behöver teknisk hjälp
