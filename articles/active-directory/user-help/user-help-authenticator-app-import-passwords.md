---
title: Importera lösen ord till Microsoft Authenticator app – Azure AD
description: Så här importerar du lösen ord till Microsoft Authentication-appen från populära lösen ords hanterare.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b0d7eeeb840a3efc560c20310b38bee93a038795
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056266"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importera lösen ord till Microsoft Authenticator-appen

Microsoft Authenticator stöder import av lösen ord från Google Chrome, Firefox, LastPass, Bitwarden och RoboForm. Om Microsoft inte har stöd för din befintliga lösen ords hanterare kan du [manuellt ange inloggnings uppgifter till vår mall CSV](https://go.microsoft.com/fwlink/?linkid=2134938). Om du vill importera dina befintliga lösen ord och hantera dem i Authenticator-appen, behöver du bara exportera dina lösen ord från din befintliga lösen ords hanterare till formatet CSV (kommaavgränsade värden). Importera sedan den exporterade CSV-filen till Authenticator i webb läsar tillägget för Chrome eller direkt till Authenticator-appen (Android och iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importera från Google Chrome eller Android Smart Lock

Du kan importera dina lösen ord från Google Chrome eller Android Smart Lock till Authenticator på antingen din smartphone eller din station ära dator. Du kan:

- [Importera från Chrome på Android och iOS](#import-from-chrome-on-android-and-ios)
- [Importera från Chrome Desktop Browser](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importera från Chrome på Android och iOS

Google Chrome-användare på Android och Apple-telefoner kan importera sina lösen ord direkt från deras telefon med några få enkla steg.

1. Installera Authenticator-appen på din telefon och öppna fliken **lösen ord** .

1. Logga in på Google Chrome på din telefon.

1. Tryck på ![ Google Chrome-ellipsen längst ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) upp till höger för Android-telefoner eller längst ned till höger för iOS-enheter och tryck sedan på **Inställningar.**

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Meny plats för Google Chrome-inställningar](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Meny ikonen Google Chrome-inställningar](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. I **Inställningar** öppnar du **lösen ord**.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Sökväg till kommandot Andoid Chrome passwords](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Kommando plats för Apple Chrome-lösenord](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. På Android-enheter trycker du på ![ Google Chrome ellips-menyn längst ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) upp till höger för Android-telefoner eller längst ned till höger för iOS-enheter och sedan på **Exportera lösen ord**.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Sökväg till exportera lösen ord för Android Chrome](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple Chrome-sökväg för lösen ords export](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Du måste ange en PIN-kod, ett finger avtryck eller ansikts igenkänning. Bekräfta din identitet och tryck på **Exportera lösen ord** igen för att starta exporten.

1. När lösen orden har exporter ATS, uppmanas du att välja vilken app du importerar till. Välj **autentiserare** för att starta importen av lösen ord. Du får information om import status när den är klar.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Sökväg för att importera lösen ord för Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Apple Chrome-plats för import av lösen ord](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importera från Chrome Desktop Browser

Innan du börjar måste du installera och logga in på [Microsoft Auto-tillägget](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) i din Chrome-webbläsare.

1. Öppna [Google Password Manager](https://passwords.google.com) i valfri webbläsare. Logga in på ditt Google-konto om du inte redan gjort det.

1. Välj kugg hjuls ikonen ![Kugg hjuls ikonen för Desktop Password Manager](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) för att öppna på sidan med lösen ords inställningar.

1. Välj **Exportera**. på nästa sida väljer du **Exportera** igen för att börja exportera dina lösen ord. Ange ditt Google-lösenord när du uppmanas att bekräfta din identitet. Du får information om import status när den är klar.

   ![Skriv bords Chrome-webbläsare exportera lösen ord, kommando plats](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Öppna tillägget Autofyll Chrome och välj **Inställningar**.

   ![Skriv bords Chrome webbläsare alternativ för Autofyll tillägg](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Välj **Importera data** för att öppna en dialog ruta. Välj sedan **Välj fil** för att hitta och importera CSV-filen.

   ![Desktop Chrome-webbläsare importera data CSV-plats](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importera från Firefox

Med Firefox kan du bara exportera lösen ord från Skriv bordets webbläsare, så se till att du har åtkomst till webbläsaren Firefox Desktop innan du importerar lösen ord från Firefox.

1. Logga in på den senaste versionen av Firefox på Skriv bordet och välj ![Firefox "Hamburger"-menyn](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) på menyn längst upp till höger på skärmen.

1. Välj **inloggningar och lösen ord**.

   ![Inloggnings platser och lösen ord för Skriv bords Firefox webbläsare](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. På sidan Firefox Lockwise väljer du menyn ![ Firefox-menyn ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) , väljer **Exportera inloggningar** och bekräftar sedan din avsikt genom att välja **Exportera**. Du uppmanas att identifiera dig själv genom att ange din PIN-kod, enhets lösen ord eller genom att skanna dina finger avtryck. När du har identifierat, exporterar Firefox dina lösen ord i CSV-format till den valda platsen.

   ![Skriv bords Firefox webbläsare exportera lösen ord plats](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Du kan importera dina lösen ord till autentiseraren från en webbläsare eller iOS-eller Android-telefoner. Så här importerar du till Authenticator-appen på din telefon:

      1. Överför den exporterade CSV-filen på din Android-eller iOS-telefon med ett önskat och säkert sätt och hämta den sedan. Sedan delar du CSV-filen med Authenticator-appen för att starta importen.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Sökväg för att importera lösen ord för Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Chrome-plats för import av lösen ord](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. När du har importerat lösen ordet till autentiseraren tar du bort CSV-filen från Skriv bordet eller mobil telefonen.

## <a name="import-from-lastpass"></a>Importera från LastPass

LastPass har endast stöd för att exportera lösen ord från en webbläsare, så se till att du har åtkomst till en Skriv bords webbläsare innan du börjar importera lösen ord.

1. Logga in på [Lastpass-webbplatsen](https://lastpass.com) och välj **Avancerade alternativ** och välj sedan **Exportera**.

   ![Desktop LastPass-sökväg för lösen ords export](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Identifiera dig själv när du uppmanas att ange ditt huvud lösen ord. Därefter ser du de exporterade lösen orden på webb sidan.

1. Kopiera innehållet på webb sidan.

1. Öppna Anteckningar (eller din favorit text redigerare) och klistra in det kopierade innehållet.

1. Spara den här anteckningar-filen genom att välja **Arkiv** &gt; **Spara som**. Ange ett namn som slutar med ". csv" (till exempel LastPass.csv) på en säker plats på Skriv bordet.

   ![Desktop-LastPass spara CSV-fil](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Du kan importera dina lösen ord till autentiseraren i en webbläsare eller på iOS-eller Android-telefoner. Så här importerar du till Authenticator-appen på din telefon:

      1. Överför den exporterade CSV-filen på din smartphone med ett önskat och säkert sätt och hämta den sedan. Dela sedan CSV-filen med Authenticator-appen för att starta importen.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Sökväg till import lösen ord för Android-LastPass](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple LastPass-plats för import av lösen ord](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. När du har importerat lösen ordet till autentiseraren tar du bort CSV-filen från Skriv bordet eller mobil telefonen.

## <a name="import-from-bitwarden"></a>Importera från Bitwarden

Bitwarden har endast stöd för att exportera lösen ord från en webbläsare, så se till att du har åtkomst till en Skriv bords webbläsare innan du börjar importera lösen ord.

1. Logga in i https://vault.bitwarden.com/ och välj **verktyg** &gt; **export valv**. Välj fil formatet som CSV, ange ditt huvud lösen ord och välj sedan **Exportera valv** för att starta exporten.

   ![Plats för Bitwarden export valv](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Du kan importera dina lösen ord till autentiseraren i en webbläsare eller på iOS-eller Android-telefoner. Så här importerar du till Authenticator-appen på din telefon:

      1. Överför den exporterade CSV-filen på din smartphone med ett önskat och säkert sätt och hämta den sedan. Dela sedan CSV-filen med Authenticator-appen för att starta importen.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Sökväg till import lösen ord för Android-Bitwarden](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Bitwarden-plats för import av lösen ord](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. När du har importerat lösen ordet till autentiseraren tar du bort CSV-filen från Skriv bordet eller mobil telefonen.

## <a name="import-from-roboform"></a>Importera från RoboForm

RoboForm tillåter endast export av lösen ord från sin Desktop-app, så se till att du har åtkomst till RoboForm-appen på ett skriv bord innan du påbörjar importen.

1. Starta RoboForm från Skriv bords klienten och logga in på ditt konto.

1. Välj **alternativ** på menyn **RoboForm** .

   ![Alternativ-menyn för Station ära RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Välj **konto & data** &gt; **export**.

   ![Skriv bords RoboForm export kommando plats](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Välj en säker plats där du vill spara den exporterade filen. Välj **inloggningar** som **DATATYP** och välj CSV-filen som format och välj sedan **Exportera**.

   ![Dialog rutan export av skriv bord RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Bekräfta din avsikt och att CSV-filen exporteras till den valda platsen.

   ![Bekräftelse dialog ruta för RoboForm av skriv bord](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Du kan importera dina lösen ord till autentiseraren i en webbläsare eller på iOS-eller Android-telefoner. Så här importerar du till Authenticator-appen på din telefon:

      1. Överför den exporterade CSV-filen på din smartphone med ett önskat och säkert sätt och hämta den sedan. Dela sedan CSV-filen med Authenticator-appen för att starta importen.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Sökväg till import lösen ord för Android-RoboForm](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple RoboForm-plats för import av lösen ord](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. När du har importerat lösen ordet till autentiseraren tar du bort CSV-filen från Skriv bordet eller mobil telefonen.

## <a name="import-by-creating-a-csv"></a>Importera genom att skapa en CSV

Om steg för att importera lösen ord från din lösen ords hanterare inte visas i den här artikeln kan du skapa en CSV-fil som du kan använda för att importera dina lösen ord till autentiseraren. Microsoft rekommenderar att du följer de här stegen på en stationär dator för att under lätta formateringen.

1. [Ladda ned och öppna vår import-mall](https://go.microsoft.com/fwlink/?linkid=2134938)på Skriv bordet. Om du är en Apple iPhone-, Safari-och nyckel rings användare kan du nu gå vidare till steg 4.

1. Exportera dina lösen ord från din befintliga lösen ords hanterare i en okrypterad CSV-fil.

1. Kopiera de relevanta kolumnerna från din exporterade CSV till mallen CSV och spara sedan.

1. Om du inte har en exporterad CSV-fil kan du kopiera varje inloggning från din befintliga lösen ords hanterare till mallen CSV. Ta inte bort eller ändra rubrik raden. När du är klar kontrollerar du integriteten för dina data innan du påbörjar nästa steg.

1. Du kan importera dina lösen ord till autentiseraren i en webbläsare eller på iOS-eller Android-telefoner. Så här importerar du till Authenticator-appen på din telefon:

      1. Överför den exporterade CSV-filen på din smartphone med ett önskat och säkert sätt och hämta den sedan. Dela sedan CSV-filen med Authenticator-appen för att starta importen.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Plats för import lösen ord för Android CSV](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Plats för import lösen ord för Apple CSV](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. När du har importerat lösen ordet till autentiseraren tar du bort CSV-filen från Skriv bordet eller mobil telefonen.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Den vanligaste orsaken till misslyckade importer är felaktig formatering i CSV-filen. Du kan prova följande steg för att felsöka problemet.

- Se efter i den här artikeln om vi redan har stöd för import av lösen ord från den aktuella lösen ords hanteraren. Om vi gör det kanske du vill försöka importera igen genom att följa anvisningarna för respektive Provider.

- Om vi för närvarande inte har stöd för att importera formatet för din lösen ords hanterare kan du försöka igen genom [att skapa CSV-filen manuellt](#import-by-creating-a-csv).

- Du kan kontrol lera integriteten för CSV-data med följande förslag:

  - Första raden måste innehålla ett sidhuvud med tre kolumner: **URL**, **användar namn** och **lösen ord**.

  - Varje rad måste innehålla ett värde under kolumnerna **URL** och **lösen ord** .

- Du kan återskapa CSV-filen genom att klistra in innehållet i [CSV-mallfilen](https://go.microsoft.com/fwlink/?linkid=2134938).

- Om inget annat fungerar kan du rapportera problemet med hjälp av länken **Skicka feedback** från verifierings program inställningar.
