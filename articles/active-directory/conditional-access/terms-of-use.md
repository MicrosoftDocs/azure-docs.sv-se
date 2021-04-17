---
title: Användningsvillkor – Azure Active Directory | Microsoft Docs
description: Kom igång med Azure Active Directory användningsvillkor för att presentera information för anställda eller gäster innan du får åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530251"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory användningsvillkor

Användningsvillkoren för Azure AD-principer är en enkel metod som organisationer kan använda för att presentera information för slutanvändare. Den här presentationen gör att användare kan se relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav. Den här artikeln beskriver hur du kommer igång med användningsvillkorsprinciper (ToU).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Översiktsvideor

Följande videoklipp ger en snabb översikt över ToU-principer.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Fler videor finns i:
- [Så här distribuerar du en användningsvillkorsprincip i Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Så här distribuerar du en princip för användningsvillkor i Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Vad kan jag göra med användningsvillkoren?

Användningsvillkoren för Azure AD har följande funktioner:

- Kräv att anställda eller gäster godkänner användningsvillkoren innan de får åtkomst.
- Kräv att anställda eller gäster godkänner användningsvillkoren på varje enhet innan de får åtkomst.
- Kräv att anställda eller gäster godkänner användningsvillkoren enligt ett återkommande schema.
- Kräv att anställda eller gäster godkänner användningsvillkoren innan de registrerar säkerhetsinformation i Azure AD Multi-Factor Authentication (MFA).
- Kräv att anställda godkänner användningsvillkoren innan de registrerar säkerhetsinformation i Azure AD-lösenordsåterställning via självbetjäning (SSPR).
- Presentera en princip för allmänna användningsvillkor för alla användare i din organisation.
- Presentera specifika användningsvillkorsprinciper baserat på användarattribut (t.ex. läkare jämfört med sjuksköterskor eller inhemska jämfört med internationella medarbetare, via [dynamiska grupper](../enterprise-users/groups-dynamic-membership.md)).
- Presentera specifika användningsvillkorsprinciper vid åtkomst till program med stor inverkan på verksamheten, till exempel Salesforce.
- Presentera användningsvillkorsprinciper på olika språk.
- Lista vem som har eller inte har godkänt dina användningsvillkorsprinciper.
- Hjälp med att uppfylla sekretessbestämmelser.
- Visa en logg över användningsvillkorsprincipaktivitet för efterlevnad och granskning.
- Skapa och hantera användningsvillkor med hjälp Microsoft Graph [API:er](/graph/api/resources/agreement) (för närvarande i förhandsversion).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda och konfigurera användningsvillkor för Azure AD måste du ha:

- Azure AD Premium P1-, P2-, EMS E3- eller EMS E5-prenumeration.
   - Om du inte har en av de här prenumerationerna, kan du [hämta Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) eller [aktivera Azure AD Premium-utvärderingsversionen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Ett av följande administratörskonton för den katalog som du vill konfigurera:
   - Global administratör
   - Säkerhetsadministratör
   - Administratör för villkorsstyrd åtkomst

## <a name="terms-of-use-document"></a>Dokument med användningsvillkor

Användningsvillkoren för Azure AD använder PDF-formatet för att presentera innehåll. PDF-filen kan innehålla valfritt innehåll, som befintliga kontraktdokument, så att du kan samla in slutanvändaravtal under inloggningen. Den rekommenderade teckenstorleken i PDF-filen är 24 poäng för att stödja användare på mobila enheter.

## <a name="add-terms-of-use"></a>Lägga till användningsvillkor

När du har gjort klart principdokumentet för användningsvillkoren använder du följande procedur för att lägga till den.

1. Logga in på Azure som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Gå till **användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Villkorlig åtkomst – Användningsvillkor bladet](./media/terms-of-use/tou-blade.png)

1. Klicka på **Nya villkor**.

    ![Fönstret Ny period för användning för att ange inställningar för användningsvillkor](./media/terms-of-use/new-tou.png)

1. I **rutan Namn** anger du ett namn på principen för användningsvillkor som ska användas i Azure Portal.
1. I rutan **Visningsnamn** anger du en rubrik som användarna ser när de loggar in.
1. Om **Användningsvillkor dokument** bläddrar du till pdf-filen med de färdiga användningsvillkoren och väljer den.
1. Välj språk för principdokumentet för användningsvillkoren. Med språkalternativet kan du ladda upp flera användningsvillkor, var och en med olika språk. Den version av principen för användningsvillkor som en slutanvändare ser baseras på deras webbläsarinställningar.
1. Om du vill kräva att slutanvändarna visar användningsvillkoren innan de godkänns anger du Kräv att användarna utökar **användningsvillkoren** till **På.**
1. Om du vill kräva att slutanvändarna godkänner användningsvillkoren på varje enhet de använder från anger du Kräv att användarna godkänner varje **enhet** till **På.** Användare kan behöva installera ytterligare program om det här alternativet är aktiverat. Mer information finns i [Användningsvillkor per enhet.](#per-device-terms-of-use)
1. Om du vill att medgivanden för användningspolicyn ska upphöra att gälla enligt ett schema anger du **Förfallogodkännanden** **till På.** När det här är inställt på På visas två ytterligare schemainställningar.

    ![Inställningar för att förfalla medgivanden för att ange startdatum, frekvens och varaktighet](./media/terms-of-use/expire-consents.png)

1. Använd inställningarna **Expire starting on** **(Upphör** att gälla) och Frequency (Frekvens) för att ange ett schema för förfallotid för användningspolicyn. I följande tabell visas resultatet för ett par exempelinställningar:

   | Upphör att gälla från och med | Frekvens | Resultat |
   | --- | --- | --- |
   | Dagens datum  | Månadsvis | Från och med idag måste användarna godkänna användningsvillkoren och sedan godkänna dem varje månad. |
   | Datum i framtiden  | Månadsvis | Från och med idag måste användarna godkänna användningsvillkorens princip. När det framtida datumet inträffar upphör medgivandena att gälla och sedan måste användarna godkänna dem varje månad.  |

   Om du till exempel ställer in förfallodatumet som börjar på **1** januari och frekvensen Månadsvis **så** här kan förfallotiderna uppstå för två användare:

   | Användare | Första acceptdatumet | Första utgångsdatum | Andra utgångsdatum | Tredje utgångsdatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 feb | 1 mars | 1 april |
   | Bob | 15 jan | 1 feb | 1 mars | 1 april |

1. Använd inställningen **Duration before reacceptance requires (days) (Varaktighet** före återacceptans kräver (dagar) för att ange antalet dagar innan användaren måste uppfylla användningsvillkorens princip. På så sätt kan användarna följa sitt eget schema. Om du till exempel anger varaktigheten till **30** dagar, så här kan förfallotid uppstå för två användare:

   | Användare | Första acceptdatum | Första utgångsdatum | Andra utgångsdatum | Tredje utgångsdatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 januari | 2 mars | 1 april |
   | Bob | 15 jan | 14 feb | 16 mars | 15 april |

   Det är möjligt att använda inställningarna **Förfalla** medgivanden och Varaktighet innan godkännande krävs **(dagar),** men vanligtvis använder du den ena eller den andra.

1. Under **Villkorlig åtkomst** använder du **malllistan Framtvinga** med princip för villkorsstyrd åtkomst för att välja mallen för att tillämpa användningsvillkoren.

    ![Listrutan Villkorsstyrd åtkomst för att välja en principmall](./media/terms-of-use/conditional-access-templates.png)

   | Mall | Description |
   | --- | --- |
   | **Åtkomst till molnappar för alla gäster** | En princip för villkorlig åtkomst skapas för alla gäster och alla molnappar. Den här principen påverkar Azure Portal. När det här har skapats kan du behöva logga ut och logga in. |
   | **Åtkomst till molnappar för alla användare** | En princip för villkorlig åtkomst skapas för alla användare och alla molnappar. Den här principen påverkar Azure Portal. När detta har skapats måste du logga ut och logga in. |
   | **Anpassad princip** | Välj de användare, grupper och appar som principen för användningsvillkor ska tillämpas på. |
   | **Skapa en princip för villkorlig åtkomst senare** | Den här principen för användningsvillkor visas i listan över beviljandekontroll när du skapar en princip för villkorlig åtkomst. |

   >[!IMPORTANT]
   >Principkontroller för villkorsstyrd åtkomst (inklusive användningsvillkor) stöder inte tillämpning av tjänstkonton. Vi rekommenderar att du exkluderar alla tjänstkonton från principen för villkorlig åtkomst.

    Anpassade principer för villkorsstyrd åtkomst möjliggör detaljerade användningsvillkorsprinciper, ned till ett specifikt molnprogram eller en grupp användare. Mer information finns i [Snabbstart: Kräv att användningsvillkoren godkänns innan du får åtkomst till molnappar.](require-tou.md)

1. Klicka på **Skapa**.

    Om du har valt en anpassad mall för villkorsstyrd åtkomst visas en ny skärm där du kan skapa den anpassade principen för villkorlig åtkomst.

   ![Nytt fönster för villkorlig åtkomst om du väljer den anpassade principmallen för villkorlig åtkomst](./media/terms-of-use/custom-policy.png)

   Nu bör du se dina nya användningsvillkorsprinciper.

   ![Nya användningsvillkor som anges i bladet med användningsvillkor](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visa rapport över vem som har accepterat och avvisat

Bladet för användningsvillkor visar antalet användare som har godkänt och avböjt. Dessa antal och vem som accepterat/avböjt lagras under användningsvillkorens livslängd.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Användningsvillkor som visar antalet användare som har accepterat och avvisat](./media/terms-of-use/view-tou.png)

1. För en princip för användningsvillkor klickar du på numren under **Accepterad** eller **Nekad** för att visa det aktuella tillståndet för användare.

    ![Användningsvillkor fönster med medgivanden som visar de användare som har godkänt](./media/terms-of-use/accepted-tou.png)

1. Om du vill visa historiken för en enskild användare klickar du på ellipsen (**...**) och sedan **på Visa historik**.

    ![Visa historikkontextmenyn för en användare](./media/terms-of-use/view-history-menu.png)

   I fönstret visningshistorik visas en historik över alla accepterande, nekade och förfallotid.

   ![Fönstret Visningshistorik visar historiken accepterar, avböjer och förfaller för en användare](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visa Azure AD-granskningsloggar

Om du vill visa ytterligare aktivitet innehåller Användningsprinciper för Azure AD granskningsloggar. Varje användarmedgivande utlöser en händelse i granskningsloggarna som lagras i **30 dagar**. Du kan visa dessa loggar i portalen eller hämta dem som en CSV-fil.

Kom igång med Azure AD-granskningsloggar med hjälp av följande procedur:

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj en princip för användningsvillkor.
1. Klicka på **Visa granskningsloggar**.

    ![Användningsvillkor med alternativet Visa granskningsloggar markerat](./media/terms-of-use/audit-tou.png)

1. På skärmen azure AD-granskningsloggar kan du filtrera informationen med hjälp av de angivna listorna för att rikta in dig på specifik granskningslogginformation.

    Du kan också klicka på **Hämta** för att ladda ned informationen i en CSV-fil och använda den lokalt.

   ![Azure AD-granskningsloggar skärmlistningsdatum, målprincip, initierad av och aktivitet](./media/terms-of-use/audit-logs-tou.png)

   Om du klickar på en logg visas ett fönster med ytterligare aktivitetsinformation.

   ![Aktivitetsinformation för en logg som visar aktivitet, aktivitetsstatus, initierad av, målprincip](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Hur användningsvillkor ser ut för användare

När en ToU-princip har skapats och framtvingas visas följande skärm för användare som ingår i omfånget under inloggningen.

![Exempel på användningsvillkor som visas när en användare loggar in](./media/terms-of-use/user-tou.png)

Användarna kan visa användningsvillkorens princip och, om det behövs, använda knappar för att zooma in och ut.

![Vy över användningsvillkor med zoomknappar](./media/terms-of-use/zoom-buttons.png)

Följande skärmbild visar hur en ToU-princip ser ut på mobila enheter.

![Exempel på användningsvillkor som visas när en användare loggar in på en mobil enhet](./media/terms-of-use/mobile-tou.png)

Användarna behöver bara godkänna användningsvillkoren en gång och de ser inte användningsvillkoren igen vid efterföljande inloggningar.

### <a name="how-users-can-review-their-terms-of-use"></a>Så här kan användarna granska sina användningsvillkor

Användare kan granska och se användningsvillkorens principer som de har godkänt med hjälp av följande procedur.

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Klicka på ditt namn i det övre högra hörnet och välj **Profil.**

    ![MyApps-webbplats med användarens fönster öppet](./media/terms-of-use/tou14.png)

1. Klicka på **Granska användningsvillkoren** på din profilsida.

    ![Profilsida för en användare som visar länken Granska användningsvillkor](./media/terms-of-use/tou13a.png)

1. Därifrån kan du granska användningsvillkoren som du har godkänt.

## <a name="edit-terms-of-use-details"></a>Redigera användningsvillkor

Du kan redigera viss information om användningsvillkorsprinciper, men du kan inte ändra ett befintligt dokument. Följande procedur beskriver hur du redigerar informationen.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj den användningsprincip som du vill redigera.
1. Klicka **på Redigera termer.**
1. I fönstret Redigera användningsvillkor kan du ändra följande:
    - **Namn** – det här är det interna namnet på den toU som inte delas med slutanvändare
    - **Visningsnamn** – det här är det namn som slutanvändarna kan se när de visar toU
    - **Kräv att användarna utökar** användningsvillkoren – Om du ställer in detta på **På** tvingar du slutanvändningen att expandera dokumentet med användningsvillkoren innan de godkänner det.
    - (Förhandsversion) Du kan **uppdatera ett befintligt användningsvillkorsdokument**
    - Du kan lägga till ett språk i en befintlig toU

   Om det finns andra inställningar som du vill ändra, till exempel PDF-dokument, kräver att användarna godkänner varje enhet, förfaller medgivanden, varaktighet före godkännande eller princip för villkorsstyrd åtkomst, måste du skapa en ny toU-princip.

    ![Redigera som visar olika språkalternativ ](./media/terms-of-use/edit-terms-use.png)

1. När du är klar klickar du på **Spara** för att spara ändringarna.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Uppdatera versionen eller pdf-filen för befintliga användningsvillkor

1.  Logga in på Azure och gå till [Användningsvillkor](https://aka.ms/catou)
2.  Välj den användningsprincip som du vill redigera.
3.  Klicka **på Redigera termer.**
4.  För det språk som du vill uppdatera en ny version klickar du på **Uppdatera** under åtgärdskolumnen

    ![Fönstret Redigera användningsvillkor som visar namn och alternativ för att expandera](./media/terms-of-use/edit-terms-use.png)

5.  Ladda upp pdf-filen för den nya versionen i fönstret till höger
6.  Det finns också ett växlingsalternativ här **Kräv återaccept** om du vill kräva att användarna ska godkänna den nya versionen nästa gång de loggar in. Om du kräver att användarna godkänner den nya versionen uppmanas de att godkänna den nya versionen nästa gång de försöker komma åt resursen som definierats i principen för villkorlig åtkomst. Om du inte kräver att användarna godkänns på nytt förblir deras tidigare medgivande aktuellt och endast nya användare som inte har samtyckt innan eller vars medgivande upphör att gälla kommer att se den nya versionen.

    ![Alternativet Redigera användningsvillkor för att godkänna på ett annat sätt är markerat](./media/terms-of-use/re-accept.png)

7.  När du har laddat upp din nya pdf-fil och bestämt dig för att återtilldela klickar du på Lägg till längst ned i fönstret.
8.  Du ser nu den senaste versionen under kolumnen Dokument.

## <a name="view-previous-versions-of-a-tou"></a>Visa tidigare versioner av en toU

1.  Logga in på Azure och gå till **Användningsvillkoren** på https://aka.ms/catou.
2.  Välj principen för användningsvillkor som du vill visa en versionshistorik för.
3.  Klicka på **Språk och versionshistorik**
4.  Klicka på **Visa tidigare versioner.**

    ![dokumentinformation, inklusive språkversioner](./media/terms-of-use/document-details.png)

5.  Du kan klicka på namnet på dokumentet för att ladda ned den versionen

## <a name="see-who-has-accepted-each-version"></a>Se vem som har godkänt varje version

1.  Logga in på Azure och gå till **Användningsvillkoren** på https://aka.ms/catou.
2.  Om du vill se vem som har godkänt toU klickar du på numret under **kolumnen Godkänt** för den ToU som du vill använda.
3.  Som standard visar nästa sida det aktuella tillståndet för varje användares godkännande av ToU
4.  Om du vill se tidigare medgivandehändelser kan du välja **Alla** i **listrutan Aktuellt** tillstånd. Nu kan du se varje användarhändelser i information om varje version och vad som hände.
5.  Du kan också välja en specifik version i **listrutan Version för**  att se vem som har godkänt den specifika versionen.


## <a name="add-a-tou-language"></a>Lägga till ett ToU-språk

Följande procedur beskriver hur du lägger till ett ToU-språk.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj den användningsprincip som du vill redigera.
1. Klicka **på Redigera villkor**
1. Klicka **på Lägg** till språk längst ned på sidan.
1. I fönstret Lägg till användningsvillkorsspråk laddar du upp din lokaliserade PDF och väljer språk.

    ![Användningsvillkor valt och visar fliken Språk i informationsfönstret](./media/terms-of-use/select-language.png)

1. Klicka **på Lägg till språk.**
1. Klicka på **Spara**

1. Klicka **på Lägg** till för att lägga till språket.

## <a name="per-device-terms-of-use"></a>Användningsvillkor per enhet

Med **inställningen Kräv att** användarna godkänner alla enheter kan du kräva att slutanvändarna godkänner användningsvillkoren på varje enhet som de kommer åt från. Slutanvändaren måste registrera sin enhet i Azure AD. När enheten har registrerats används enhets-ID:t för att framtvinga användningsvillkoren på varje enhet.

Här är en lista över plattformar och programvara som stöds.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Övrigt |
> | --- | --- | --- | --- | --- |
> | **Inbyggd app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (med tillägg)** | Ja | Ja | Ja |  |

Användningsvillkoren per enhet har följande begränsningar:

- En enhet kan bara vara ansluten till en klientorganisation.
- En användare måste ha behörighet att ansluta till sin enhet.
- Intune-registreringsappen stöds inte. Se till att den undantas från principer för villkorsstyrd åtkomst som kräver användningsvillkor.
- Azure AD B2B-användare stöds inte.

Om användarens enhet inte är ansluten visas ett meddelande om att användaren måste ansluta till enheten. Deras upplevelse är beroende av plattformen och programvaran.

### <a name="join-a-windows-10-device"></a>Anslut en Windows 10-enhet

Om en användare använder Windows 10 och Microsoft Edge får användaren ett meddelande som liknar följande för att [ansluta sin enhet](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 och Microsoft Edge – Meddelande som anger att enheten måste vara registrerad](./media/terms-of-use/per-device-win10-edge.png)

Om de använder Chrome uppmanas de att installera tillägget [Windows 10 Accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrera en iOS-enhet

Om en användare använder en iOS-enhet uppmanas de att installera [Microsoft Authenticator appen](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registrera en Android-enhet

Om en användare använder en Android-enhet uppmanas de att installera [Microsoft Authenticator appen](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Webbläsare

Om en användare använder en webbläsare som inte stöds uppmanas de att använda en annan webbläsare.

![Meddelande som anger att enheten måste vara registrerad, men webbläsaren stöds inte](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Ta bort användningsvillkor

Du kan ta bort gamla användningsvillkor med hjälp av följande procedur.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj principen för användningsvillkor som du vill ta bort.
1. Klicka på **Ta bort villkor**.
1. På meddelandet som visas som frågar om du vill fortsätta klickar du **Ja**.

    ![Meddelande där du uppmanas att bekräfta att användningsvillkoren ska tas bort](./media/terms-of-use/delete-tou.png)

   Du bör inte längre se din användningsvillkorsprincip.

## <a name="user-acceptance-record-deletion"></a>Borttagning av användargodkännandepost

Användargodkännandeposter tas bort:

- När administratören uttryckligen tar bort toU. När detta sker raderas även alla godkännandeposter som är associerade med den specifika tou:en.
- När klienten förlorar sin Azure Active Directory Premium licens.
- När klienten tas bort.

## <a name="policy-changes"></a>Principändringar

Principer för villkorlig åtkomst börjar gälla omedelbart. När detta inträffar börjar administratören se "ledsna moln" eller "Problem med Azure AD-token". Administratören måste logga ut och logga in igen för att uppfylla den nya principen.

> [!IMPORTANT]
> Användare inom området behöver logga ut och logga in för att uppfylla en ny princip om:
>
> - en princip för villkorsstyrd åtkomst är aktiverad för en användningsvillkorsprincip
> - eller så skapas en andra användningsvillkorsprincip

## <a name="b2b-guests"></a>B2B-gäster

De flesta organisationer har en process där de anställda kan godkänna organisationens användningsvillkor och sekretesspolicyer. Men hur kan du tillämpa samma medgivanden för B2B-gäster (business-to-business) i Azure AD när de läggs till via SharePoint eller Teams? Med hjälp av principer för villkorsstyrd åtkomst och användningsvillkor kan du tillämpa en princip direkt mot B2B-gästanvändare. Under inlösningsflödet för inbjudan visas användningsvillkoren för användaren. Det här stödet är för närvarande i förhandsversion.

Användningsvillkor principer visas bara när användaren har ett gästkonto i Azure AD. SharePoint Online har för närvarande [en ad hoc extern](/sharepoint/what-s-new-in-sharing-in-targeted-release) delningsmottagare för att dela ett dokument eller en mapp som inte kräver att användaren har ett gästkonto. I det här fallet visas inte en användningsprincip.

![Fönstret Användare och grupper – Fliken Inkludera med alternativet Alla gästanvändare markerat](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Stöd för molnappar

Användningsvillkor kan användas för olika molnappar, till exempel Azure Information Protection och Microsoft Intune. Det här stödet är för närvarande i förhandsversion.

### <a name="azure-information-protection"></a>Azure Information Protection

Du kan konfigurera en princip för villkorlig åtkomst för Azure Information Protection appen och kräva en användningsvillkorsprincip när en användare kommer åt ett skyddat dokument. Detta utlöser en användningsvillkorsprincip innan en användare får åtkomst till ett skyddat dokument för första gången.

![Fönstret Molnappar med Microsoft Azure Information Protection valt](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune registrering

Du kan konfigurera en princip för villkorlig åtkomst för appen Microsoft Intune-registrering och kräva en användningsvillkorsprincip innan du kan registrera en enhet i Intune. Mer information finns i blogginlägget Om att välja rätt lösning [för villkor för din organisation.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Fönstret Molnappar med Microsoft Intune valt](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune-registreringsappen stöds inte [för användningsvillkor per enhet.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Jag kan inte logga in med PowerShell när användningsvillkor är aktiverade.**<br />
S: Användningsvillkor kan bara godkännas när du autentiserar interaktivt.

**F: Hur ser jag om/när en användare har godkänt användningsvillkoren?**<br />
S: Klicka Användningsvillkor under Godkänt på bladet **.** Du kan också visa eller söka i accept-aktiviteten i Azure AD-granskningsloggarna. Mer information finns i Visa rapport över vem som har accepterat och avvisat och Visa [Azure AD-granskningsloggar.](#view-azure-ad-audit-logs)

**F: Hur länge lagras informationen?**<br />
S: Antalet användare i användningsvillkoren och vem som accepterat/avböjt lagras under användningsvillkorens livslängd. Azure AD-granskningsloggarna lagras i 30 dagar.

**F: Varför ser jag ett annat antal medgivanden i användningsvillkoren jämfört med Azure AD-granskningsloggarna?**<br />
S: Användningsvillkoren lagras under användningsprincipens livslängd, medan Azure AD-granskningsloggarna lagras i 30 dagar. Dessutom visar användningsvillkoren endast användarens aktuella medgivandetillstånd. Om en användare t.ex. avböjer och sedan godkänner, visar användningsvillkoren endast användarens accept. Om du behöver se historiken kan du använda Azure AD-granskningsloggarna.

**F: Om jag redigerar informationen för en princip för användningsvillkor, kräver det då att användarna godkänner dem igen?**<br />
S: Nej, om en administratör redigerar informationen för en användningsvillkorsprincip (namn, visningsnamn, kräv att användarna expanderar eller lägger till ett språk) kräver det inte att användarna tillåter de nya villkoren.

**F: Kan jag uppdatera ett befintligt principdokument för användningsvillkor?**<br />
S: För närvarande kan du inte uppdatera ett befintligt dokument för användningsvillkor. Om du vill ändra ett principdokument för användningsvillkor måste du skapa en ny instans av användningsvillkoren.

**F: Kommer slutanvändarna att kunna klicka på hyperlänkar i PDF-dokumentet för användningsvillkor?**<br />
S: Ja, slutanvändarna kan välja hyperlänkar till ytterligare sidor, men länkar till avsnitt i dokumentet stöds inte. Hyperlänkar i pdf-filer för användningsprincip fungerar inte heller vid åtkomst från Azure AD MyApps/MyAccount-portalen.

**F: Kan en användningsvillkorsprincip stödja flera språk?**<br />
S: Ja. För närvarande finns det 108 olika språk som en administratör kan konfigurera för en enda användningsvillkorsprincip. En administratör kan ladda upp flera PDF-dokument och tagga dokumenten med ett motsvarande språk (upp till 108). När slutanvändarna loggar in tittar vi på deras webbläsarspråkinställningar och visar det matchande dokumentet. Om det inte finns någon matchning visas standarddokumentet, som är det första dokumentet som laddas upp.

**F: När utlöses principen för användningsvillkor?**<br />
S: Användningsvillkoren utlöses under inloggningen.

**F: Vilka program kan jag rikta en princip för användningsvillkor till?**<br />
S: Du kan skapa en princip för villkorlig åtkomst för företagsprogram med modern autentisering. Mer information finns i [enterprise applications](./../manage-apps/view-applications-portal.md) (företagsprogram).

**F: Kan jag lägga till flera användningsvillkorsprinciper till en viss användare eller app?**<br />
S: Ja, genom att skapa flera principer för villkorlig åtkomst för dessa grupper eller program. Om en användare omfattas av flera användningsvillkor godkänner de en användningsprincip i taget.

**F: Vad händer om en användare avböjer användningsvillkoren?**<br />
S: Användare blockeras från åtkomst till programmet. Användaren måste logga in igen och godkänna villkoren för att få åtkomst.

**F: Är det möjligt att ta bort en princip för användningsvillkor som godkändes tidigare?**<br />
S: Du kan [granska tidigare godkända användningsvillkorsprinciper,](#how-users-can-review-their-terms-of-use)men för närvarande finns det inget sätt att ta bort det.

**F: Vad händer om jag också använder allmänna villkor för Intune?**<br />
S: Om du har konfigurerat både användningsvillkoren för Azure AD och Villkoren för [Intune](/intune/terms-and-conditions-create)måste användaren godkänna båda. Mer information finns i [blogginlägget Choosing the right Terms solution for your organization (Välja](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)rätt lösning för villkor för din organisation).

**F: Vilka slutpunkter använder användningsvillkoren för autentisering?**<br />
S: Användningsvillkor använder följande slutpunkter för autentisering: https://tokenprovider.termsofuse.identitygovernance.azure.com och https://account.activedirectory.windowsazure.com . Om din organisation har en lista över tillåtna url:er för registrering måste du lägga till dessa slutpunkter i listan över tillåtna, tillsammans med Azure AD-slutpunkter för inloggning.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Kräv att användningsvillkoren godkänns innan du får åtkomst till molnappar](require-tou.md)
