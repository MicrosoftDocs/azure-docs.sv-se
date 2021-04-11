---
title: Exportera certifikat kedja för betrodda klient certifikat utfärdare för klientautentisering
titleSuffix: Azure Application Gateway
description: Lär dig hur du exporterar en certifikat kedja för en betrodd klient certifikat utfärdare för klientautentisering på Azure Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231632"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Exportera en certifikat kedja för betrodda klient certifikat utfärdare som ska användas med klientautentisering
För att kunna konfigurera ömsesidig autentisering med-klienten, eller klientautentisering, kräver Application Gateway en betrodd certifikat utfärdare för certifikat utfärdare som ska överföras till gatewayen. Om du har flera certifikat kedjor måste du skapa kedjorna separat och ladda upp dem som olika filer på Application Gateway. I den här artikeln får du lära dig hur du exporterar en certifikat kedja för betrodda klient certifikat utfärdare som du kan använda i konfigurationen av klientautentisering på din gateway.  

## <a name="prerequisites"></a>Förutsättningar

Ett befintligt klient certifikat krävs för att generera kedjan över betrodda klient certifikat utfärdare. 

## <a name="export-trusted-client-ca-certificate"></a>Exportera certifikat för betrodd klient certifikat utfärdare

Certifikat från betrodd klient certifikat utfärdare krävs för att tillåta klientautentisering på Application Gateway. I det här exemplet ska vi använda ett TLS/SSL-certifikat för klient certifikatet, exportera dess offentliga nyckel och sedan exportera certifikat UTFÄRDARens certifikat från den offentliga nyckeln för att hämta certifikat för betrodda klient certifikat utfärdare. Sedan sammanfogar vi alla klient certifikat utfärdares certifikat till en certifikat kedja för betrodda klient certifikat utfärdare. 

Med följande steg kan du exportera. pem-eller. CER-filen för ditt certifikat:

### <a name="export-public-certificate"></a>Exportera offentligt certifikat 

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp certifikatet, vanligt vis i "certificates-Current User\Personal\Certificates" och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte kan hitta certifikatet under aktuell User\Personal\Certificates, kan du av misstag ha öppnat "certifikat-lokal dator" i stället för "certifikat – aktuell användare"). Om du vill öppna certifikat hanteraren i aktuell användar omfattning med PowerShell skriver du *certmgr* i konsol fönstret.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar certifikat hanteraren med certifikat som valts och en snabb meny med alla aktiviteter och sedan Exportera markerade.](./media/certificates-for-backend-authentication/export.png)

2. Klicka på **Nästa** i guiden.
    > [!div class="mx-imgBorder"]
    > ![Exportera certifikatet](./media/certificates-for-backend-authentication/exportwizard.png)

3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.
    > [!div class="mx-imgBorder"]
    > ![Exportera inte den privata nyckeln](./media/certificates-for-backend-authentication/notprivatekey.png)

4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.
    > [!div class="mx-imgBorder"]
    > ![Base – 64-kodad](./media/certificates-for-backend-authentication/base64.png)

5. För **fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa.**

    > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar guiden Exportera certifikat där du anger en fil som ska exporteras.](./media/certificates-for-backend-authentication/browse.png)

6. Klicka på **Slutför** för att exportera certifikatet.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar guiden Exportera certifikat när du har slutfört fil exporten.](./media/certificates-for-backend-authentication/finish.png)

7. Ditt certifikat har exporter ATS.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar guiden Exportera certifikat med ett meddelande som visar att meddelandet lyckades.](./media/certificates-for-backend-authentication/success.png)

   Det exporterade certifikatet ser ut ungefär så här:

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar en certifikat symbol.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>Exportera CA-certifikat från det offentliga certifikatet

Nu när du har exporterat ditt offentliga certifikat kommer du nu att exportera certifikat UTFÄRDARens certifikat från ditt offentliga certifikat. Om du bara har en rot certifikat utfärdare behöver du bara exportera certifikatet. Men om du har 1 + mellanliggande certifikat utfärdare måste du även exportera var och en av dem. 

1. När den offentliga nyckeln har exporter ATS öppnar du filen.

    > [!div class="mx-imgBorder"]
    > ![Öppna certifikat för auktorisering](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![om certifikat](./media/mutual-authentication-certificate-management/general.png)

1. Välj fliken certifierings Sök väg om du vill visa certifikat utfärdaren.

    > [!div class="mx-imgBorder"]
    > ![certifikat information](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Välj rot certifikatet och klicka på **Visa certifikat**.

    > [!div class="mx-imgBorder"]
    > ![certifikat Sök väg](./media/mutual-authentication-certificate-management/root-cert.png) 

   Du bör se information om rot certifikatet.

    > [!div class="mx-imgBorder"]
    > ![certifikat information](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Välj fliken **information** och klicka på **Kopiera till fil...**

    > [!div class="mx-imgBorder"]
    > ![Kopiera rot certifikat](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. Nu har du extraherat informationen om rot certifikat utfärdarens certifikat från det offentliga certifikatet. Du ser **guiden Exportera certifikat**. Följ steg 2-7 från föregående avsnitt ([Exportera offentligt certifikat](./mutual-authentication-certificate-management.md#export-public-certificate)) för att slutföra guiden Exportera certifikat. 

1. Upprepa nu steg 2-6 från det aktuella avsnittet ([Exportera CA-certifikat från det offentliga certifikatet](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) för alla mellanliggande certifikat utfärdare för att exportera alla mellanliggande CA-certifikat i Base-64-kodad X. 509 (. CER-format.

    > [!div class="mx-imgBorder"]
    > ![mellanliggande certifikat](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Du kan till exempel Upprepa steg 2-6 från det här avsnittet på *MSIT CAZ2* -mellanliggande certifikat utfärdare för att extrahera det som ett eget certifikat. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Sammanfoga alla CA-certifikat till en fil

1. Kör följande kommando med alla certifikat utfärdare som du extraherade tidigare. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Ditt resulterande kombinerade certifikat bör se ut ungefär så här:
    
    > [!div class="mx-imgBorder"]
    > ![kombinerat certifikat](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Nästa steg

Nu har du den betrodda certifikat UTFÄRDARens certifikat kedja. Du kan lägga till detta i konfigurationen av klientautentisering på Application Gateway för att tillåta ömsesidig autentisering med din gateway. Se [Konfigurera ömsesidig autentisering med Application Gateway med Portal](./mutual-authentication-portal.md) eller [Konfigurera ömsesidig autentisering med Application Gateway med PowerShell](./mutual-authentication-powershell.md).

