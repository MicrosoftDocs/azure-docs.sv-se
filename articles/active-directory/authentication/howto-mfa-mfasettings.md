---
title: Konfigurera Azure AD Multi-Factor Authentication – Azure Active Directory
description: Lär dig hur du konfigurerar inställningar för Azure AD Multi-Factor Authentication i Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: c067dba3a8af87e354019154fad8304fe9edfbbc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829664"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Konfigurera inställningar för Azure AD Multi-Factor Authentication

Om du vill anpassa slutanvändarupplevelsen för Azure AD Multi-Factor Authentication kan du konfigurera alternativ för inställningar som tröskelvärden för kontolåsning eller bedrägeriaviseringar och meddelanden. Vissa inställningar finns direkt i Azure Portal for Azure Active Directory (Azure AD) och vissa i en separat Azure AD Multi-Factor Authentication-portal.

Följande inställningar för Azure AD Multi-Factor Authentication är tillgängliga i Azure Portal:

| Funktion | Beskrivning |
| ------- | ----------- |
| [Kontoutelåsning](#account-lockout) | Tillfälligt låsa konton från att använda Azure AD Multi-Factor Authentication om det finns för många nekade autentiseringsförsök på en rad. Den här funktionen gäller endast för användare som anger en PIN-kod för autentisering. (MFA Server) |
| [Blockera/avblockera användare](#block-and-unblock-users) | Blockera specifika användare från att kunna ta emot Azure AD Multi-Factor Authentication-begäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användarna förblir blockerade i 90 dagar från den tidpunkt då de blockeras eller avblockerade manuellt. |
| [Bedrägerivarning](#fraud-alert) | Konfigurera inställningar som tillåter användare att rapportera bedrägliga verifieringsbegäranden. |
| [Aviseringar](#notifications) | Aktivera meddelanden om händelser från MFA Server. |
| [OATH-token](concept-authentication-oath-tokens.md) | Används i molnbaserade Azure AD MFA-miljöer för att hantera OATH-token för användare. |
| [Inställningar för telefonsamtal](#phone-call-settings) | Konfigurera inställningar för telefonsamtal och hälsning för molnmiljöer och lokala miljöer. |
| Leverantörer | Då visas alla befintliga autentiseringsproviders som du kan ha associerat med ditt konto. Nya autentiseringsproviders kanske inte skapas från och med den 1 september 2018 |

![Azure Portal – Inställningar för Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Kontoutelåsning

För att förhindra upprepade MFA-försök som en del av en attack kan du med inställningarna för kontolåsning ange hur många misslyckade försök som ska tillåtas innan kontot blir utelåst under en viss tidsperiod. Inställningarna för kontolåsning tillämpas endast när en PIN-kod anges för MFA-prompten.

Följande inställningar är tillgängliga:

* Antal MFA-nekade för att utlösa kontolåsning
* Minuter tills räknaren för kontolåsning återställs
* Minuter tills kontot avblockerades automatiskt

Om du vill konfigurera inställningar för kontolåsning slutför du följande inställningar:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Bläddra till **Azure Active Directory**  >  **MFA-kontolåsning**  >    >  **för säkerhet.**
1. Ange kräv-värden för din miljö och välj sedan **Spara.**

    ![Skärmbild av inställningarna för kontolåsning i Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Blockera och avblockera användare

Om en användares enhet har tappats bort eller blivit stulen kan du blockera Azure AD Multi-Factor Authentication-försök för det associerade kontot. Alla Azure AD Multi-Factor Authentication-försök för blockerade användare nekas automatiskt. Användarna är blockerade i 90 dagar från blockeringens starttid. Vi har publicerat en video om [hur du blockerar och avblockerar användare i din klientorganisation för](https://www.youtube.com/watch?v=WdeE1On4S1o) att visa hur du gör detta.

### <a name="block-a-user"></a>Blockera en användare

Utför följande steg för att blockera en användare:

1. Bläddra till **Azure Active Directory**  >  **MFA**  >    >  **Blockera/avblockera användare i Security.**
1. Välj **Lägg till** för att blockera en användare.
1. Ange användarnamnet för den blockerade användaren `username@domain.com` som och ange sedan en kommentar i *fältet* Orsak.
1. När du är klar väljer **du OK** för att blockera användaren.

### <a name="unblock-a-user"></a>Avblockera en användare

Avblockera en användare genom att utföra följande steg:

1. Bläddra till **Azure Active Directory**  >  **MFA-blockera/avblockera**  >    >  **användare.**
1. I kolumnen *Åtgärd* bredvid önskad användare väljer du **Avblockera**.
1. Ange en kommentar i *fältet Orsak till avblockering.*
1. När du är klar väljer **du OK** för att avblockera användaren.

## <a name="fraud-alert"></a>Bedrägerivarning

Med funktionen för bedrägeriavisering kan användare rapportera bedrägliga försök att komma åt sina resurser. När en okänd och misstänkt MFA-prompt tas emot kan användarna rapportera bedrägeriförsöket med hjälp av Microsoft Authenticator appen eller via sin telefon.

Följande konfigurationsalternativ för bedrägeriaviseringar är tillgängliga:

* **Blockera automatiskt användare som** rapporterar bedrägeri: Om en användare rapporterar bedrägeri blockeras Azure AD MFA-autentiseringsförsöken för användarkontot i 90 dagar eller tills en administratör avblockerar sitt konto. En administratör kan granska inloggningar med hjälp av inloggningsrapporten och vidta lämpliga åtgärder för att förhindra framtida bedrägerier. En administratör kan [sedan avblockera](#unblock-a-user) användarens konto.
* **Kod för att rapportera bedrägerier under den** första hälsningen: När användarna får ett telefonsamtal för att utföra multifaktorautentisering trycker de normalt för att bekräfta sin **#** inloggning. Om du vill rapportera bedrägerier anger användaren en kod innan han eller hon trycker på **#** . Den här koden **är 0** som standard, men du kan anpassa den.

   > [!NOTE]
   > Standardrösthälsningarna från Microsoft instruerar användarna att trycka **på 0# för** att skicka en bedrägerivarning. Om du vill använda en annan kod än **0** spelar du in och laddar upp dina egna anpassade rösthälsningar med lämpliga instruktioner för dina användare.

Utför följande steg för att aktivera och konfigurera bedrägeriaviseringar:

1. Bläddra till **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Fraud alert**.
1. Ange inställningen *Tillåt användare att skicka bedrägeriaviseringar* till **På.**
1. Konfigurera inställningen *Blockera automatiskt användare som rapporterar bedrägeri eller* Kod för att rapportera bedrägerier under den första *hälsningen* efter behov.
1. När du är klar väljer du **Spara**.

### <a name="view-fraud-reports"></a>Visa bedrägerirapporter

Välj **Azure Active Directory**  >  **autentiseringsinformation för**  >  **inloggningar.** Bedrägerirapporten är nu en del av standardrapporten för Azure AD-inloggningar och visas i **"Resultatinformation"** som MFA nekad, Bedrägerikod angivet.
 
## <a name="notifications"></a>Meddelanden

E-postmeddelanden kan konfigureras när användare rapporterar bedrägeriaviseringar. Dessa meddelanden skickas vanligtvis till identitetsadministratörer eftersom användarens kontoautentiseringsuppgifter troligen komprometteras. I följande exempel visas hur ett e-postmeddelande med en bedrägeriavisering ser ut:

![Exempel på e-postmeddelande om bedrägeriavisering](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Om du vill konfigurera meddelanden om bedrägeriaviseringar slutför du följande inställningar:

1. Bläddra till **Azure Active Directory**  >  **Security**  >  **Multi-Factor Authentication-meddelanden**  >  .
1. Ange den e-postadress som ska läggas till i nästa ruta.
1. Om du vill ta bort en befintlig e-postadress väljer du alternativet **...** bredvid önskad e-postadress och väljer sedan Ta **bort**.
1. När du är klar väljer du **Spara**.

## <a name="oath-tokens"></a>OATH-token

Azure AD stöder användning av OATH-TOTP SHA-1-tokens som uppdaterar koder var 30:e eller 60:e sekund. Kunder kan köpa dessa token från valfri leverantör.

OATH TOTP-maskinvarutoken har vanligtvis en hemlig nyckel, eller seed, förprogrammerat i token. Dessa nycklar måste anges i Azure AD enligt beskrivningen i följande steg. Hemliga nycklar är begränsade till 128 tecken, vilket kanske inte är kompatibelt med alla token. Den hemliga nyckeln får bara innehålla tecknen *a-z* eller *A-Z* och siffrorna *1–7* och måste vara kodade i *Base32*.

Programmerbara OATH TOTP-maskinvarutoken som kan skickas igen kan också konfigureras med Azure AD i konfigurationsflödet för programvarutoken.

OATH-maskinvarutoken stöds som en del av en offentlig förhandsversion. Mer information om förhandsversioner finns i [Kompletterande användningsvillkor för Microsoft Azure förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Ladda upp OATH-token till bladet MFA OATH-token](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

När token har köpts måste de laddas upp i ett filformat med kommaavgränsade värden (CSV), inklusive UPN, serienummer, hemlig nyckel, tidsintervall, tillverkare och modell enligt följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Se till att du inkluderar rubrikraden i CSV-filen.

När den är korrekt formaterad som en CSV-fil kan en administratör logga in på Azure Portal, navigera till **Azure Active Directory > Security > MFA > OATH-token och** ladda upp den resulterande CSV-filen.

Beroende på CSV-filens storlek kan det ta några minuter att bearbeta den. Välj knappen **Uppdatera** för att få den aktuella statusen. Om det finns några fel i filen kan du ladda ned en CSV-fil som visar eventuella fel som du kan lösa. Fältnamnen i den nedladdade CSV-filen skiljer sig från den uppladdade versionen.

När eventuella fel har åtgärdats kan administratören aktivera  varje nyckel genom att välja Aktivera för token och ange OTP som visas på token.

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller autentiseringsprogram, till exempel Microsoft Authenticator-appen, som konfigurerats för användning när som helst.

## <a name="phone-call-settings"></a>Inställningar för telefonsamtal

Om användarna får telefonsamtal för MFA-frågor kan du konfigurera deras upplevelse, till exempel nummerpresentation eller rösthälsning som de hör.

Om du USA konfigurerat MFA-nummerpresentationen i den här artikeln kommer röstsamtal från Microsoft från följande nummer. Om du använder skräppostfilter ska du utesluta följande siffror:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> När Azure AD Multi-Factor Authentication-samtal placeras via det offentliga telefonnätverket dirigeras ibland samtalen via ett transportföretag som inte stöder nummerpresentation. Därför garanteras inte nummerpresentation, även om Azure AD Multi-Factor Authentication alltid skickar det. Detta gäller både telefonsamtal och SMS som tillhandahålls av Azure AD Multi-Factor Authentication. Om du behöver verifiera att ett sms kommer från Azure AD Multi-Factor Authentication kan du läsa [Vilka SMS-kortkoder används för att skicka meddelanden?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Utför följande steg för att konfigurera ditt eget nummerpresentation:

1. Bläddra till **Azure Active Directory**  >  **för**  >  **MFA-telefonsamtal.**  >  
1. Ange **MFA-nummerpresentationen till** det nummer som du vill att användarna ska se på sin telefon. Endast USA-baserade nummer tillåts.
1. När du är klar väljer du **Spara**.

### <a name="custom-voice-messages"></a>Anpassade röstmeddelanden

Du kan använda dina egna inspelningar eller hälsningar för Azure AD Multi-Factor Authentication med funktionen för anpassade röstmeddelanden. Dessa meddelanden kan användas utöver eller för att ersätta Microsofts standardinspelningar.

Innan du börjar bör du vara medveten om följande begränsningar:

* Filformaten som stöds är *WAV* och *.mp3.*
* Filstorleksgränsen är 1 MB.
* Autentiseringsmeddelanden bör vara kortare än 20 sekunder. Meddelanden som är längre än 20 sekunder kan orsaka att verifieringen misslyckas. Användaren kanske inte svarar innan meddelandet är klart och verifieringen tar för många minuter.

### <a name="custom-message-language-behavior"></a>Anpassat beteende för meddelandespråk

När ett anpassat röstmeddelande spelas upp för användaren beror språket på meddelandet på följande faktorer:

* Den aktuella användarens språk.
  * Det språk som identifieras av användarens webbläsare.
  * Andra autentiseringsscenarier kan bete sig annorlunda.
* Språket för alla tillgängliga anpassade meddelanden.
  * Det här språket väljs av administratören när ett anpassat meddelande läggs till.

Om det till exempel bara finns ett anpassat meddelande, med språket tyska:

* En användare som autentiseras på tyska kommer att höra det anpassade tyska meddelandet.
* En användare som autentiseras på engelska kommer att höra standardmeddelandet på engelska.

### <a name="custom-voice-message-defaults"></a>Standardinställningar för anpassade röstmeddelanden

Följande exempelskript kan användas för att skapa egna anpassade meddelanden. Dessa fraser är standardvärdena om du inte konfigurerar dina egna anpassade meddelanden:

| Meddelandenamn | Skript |
| --- | --- |
| Autentiseringen lyckades | Inloggningen har verifierats. Hej då. |
| Fråga om tillägg | Tack för att du använder Microsofts system för inloggningsverifiering. Fortsätt genom att trycka på pundtangenten. |
| Bekräftelse på bedrägeri | En bedrägerivarning har skickats. Om du vill avblockera ditt konto kontaktar du företagets IT-support. |
| Bedrägerihälsning (standard) | Tack för att du använder Microsofts system för inloggningsverifiering. Tryck på pundtangenten för att slutföra verifieringen. Om du inte har initierat den här verifieringen kanske någon försöker komma åt ditt konto. Tryck på noll pund för att skicka en bedrägerivarning. Detta meddelar företagets IT-team och blockerar ytterligare verifieringsförsök. |
| Bedrägerirapporter En bedrägerivarning har skickats. | Om du vill avblockera ditt konto kontaktar du företagets IT-support. |
| Aktivering | Tack för att du använder Microsofts system för inloggningsverifiering. Tryck på pundtangenten för att slutföra verifieringen. |
| Autentisering nekad återförsök | Verifiering nekad. |
| Försök igen (standard) | Tack för att du använder Microsofts system för inloggningsverifiering. Slutför verifieringen genom att trycka på pundtangenten. |
| Hälsning (standard) | Tack för att du använder Microsofts system för inloggningsverifiering. Slutför verifieringen genom att trycka på pundtangenten. |
| Hälsning (PIN-kod) | Tack för att du använder Microsofts system för inloggningsverifiering. Ange PIN-koden följt av pundnyckeln för att slutföra verifieringen. |
| Bedrägerihälsning (PIN) | Tack för att du använder Microsofts system för inloggningsverifiering.  Ange PIN-koden följt av pundnyckeln för att slutföra verifieringen. Om du inte har initierat den här verifieringen kanske någon försöker komma åt ditt konto. Tryck på noll pund för att skicka en bedrägerivarning. Detta meddelar företagets IT-team och blockerar ytterligare verifieringsförsök. |
| Försök igen (PIN-kod) | Tack för att du använder Microsofts system för inloggningsverifiering. Ange PIN-koden följt av pundnyckeln för att slutföra verifieringen. |
| Fråga efter siffror för tillägg | Om du redan har det här tillägget trycker du på pundtangenten för att fortsätta. |
| Autentisering nekad | Vi kan tyvärr inte logga in dig just nu. Försök igen senare. |
| Aktiveringshälsning (standard) | Tack för att du använder Microsofts system för inloggningsverifiering. Tryck på pundtangenten för att slutföra verifieringen. |
| Återförsök för aktivering (standard) | Tack för att du använder Microsofts system för inloggningsverifiering. Tryck på pundtangenten för att slutföra verifieringen. |
| Aktiveringshälsning (PIN-kod) | Tack för att du använder Microsofts system för inloggningsverifiering. Ange din PIN-kod följt av pundnyckeln för att slutföra verifieringen. |
| Fråga efter tillägg före siffror | Tack för att du använder Microsofts system för inloggningsverifiering. Överför anropet till tillägget ... |

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

Om du vill använda dina egna anpassade meddelanden utför du följande steg:

1. Bläddra till **Azure Active Directory**  >  **för**  >  **MFA-telefonsamtal.**  >  
1. Välj Lägg **till hälsning.**
1. Välj typ **av hälsning,** till exempel *Hälsning (standard)* eller *Autentisering lyckades.*
1. Välj **Språk**, baserat på föregående avsnitt om anpassat [meddelandespråksbeteende.](#custom-message-language-behavior)
1. Bläddra efter och välj en *.mp3- eller* *WAV-ljudfil* att ladda upp.
1. När du är klar väljer **du Lägg** till och **sedan Spara.**

## <a name="mfa-service-settings"></a>MFA-tjänstinställningar

Inställningar för applösenord, betrodda IP-adresser, verifieringsalternativ och kom ihåg att multifaktorautentisering för Azure AD Multi-Factor Authentication finns i tjänstinställningarna. Det här är mer av en äldre portal och är inte en del av den vanliga Azure AD-portalen.

Tjänstinställningar kan nås från Azure Portal genom att gå **till Azure Active Directory** Security MFA Komma igång Konfigurera ytterligare  >    >    >    >    >  **molnbaserade MFA-inställningar.** Ett nytt fönster eller en ny flik öppnas med ytterligare *alternativ för tjänstinställningar.*

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Funktionen _tillförlitliga IP-adresser_ i Azure AD Multi-Factor Authentication kringgår uppmaningar om multifaktorautentisering för användare som loggar in från ett definierat IP-adressintervall. Du kan ange betrodda IP-intervall för dina lokala miljöer så att det inte finns någon fråga om Azure AD Multi-Factor Authentication när användarna är på någon av dessa platser. Funktionen _betrodda IP-adresser_ i Azure AD Multi-Factor Authentication kräver Azure AD Premium P1-version. 

> [!NOTE]
> Betrodda IP-adresser kan endast innehålla privata IP-intervall när du använder MFA Server. För molnbaserad Azure AD Multi-Factor Authentication kan du bara använda offentliga IP-adressintervall.
>
> IPv6-intervall stöds endast i gränssnittet [Namngiven plats (förhandsversion).](../conditional-access/location-condition.md)

Om din organisation distribuerar NPS-tillägget för att tillhandahålla MFA till lokala program observerar du att käll-IP-adressen alltid verkar vara NPS-servern som autentiseringsförsöket flödar igenom.

| Azure AD-klientorganisationstyp | Alternativ för betrodd IP-funktion |
|:--- |:--- |
| Hanterad |**Specifikt intervall med IP-adresser:** Administratörer anger ett intervall med IP-adresser som kan kringgå multifaktorautentisering för användare som loggar in från företagets intranät. Högst 50 betrodda IP-intervall kan konfigureras.|
| Federerade |**Alla federerade användare:** Alla federerade användare som loggar in inifrån organisationen kan kringgå multifaktorautentisering. Användarna kringgår verifieringen genom att använda ett anspråk som utfärdas av Active Directory Federation Services (AD FS) (AD FS).<br/>**Specifikt intervall med IP-adresser:** Administratörer anger ett intervall med IP-adresser som kan kringgå multifaktorautentisering för användare som loggar in från företagets intranät. |

Kringgå betrodd IP fungerar bara inifrån företagets intranät. Om du väljer **alternativet Alla federerade** användare och en användare loggar in utanför företagets intranät måste användaren autentisera med hjälp av multifaktorautentisering. Processen är densamma även om användaren presenterar ett AD FS anspråk.

### <a name="end-user-experience-inside-of-corpnet"></a>Slutanvändarupplevelse i corpnet

När funktionen för betrodda IP-adresser är inaktiverad krävs multifaktorautentisering för webbläsarflöden. Applösenord krävs för äldre avancerade klientprogram.

När betrodda IP-adresser används krävs inte multifaktorautentisering för webbläsarflöden. Applösenord krävs inte för äldre avancerade klientprogram, förutsatt att användaren inte har skapat något applösenord. När ett applösenord används förblir lösenordet obligatoriskt.

### <a name="end-user-experience-outside-corpnet"></a>Slutanvändarupplevelse utanför corpnet

Oavsett om betrodd IP-adress har definierats krävs multifaktorautentisering för webbläsarflöden. Applösenord krävs för äldre avancerade klientprogram.

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivera namngivna platser med villkorlig åtkomst

Du kan använda regler för villkorlig åtkomst för att definiera namngivna platser med hjälp av följande steg:

1. I den Azure Portal du efter och väljer **Azure Active Directory** och bläddrar sedan till **Security**  >  **Conditional Access**  >  **Named locations**.
1. Välj **Ny plats.**
1. Ange ett namn för platsen.
1. Välj **Markera som betrodd plats.**
1. Ange IP-intervallet i CIDR-notationen för din miljö, till exempel *40.77.182.32/27.*
1. Välj **Skapa**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivera funktionen betrodda IP-adresser med villkorlig åtkomst

Utför följande steg för att aktivera betrodda IP-adresser med principer för villkorsstyrd åtkomst:

1. I listan Azure Portal du efter och väljer **Azure Active Directory** och bläddrar sedan till **Security**  >   **Conditional Access**  >  **Named locations (Villkorsstyrd** åtkomst för säkerhet med namnet platser).
1. Välj **Konfigurera betrodda IP-adresser för MFA.**
1. På sidan **Tjänstinställningar** under **Betrodda IP-adresser** väljer du något av följande två alternativ:

   * **För begäranden från federerade användare som kommer från mitt intranät:** Markera kryssrutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företagsnätverket kringgår multifaktorautentisering med hjälp av ett anspråk som utfärdas av AD FS. Se till AD FS har en regel för att lägga till intranätsanspråk till lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * För begäranden från ett specifikt intervall med offentliga **IP-adresser:** Om du vill välja det här alternativet anger du IP-adresserna i textrutan med hjälp av CIDR-notation.
      * För IP-adresser i intervallet xxx.xxx.xxx.1 till och med xxx.xxx.xxx.254 använder du notation som **xxx.xxx.xxx.0/24.**
      * För en enskild IP-adress använder du notation som **xxx.xxx.xxx.xxx/32**.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgår multifaktorautentisering.

1. Välj **Spara**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivera funktionen tillförlitliga IP-adresser med hjälp av tjänstinställningar

Om du inte vill använda principer för villkorsstyrd åtkomst för  att aktivera betrodda IP-adresser kan du konfigurera tjänstinställningarna för Azure AD Multi-Factor Authentication med hjälp av följande steg:

1. I listan Azure Portal du efter och väljer **Azure Active Directory** och väljer sedan **Användare.**
1. Välj **Multi-Factor Authentication**.
1. Under Multi-Factor Authentication väljer du **tjänstinställningar**.
1. På sidan **Tjänstinställningar** under **Betrodda IP-adresser** väljer du ett (eller båda) av följande två alternativ:

   * **För begäranden från federerade användare på mitt intranät:** Markera kryssrutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företagsnätverket kringgår multifaktorautentisering med hjälp av ett anspråk som utfärdas av AD FS. Se till AD FS har en regel för att lägga till intranätsanspråk till lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För begäranden från ett angivet intervall med** IP-adressundernät: Om du vill välja det här alternativet anger du IP-adresserna i textrutan med hjälp av CIDR-notation.
      * För IP-adresser i intervallet xxx.xxx.xxx.1 till och med xxx.xxx.xxx.xxx.254 använder du notation som **xxx.xxx.xxx.0/24.**
      * För en enskild IP-adress använder du notation som **xxx.xxx.xxx.xxx/32**.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgår multifaktorautentisering.

1. Välj **Spara**.

## <a name="verification-methods"></a>Verifieringsmetoder

Du kan välja de verifieringsmetoder som är tillgängliga för användarna i portalen för tjänstinställningar. När användarna registrerar sina konton för Azure AD Multi-Factor Authentication väljer de önskad verifieringsmetod bland de alternativ som du har aktiverat. Vägledning för användarregistreringsprocessen finns i Konfigurera [mitt konto för multifaktorautentisering.](../user-help/multi-factor-authentication-end-user-first-time.md)

Följande verifieringsmetoder är tillgängliga:

| Metod | Beskrivning |
|:--- |:--- |
| Ring till telefon |Ringer ett automatiserat röstsamtal. Användaren identifierar sig genom att svara och trycka på #-tangenten. Telefonnumret synkroniseras inte för att lokal Active Directory. |
| SMS till telefon |Skickar ett sms som innehåller en verifieringskod. Användaren uppmanas att ange verifieringskoden i inloggningsgränssnittet. Den här processen kallas för envägs-SMS. Tvåvägs-SMS innebär att användaren måste skicka tillbaka en viss kod. Tvåvägs-SMS är inaktuellt och stöds inte efter den 14 november 2018. Administratörer bör aktivera en annan metod för användare som tidigare använde tvåvägs-SMS.|
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller registrerade enhet. Användaren visar meddelandet och väljer Verifiera **för att** slutföra verifieringen. Appen Microsoft Authenticator är tillgänglig för [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Verifieringskod från mobilapp eller maskinvarutoken |Appen Microsoft Authenticator genererar en ny OATH-verifieringskod var 30:e sekund. Användaren anger verifieringskoden i inloggningsgränssnittet. Appen Microsoft Authenticator är tillgänglig för [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

Mer information finns i Vilka [autentiserings- och verifieringsmetoder är tillgängliga i Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Aktivera och inaktivera verifieringsmetoder

Utför följande steg för att aktivera eller inaktivera verifieringsmetoder:

1. I listan Azure Portal du efter och väljer **Azure Active Directory** och väljer sedan **Användare.**
1. Välj **Multi-Factor Authentication**.
1. Under Multi-Factor Authentication väljer du **tjänstinställningar**.
1. På sidan **Tjänstinställningar** går du till **verifieringsalternativen** och väljer/avmarkerar de metoder som ska tillhandahållas till användarna.
1. Klicka på **Spara**.

## <a name="remember-multi-factor-authentication"></a>Kom ihåg Multi-Factor Authentication

Funktionen _Kom ihåg multifaktorautentisering_ gör att användare kan kringgå efterföljande verifieringar under ett angivet antal dagar efter att de har loggat in på en enhet med hjälp av Multi-Factor Authentication. Om du vill förbättra användbarheten och minimera antalet gånger som en användare måste utföra MFA på samma enhet väljer du en varaktighet på 90 dagar eller mer.

> [!IMPORTANT]
> Om ett konto eller en enhet komprometteras kan kom ihåg multifaktorautentisering för betrodda enheter påverka säkerheten. Om ett företagskonto komprometteras eller om en betrodd enhet förloras eller blir stulen bör du [återkalla MFA-sessioner.](howto-mfa-userdevicesettings.md)
>
> Återställningsåtgärden återkallar den betrodda statusen från alla enheter och användaren måste utföra multifaktorautentisering igen. Du kan också instruera användarna att återställa Multi-Factor Authentication på sina egna enheter enligt vad som anges i Hantera dina inställningar [för multifaktorautentisering.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Så här fungerar funktionen

Funktionen Kom ihåg multifaktorautentisering anger en beständig cookie i webbläsaren när en användare väljer alternativet Fråga inte igen för **X** dagar vid inloggning. Användaren uppmanas inte igen om Multi-Factor Authentication från samma webbläsare förrän cookien upphör att gälla. Om användaren öppnar en annan webbläsare på samma enhet eller rensar sina cookies uppmanas de igen att verifiera.

Alternativet **Fråga inte igen för X dagar** visas inte i icke-webbläsarprogram, oavsett om appen stöder modern autentisering eller inte. De här _apparna använder uppdateringstoken_ som tillhandahåller nya åtkomsttoken varje timme. När en uppdateringstoken verifieras kontrollerar Azure AD att den senaste multifaktorautentisering har inträffat inom det angivna antalet dagar.

Funktionen minskar antalet autentiseringar på webbappar, som normalt frågar varje gång. Funktionen kan öka antalet autentiseringar för moderna autentiseringsklienter som normalt frågar var 180:e dag om en lägre varaktighet har konfigurerats. Kan också öka antalet autentiseringar i kombination med principer för villkorsstyrd åtkomst.

> [!IMPORTANT]
> Kom **ihåg att funktionen Multi-Factor Authentication** inte är kompatibel med funktionen för att hålla mig **inloggad** i AD FS när användare utför multifaktorautentisering för AD FS via Azure Multi-Factor Authentication-server eller en lösning för multifaktorautentisering från tredje part.
>
> Om användarna  väljer att jag ska vara inloggad på AD FS och även markera enheten som betrodd för Multi-Factor Authentication verifieras inte användaren automatiskt när antalet dagar för multifaktorautentisering har gått ut.  Azure AD begär en ny multifaktorautentisering, men AD FS returnerar en token med det ursprungliga Multi-Factor Authentication-anspråket och -datumet i stället för att utföra multifaktorautentisering igen. **Den här reaktionen anger en verifieringsloop mellan Azure AD och AD FS.**
>
> Funktionen **Kom ihåg multifaktorautentisering** är inte kompatibel med B2B-användare och visas inte för B2B-användare när de loggar in på de inbjudna klienterna.
>

### <a name="enable-remember-multi-factor-authentication"></a>Kom ihåg multifaktorautentisering

Om du vill aktivera och konfigurera alternativet för användare att komma ihåg sin MFA-status och kringgå uppmaningar, utför du följande steg:

1. I listan Azure Portal du efter och väljer **Azure Active Directory** och väljer sedan **Användare.**
1. Välj **Multi-Factor Authentication**.
1. Under Multi-Factor Authentication väljer du **tjänstinställningar**.
1. På sidan **Tjänstinställningar,** under Kom ihåg **multifaktorautentisering,** väljer du alternativet Tillåt användare att komma ihåg multifaktorautentisering på enheter som de **litar** på.
1. Ange antalet dagar som betrodda enheter ska kunna kringgå multifaktorautentisering. För en optimal användarupplevelse kan du utöka varaktigheten till *90* eller fler dagar.
1. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du aktiverar funktionen Kom ihåg multifaktorautentisering kan användarna markera en enhet som betrodd när de loggar in genom att välja alternativet Fråga **inte igen.**

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga metoder för användning i Azure AD Multi-Factor Authentication finns i Vilka autentiserings- och [verifieringsmetoder finns i Azure Active Directory?](concept-authentication-methods.md)
