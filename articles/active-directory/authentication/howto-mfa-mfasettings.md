---
title: Konfigurera Azure AD-Multi-Factor Authentication – Azure Active Directory
description: Lär dig hur du konfigurerar inställningar för Azure AD-Multi-Factor Authentication i Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 4625b0a750c2b3ff63879bb9ea306bc69b1bb64e
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471643"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Konfigurera inställningar för Azure AD-Multi-Factor Authentication

Om du vill anpassa slutanvändarens upplevelse för Azure AD Multi-Factor Authentication kan du konfigurera alternativ för inställningar som tröskelvärden för konto utelåsning eller bedrägerier och aviseringar. Vissa inställningar är direkt i Azure Portal för Azure Active Directory (Azure AD) och vissa i en separat Azure AD Multi-Factor Authentication-Portal.

Följande Azure AD Multi-Factor Authentication-inställningar finns i Azure Portal:

| Funktion | Beskrivning |
| ------- | ----------- |
| [Konto utelåsning](#account-lockout) | Lås tillfälligt konton från att använda Azure AD Multi-Factor Authentication om det finns alltför många nekade autentiseringsförsök i en rad. Den här funktionen gäller endast för användare som anger en PIN-kod för autentisering. (MFA-Server) |
| [Blockera/avblockera användare](#block-and-unblock-users) | Blockera vissa användare från att kunna ta emot Azure AD Multi-Factor Authentication-begäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användarna är fortfarande blockerade i 90 dagar från den tid som de är blockerade eller har avblockerats manuellt. |
| [Bedrägerivarning](#fraud-alert) | Konfigurera inställningar som tillåter att användare rapporterar falska verifierings begär Anden. |
| [Aviseringar](#notifications) | Aktivera meddelanden om händelser från MFA Server. |
| [OATH-token](concept-authentication-oath-tokens.md) | Används i Cloud-baserade Azure AD MFA-miljöer för att hantera OATH-token för användare. |
| [Telefonsamtals inställningar](#phone-call-settings) | Konfigurera inställningar för telefonsamtal och hälsningar för moln miljöer och lokala miljöer. |
| Leverantörer | Då visas befintliga autentiseringsproviders som du kan ha associerat med ditt konto. Nya autentiseringsproviders får inte skapas från den 1 september 2018 |

![Azure Portal-inställningar för Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Konto utelåsning

För att förhindra upprepade MFA-försök som en del av ett angrepp kan du med inställningarna för konto utelåsning ange hur många misslyckade försök som ska tillåtas innan kontot blir utelåst under en viss tids period. Inställningarna för konto utelåsning tillämpas bara när en PIN-kod anges för MFA-prompten.

Följande inställningar är tillgängliga:

* Antal MFA-nekanden för att utlösa konto utelåsning
* Minuter tills räknaren för konto utelåsning återställs
* Minuter tills kontot automatiskt avblockeras

Konfigurera utelåsnings inställningarna för kontot genom att utföra följande inställningar:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >    >  **konto utelåsning** för MFA.
1. Ange de krävda värdena för din miljö och välj sedan **Spara**.

    ![Skärm bild av inställningarna för konto utelåsning i Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Blockera och avblockera användare

Om en användares enhet har tappats bort eller blivit stulen kan du blockera Azure AD Multi-Factor Authentication-försök för det associerade kontot. Alla Azure AD Multi-Factor Authentication-försök för blockerade användare nekas automatiskt. Användarna är blockerade i 90 dagar från blockeringens starttid. Vi har publicerat en video om [hur du blockerar och avblockerar användare i din klient organisation](https://www.youtube.com/watch?v=WdeE1On4S1o) för att visa hur du gör detta.

### <a name="block-a-user"></a>Blockera en användare

Utför följande steg för att blockera en användare:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **MFA**  >  **blockera/avblockera användare**.
1. Välj **Lägg till** för att blockera en användare.
1. Ange användar namnet för den blockerade användaren som `username@domain.com` och ange sedan en kommentar i fältet *orsak* .
1. När du är klar väljer du **OK** för att blockera användaren.

### <a name="unblock-a-user"></a>Avblockera en användare

Utför följande steg för att avblockera en användare:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **MFA**  >  **blockera/avblockera användare**.
1. I kolumnen *åtgärd* bredvid önskad användare väljer du **avblockera**.
1. Ange en kommentar i fältet *orsak till att fältet avblockeras* .
1. När du är klar väljer du **OK** för att avblockera användaren.

## <a name="fraud-alert"></a>Bedrägerivarning

Med funktionen bedrägeri avisering kan användare rapportera falska försök att komma åt sina resurser. När en okänd och misstänkt MFA-prompt tas emot kan användare rapportera bedrägerier-försöket med hjälp av Microsoft Authenticator appen eller via telefon.

Följande konfigurations alternativ för bedrägeri avisering är tillgängliga:

* **Blockera användare som rapporterar bedrägerier**: om en användare rapporterar bedrägerier, blockeras Azure AD MFA-autentiseringen för användar kontot i 90 dagar eller tills en administratör avblockerar sitt konto. En administratör kan granska inloggningar med hjälp av inloggnings rapporten och vidta lämpliga åtgärder för att förhindra framtida bedrägerier. En administratör kan sedan [avblockera](#unblock-a-user) användarens konto.
* **Kod för att rapportera bedrägeri under inledande hälsning**: När användarna får ett telefonsamtal för att utföra Multi-Factor Authentication, trycks vanligt vis **#** för att bekräfta inloggningen. Användaren kan rapportera bedrägerier genom att ange en kod innan du trycker på **#** . Den här koden är **0** som standard, men du kan anpassa den.

   > [!NOTE]
   > Vanliga röst hälsningar från Microsoft instruerar användarna att trycka på **0 #** för att skicka en bedrägeri avisering. Om du vill använda en annan kod än **0**, spelar du in och laddar upp egna röst hälsningar med lämpliga instruktioner för dina användare.

Utför följande steg för att aktivera och konfigurera bedrägeri aviseringar:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **MFA**  >  **bedrägeri avisering**.
1. Ange inställningen *Tillåt användare att skicka bedrägeri varningar* till **på**.
1. Konfigurera *automatiskt blockera användare som rapporterar bedrägerier* eller *kod för att rapportera bedrägerier under den inledande hälsnings* inställningen som du vill.
1. När du är klar väljer du **Spara**.

### <a name="view-fraud-reports"></a>Visa bedrägerier-rapporter

Välj   >    >  **information om** Azure Active Directory inloggningar. Bedrägeri rapporten är nu en del av standard rapporten för Azure AD-inloggningar och visas i **"resultat information"** som MFA nekad, bedrägeri kod angiven.
 
## <a name="notifications"></a>Meddelanden

E-postaviseringar kan konfigureras när användare rapporterar bedrägeri aviseringar. Dessa meddelanden skickas vanligt vis till identitets administratörer, eftersom användarens konto uppgifter sannolikt komprometteras. I följande exempel visas hur ett meddelande om bedrägeri aviseringar ser ut så här:

![Exempel på e-postavisering om bedrägeri avisering](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Konfigurera bedrägeri aviserings aviseringar genom att slutföra följande inställningar:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **Multi-Factor Authentication**  >  **aviseringar**.
1. Ange den e-postadress som du vill lägga till i nästa ruta.
1. Om du vill ta bort en befintlig e-postadress väljer du alternativet **...** bredvid önskad e-postadress och väljer sedan **ta bort**.
1. När du är klar väljer du **Spara**.

## <a name="oath-tokens"></a>OATH-token

Azure AD stöder användningen av OATH-TOTP mobilapp SHA-1-token som uppdaterar koder var 30: e sekund eller 60 sekunder. Kunder kan köpa dessa token från den leverantör de väljer.

OATH TOTP mobilapp-token levereras vanligt vis med en hemlig nyckel eller dirigeras, förprogrammeras i token. De här nycklarna måste vara inmatade i Azure AD enligt beskrivningen i följande steg. Hemliga nycklar är begränsade till 128 tecken, som kanske inte är kompatibla med alla tokens. Den hemliga nyckeln får bara innehålla tecknen *a-z* eller *a-z* och siffror *1-7* och måste kodas i *Base32*.

Programmerbara OATH TOTP mobilapp-maskinvaru-token som kan dirigeras om kan också konfigureras med Azure AD i installations flödet för programtoken.

OATH-token för OATH-enheter stöds som en del av en offentlig för hands version. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

![Laddar upp OATH-token till bladet MFA OATH-token](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

När token har erhållits måste de överföras i ett fil format med kommaavgränsade värden (CSV), inklusive UPN, serie nummer, hemlig nyckel, tidsintervall, tillverkare och modell som visas i följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Se till att du inkluderar rubrik raden i CSV-filen.

När den är korrekt formaterad som en CSV-fil kan en administratör logga in på Azure Portal, navigera till **Azure Active Directory > säkerhet > MFA > Oath-token** och ladda upp den resulterande CSV-filen.

Beroende på storleken på CSV-filen kan det ta några minuter att bearbeta. Välj knappen **Uppdatera** för att hämta aktuell status. Om det finns fel i filen kan du ladda ned en CSV-fil som visar eventuella fel som du kan lösa. Fält namnen i den hämtade CSV-filen skiljer sig från den överförda versionen.

När eventuella fel har åtgärd ATS kan administratören aktivera varje nyckel genom att välja **Aktivera** för token och ange det eng ång slö sen ord som visas i token.

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen, som har kon figurer ATS för användning när som helst.

## <a name="phone-call-settings"></a>Telefonsamtals inställningar

Om användarna får telefonsamtal för MFA-prompter kan du konfigurera deras upplevelse, till exempel uppringnings-ID eller röst hälsning som de hör till.

Om du inte har konfigurerat MFA-uppringarens ID i USA, kommer röst samtal från Microsoft att komma från följande nummer. Om du använder skräp post filter, se till att undanta dessa tal:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> När Azure AD Multi-Factor Authentication-anrop placeras via det offentliga telefonnätet dirigeras ibland anropen via en bärvåg som inte stöder ID för uppringarens ID. Därför är det inte säkert att anropar-ID: t, även om Azure AD Multi-Factor Authentication alltid skickar det. Detta gäller både telefonsamtal och SMS-meddelanden från Azure AD Multi-Factor Authentication. Om du behöver verifiera att ett textmeddelande kommer från Azure AD Multi-Factor Authentication, se [vilka SMS-koder som används för att skicka meddelanden?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Följ stegen nedan om du vill konfigurera ett eget nummer för anroparens ID:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  Inställningar för **MFA**-  >  **telefonsamtal**.
1. Ange **ID-numret för MFA-anroparen** till det nummer som du vill att användarna ska se på sin telefon. Endast amerikanska-baserade tal är tillåtna.
1. När du är klar väljer du **Spara**.

### <a name="custom-voice-messages"></a>Anpassade röst meddelanden

Du kan använda dina egna inspelningar eller hälsningar för Azure AD Multi-Factor Authentication med funktionen för anpassade röst meddelanden. Dessa meddelanden kan användas utöver eller för att ersätta Microsofts standard inspelningar.

Innan du börjar bör du vara medveten om följande begränsningar:

* De fil format som stöds är *. wav* och *. mp3*.
* Gränsen för fil storlek är 1 MB.
* Autentiseringsfel bör vara kortare än 20 sekunder. Meddelanden som är längre än 20 sekunder kan göra att verifieringen inte fungerar. Användaren kanske inte svarar innan meddelandet har slutförts och verifierings tiden är slut.

### <a name="custom-message-language-behavior"></a>Språk beteende för anpassade meddelanden

När ett anpassat röst meddelande spelas upp till användaren, beror språket i meddelandet på följande faktorer:

* Den aktuella användarens språk.
  * Språket som identifieras av användarens webbläsare.
  * Andra verifierings scenarier kan bete sig annorlunda.
* Språket för alla tillgängliga anpassade meddelanden.
  * Det här språket väljs av administratören när ett anpassat meddelande läggs till.

Om det till exempel bara finns ett anpassat meddelande, med språket tyska:

* En användare som autentiserar på tyska språk kommer att höra det anpassade tyska meddelandet.
* En användare som autentiserar på engelska kommer att höra det engelska standard meddelandet.

### <a name="custom-voice-message-defaults"></a>Standardinställningar för anpassade röst meddelanden

Följande exempel skript kan användas för att skapa egna anpassade meddelanden. Dessa fraser är standardvärden om du inte konfigurerar egna anpassade meddelanden:

| Meddelande namn | Skript |
| --- | --- |
| Autentiseringen lyckades | Din inloggning har verifierats. Hej då. |
| Tilläggs varning | Tack för att du använder Microsofts inloggnings verifierings system. Fortsätt genom att trycka på fyrkant. |
| Bekräfta bedrägeri | En bedrägeri avisering har skickats. Om du vill häva blockeringen av ditt konto kontaktar du ditt företags IT-supportavdelning. |
| Bedrägeri meddelande (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Slutför verifieringen genom att trycka på fyrkant. Om du inte har initierat den här verifieringen kan någon försöka komma åt ditt konto. Skicka en bedrägeri avisering genom att trycka på noll. Detta kommer att meddela ditt företags IT-team och blockera ytterligare verifierings försök. |
| Bedrägerier rapporterade att en bedrägeri avisering har skickats. | Om du vill häva blockeringen av ditt konto kontaktar du ditt företags IT-supportavdelning. |
| Aktivering | Tack för att du använder Microsofts inloggnings verifierings system. Slutför verifieringen genom att trycka på fyrkant. |
| Försök att neka autentisering | Verifiering nekad. |
| Försök igen (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Slutför verifieringen genom att trycka på fyrkant. |
| Hälsning (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Slutför verifieringen genom att trycka på fyrkant. |
| Hälsning (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system. Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. |
| Bedrägeri meddelande (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system.  Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. Om du inte har initierat den här verifieringen kan någon försöka komma åt ditt konto. Skicka en bedrägeri avisering genom att trycka på noll. Detta kommer att meddela ditt företags IT-team och blockera ytterligare verifierings försök. |
| Försök igen (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system. Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. |
| Utöknings varning efter siffror | Om du redan har gjort det här tillägget trycker du på knappen fyrkant för att fortsätta. |
| Autentisering nekad | Jag kan tyvärr inte logga in dig just nu. Försök igen senare. |
| Aktiverings hälsning (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Slutför verifieringen genom att trycka på fyrkant. |
| Nytt aktiverings försök (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Slutför verifieringen genom att trycka på fyrkant. |
| Aktiverings hälsning (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system. Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. |
| Utöknings varning före siffror | Tack för att du använder Microsofts inloggnings verifierings system. Överför det här anropet till Extension... |

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

Utför följande steg för att använda dina egna anpassade meddelanden:

1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  Inställningar för **MFA**-  >  **telefonsamtal**.
1. Välj **Lägg till hälsning**.
1. Välj **typ** av hälsning, till exempel *hälsning (standard)* eller  *autentiseringen lyckades*.
1. Välj **språk**, baserat på föregående avsnitt om funktioner för [anpassat meddelande språk](#custom-message-language-behavior).
1. Bläddra efter och välj en *. mp3* -eller *. wav* -ljudfil som ska överföras.
1. När du är klar väljer du **Lägg till** och sedan **Spara**.

## <a name="mfa-service-settings"></a>Inställningar för MFA-tjänsten

Inställningar för applösenord, tillförlitliga IP-adresser, verifierings alternativ och kom ihåg Multi-Factor Authentication för Azure AD Multi-Factor Authentication finns i tjänst inställningar. Detta är mer av en äldre Portal och ingår inte i den vanliga Azure AD-portalen.

Du kan komma åt tjänst inställningar från Azure Portal genom att bläddra till **Azure Active Directory**  >    >  **säkerhetsmfa**  >  **komma igång**  >  **Konfigurera**  >  **ytterligare molnbaserade MFA-inställningar**. Ett nytt fönster eller en ny flik öppnas med ytterligare alternativ för *tjänst inställningar* .

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Funktionen _betrodda IP-adresser_ i Azure AD Multi-Factor Authentication kringgår Multi-Factor Authentication-prompter för användare som loggar in från ett definierat IP-adressintervall. Du kan ange betrodda IP-intervall för dina lokala miljöer när användarna är på någon av dessa platser, så finns det ingen Azure AD-Multi-Factor Authentication-prompt.

> [!NOTE]
> De betrodda IP-adresserna kan bara innehålla privata IP-adressintervall när du använder MFA Server. För molnbaserad Azure AD-Multi-Factor Authentication kan du bara använda offentliga IP-adressintervall.
>
> IPv6-intervall stöds bara i gränssnittet för den [namngivna platsen (för hands version)](../conditional-access/location-condition.md#preview-features) .

Om din organisation distribuerar NPS-tillägget för att tillhandahålla MFA till lokala program, ser käll-IP-adressen alltid vara den NPS-server som autentiseringen försöker flöda genom.

| Typ av Azure AD-klient | Alternativ för betrodda IP-funktioner |
|:--- |:--- |
| Hanterad |Ett **särskilt intervall med IP-adresser**: administratörer anger ett intervall med IP-adresser som kan kringgå Multi-Factor Authentication för användare som loggar in från företagets intranät. Högst 50 betrodda IP-intervall kan konfigureras.|
| Federerade |**Alla federerade användare**: alla federerade användare som loggar in från i organisationen kan kringgå Multi-Factor Authentication. Användarna kringgår verifieringen genom att använda ett anspråk som utfärdats av Active Directory Federation Services (AD FS) (AD FS).<br/>Ett **särskilt intervall med IP-adresser**: administratörer anger ett intervall med IP-adresser som kan kringgå Multi-Factor Authentication för användare som loggar in från företagets intranät. |

Överanvändning av betrodda IP-arbeten är bara inifrån företagets intranät. Om du väljer alternativet **alla federerade användare** och en användare loggar in utanför företagets intranät, måste användaren autentisera med hjälp av Multi-Factor Authentication. Processen är densamma även om användaren presenterar ett AD FS-anspråk.

### <a name="end-user-experience-inside-of-corpnet"></a>Slut användar upplevelse i Corpnet

När funktionen betrodda IP-adresser är inaktive rad krävs Multi-Factor Authentication för webb läsar flöden. Applösenord krävs för äldre avancerade klient program.

När betrodda IP-adresser används krävs ingen Multi-Factor Authentication för webb läsar flöden. Applösenord krävs inte för äldre avancerade klient program, förutsatt att användaren inte har skapat ett applösenord. När ett applösenord används är lösen ordet obligatoriskt.

### <a name="end-user-experience-outside-corpnet"></a>Slut användar upplevelse utanför Corpnet

Oavsett om betrodd IP definieras krävs Multi-Factor Authentication för webb läsar flöden. Applösenord krävs för äldre avancerade klient program.

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivera namngivna platser med hjälp av villkorlig åtkomst

Du kan använda regler för villkorlig åtkomst för att definiera namngivna platser med hjälp av följande steg:

1. I Azure Portal söker du efter och väljer **Azure Active Directory** och bläddrar sedan till   >    >  **namngivna platser** för säkerhets villkorlig åtkomst.
1. Välj **ny plats**.
1. Ange ett namn på platsen.
1. Välj **Markera som betrodd plats**.
1. Ange IP-intervallet i CIDR-format för din miljö, till exempel *40.77.182.32/27*.
1. Välj **Skapa**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivera funktionen för betrodda IP-adresser med hjälp av villkorlig åtkomst

Utför följande steg för att aktivera betrodda IP-adresser med villkorliga åtkomst principer:

1. I Azure Portal söker du efter och väljer **Azure Active Directory** och bläddrar sedan till   >     >  **namngivna platser** för säkerhets villkorlig åtkomst.
1. Välj **Konfigurera MFA-betrodda IP-adresser**.
1. På sidan **tjänst inställningar** under **betrodda IP-adresser** väljer du något av följande två alternativ:

   * **För förfrågningar från federerade användare som kommer från mitt intranät**: Markera kryss rutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företags nätverket kringgår Multi-Factor Authentication genom att använda ett anspråk som utfärdas av AD FS. Se till att AD FS har en regel för att lägga till intranät anspråk till lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För förfrågningar från ett särskilt utbud av offentliga IP-adresser**: om du vill välja det här alternativet anger du IP-adresserna i text rutan med CIDR-notering.
      * För IP-adresser som ligger inom intervallet xxx. xxx. xxx. 1 till xxx. xxx. xxx. 254 använder du notation som **xxx. xxx. xxx. 0/24**.
      * Använd notation som **xxx.xxx.xxx.xxx/32** för en enskild IP-adress.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgår Multi-Factor Authentication.

1. Välj **Spara**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivera funktionen för betrodda IP-adresser med hjälp av tjänst inställningar

Om du inte vill använda principer för villkorlig åtkomst för att aktivera betrodda IP-adresser kan du konfigurera *tjänst inställningarna* för Azure AD Multi-Factor Authentication med hjälp av följande steg:

1. I Azure Portal söker du efter och väljer **Azure Active Directory** och väljer sedan **användare**.
1. Välj **Multi-Factor Authentication**.
1. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
1. På sidan **tjänst inställningar** under **betrodda IP-adresser** väljer du ett (eller båda) av följande två alternativ:

   * **För förfrågningar från federerade användare i mitt intranät**: om du vill välja det här alternativet markerar du kryss rutan. Alla federerade användare som loggar in från företags nätverket kringgår Multi-Factor Authentication genom att använda ett anspråk som utfärdas av AD FS. Se till att AD FS har en regel för att lägga till intranät anspråk till lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För förfrågningar från ett angivet intervall IP-adressundernät**: om du vill välja det här alternativet anger du IP-adresserna i text rutan med CIDR-notering.
      * För IP-adresser som ligger inom intervallet xxx. xxx. xxx. 1 till xxx. xxx. xxx. 254 använder du notation som **xxx. xxx. xxx. 0/24**.
      * Använd notation som **xxx.xxx.xxx.xxx/32** för en enskild IP-adress.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgår Multi-Factor Authentication.

1. Välj **Spara**.

## <a name="verification-methods"></a>Verifierings metoder

Du kan välja de verifierings metoder som är tillgängliga för dina användare i tjänst inställnings portalen. När dina användare registrerar sina konton för Azure AD Multi-Factor Authentication väljer de den önskade verifierings metoden från de alternativ som du har aktiverat. Vägledning för användar registrerings processen finns i [Konfigurera mitt konto för Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).

Följande verifierings metoder är tillgängliga:

| Metod | Beskrivning |
|:--- |:--- |
| Ring till telefon |Placerar ett automatiskt röst samtal. Användaren identifierar sig genom att svara och trycka på #-tangenten. Telefonnumret är inte synkroniserat med lokala Active Directory. |
| Textmeddelande till telefon |Skickar ett textmeddelande som innehåller en verifierings kod. Användaren uppmanas att ange verifierings koden i inloggnings gränssnittet. Den här processen kallas ett enkelriktat SMS. Dubbelriktat SMS innebär att användaren måste använda text för att återställa en viss kod. Dubbelriktat SMS är föråldrat och stöds inte efter 14 november 2018. Administratörer bör aktivera en annan metod för användare som tidigare har använt dubbelriktat SMS.|
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller registrerade enhet. Användaren visar meddelandet och väljer **Verifiera** för att slutföra verifieringen. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifierings kod från mobilapp eller maskinvaru-token |Microsoft Authenticator-appen genererar en ny OATH-verifierings kod var 30: e sekund. Användaren anger verifierings koden i inloggnings gränssnittet. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

Mer information finns i [vilka autentiserings-och verifierings metoder är tillgängliga i Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Aktivera och inaktivera verifierings metoder

Utför följande steg för att aktivera eller inaktivera verifierings metoder:

1. I Azure Portal söker du efter och väljer **Azure Active Directory** och väljer sedan **användare**.
1. Välj **Multi-Factor Authentication**.
1. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
1. På sidan **tjänst inställningar** under **verifierings alternativ** väljer/avmarkerar du de metoder som du vill använda för dina användare.
1. Klicka på **Spara**.

## <a name="remember-multi-factor-authentication"></a>Kom ihåg Multi-Factor Authentication

Med funktionen _kom ihåg Multi-Factor Authentication_ kan användarna kringgå efterföljande verifieringar under ett visst antal dagar efter att de har loggat in på en enhet med hjälp av Multi-Factor Authentication. För att förbättra användbarhet och minimera antalet gånger som en användare måste utföra MFA på samma enhet väljer du en varaktighet på 90 dagar eller mer.

> [!IMPORTANT]
> Om ett konto eller en enhet komprometteras kan det påverka säkerheten genom att komma ihåg Multi-Factor Authentication för betrodda enheter. Om ett företags konto blir komprometterat eller om en betrodd enhet tappas bort eller blir stulen, bör du [återkalla MFA-sessioner](howto-mfa-userdevicesettings.md).
>
> Återställnings åtgärden återkallar betrodd status från alla enheter och användaren måste utföra Multi-Factor Authentication igen. Du kan också instruera användarna att återställa Multi-Factor Authentication på sina egna enheter enligt vad som anges i [Hantera dina inställningar för Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Så här fungerar funktionen

Funktionen kom ihåg Multi-Factor Authentication anger en permanent cookie i webbläsaren när användaren väljer alternativet **fråga inte igen om X dagar** vid inloggning. Användaren tillfrågas inte igen för Multi-Factor Authentication från samma webbläsare tills cookien upphör att gälla. Om användaren öppnar en annan webbläsare på samma enhet eller rensar sina cookies uppmanas de att verifiera.

Alternativet **fråga inte igen om X dagar** visas inte i program som inte är webbläsare, oavsett om appen stöder modern autentisering eller inte. De här apparna använder _uppdateringstoken_ som ger nya åtkomsttoken varje timma. När en uppdateringstoken verifieras kontrollerar Azure AD att den senaste Multi-Factor Authentication har inträffat inom det angivna antalet dagar.

Funktionen minskar antalet autentiseringar i Web Apps, som normalt frågas varje gång. Funktionen kan öka antalet autentiseringar för moderna autentiserings klienter som normalt uppmanas var 90: e dag, om en lägre varaktighet har kon figurer ATS. Kan också öka antalet autentiseringar när de kombineras med villkorliga åtkomst principer.

> [!IMPORTANT]
> Funktionen **kom ihåg Multi-Factor Authentication** är inte kompatibel med funktionen **Håll mig inloggad i** AD FS när användare utför Multi-factor authentication för AD FS via Azure Multi-Factor Authentication-Server eller en Multi-Factor Authentication-lösning från tredje part.
>
> Om användarna väljer att **låta mig vara inloggad** på AD FS och även markera enheten som betrodd för Multi-Factor Authentication, kontrol leras inte användaren automatiskt när det **sparade Multi-Factor Authentication** -antalet dagar upphör att gälla. Azure AD begär en ny Multi-Factor Authentication, men AD FS returnerar en token med den ursprungliga Multi-Factor Authentication-anspråket och det aktuella datumet, i stället för att utföra Multi-Factor Authentication igen. **Den här reaktionen anger en verifierings slinga mellan Azure AD och AD FS.**
>
> Funktionen **kom ihåg Multi-Factor Authentication** är inte kompatibel med B2B-användare och kommer inte att vara synlig för B2B-användare när de loggar in på inbjudna klienter.
>

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera kom ihåg Multi-Factor Authentication

Gör så här för att aktivera och konfigurera alternativet för användarna att komma ihåg MFA-status och kringgå prompter:

1. I Azure Portal söker du efter och väljer **Azure Active Directory** och väljer sedan **användare**.
1. Välj **Multi-Factor Authentication**.
1. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
1. På sidan **tjänst inställningar** under **kom ihåg Multi-Factor Authentication** väljer du alternativet **Tillåt användare att komma ihåg Multi-Factor Authentication på enheter som de litar på** .
1. Ange antalet dagar att tillåta att betrodda enheter kringgår Multi-Factor Authentication. För den bästa användar upplevelsen kan du förlänga varaktigheten till *90* eller flera dagar.
1. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du har aktiverat funktionen kom ihåg Multi-Factor Authentication kan användare Markera en enhet som betrodd när de loggar in genom att välja alternativet för **fråga inte igen**.

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga metoder för användning i Azure AD Multi-Factor Authentication finns i [vilka autentiserings-och verifierings metoder är tillgängliga i Azure Active Directory?](concept-authentication-methods.md)
