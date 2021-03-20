---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12e9bec0c560f1b068b07a1b6afe218a112e439f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553196"
---
När du har skapat ett självsignerat rot certifikat exporterar du rot certifikatet offentlig nyckel. cer-fil (inte den privata nyckeln). Du kommer senare att ladda upp den här filen till Azure. Med följande steg kan du exportera. CER-filen för ditt självsignerade rot certifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte kan hitta certifikatet under aktuell User\Personal\Certificates, kan du av misstag ha öppnat "certifikat-lokal dator" i stället för "certifikat – aktuell användare"). Om du vill öppna certifikat hanteraren i aktuell användar omfattning med PowerShell skriver du *certmgr* i konsol fönstret.

   ![Skärm bild som visar fönstret certifikat för den aktuella användaren med certifikat som valts och en snabb meny med exportera valt från alla aktiviteter.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klicka på **Nästa** i guiden.

   ![Exportera certifikatet](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

   ![Exportera inte den privata nyckeln](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

   ![Base – 64-kodad](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. För **fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa.**

   ![Skärm bild som visar guiden Exportera certifikat med text rutan fil namn och ett alternativ för bläddring.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klicka på **Slutför** för att exportera certifikatet.

   ![Skärm bild som visar guiden Exportera certifikat med de valda inställningarna.](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Ditt certifikat har exporter ATS.

   ![Skärm bild som visar ett meddelande om att exporten lyckades.](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Det exporterade certifikatet ser ut ungefär så här:

   ![Skärm bild som visar en certifikat ikon och ett fil namn med fil namns tillägget c e r.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Om du öppnar det exporterade certifikatet med hjälp av anteckningar ser du något som liknar det här exemplet. Avsnittet i blått innehåller den information som överförs till Azure. Om du öppnar ditt certifikat med anteckningar och det inte ser ut ungefär så här betyder det vanligt vis att du inte exporterade det med hjälp av Base-64-kodad X. 509 (. CER-format. Dessutom, om du vill använda en annan text redigerare, vet du att vissa redigerare kan introducera oavsiktlig formatering i bakgrunden. Detta kan skapa problem när du överför texten från det här certifikatet till Azure.

   ![Öppna med anteckningar](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
