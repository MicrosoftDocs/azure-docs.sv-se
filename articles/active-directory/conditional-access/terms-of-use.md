---
title: Användningsvillkor-Azure Active Directory | Microsoft Docs
description: Kom igång med att använda Azure Active Directory användnings villkor för att presentera information till anställda eller gäster innan du får åtkomst.
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
ms.openlocfilehash: 95fe70c774b933113c94125d227976e32a9e353f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919637"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory användnings villkor

Användnings principer för Azure AD ger en enkel metod som organisationer kan använda för att presentera information för slutanvändare. Den här presentationen gör att användare kan se relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav. Den här artikeln beskriver hur du kommer igång med användnings villkor för ToU-principer.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Översikts videor

Följande videoklipp ger en snabb översikt över ToU-principer.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Mer information finns i:
- [Så här distribuerar du en princip för användnings villkor i Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Så här distribuerar du en princip för användnings villkor i Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Vad kan jag göra med användnings villkoren?

Principer för användnings villkor för Azure AD har följande funktioner:

- Kräv att medarbetare eller gäster accepterar dina användnings villkor innan du får åtkomst.
- Kräv att medarbetare eller gäster accepterar dina användnings villkor på varje enhet innan du får åtkomst.
- Kräv att medarbetare eller gäster accepterar dina användnings villkor enligt ett återkommande schema.
- Kräv att medarbetare eller gäster accepterar dina användnings villkor innan säkerhets information registreras i Azure AD Multi-Factor Authentication (MFA).
- Kräv att medarbetarna accepterar dina användnings villkor innan säkerhets information registreras i Azure AD självbetjäning för återställning av lösen ord (SSPR).
- Presentera en allmän användnings princip för alla användare i din organisation.
- Visa vissa användnings villkor baserat på användarattribut (t. ex. läkare jämfört med sjuksköterskor eller inhemska jämfört med internationella medarbetare, via [dynamiska grupper](../enterprise-users/groups-dynamic-membership.md)).
- Presentera specifika användnings villkor vid åtkomst till program med höga affärs effekter, till exempel Salesforce.
- Använd principer för användnings villkor på olika språk.
- Lista med eller har inte accepterat villkoren för användnings villkor.
- Hjälp i att uppfylla sekretess bestämmelser.
- Visa en logg över villkoren för användnings princip aktivitet för efterlevnad och granskning.
- Skapa och hantera användnings principer med hjälp av [Microsoft Graph-API: er](/graph/api/resources/agreement?view=graph-rest-beta) (för närvarande i för hands version).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda och konfigurera principer för användnings villkor för Azure AD måste du ha:

- Azure AD Premium P1-, P2-, EMS E3- eller EMS E5-prenumeration.
   - Om du inte har en av de här prenumerationerna, kan du [hämta Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) eller [aktivera Azure AD Premium-utvärderingsversionen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Ett av följande administratörskonton för den katalog som du vill konfigurera:
   - Global administratör
   - Säkerhetsadministratör
   - Administratör för villkorsstyrd åtkomst

## <a name="terms-of-use-document"></a>Dokument med användningsvillkor

Användnings principer för Azure AD använder PDF-formatet för att presentera innehåll. PDF-filen kan innehålla valfritt innehåll, som befintliga kontraktdokument, så att du kan samla in slutanvändaravtal under inloggningen. För att stödja användare på mobila enheter är den rekommenderade tecken storleken i PDF-filen 24 punkter.

## <a name="add-terms-of-use"></a>Lägg till användnings villkor

När du har slutfört användar villkoren använder du följande procedur för att lägga till det.

1. Logga in på Azure som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Gå till **användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Villkorlig åtkomst – Användningsvillkor bladet](./media/terms-of-use/tou-blade.png)

1. Klicka på **Nya villkor**.

    ![Nytt användnings villkor för att ange dina användnings villkor](./media/terms-of-use/new-tou.png)

1. I rutan **namn** anger du ett namn på villkoren för användnings principen som ska användas i Azure Portal.
1. I rutan **visnings namn** anger du en rubrik som användarna ser när de loggar in.
1. För **användningsvillkor dokument** bläddrar du till dina slutgiltiga villkor för användnings princip PDF och markerar den.
1. Välj språk för användnings princip dokumentet. Med alternativet språk kan du överföra flera användnings villkor, var och en med ett annat språk. Vilken version av användnings principen som en slutanvändare ser kommer att baseras på deras webb läsar inställningar.
1. Om du vill att slutanvändarna ska kunna se användnings principerna innan de accepterar dem, ställer du in **Kräv att användarna expanderar användnings villkoren** till **på**.
1. Om du vill att slutanvändarna ska kunna godkänna användnings villkoren på varje enhet som de har åtkomst till från, ställer du in **Kräv att användare godkänner varje enhet** till **på**. Användare kan behöva installera ytterligare program om det här alternativet är aktiverat. Mer information finns i [användnings villkoren per enhet](#per-device-terms-of-use).
1. Om du vill att villkoren för användnings villkor ska upphöra att gälla för ett schema anger du **förfallo datum för medgivanden** till **på**. När värdet är på visas två ytterligare schema inställningar.

    ![Inställningar som upphör att gälla anger start datum, frekvens och varaktighet](./media/terms-of-use/expire-consents.png)

1. Använd inställningarna för att **börja om från** och med **frekvensen** för att ange schemat för villkor för användnings villkor. I följande tabell visas resultatet av ett par exempel inställningar:

   | Förfaller från | Frekvens | Resultat |
   | --- | --- | --- |
   | Dagens datum  | Varje månad | Från och med idag måste användarna godkänna villkoren för användning och sedan acceptera varje månad igen. |
   | Datum i framtiden  | Varje månad | Från och med idag måste användarna godkänna användnings villkoren. När det framtida datumet inträffar upphör medgivanden att gälla och användarna måste sedan acceptera varje månad igen.  |

   Om du t. ex. ställer in sista giltighets datum till **1 januari** och frekvens till **månatlig**, så kan det uppstå förfallo datum för två användare:

   | User | Första godkännande datum | Första utgångs datum | Förfallo datum för andra | Datum för tredje utgångs datum |
   | --- | --- | --- | --- | --- |
   | Anna | 1 jan | Feb 1 | Mar 1 | Apr 1 |
   | Bob | 15 jan | Feb 1 | Mar 1 | Apr 1 |

1. Använd inställningen **varaktighet före godkännande kräver (dagar)** för att ange antalet dagar innan användaren måste godkänna villkoren för användning. Detta gör att användarna kan följa sitt eget schema. Om du till exempel ställer in varaktigheten på **30** dagar så är det hur förfallo datum kan uppstå för två användare:

   | User | Första godkännande datum | Första utgångs datum | Förfallo datum för andra | Datum för tredje utgångs datum |
   | --- | --- | --- | --- | --- |
   | Anna | 1 jan | 31 januari | Mar 2 | Apr 1 |
   | Bob | 15 jan | Feb 14 | Mar 16 | April 15 |

   Det går att använda **förfallna godkännanden** och **varaktighet innan omgodkännandet kräver (dagar)** inställningar tillsammans, men vanligt vis använder du en eller ett annat.

1. Under **villkorlig åtkomst** använder du listan **tillämpa med princip mal len för villkorlig åtkomst** för att välja den mall som ska användas för användnings villkoren.

    ![Listruta för villkorlig åtkomst för att välja en principmall](./media/terms-of-use/conditional-access-templates.png)

   | Mall | Beskrivning |
   | --- | --- |
   | **Åtkomst till molnappar för alla gäster** | En princip för villkorlig åtkomst skapas för alla gäster och molnappar. Den här principen påverkar Azure Portal. När detta har skapats kan du behöva logga ut och logga in. |
   | **Åtkomst till molnappar för alla användare** | En princip för villkorlig åtkomst skapas för alla användare och alla molnappar. Den här principen påverkar Azure Portal. När det har skapats måste du logga ut och logga in. |
   | **Anpassad princip** | Välj de användare, grupper och appar som den här användnings principen ska tillämpas på. |
   | **Skapa en princip för villkorlig åtkomst senare** | Den här principen för användning visas i listan bevilja kontroll när du skapar en princip för villkorlig åtkomst. |

   >[!IMPORTANT]
   >Princip kontroller för villkorlig åtkomst (inklusive villkor för användnings principer) stöder inte tillämpning av tjänst konton. Vi rekommenderar att du undantar alla tjänst konton från principen för villkorlig åtkomst.

    Anpassade principer för villkorlig åtkomst möjliggör detaljerade villkor för användnings principer, ned till ett bestämt moln program eller en grupp med användare. Mer information finns i [snabb start: Kräv användnings villkor som ska accepteras innan du får åtkomst till molnappar](require-tou.md).

1. Klicka på **Skapa**.

    Om du har valt en anpassad mall för villkorlig åtkomst visas en ny skärm där du kan skapa en anpassad princip för villkorlig åtkomst.

   ![Nytt fönster för villkorlig åtkomst om du har valt mallen anpassad princip för villkorlig åtkomst](./media/terms-of-use/custom-policy.png)

   Nu bör du se dina nya användnings villkor.

   ![Nya användnings villkor visas i bladet användnings villkor](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visa en rapport över vem som har accepterat och avböjt

Bladet för användningsvillkor visar antalet användare som har godkänt och avböjt. De här räknarna och vilka som har accepterat/avböjt lagras i användnings villkorets livs längd.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).

    ![Användningsvillkor bladet visar antalet användare som har accepterat och avböjt](./media/terms-of-use/view-tou.png)

1. För en princip för användnings villkor klickar du på numren under **accepterad** eller **nekad** för att visa det aktuella läget för användare.

    ![Rutan Användningsvillkor-fönster visar en lista över användare som har accepterat](./media/terms-of-use/accepted-tou.png)

1. Om du vill visa historiken för en enskild användare klickar du på ellipsen (**...**) och sedan på **Visa historik**.

    ![Visa snabb meny för historik för en användare](./media/terms-of-use/view-history-menu.png)

   I fönstret Visa historik visas en historik över alla godkännanden, avböjer och förfallo datum.

   ![Visa historik fönstret listar historiken godkänner, avböjer och upphör Ande för en användare](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visa Azure AD-gransknings loggar

Om du vill visa ytterligare aktiviteter innehåller principer för användnings villkor för Azure AD gransknings loggar. Varje användar medgivande utlöser en händelse i gransknings loggarna som lagras i **30 dagar**. Du kan visa dessa loggar i portalen eller hämta dem som en CSV-fil.

Använd följande procedur för att komma igång med Azure AD audit-loggar:

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj en princip för användnings villkor.
1. Klicka på **Visa granskningsloggar**.

    ![Användningsvillkor blad med alternativet Visa gransknings loggar markerat](./media/terms-of-use/audit-tou.png)

1. På skärmen för gransknings loggar i Azure AD kan du filtrera informationen med hjälp av de angivna listorna för att fokusera på specifika Gransknings logg information.

    Du kan också klicka på **Hämta** för att ladda ned informationen i en CSV-fil och använda den lokalt.

   ![Skärm listans datum, mål princip, initierad av och aktivitet i gransknings loggar i Azure AD](./media/terms-of-use/audit-logs-tou.png)

   Om du klickar på en logg visas ett fönster med ytterligare aktivitets information.

   ![Aktivitets information för en logg med aktivitet, aktivitets status, initierad av, mål princip](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Vilka användnings villkor ser ut för användarna

När en ToU-princip har skapats och tillämpats, kommer användare, som finns inom området, att se följande skärm under inloggningen.

![Exempel användnings villkor som visas när en användare loggar in](./media/terms-of-use/user-tou.png)

Användare kan visa användnings villkoren och, om det behövs, använda knappar för att zooma in och ut.

![Vy över användnings villkor med zoomnings knappar](./media/terms-of-use/zoom-buttons.png)

Följande skärm bild visar hur en ToU-princip ser ut på mobila enheter.

![Exempel användnings villkor som visas när en användare loggar in på en mobil enhet](./media/terms-of-use/mobile-tou.png)

Användarna behöver bara godkänna användnings villkoren en gång och de kommer inte att se användnings villkoren igen på efterföljande inloggningar.

### <a name="how-users-can-review-their-terms-of-use"></a>Hur användarna kan granska deras användnings villkor

Användare kan granska och se villkoren för användning som de har accepterat med hjälp av följande procedur.

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Klicka på ditt namn i det övre högra hörnet och välj **profil**.

    ![Webbplatsen för min Apps med användarens fönster öppen](./media/terms-of-use/tou14.png)

1. Klicka på **Granska användningsvillkoren** på din profilsida.

    ![Profil sida för en användare som visar länken gransknings villkor för användning](./media/terms-of-use/tou13a.png)

1. Därifrån kan du granska användnings villkoren som du har accepterat.

## <a name="edit-terms-of-use-details"></a>Redigera användnings villkors information

Du kan redigera viss information om användnings principer, men du kan inte ändra ett befintligt dokument. I följande procedur beskrivs hur du redigerar informationen.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj de användnings villkor som du vill redigera.
1. Klicka på **Redigera villkor**.
1. I rutan Redigera användnings villkor kan du ändra följande:
    - **Namn** – det här är det interna namnet på ToU som inte delas med slutanvändare
    - **Visnings namn** – det här är det namn som slutanvändarna kan se när de visar ToU
    - **Kräv att användarna expanderar** användnings villkoren – om du anger detta till **på** , tvingas slutanvändaren att expandera villkoren för användnings principer innan det accepteras.
    - Förhandsgranskningsvyn Du kan **Uppdatera ett befintligt dokument med användnings villkor**
    - Du kan lägga till ett språk i en befintlig ToU

   Om det finns andra inställningar som du vill ändra, till exempel PDF-dokument, kräver att användare godkänner varje enhet, upphör att gälla, varaktighet före godkännande eller princip för villkorlig åtkomst måste du skapa en ny ToU-princip.

    ![Redigera visar olika språk alternativ ](./media/terms-of-use/edit-terms-use.png)

1. När du är färdig klickar du på **Spara** för att spara ändringarna.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Uppdatera versionen eller PDF-filen för befintliga användnings villkor

1.  Logga in på Azure och navigera till [användningsvillkor](https://aka.ms/catou)
2.  Välj de användnings villkor som du vill redigera.
3.  Klicka på **Redigera villkor**.
4.  För det språk som du vill uppdatera en ny version klickar du på **Uppdatera** under kolumnen åtgärd

    ![Redigera användnings villkors fönstret som visar namn och visnings alternativ](./media/terms-of-use/edit-terms-use.png)

5.  I rutan till höger laddar du upp PDF-filen för den nya versionen
6.  Det finns också ett växlings alternativ här kräver att du **godkänner** igen om du vill kräva att användarna accepterar den nya versionen nästa gången de loggar in. Om du vill att användarna ska kunna godkänna igen kommer de att uppmanas att godkänna den nya versionen nästa gång de försöker komma åt resursen som definierats i principen för villkorlig åtkomst. Om du inte vill att användarna ska godkänna igen förblir deras tidigare medgivande aktuella och endast nya användare som inte har meddelats innan eller vars medgivande upphör att gälla kommer att se den nya versionen.

    ![Redigera användnings villkoren för att acceptera igen markeras](./media/terms-of-use/re-accept.png)

7.  När du har laddat upp den nya PDF-filen och valt att godkänna, klickar du på Lägg till längst ned i fönstret.
8.  Du ser nu den senaste versionen under kolumnen Document.

## <a name="view-previous-versions-of-a-tou"></a>Visa tidigare versioner av en ToU

1.  Logga in på Azure och gå till **Användningsvillkoren** på https://aka.ms/catou.
2.  Välj de användnings villkor som du vill visa en versions historik för.
3.  Klicka på **språk och versions historik**
4.  Klicka på **Visa tidigare versioner.**

    ![dokument information inklusive språk versioner](./media/terms-of-use/document-details.png)

5.  Du kan klicka på namnet på dokumentet för att ladda ned den versionen

## <a name="see-who-has-accepted-each-version"></a>Se vem som har godkänt varje version

1.  Logga in på Azure och gå till **Användningsvillkoren** på https://aka.ms/catou.
2.  Om du vill se vem som för närvarande har godkänt ToU klickar du på siffran under kolumnen **accepterad** för den ToU som du vill använda.
3.  Som standard visar nästa sida det aktuella tillståndet för varje användare som godkänner ToU
4.  Om du vill se de tidigare medgivande händelserna kan du välja **alla** från List rutan **aktuella tillstånd** . Nu kan du se varje användares händelser i information om varje version och vad som hände.
5.  Alternativt kan du välja en annan version i list rutan **version**  för att se vem som har accepterat den aktuella versionen.


## <a name="add-a-tou-language"></a>Lägg till ett ToU-språk

Följande procedur beskriver hur du lägger till ett ToU-språk.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj de användnings villkor som du vill redigera.
1. Klicka på **Redigera villkor**
1. Klicka på **Lägg till språk** längst ned på sidan.
1. I fönstret Lägg till användnings villkors språk laddar du upp din lokaliserade PDF och väljer språket.

    ![Användningsvillkor markerat och visar fliken språk i informations fönstret](./media/terms-of-use/select-language.png)

1. Klicka på **Lägg till språk**.
1. Klicka på **Spara**

1. Klicka på **Lägg till** för att lägga till språket.

## <a name="per-device-terms-of-use"></a>Användnings villkor per enhet

Inställningen **Kräv att användare samtycker till varje enhets** inställning gör att du kan kräva att slutanvändarna godkänner dina användnings villkor på varje enhet som de ansluter till från. Slutanvändaren kommer att behöva registrera sin enhet i Azure AD. När enheten har registrerats används enhets-ID: t för att genomdriva villkoren för användnings principen på varje enhet.

Här är en lista över plattformar och program som stöds.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Övrigt |
> | --- | --- | --- | --- | --- |
> | **Inbyggd app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (med tillägget)** | Ja | Ja | Ja |  |

Användnings villkoren per enhet har följande begränsningar:

- En enhet kan endast anslutas till en klient.
- En användare måste ha behörighet att ansluta sina enheter.
- Appen Intune-registrering stöds inte. Se till att den utesluts från en princip för villkorlig åtkomst som kräver princip för användnings villkor.
- Azure AD B2B-användare stöds inte.

Om användarens enhet inte är ansluten får de ett meddelande som de behöver för att ansluta till sin enhet. Deras upplevelse kommer att vara beroende av plattform och program vara.

### <a name="join-a-windows-10-device"></a>Anslut en Windows 10-enhet

Om en användare använder Windows 10 och Microsoft Edge får de ett meddelande som liknar följande för att [ansluta sina enheter](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 och Microsoft Edge – meddelande som anger att enheten måste registreras](./media/terms-of-use/per-device-win10-edge.png)

Om de använder Chrome uppmanas de att installera [tillägg för Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrera en iOS-enhet

Om en användare använder en iOS-enhet uppmanas de att installera [Microsoft Authenticator-appen](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registrera en Android-enhet

Om en användare använder en Android-enhet uppmanas de att installera [Microsoft Authenticator-appen](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Webbläsare

Om en användare använder en webbläsare som inte stöds uppmanas han eller hon att använda en annan webbläsare.

![Meddelande som anger att enheten måste vara registrerad, men webbläsaren stöds inte](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Ta bort användnings villkor

Du kan ta bort gamla villkor för användning med hjälp av följande procedur.

1. Logga in på Azure och gå till **Användningsvillkoren** på [https://aka.ms/catou](https://aka.ms/catou).
1. Välj de användnings villkor som du vill ta bort.
1. Klicka på **Ta bort villkor**.
1. På meddelandet som visas som frågar om du vill fortsätta klickar du **Ja**.

    ![Meddelande som ber om bekräftelse för att ta bort användnings villkor](./media/terms-of-use/delete-tou.png)

   Du bör inte längre se dina användnings principer.

## <a name="user-acceptance-record-deletion"></a>Borttagning av användar godkännande post

Poster för användar godkännande tas bort:

- När administratören tar bort ToU explicit. När detta händer tas alla godkännande poster som är kopplade till den aktuella ToU också bort.
- När klienten förlorar sin Azure Active Directory Premium-licens.
- När klienten tas bort.

## <a name="policy-changes"></a>Principändringar

Principer för villkorlig åtkomst börjar gälla omedelbart. När detta inträffar kommer administratören att börja se "Sad-moln" eller "Azure AD-token-problem". Administratören måste logga ut och logga in igen för att kunna uppfylla den nya principen.

> [!IMPORTANT]
> Användare inom området behöver logga ut och logga in för att uppfylla en ny princip om:
>
> - en princip för villkorlig åtkomst är aktive rad enligt en princip för användnings villkor
> - eller så skapas ett andra villkor för användnings principen

## <a name="b2b-guests"></a>B2B-gäster

De flesta organisationer har en process på plats för sina anställda att godkänna sin organisations användnings villkor och sekretess policy. Men hur kan du tvinga samma medgivanden för Azure AD Business-to-Business (B2B)-gäster när de läggs till via SharePoint eller team? Med villkorlig åtkomst och villkor för användnings principer kan du tillämpa en princip direkt på B2B-gäst användare. Under inbjudan till inlösen av inbjudan visas användaren villkoren för användning. Detta stöd är för närvarande en för hands version.

Användningsvillkor principer visas endast när användaren har ett gäst konto i Azure AD. SharePoint Online har för närvarande en [ad hoc extern delnings funktion](/sharepoint/what-s-new-in-sharing-in-targeted-release) för att dela ett dokument eller en mapp som inte kräver att användaren har ett gäst konto. I det här fallet visas inte villkor för användnings principen.

![Fönstret användare och grupper – fliken inkludera med alternativet alla gäst användare har marker ATS](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Stöd för molnappar

Användningsvillkor principer kan användas för olika molnappar, till exempel Azure Information Protection och Microsoft Intune. Detta stöd är för närvarande en för hands version.

### <a name="azure-information-protection"></a>Azure Information Protection

Du kan konfigurera en princip för villkorlig åtkomst för Azure Information Protection app och kräva en princip för användnings villkor när en användare kommer åt ett skyddat dokument. Detta utlöser en princip för användnings villkor innan en användare som har åtkomst till ett skyddat dokument för första gången.

![Fönstret Cloud Apps med Microsoft Azure Information Protection app valt](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune registrering

Du kan konfigurera en princip för villkorlig åtkomst för appen för Microsoft Intune registrering och kräva en princip för användnings villkor innan du registrerar en enhet i Intune. Mer information finns i avsnittet om [att välja rätt villkor för din organisations blogg inlägg](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Fönstret Cloud Apps med Microsoft Intune App valt](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Appen Intune-registrering stöds inte för [användnings villkor per enhet](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Jag kan inte logga in med PowerShell när användnings villkoren är aktiverade.**<br />
A: Användningsvillkor kan bara godkännas vid autentisering interaktivt.

**F: Hur ser jag om/när en användare har godkänt användningsvillkoren?**<br />
A: Klicka på det antal som **godkänns** på bladet användningsvillkor. Du kan också visa eller söka i accept-aktiviteten i gransknings loggarna för Azure AD. Mer information finns i Visa en rapport över vem som har accepterat och avböjt och [Visa Azure AD-gransknings loggar](#view-azure-ad-audit-logs).

**F: Hur länge lagras informationen?**<br />
A: användaren räknar med användnings villkoren och vem som accepterade/avböjde lagras under användnings villkoren. Gransknings loggarna i Azure AD lagras i 30 dagar.

**F: Varför ser jag ett annat antal medgivanden i användnings villkoren för Azure AD i gransknings loggarna?**<br />
S: rapport användnings villkoren lagras under den livs längd som används av villkoren, medan Azure AD audit-loggarna lagras i 30 dagar. Användnings villkors rapporten visar också endast användarens aktuella tillstånds tillstånd. Om en användare t. ex. avböjer och sedan accepterar, visar användnings villkoren endast den användaren accepterar. Om du behöver se historiken kan du använda gransknings loggarna i Azure AD.

**F: om jag redigerar informationen för en princip för användnings villkor måste användarna godkänna det igen?**<br />
A: Nej, om en administratör redigerar informationen för en princip för användnings villkor (namn, visnings namn, Kräv att användare expanderar eller lägger till ett språk) behöver inte användare acceptera de nya villkoren igen.

**F: kan jag uppdatera ett befintligt användnings villkors dokument?**<br />
A: för närvarande kan du inte uppdatera en befintlig användnings princip dokument. Om du vill ändra ett princip dokument för användnings villkor måste du skapa en ny användnings princip instans.

**F: om hyperlänkarna finns i användnings principen som PDF-dokument, kommer användarna att kunna Klicka på dem?**<br />
A: Ja, slutanvändarna kan välja hyperlänkar till ytterligare sidor, men länkar till avsnitt i dokumentet stöds inte. Dessutom fungerar inte hyperlänkar i villkor för användnings principer-PDF-filer vid åtkomst från portalen för Azure AD-appar/-konto.

**F: kan en princip för användnings villkor ha stöd för flera språk?**<br />
S: Ja. För närvarande finns det 108 olika språk som en administratör kan konfigurera för en enskild princip för användnings villkor. En administratör kan ladda upp flera PDF-dokument och tagga dessa dokument med ett motsvarande språk (upp till 108). När slutanvändarna loggar in tittar vi på deras inställningar för webbläsarens språk och visar det matchande dokumentet. Om det inte finns någon matchning, visas standard dokumentet, vilket är det första dokument som överförs.

**F: När är användnings principen aktive rad?**<br />
A: användnings villkoren utlöses under inloggnings upplevelsen.

**F: vilka program kan jag rikta in sig på en princip för användnings villkor?**<br />
A: du kan skapa en princip för villkorlig åtkomst i företags program med modern autentisering. Mer information finns i [enterprise applications](./../manage-apps/view-applications-portal.md) (företagsprogram).

**F: kan jag lägga till flera användnings villkor för en specifik användare eller app?**<br />
A: Ja, genom att skapa flera principer för villkorlig åtkomst som riktar sig mot dessa grupper eller program. Om en användare befinner sig i omfånget för flera användnings villkor, godkänner de en princip för användning i taget.

**F: Vad händer om en användare avböjer villkoren i användnings principen?**<br />
S: Användare blockeras från åtkomst till programmet. Användaren måste logga in igen och godkänna villkoren för att få åtkomst.

**F: är det möjligt att ta emot en princip för användnings villkor som tidigare har accepterats?**<br />
A: du kan [Granska tidigare godkända användnings villkor](#how-users-can-review-their-terms-of-use), men för närvarande finns det inget sätt att ta emot.

**F: Vad händer om jag också använder Intune-villkor?**<br />
A: om du har konfigurerat både användnings villkoren för Azure AD och användar villkoren för [Intune](/intune/terms-and-conditions-create)måste användaren godkänna båda. Mer information finns i avsnittet [välja rätt lösning för din organisations blogg inlägg](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**F: vilka slut punkter används vid användnings användnings tjänsten för autentisering?**<br />
A: Användningsvillkor använder följande slut punkter för autentisering: https://tokenprovider.termsofuse.identitygovernance.azure.com och https://account.activedirectory.windowsazure.com . Om din organisation har en lista över tillåtna URL: er för registrering måste du lägga till dessa slut punkter i listan över tillåtna platser, tillsammans med Azure AD-slutpunkter för inloggning.

## <a name="next-steps"></a>Nästa steg

- [Snabb start: Kräv användnings villkor för att godkännas innan du får åtkomst till molnappar](require-tou.md)
