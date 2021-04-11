---
title: 'Självstudie: kom igång med verifierbara autentiseringsuppgifter med en exempel App (för hands version)'
description: I den här självstudien får du lära dig hur du utfärdar verifierbara autentiseringsuppgifter med hjälp av vår exempel-App och test klient
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 03/31/2021
ms.openlocfilehash: 4914df0df30ed990dd2c692f0f5b57586d2a75c6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106171996"
---
# <a name="tutorial-get-started-with-verifiable-credentials-using-a-sample-app-preview"></a>Självstudie: kom igång med verifierbara autentiseringsuppgifter med en exempel App (för hands version)

I den här självstudien går vi igenom de steg som krävs för att utfärda dina första verifierbara autentiseringsuppgifter: ett kontrollerat expert kort för autentiseringsuppgifter. Du kan sedan använda det här kortet för att bevisa att du är en verifierad administratörs expert, som är Master i den digitala autentiseringsuppgiften. Kom igång med Azure Active Directory verifierbara autentiseringsuppgifter genom att använda exempel appen för verifierbara autentiseringsuppgifter för att utfärda dina första verifierbara autentiseringsuppgifter.

![Det här är en bild av ett exempel kort](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

- [NodeJS](https://nodejs.org/en/download/) version 10,14 eller senare installerat på vårt test system.
- Du behöver [git](https://git-scm.com/downloads) installerat om du vill klona lagrings platsen som är värd för exempel appen
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Ett system som är värd för vår exempel webbplats.
- En mobil enhet med Microsoft Authenticator version 6.2005.3599 eller senare installerad.
- [NGROK](https://ngrok.com/) kostnads fritt.

## <a name="download-the-sample-code"></a>Hämta exempel koden

Om du vill skicka ett expert kort till en verifierad autentiseringsuppgift måste du köra en webbplats på den lokala datorn. Webbplatsen används för att initiera en process för utfärdande av verifierbara autentiseringsuppgifter. Vi har tillhandahållit en enkel webbplats, skriven i NodeJS, som vi använder i den här självstudien.

Börja med att hämta exempel koden från GitHub [här](https://github.com/Azure-Samples/active-directory-verifiable-credentials), eller klona lagrings platsen till din lokala dator:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Du kanske vill bekanta dig med koden i exempel webbplatserna. `issuer`Mappen innehåller all kod som används för att utfärda verifierbara autentiseringsuppgifter. Mer information finns i exempel filen [README](https://github.com/Azure-Samples/active-directory-verifiable-credentials).

## <a name="run-the-issuer-website"></a>Köra webbplatsen för utfärdare

Du kan köra stegen inifrån Visual Studio Code eller en kommando rad som är tillgänglig i operativ systemet. 

1. Gå till mappen `issuer`. 

    ```terminal
    cd issuer
    ```

2. När vi behöver installera alla nödvändiga paket och starta-platsen.

   ```terminal
    npm install
    node app.js
    ```

3. I terminalen visas nu att din Issuer-app lyssnar på port 8081. Nu ska vi konfigurera en omvänd proxy med Ngrok så att autentiseraren kan kommunicera med din app. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Skapa en omvänd proxy med Ngrok

När du kör exempel webbplatsen måste enheten kommunicera med den nod server som körs på den lokala datorn. Vi rekommenderar att du använder [ngrok](https://ngrok.com/) som ett enkelt sätt att göra din lokala utvecklings server tillgänglig via Internet.

1.  När du har laddat ned och extraherat **ngrok** måste vi köra:

    ```terminal
     ngrok http 8081
    ```

Som standard körs exempel webbplatsen på port `8081` . **Ngrok** matar ut två URL: er för vidarebefordran för servern. Kopiera URL: en med `https://` prefixet.

![ngrok hjälper dig att göra dina program slut punkter tillgängliga via Internet](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Om du använder PowerShell kan du behöva ange för att `./ngrok` kommandot ska kunna identifieras.

Nu när den lokala porten exponeras för Internet med hjälp av ngrok använder exempel platsen automatiskt det värdnamn som genereras av ngrok. Öppna webbläsaren och gå till URL: en för vidarebefordring av ngrok https. Du bör kunna besöka exempel platsens start sida. Om sidan öppnas kan enheten kommunicera med det exempel program som körs på den lokala servern. Nu är du redo att skicka ett expert kort till en verifierad autentiseringsuppgift.

## <a name="issue-a-credential"></a>Utfärda autentiseringsuppgifter

1. Installera autentiserare på din mobila enhet. Microsoft Authenticator används för att ta emot, lagra och presentera dina verifierbara autentiseringsuppgifter för berörda parter.

2. Sedan ska du utfärda en verifierbar autentiseringsuppgift. **Klicka på** knappen **Hämta autentiseringsuppgifter** . När du klickar på knappen **Hämta autentiseringsuppgifter** visas en QR-kod i exempel webbplatsen som du kan genomsöka med hjälp av autentiseraren. Om du visar webbplatsen från webbläsaren på din mobila enhet klickar du på knappen **Hämta autentiseringsuppgifter** utlöser en djup länk som öppnar Authenticator-appen och kräver inte genomsökning av en QR-kod.

   ![Knappen Hämta autentiseringsuppgifter](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Sök igenom webbplatsens QR-kod med hjälp av autentiserare, eller om du ansluter till webbplatsen via en mobil, klickar du på knappen Hämta autentiseringsuppgifter för att utlösa djup länken. 

   ![QR-kod ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Observera att knappen **Lägg till** är nedtonad för tillfället. Välj **Logga in på ditt konto** under kort bilden.

   ![Logga in ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Innan du får ditt expert kort för autentiseringsuppgifter måste klienten som vi använder ha autentiseringsinformation. Om det här är första gången du går igenom självstudien har du inte något expert konto för autentiseringsuppgifter, skapa ett nytt användar konto i vår B2C-klient.

   ![autentisera innan du fortsätter](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. När du har loggat in är knappen **Lägg till** inte längre nedtonad. Välj **Lägg till** för att acceptera din nya VC.

   ![Välj Lägg till efter autentisering](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Grattis! Nu har du en verifierad Credential-expert VC.

   ![Credential expert VC har lagts till](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Nu är det dags att verifiera dina autentiseringsuppgifter.

## <a name="validate-credentials"></a>Verifiera autentiseringsuppgifter

Nu när du har slutfört utfärdande delen av själv studie kursen och har en verifierbar autentiseringsuppgift i autentiserare, är det dags att validera den i din egen Verifier-app.

1.  Sluta köra din Issuer ngrok-tjänst.

    ```terminal
     control+c
    ```


2. Öppna mappen Verifier i ett annat terminalfönster och kör den på samma sätt som du körde utfärdar-appen.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Kör nu ngrok med Verifier-porten 8082.

    ```terminal
    ngrok http 8082
    ```

4. Öppna ngrok https Forwarding URL i webbläsaren och tryck på knappen **Verifiera autentiseringsuppgifter** .  

   ![knappen verifiera experten för autentiseringsuppgift](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Öppna Authenticator och skanna QR-koden.

   ![Skanna QR-kod för att verifiera autentiseringsuppgifter](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > I iOS är det överst till höger och på Android är det längst ned till höger. Skanna QR-koden.

6. Välj **Tillåt** på skärmen ny behörighets förfrågan i autentiseraren. Genom att trycka på Tillåt signerar du en verifierbar presentation med din uppdelade (decentraliserade identifierare) för att bevisa att du i själva verket kontrollerar den verifierbara autentiseringsuppgiften.

   ![ny behörighets förfrågan](media/get-started-verifiable-credentials/new-permission-request.png)

    När en lyckad presentation tre saker bör ha uppdaterats:

   1. Webb sidan ska nu Visa "Grattis, ditt namn" + är en verifierad autentiseringsuppgift expert! ".
   
    ![Grattis, verifiera igen](media/get-started-verifiable-credentials/congratulations.png)


   2. Din Verifier app Terminal ska också visa samma meddelande från loggarna.
   
    ![program aktivitet i kommando tolken](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. I autentiserare bör det finnas en post för den senaste aktiviteten i presentationen.

    ![Aktivitet i autentiserare](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> När du kör Verifier-appen kan ngrok sluta fungera och visa ett fel meddelande om att det finns för många anslutningar. Vi har hittat detta kan undvikas genom att registrera ditt konto med ngrok. 

## <a name="next-steps"></a>Nästa steg

Nu när du har slutfört snabb starts guiden är det dags att skapa en egen decentraliserad identifierare i tjänsten Azure AD verifierbara autentiseringsuppgifter och utfärda egna verifierbara autentiseringsuppgifter.

>[!div class="nextstepaction"] 
>[Konfigurera din egen utfärdare med hjälp av exempel appen för verifierbara autentiseringsuppgifter](./enable-your-tenant-verifiable-credentials.md)
