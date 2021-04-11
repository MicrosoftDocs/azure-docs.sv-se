---
title: 'Självstudiekurs: Konfigurera HTTPS på en anpassad Azure CDN-domän'
description: I den här kursen får du lära dig hur du aktiverar och inaktiverar HTTPS på en anpassad domän på en Azure CDN-slutpunkt.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 6f77bac93b7bb5e3319409c01e328c73cd08a9a0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058960"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Självstudiekurs: Konfigurera HTTPS på en anpassad Azure CDN-domän

Den här självstudien visar hur du aktiverar HTTPS-protokollet för en anpassad domän som är associerad med en Azure CDN-slutpunkt. 

HTTPS-protokollet på din anpassade domän (till exempel https: \/ /www.contoso.com) garanterar att känsliga data levereras säkert via TLS/SSL. När webbläsaren är ansluten via HTTPS, validerar webbläsaren webbplatsens certifikat. Webbläsaren verifierar att den utfärdats av en legitim certifikat utfärdare. Den här processen ger trygghet och skyddar dina webbprogram mot attacker.

Azure CDN stöder HTTPS i CDN-slutpunktens värdnamn som standard. Om du skapar en CDN-slutpunkt som till exempel https:\//contoso.azureedge.net aktiveras HTTPS automatiskt.  

Några viktiga attribut i den anpassade HTTPS-funktionen är:

- Ingen extra kostnad: det kostar inget att köpa eller förnya certifikat och ingen extra kostnad för HTTPS-trafik. Du betalar bara för utgående trafik (GB) från CDN.

- Enkel aktivering: enklicksetablering är tillgänglig från [Azure Portal](https://portal.azure.com). Du kan också aktivera funktionen med REST API eller andra utvecklingsverktyg.

- Fullständig certifikathantering är tillgänglig: 
    * All anskaffning och hantering av certifikat hanteras åt dig. 
    * Certifikat tillhandahålls och förnyas automatiskt innan det upphör att gälla.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på din anpassade domän.
> - Använda ett CDN-hanterat certifikat 
> - Använda ditt eget certifikat
> - Verifiera domänen
> - Inaktivera HTTPS-protokollet på din anpassade domän.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Innan du kan slutföra stegen i den här självstudien skapar du en CDN-profil och minst en CDN-slutpunkt. Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).

