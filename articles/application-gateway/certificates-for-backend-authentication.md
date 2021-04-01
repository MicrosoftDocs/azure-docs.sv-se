---
title: Certifikat som krävs för att tillåta backend-servrar
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller exempel på hur ett TLS/SSL-certifikat kan konverteras till autentiseringscertifikat och betrodda rot certifikat som krävs för att tillåta Server dels instanser i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 874e554063f64ddefce99a223678d64b2e0774c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397730"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Skapa certifikat för att tillåta serverdelen i Azure Application Gateway

För att kunna avsluta TLS måste Application Gateway kräva att Server dels instanserna ska tillåtas genom att överföra autentisering/betrodda rot certifikat. För v1 SKU krävs autentiserings-certifikat, men för de v2 SKU-betrodda rot certifikaten krävs för att tillåta certifikaten.

I den här artikeln kan du se hur du:


- Exportera autentiseringscertifikat från ett Server dels certifikat (för v1 SKU)
- Exportera ett betrott rot certifikat från ett Server dels certifikat (för v2-SKU)

## <a name="prerequisites"></a>Förutsättningar

Ett befintligt Server dels certifikat krävs för att generera de autentiseringscertifikat eller betrodda rot certifikat som krävs för att tillåta Server dels instanser med Application Gateway. Server dels certifikatet kan vara detsamma som TLS/SSL-certifikatet eller ett annat för ytterligare säkerhet. Application Gateway ger dig ingen mekanism för att skapa eller köpa ett TLS/SSL-certifikat. I test syfte kan du skapa ett självsignerat certifikat men du bör inte använda det för produktions arbets belastningar. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportera certifikat för autentisering (för v1 SKU)

Ett autentiseringscertifikat krävs för att tillåta Server dels instanser i Application Gateway v1 SKU. Autentiseringscertifikatet är den offentliga nyckeln för backend-server certifikat i Base-64-kodad X. 509 (. CER-format. I det här exemplet ska du använda ett TLS/SSL-certifikat för Server dels certifikatet och exportera dess offentliga nyckel som ska användas som autentiserings-certifiering. I det här exemplet ska du också använda Windows Certificate Manager-verktyget för att exportera de certifikat som krävs. Du kan välja att använda andra verktyg som är praktiska.

Exportera filen offentlig Key. cer från TLS/SSL-certifikatet (inte den privata nyckeln). Med följande steg kan du exportera. CER-filen i Base-64-kodad X. 509 (. CER-format för certifikatet:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp certifikatet, vanligt vis i "certificates-Current User\Personal\Certificates" och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte kan hitta certifikatet under aktuell User\Personal\Certificates, kan du av misstag ha öppnat "certifikat-lokal dator" i stället för "certifikat – aktuell användare"). Om du vill öppna certifikat hanteraren i aktuell användar omfattning med PowerShell skriver du *certmgr* i konsol fönstret.

   ![Skärm bild som visar certifikat hanteraren med certifikat som valts och en snabb meny med alla aktiviteter och sedan Exportera markerade.](./media/certificates-for-backend-authentication/export.png)

2. Klicka på **Nästa** i guiden.

   ![Exportera certifikatet](./media/certificates-for-backend-authentication/exportwizard.png)

3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

   ![Exportera inte den privata nyckeln](./media/certificates-for-backend-authentication/notprivatekey.png)

4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

   ![Base – 64-kodad](./media/certificates-for-backend-authentication/base64.png)

5. För **fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa.**

   ![Skärm bild som visar guiden Exportera certifikat där du anger en fil som ska exporteras.](./media/certificates-for-backend-authentication/browse.png)

6. Klicka på **Slutför** för att exportera certifikatet.

   ![Skärm bild som visar guiden Exportera certifikat när du har slutfört fil exporten.](./media/certificates-for-backend-authentication/finish.png)

7. Ditt certifikat har exporter ATS.

   ![Skärm bild som visar guiden Exportera certifikat med ett meddelande som visar att meddelandet lyckades.](./media/certificates-for-backend-authentication/success.png)

   Det exporterade certifikatet ser ut ungefär så här:

   ![Skärm bild som visar en certifikat symbol.](./media/certificates-for-backend-authentication/exported.png)

8. Om du öppnar det exporterade certifikatet med hjälp av anteckningar ser du något som liknar det här exemplet. Avsnittet i blått innehåller den information som överförs till Application Gateway. Om du öppnar ditt certifikat med anteckningar och det inte ser ut ungefär så här betyder det vanligt vis att du inte exporterade det med hjälp av Base-64-kodad X. 509 (. CER-format. Dessutom, om du vill använda en annan text redigerare, vet du att vissa redigerare kan introducera oavsiktlig formatering i bakgrunden. Detta kan skapa problem när du överför texten från det här certifikatet till Azure.

   ![Öppna med anteckningar](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportera betrott rot certifikat (för v2-SKU)

Ett betrott rot certifikat krävs för att tillåta Server dels instanser i Application Gateway v2 SKU. Rot certifikatet är en Base-64-kodad X. 509 (. CER) formatera rot certifikat från backend-serverns certifikat. I det här exemplet ska vi använda ett TLS/SSL-certifikat för Server dels certifikatet, exportera dess offentliga nyckel och sedan exportera rot certifikatet för den betrodda certifikat utfärdaren från den offentliga nyckeln i Base64-kodat format för att hämta det betrodda rot certifikatet. De mellanliggande certifikaten ska paketeras med ett Server certifikat och installeras på backend-servern.

Med följande steg kan du exportera. CER-filen för certifikatet:

1. Använd stegen 1-8 som nämns i föregående avsnitt [Exportera autentiseringscertifikat (för v1 SKU)](#export-authentication-certificate-for-v1-sku) för att exportera den offentliga nyckeln från Server dels certifikatet.

2. När den offentliga nyckeln har exporter ATS öppnar du filen.

   ![Öppna certifikat för auktorisering](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![om certifikat](./media/certificates-for-backend-authentication/general.png)

3. Gå till vyn certifierings Sök väg om du vill visa certifikat utfärdaren.

   ![certifikat information](./media/certificates-for-backend-authentication/certdetails.png)

4. Välj rot certifikatet och klicka på **Visa certifikat**.

   ![certifikat Sök väg](./media/certificates-for-backend-authentication/rootcert.png)

   Du bör se information om rot certifikatet.

   ![certifikat information](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Flytta till vyn **information** och klicka på **Kopiera till fil...**

   ![Kopiera rot certifikat](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Nu har du extraherat informationen om rot certifikatet från Server dels certifikatet. Du ser **guiden Exportera certifikat**. Använd nu steg 2-9 som nämns i avsnittet **Exportera autentiseringscertifikat från ett Server dels certifikat (för v1 SKU)** ovan för att exportera det betrodda rot certifikatet i Base-64-kodad X. 509 (. CER-format.

## <a name="next-steps"></a>Nästa steg

Nu har du autentiserings certifikatet/det betrodda rot certifikatet i Base-64-kodad X. 509 (. CER-format. Du kan lägga till detta i Application Gateway för att tillåta Server dels servrar för slut punkt till slut punkt för TLS-kryptering. Se [Konfigurera end to end-TLS med hjälp av Application Gateway med PowerShell](./application-gateway-end-to-end-ssl-powershell.md).