Associera en Azure CDN anpassad domän på CDN-slutpunkten. Mer information finns i [Självstudier: Lägg till en anpassad domän i Azure CDN slut punkten](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> CDN-hanterade certifikat är inte tillgängliga för rot-eller Apex-domäner. Om din Azure CDN anpassade domän är en rot-eller Apex-domän måste du använda funktionen ta med din egen certifikat. 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL-certifikat

Om du vill aktivera HTTPS på en Azure CDN anpassad domän använder du ett TLS/SSL-certifikat. Du väljer att använda ett certifikat som hanteras av Azure CDN eller använder ditt certifikat.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Alternativ 1 (standard): Aktivera HTTPS med ett CDN-hanterat certifikat](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN hanterar certifikat hanterings uppgifter som anskaffning och förnyelse. När du har aktiverat funktionen startar processen omedelbart. 

Om den anpassade domänen redan har mappats till CDN-slutpunkten krävs ingen ytterligare åtgärd. Azure CDN behandlar stegen och slutför din begäran automatiskt.

Om din anpassade domän mappas någon annan stans använder du e-post för att verifiera din domän ägarskap.

Följ dessa steg om du vill aktivera HTTPS på en anpassad domän:

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta ett certifikat som hanteras av din Azure CDN. Sök efter och välj **CDN-profiler**. 

2. Välj din profil:
    * **Azure CDN Standard från Microsoft**
    * **Azure CDN Standard från Akamai**
    * **Azure CDN Standard från Verizon**
    * **Azure CDN Premium från Verizon**

3. I listan med CDN-slutpunkter väljer du slutpunkten som innehåller din anpassade domän.

    ![Lista med slutpunkter](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Sidan **Slutpunkt** visas.

4. I listan med anpassade domäner väljer du den anpassade domänen som du vill aktivera HTTPS för.

    ![Skärm bild som visar sidan anpassad domän med alternativet att använda ett eget certifikat.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Sidan **Anpassad domän** visas.

5. Under certifikathanteringstyp väljer du **CDN-hanterat**.

6. Välj **På** för att aktivera HTTPS.

    ![HTTPS-status för anpassad domän](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Fortsätt att [verifiera domänen](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Alternativ 2: Aktivera HTTPS med ditt eget certifikat](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Det här alternativet är endast tillgängligt med **Azure CDN från Microsoft** och **Azure CDN från Verizon** -profiler. 
>
 
Du kan använda ditt eget certifikat för att aktivera HTTPS. Detta görs via en integrering med Azure Key Vault där du kan lagra certifikaten säkert. Azure CDN använder den här säkra mekanismen för att hämta ditt certifikat och det krävs några extra steg. När du skapar ditt TLS/SSL-certifikat måste du skapa det med en tillåten certifikat utfärdare (CA). Om du använder en icke-tillåten certifikatutfärdare kan din begäran avvisas. En lista över tillåtna certifikatutfärdare finns i [Allowed certificate authorities for enabling custom HTTPS on Azure CDN](cdn-troubleshoot-allowed-ca.md) (Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure CDN). För **Azure CDN från Verizon** godkänns alla giltiga ca: er. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Förbered ditt Azure Key Vault-konto och certifikat
 
1. Azure Key Vault: Du måste ha ett aktivt Azure Key Vault-konto under samma prenumeration som Azure CDN-profilen och CDN-slutpunkterna där du vill aktivera anpassad HTTPS. Skapa ett Azure Key Vault-konto om du inte redan har ett.
 
2. Azure Key Vault certifikat: om du har ett certifikat laddar du upp det direkt till ditt Azure Key Vault-konto. Om du inte har ett certifikat kan du skapa ett nytt certifikat direkt via Azure Key Vault.

### <a name="register-azure-cdn"></a>Registrera Azure CDN

Registrera Azure CDN som en app i Azure Active Directory via PowerShell.

1. Om det behövs installerar du [Azure PowerShell](/powershell/azure/install-az-ps) på den lokala datorn.

2. Kör följande kommando i PowerShell:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** är tjänstens huvud namn för **Microsoft. AzureFrontDoor-CDN**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Ge Azure CDN åtkomst till ditt nyckelvalv
 
Ge Azure CDN behörighet att komma åt certifikaten (hemligheterna) på ditt Azure Key Vault-konto.

1. I ditt nyckel valv i avsnittet **Inställningar** väljer du **åtkomst principer**. I den högra rutan väljer du **+ Lägg till åtkomst princip**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Skapa åtkomst princip för nyckel valv för CDN" border="true":::

2. På sidan **Lägg till åtkomst princip** väljer du **ingen markerad** bredvid **Välj huvud konto**. På sidan **huvud namn** anger du **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**. Välj **Microsoft. AzureFrontdoor-CDN**.  Välj **Välj**:

2. I **Välj huvud namn**, Sök efter **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, väljer du **Microsoft. AzureFrontDoor-CDN**. Välj **Välj**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Välj tjänstens huvud namn för Azure CDN" border="true":::
    
3. Välj **certifikat behörigheter**. Markera kryss rutorna för **Hämta** och **lista** för att tillåta CDN-behörigheter att hämta och lista certifikaten.

4. Välj **hemliga behörigheter**. Markera kryss rutorna för **Hämta** och **lista** för att tillåta CDN-behörigheter att hämta och lista hemligheterna:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Välj behörigheter för CDN till nyckel valv" border="true":::

5. Välj **Lägg till**. 

> [!NOTE]
> Azure CDN har nu åtkomst till nyckelvalvet och certifikaten (hemligheterna) som lagras i nyckelvalvet. Alla CDN-instanser som skapats i den här prenumerationen kommer att ha åtkomst till certifikaten i det här nyckel valvet. 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Välj det certifikat som ska distribueras av Azure CDN
 
1. Gå tillbaka till Azure CDN-portalen och välj den profil och CDN-slutpunkt som du vill aktivera anpassad HTTPS för. 

2. I listan med anpassade domäner väljer du den anpassade domänen som du vill aktivera HTTPS för.

    Sidan **Anpassad domän** visas.

3. Under Certifikathanteringstyp väljer du **Använd mitt eget certifikat**. 

    :::image type="content" source="./media/cdn-custom-ssl/cdn-configure-your-certificate.png" alt-text="Skärm bild av hur du konfigurerar certifikat för CDN-slutpunkten.":::

4. Välj ett nyckel valv, certifikat/hemlighet och certifikat/hemlig version.

    Azure CDN visar följande information: 
    - Nyckelvalvskonton för ditt prenumerations-ID. 
    - Certifikaten/hemligheterna under det valda nyckel valvet. 
    - Tillgängliga Certificate/Secret-versioner.
 
    > [!NOTE]
    > Om du vill att certifikatet automatiskt ska roteras till den senaste versionen när en nyare version av certifikatet är tillgängligt i Key Vault, ställer du in certifikat/hemlig version till "senaste". Om du väljer en viss version måste du välja den nya versionen manuellt för certifikat rotationen. Det tar upp till 24 timmar innan den nya versionen av certifikatet/hemligheten kan distribueras. 
   
5. Välj **På** för att aktivera HTTPS.
  
6. När du använder ditt certifikat krävs inte domän validering. Fortsätt att [vänta på spridning](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Verifiera domänen

Om du har en anpassad domän som används mappad till din anpassade slut punkt med en CNAME-post eller om du använder ditt eget certifikat, fortsätter du till [den anpassade domänen som är mappad till CDN-slutpunkten](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

Annars, om CNAME-postposten för slut punkten inte längre finns eller om den innehåller cdnverify-underdomänen, fortsätter du till den [anpassade domänen som inte är mappad till CDN-slutpunkten](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Den anpassade domänen har mappats till CDN-slutpunkten med en CNAME-post

När du har lagt till en anpassad domän i slut punkten skapade du en CNAME-post i DNS-domän registratorn mappad till CDN-slutpunktens värdnamn. 

Om CNAME-posten fortfarande finns och inte innehåller under domänen cdnverify, använder DigiCert CA den för att automatiskt validera ägarskapet för din anpassade domän. 

Om du använder ditt eget certifikat krävs inte domän validering.

CNAME-posten ska ha följande format:

* *Namnet* är ditt anpassade domän namn.
* *Värdet* är CDN-slutpunktens värdnamn.

| Namn            | Typ  | Värde                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Mer information om CNAME-poster finns i [Skapa CNAME DNS-posten](./cdn-map-content-to-custom-domain.md).

Om din CNAME-post har rätt format, verifierar DigiCert automatiskt ditt anpassade domän namn och skapar ett certifikat för din domän. DigitCert skickar ingen bekräftelse via e-post och du behöver inte godkänna din begäran. Certifikatet är giltigt i ett år och förnyas automatiskt innan det upphör att gälla. Fortsätt att [vänta på spridning](#wait-for-propagation). 

Det tar vanligt vis några timmar att validera automatiskt. Om du inte ser din domän verifierad inom 24 timmar öppnar du ett support ärende.

>[!NOTE]
>Om du har en CAA-post (Certificate Authority Authorization) hos DNS-providern måste den innehålla DigiCert som giltig certifikatutfärdare. En CAA-post gör att domänägare kan ange med sina DNS-provider vilka certifikatutfärdare som är auktoriserade att utfärda certifikat för deras domän. Om en certifikatutfärdare mottar en order för ett certifikat för en domän som har en CAA-post och den certifikatutfärdaren inte finns med i listan över auktoriserade utfärdare hindras certifikatufärdaren att utfärda certifikatet till den domänen eller underdomänen. Information om hur du hanterar CAA poster finns i [Hantera CAA-poster](https://support.dnsimple.com/articles/manage-caa-record/). Hjälpverktyg för CAA-poster finns i [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Den anpassade domänen har inte mappats till CDN-slutpunkten

>[!NOTE]
>Om du använder **Azure CDN från Akamai** bör följande CNAME konfigureras för att aktivera automatisk domän validering. "_acme-Challenge. &lt; anpassat domän &gt; -värdnamn-> CNAME-> &lt; anpassat domän-värdnamn &gt; . AK-Acme-Challenge.azureedge.net "

Om posten CNAME innehåller cdnverify-underdomänen följer du resten av anvisningarna i det här steget.

DigiCert skickar ett verifierings-e-postmeddelande till följande e-postadresser. Kontrol lera att du kan godkänna direkt från någon av följande adresser:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Du bör få ett e-postmeddelande på några minuter så att du kan godkänna begäran. Om du använder ett skräp post filter ska du lägga till verification@digicert.com det i dess tillåten. Kontakta Microsoft-supporten om du inte får ett e-postmeddelande inom 24 timmar.
    
![Domänverifieringsmeddelande](./media/cdn-custom-ssl/domain-validation-email.png)

När du väljer länken godkännande är du dirigerad till följande formulär för online-godkännande: 
    
![Domänverifieringsformulär](./media/cdn-custom-ssl/domain-validation-form.png)

Följ instruktionerna i formuläret. Du har två verifieringsalternativ:

- Du kan godkänna alla framtida order som placerats via samma konto för samma rotdomän, till exempel contoso.com. Den här metoden rekommenderas om du planerar att lägga till andra anpassade domäner för samma rot domän.

- Du kan bara godkänna det specifika värdnamn som används i den här begäran. Ett annat godkännande krävs för senare förfrågningar.

Efter godkännandet slutför DigiCert skapandet av certifikatet för det anpassade domännamnet. Certifikatet är giltigt i ett år och förnyas automatiskt innan det upphör att gälla.

## <a name="wait-for-propagation"></a>Vänta på spridning

När domännamnet har verifierats kan det ta upp till 6–8 timmar innan HTTPS-funktionen för den anpassade domänen aktiveras. När processen har slutförts ändras den anpassade HTTPS-statusen i Azure Portal till **aktive rad**. De fyra åtgärds stegen i dialog rutan anpassad domän markeras som slutförda. Den anpassade domänen är nu klar att använda HTTPS.

![Dialogrutan Aktivera HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Åtgärdsförlopp

I följande tabell visas åtgärdsförloppet när du aktiverar HTTPS. När du har aktiverat HTTPS visas de fyra åtgärdsstegen i dialogrutan Anpassad domän. När varje steg aktive ras visas ytterligare del stegs information under steget när det fortskrider. Alla understeg utförs inte. När ett steg har slutförts visas en grön bock bredvid steget. 

| Åtgärdssteg | Information om åtgärdsundersteg | 
| --- | --- |
| 1 Skicka begäran | Begäran skickas |
| | Din HTTPS-begäran skickas. |
| | Din HTTPS-begäran har skickats. |
| 2 Domänverifiering | Domänen verifieras automatiskt om den är CNAME-mappad till CDN-slutpunkten. I annat fall skickas en verifieringsbegäran till den e-postadress som står angiven i domänens registreringspost (WHOIS-registrant).|
| | Domänägarskapet har verifierats och godkänts. |
| | Begäran om verifiering av domänägarskap har gått ut (kunden svarade troligtvis inte inom 6 dagar). HTTPS aktive ras inte på din domän. * |
| | Begäran om verifiering av domänägarskap avvisades av kunden. HTTPS aktive ras inte på din domän. * |
| 3 Etablering av certifikat | Certifikatutfärdaren håller på att utfärda det certifikat som krävs för att aktivera HTTPS på din domän. |
| | Certifikatet har utfärdats och håller på att distribueras till CDN-nätverket. Det kan ta upp till 6 timmar. |
| | Certifikatet har distribuerats till CDN-nätverket. |
| 4 Slutfört | HTTPS har aktiverats på din domän. |

\* Det här meddelandet visas bara om ett fel har uppstått. 

Om det uppstår ett fel innan begäran har skickats visas följande felmeddelande:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Rensa resurser – inaktivera HTTPS

I det här avsnittet får du lära dig hur du inaktiverar HTTPS för din anpassade domän.

### <a name="disable-the-https-feature"></a>Inaktivera HTTPS-funktionen 

1. Sök efter och välj **CDN-profiler** i [Azure Portal](https://portal.azure.com). 

2. Välj din **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profilen.

3. I listan över slut punkter väljer du slut punkten som innehåller din anpassade domän.

4. Välj den anpassade domän som du vill inaktivera HTTPS för.

    ![Lista med anpassade domäner](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Välj **inaktivera** om du vill inaktivera https och välj sedan **Använd**.

    ![Dialogrutan Anpassad HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Vänta på spridning

När HTTPS-funktionen för den anpassade domänen har inaktiverats kan det ta upp till 6–8 timmar innan ändringen börjar gälla. När processen är klar ändras den anpassade HTTPS-statusen i Azure Portal till **inaktive rad**. De tre åtgärds stegen i dialog rutan anpassad domän markeras som slutförda. Den anpassade domänen använder inte längre HTTPS.

![Dialogrutan Inaktivera HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Åtgärdsförlopp

I följande tabell visas åtgärdsförloppet när du inaktiverar HTTPS. När du har inaktiverat HTTPS visas tre åtgärds steg i dialog rutan anpassad domän. När ett steg blir aktivt visas information under steget. När ett steg har slutförts visas en grön bock bredvid steget. 

| Åtgärdsförlopp | Åtgärdsinformation | 
| --- | --- |
| 1 Skicka begäran | Begäran skickas |
| 2 Avetablering av certifikat | Tar bort certifikat |
| 3 Slutfört | Certifikatet har tagits bort |

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. *Vem är certifikatleverantör och vilken typ av certifikat används?*

    Ett dedikerat certifikat som tillhandahålls av DigiCert används för din anpassade domän för:
    
    * **Azure CDN från Verizon**
    * **Azure CDN från Microsoft**

2. *Använder du IP-baserad eller SNI-baserad TLS/SSL?*

    Både **Azure CDN från Verizon** och **Azure CDN Standard från Microsoft** använder SNI-baserad TLS/SSL.

3. *Vad händer om jag inte får domänverifieringsmeddelandet från DigiCert?*

    Om du inte använder *cdnverify* -underdomänen och din CNAME-post är för slut punktens värdnamn får du inget e-postdomän verifierings meddelande.
     
    Verifieringen sker i så fall automatiskt. Om du inte har en CNAME-post och inte har fått något e-postmeddelande inom 24 timmar kontaktar du Microsoft-supporten.

4. *Är det mindre säkert att använda ett SAN-certifikat än att använda ett dedikerat certifikat?*
    
    Ett SAN-certifikat följer samma standarder för kryptering och säkerhet som ett dedikerat certifikat. Alla utfärdade TLS/SSL-certifikat använder SHA-256 för utökad Server säkerhet.

5. *Behöver jag en CAA-post (Certificate Authority Authorization) med DNS-leverantören?*

    Verifierings posten för certifikat utfärdare krävs inte för närvarande. Men om du har en sådan måste den innehålla DigiCert som en giltig certifikatutfärdare.

6. *Den 20 juni 2018 var Azure CDN från Verizon som började använda ett dedikerat certifikat med SNI TLS/SSL som standard. Vad händer med mina befintliga anpassade domäner med hjälp av certifikat för alternativa namn (SAN) och IP-baserad TLS/SSL?*

    Dina befintliga domäner kommer gradvis att migreras till ett enskilt certifikat under de kommande månaderna om Microsoft i sina analyser ser att det bara görs SNI-klientförfrågningar till ditt program. 
    
    Om icke-SNI klienter identifieras stannar dina domäner i SAN-certifikatet med IP-baserad TLS/SSL. Begär anden till tjänsten eller klienter som inte är SNI påverkas inte.

7. *Hur fungerar certifikat förnyelser med ditt eget certifikat?*

    Om du vill se till att ett nyare certifikat distribueras till PoP-infrastrukturen laddar du upp det nya certifikatet till Azure-nyckel valvet. I TLS-inställningarna på Azure CDN väljer du den senaste certifikat versionen och väljer Spara. Azure CDN kommer sedan att sprida ditt nya uppdaterade certifikat. 

8. *Måste jag återaktivera HTTPS när slut punkten har startats om?*

    Ja. Om du använder **Azure CDN från Akamai**, om slut punkten stoppas och startas om, måste du Aktivera HTTPS-inställningen igen om inställningen var aktiv tidigare.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på din anpassade domän.
> - Använda ett CDN-hanterat certifikat 
> - Använda ditt eget certifikat
> - Verifiera domänen.
> - Inaktivera HTTPS-protokollet på din anpassade domän.

Gå vidare till nästa kurs om du vill lära dig hur du konfigurerar cachelagring på en CDN-slutpunkt.

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera Azures CDN-cachelagringsregler](cdn-caching-rules-tutorial.md)
