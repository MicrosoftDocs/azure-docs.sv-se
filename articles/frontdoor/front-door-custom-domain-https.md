---
title: Självstudie – Konfigurera HTTPS på en anpassad domän för Azure Front Door | Microsoft Docs
description: I den här självstudien lär du dig att aktivera och inaktivera HTTPS Azure Front Door konfiguration för en anpassad domän.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.openlocfilehash: 4291a7d46c723f799cf9d09ca0e7a3f6d614971f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389748"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Självstudiekurs: Konfigurera HTTPS på en anpassad Front Door-domän

Den här självstudien visar hur du aktiverar HTTPS-protokollet för en anpassad domän som är associerad med din Front Door i avsnittet om klientdelsvärdar. Genom att använda HTTPS-protokollet på din anpassade domän (till exempel https: /www.contoso.com) ser du till att känsliga data levereras säkert via TLS/SSL-kryptering när de skickas \/ via Internet. När webbläsaren är ansluten till en webbplats via HTTPS valideras webbplatsens säkerhetscertifikat och verifierar att det är utfärdat av en giltig certifikatutfärdare. Den här processen ger trygghet och skyddar dina webbprogram mot attacker.

Azure Front Door har stöd för HTTPS Front Door standardvärdnamn som standard. Om du till exempel skapar en Front Door (till exempel `https://contoso.azurefd.net` ) aktiveras HTTPS automatiskt för begäranden som görs till `https://contoso.azurefd.net` . När du har publicera den anpassade domänen "www.contoso.com" måste du dock även aktivera HTTPS för den här frontend-värden.   

Några viktiga attribut i den anpassade HTTPS-funktionen är:

- Ingen extra kostnad: Det finns inga kostnader för certifikatförvärv eller certifikatförnyelse och ingen extra kostnad för HTTPS-trafik. 

- Enkel aktivering: enklicksetablering är tillgänglig från [Azure Portal](https://portal.azure.com). Du kan också aktivera funktionen med REST API eller andra utvecklingsverktyg.

- Komplett certifikathantering finns tillgänglig: All anskaffning och hantering av certifikat hanteras åt dig. Certifikat etableras automatiskt och förnyas innan de upphör att gälla, vilket tar bort risken för avbrott i tjänsten på grund av att ett certifikat upphör att gälla.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Aktivera HTTPS-protokollet på din anpassade domän.
> - Använda ett AFD-hanterat certifikat 
> - Använd ditt eget certifikat, det vill säga ett anpassat TLS/SSL-certifikat
> - Verifiera domänen
> - Inaktivera HTTPS-protokollet på en anpassad domän


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du kan slutföra stegen i den här kursen måste du först skapa en Front Door och med minst en anpassad domän publicerad. Mer information finns i [Självstudiekurs: Lägga till en anpassad domän i din Front Door](front-door-custom-domain.md).

## <a name="tlsssl-certificates"></a>TLS/SSL-certifikat

Om du vill aktivera HTTPS-protokollet för säker leverans av innehåll Front Door en anpassad domän måste du använda ett TLS/SSL-certifikat. Du kan välja att använda ett certifikat som hanteras av Azure Front Door eller använda ett eget certifikat.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Alternativ 1 (standard): Använd ett certifikat som hanteras av Front Door

När du använder ett certifikat som Azure Front Door av en server kan DU aktivera HTTPS-funktionen med bara några klick. Azure Front Door hanterar certifikathanteringsuppgifter helt, till exempel anskaffning och förnyelse. När du har aktiverat funktionen startar processen omedelbart. Om den anpassade domänen redan har mappats till klientdelsstandardvärden för Front Door (`{hostname}.azurefd.net`) krävs ingen ytterligare åtgärd. Front Door behandlar stegen och slutför din begäran automatiskt. Men om din anpassade domän mappas någon annanstans, måste du använda e-post för att verifiera att du äger domänen.

Följ dessa steg om du vill aktivera HTTPS på en anpassad domän:

1. I [Azure-portalen](https://portal.azure.com) bläddrar du till din **Front Door**-profil.

2. I listan över klientdelsvärdar väljer du den anpassade domän som du vill aktivera HTTPS för som innehåller din anpassade domän.

3. Under avsnittet HTTPS **för anpassad** domän väljer **du Aktiverad** och väljer **Front Door hanterad** som certifikatkälla.

4. Välj Spara

5. Fortsätt till [Verifiera domänen](#validate-the-domain).

> [!NOTE]
> * För AFD-hanterade certifikat tillämpas DigiCerts gräns på 64 tecken. Valideringen misslyckas om den gränsen överskrids.
> * Aktivering av HTTPS via Front Door-certifikat stöds inte för toppdomäner/rotdomäner (exempel: contoso.com). Du kan använda ett eget certifikat för det här scenariot.  Fortsätt med alternativ 2 för ytterligare information.

### <a name="option-2-use-your-own-certificate"></a>Alternativ 2: Använda ditt eget certifikat

Du kan använda ditt eget certifikat för att aktivera HTTPS. Detta görs via en integrering med Azure Key Vault där du kan lagra certifikaten säkert. Azure Front Door använder den här säkra mekanismen för att hämta certifikatet, och det krävs några extra steg. När du skapar TLS/SSL-certifikatet måste du skapa det med en tillåten certifikatutfärdare (CA). Om du använder en icke-tillåten certifikatutfärdare kan din begäran avvisas. En lista över tillåtna certifikatutfärdare finns i [Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure Front Door](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Förbered ditt Azure Key Vault-konto och certifikat
 
1. Azure Key Vault: Du måste ha ett aktivt Azure Key Vault-konto under samma prenumeration som den Front Door där du vill aktivera anpassad HTTPS. Skapa ett Azure Key Vault-konto om du inte redan har ett.

> [!WARNING]
> Azure Front Door stöder för närvarande endast Key Vault-konton i samma prenumeration som Front Door konfiguration. Om du väljer ett Key Vault under en annan prenumeration än din Front Door inträffar ett fel.

2. Azure Key Vault-certifikat: Om du redan har ett certifikat kan du ladda upp det direkt till ditt Azure Key Vault-konto. Du kan också skapa ett nytt certifikat direkt via Azure Key Vault från en av partnercertifikatutfärdarna som Azure Key Vault integreras med. Ladda upp certifikatet som ett **certifikatobjekt** i stället för en **hemlighet**.

> [!NOTE]
> För ditt eget TLS/SSL-certifikat Front Door inte certifikat med EC-krypteringsalgoritmer.

#### <a name="register-azure-front-door"></a>Registrera Azure Front Door

Registrera tjänstens huvudnamn för Azure Front Door som en app i din Azure Active Directory via PowerShell.

> [!NOTE]
> Den här åtgärden kräver behörigheter som global administratör och behöver bara utföras en **gång** per klientorganisation.

1. Om det behövs installerar du [Azure PowerShell](/powershell/azure/install-az-ps) i PowerShell på den lokala datorn.

2. Kör följande kommando i PowerShell:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Bevilja Azure Front Door åtkomst till ditt nyckelvalv
 
Ge Azure Front Door behörighet att komma åt certifikaten i ditt Azure Key Vault konto.

1. I nyckelvalvskonto under INSTÄLLNINGAR väljer du **Åtkomstprinciper** och sedan **Lägg till nytt** för att skapa en ny princip.

2. I **Välj huvudkonto** söker du efter **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** och väljer **Microsoft.Azure.Frontdoor**. Klicka på **Välj**.

3. I **Hemliga behörigheter** väljer du **Hämta** för att Front Door att hämta certifikatet.

4. I **Certifikatbehörigheter** väljer **du Hämta** för att Front Door att hämta certifikatet.

5. Välj **OK**. 

    Azure Front Door kan nu komma åt Key Vault och de certifikat som lagras i den här Key Vault.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Välj det certifikat som Azure Front Door ska distribuera
 
1. Gå tillbaka till din Front Door i portalen. 

2. I listan med anpassade domäner väljer du den anpassade domänen som du vill aktivera HTTPS för.

    Sidan **Anpassad domän** visas.

3. Under Certifikathanteringstyp väljer du **Använd mitt eget certifikat**. 

4. Azure Front Door kräver att prenumerationen på Key Vault-kontot är samma som för ditt Front Door. Välj ett nyckelvalv, en hemlighet och en hemlig version.

    Azure Front Door visar följande information: 
    - Nyckelvalvskonton för ditt prenumerations-ID. 
    - Hemligheterna under det valda nyckelvalvet. 
    - Tillgängliga hemliga versioner.

    > [!NOTE]
    >  För att certifikatet ska roteras automatiskt till den senaste versionen när en nyare version av certifikatet är tillgänglig i din Key Vault anger du den hemliga versionen till "Senaste". Om du väljer en specifik version måste du välja den nya versionen manuellt för certifikatrotation. Det tar upp till 24 timmar innan den nya versionen av certifikatet/hemligheten distribueras. 
 
5. När du använder ett eget certifikat krävs inte domänverifiering. Fortsätt till [Vänta på spridning.](#wait-for-propagation)

## <a name="validate-the-domain"></a>Verifiera domänen

Om du redan har en anpassad domän som används och mappas till din anpassade slutpunkt med en CNAME-post, eller om du använder ett eget certifikat, fortsätter du till Anpassad domän mappas till [din Front Door](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Om CNAME-posten för din domän inte längre finns eller om den innehåller underdomänen afdverify fortsätter du annars till Anpassad domän mappas inte till [din Front Door](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Den anpassade domänen har mappats till din Front Door med en CNAME-post

När du lade till en anpassad domän till din Front Doors klientdelsvärdar skapade du en CNAME-post i din domänregistrators DNS-tabell för att mappa den till din Front Doors .azurefd.net-standardvärdnamn. Om CNAME-posten fortfarande finns kvar och inte innehåller afdverify-underdomänen använder DigiCert-certifikatutfärdaren den för att automatiskt verifiera ägarskapet för din anpassade domän. 

Om du använder ett eget certifikat krävs inte domänverifiering.

CNAME-posten ska ha följande format, där *Namn* är namnet på ditt anpassade domännamn och *Värde* är din Front Doors .azurefd.net-standardvärdnamn:

| Namn            | Typ  | Värde                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Mer information om CNAME-poster finns i [Skapa CNAME DNS-posten](../cdn/cdn-map-content-to-custom-domain.md).

Om din CNAME-post har rätt format verifierar DigiCert automatiskt det anpassade domännamnet och skapar ett dedikerat certifikat för domännamnet. DigitCert skickar ingen bekräftelse via e-post och du behöver inte godkänna din begäran. Certifikatet är giltigt i ett år och kommer att vara automatiskt nytt innan det upphör att gälla. Fortsätt till [Vänta på spridning.](#wait-for-propagation) 

Den automatiska verifieringen tar vanligtvis några minuter. Öppna ett supportärende om domänen inte har verifierats inom en timme.

>[!NOTE]
>Om du har en CAA-post (Certificate Authority Authorization) hos DNS-providern måste den innehålla DigiCert som giltig certifikatutfärdare. En CAA-post gör att domänägare kan ange med sina DNS-provider vilka certifikatutfärdare som är auktoriserade att utfärda certifikat för deras domän. Om en certifikatutfärdare mottar en order för ett certifikat för en domän som har en CAA-post och den certifikatutfärdaren inte finns med i listan över auktoriserade utfärdare hindras certifikatufärdaren att utfärda certifikatet till den domänen eller underdomänen. Information om hur du hanterar CAA poster finns i [Hantera CAA-poster](https://support.dnsimple.com/articles/manage-caa-record/). Hjälpverktyg för CAA-poster finns i [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Den anpassade domänen har inte mappats till din Front Door

Om CNAME-posten för slutpunkten inte finns längre eller om den innehåller afdverify-underdomänen följer du instruktionerna i det här steget.

När du har aktiverat HTTPS på din anpassade domän verifierar DigiCert-certifikatutfärdaren ägarskapet för din domän genom att kontakta registranten enligt domänens [WHOIS](http://whois.domaintools.com/)-registrantinformation. Kontakten sker via e-postadressen (som standard) eller telefonnumret som står i WHOIS-registreringen. Du måste slutföra domänverifieringen för att HTTPS ska aktiveras på din anpassade domän. Du har sex arbetsdagar på dig att godkänna domänen. Begäranden som inte godkänns inom sex arbetsdagar avbryts automatiskt. 

![WHOIS-post](./media/front-door-custom-domain-https/whois-record.png)

DigiCert skickar också ett verifieringsmeddelande till andra e-postadresser. Om WHOIS-registrantinformationen är privat, kontrollerar du att du kan godkänna direkt från en av följande adresser:

admin@&lt;dittdomännamn.com&gt;  
administrator@&lt;dittdomännamn.com&gt;  
webmaster@&lt;dittdomännamn.com&gt;  
hostmaster@&lt;dittdomännamn.com&gt;  
postmaster@&lt;dittdomännamn.com&gt;  

Inom ett par minuter får du ett e-postmeddelande som ser ut ungefär som i följande exempel och som ber dig godkänna begäran. Om du använder ett skräppostfilter lägger du till admin@digicert.com i listan över tillåtna. Kontakta Microsoft-supporten om du inte får ett e-postmeddelande inom 24 timmar.

När du väljer godkännandelänken dirigeras du till ett godkännandeformulär online. Följ instruktionerna i formuläret. Du har två verifieringsalternativ:

- Du kan godkänna alla framtida order som placerats via samma konto för samma rotdomän, till exempel contoso.com. Den här metoden rekommenderas om du planerar att lägga till fler anpassade domäner för samma rotdomän.

- Du kan bara godkänna det specifika värdnamn som används i den här begäran. Extra godkännande krävs för efterföljande begäranden.

Efter godkännandet slutför DigiCert skapandet av certifikatet för det anpassade domännamnet. Certifikatet är giltigt i ett år och kommer att vara automatiskt nytt innan det upphör att gälla.

## <a name="wait-for-propagation"></a>Vänta på spridning

När domännamnet har verifierats kan det ta upp till 6–8 timmar innan HTTPS-funktionen för den anpassade domänen aktiveras. När processen är klar visas HTTPS-status **Aktiverad** i Azure Portal och de fyra åtgärdsstegen i dialogrutan för anpassad domän markeras som slutförda. Den anpassade domänen är nu klar att använda HTTPS.

### <a name="operation-progress"></a>Åtgärdsförlopp

I följande tabell visas åtgärdsförloppet när du aktiverar HTTPS. När du har aktiverat HTTPS visas de fyra åtgärdsstegen i dialogrutan Anpassad domän. När varje steg blir aktivt visas mer information om delsteg under steget medan det fortskrider. Alla understeg utförs inte. När ett steg har slutförts visas en grön bock bredvid steget. 

| Åtgärdssteg | Information om åtgärdsundersteg | 
| --- | --- |
| 1 Skicka begäran | Begäran skickas |
| | Din HTTPS-begäran skickas. |
| | Din HTTPS-begäran har skickats. |
| 2 Domänverifiering | Domänen verifieras automatiskt om den är CNAME mappad till .azurefd.net .Front Door. I annat fall skickas en verifieringsbegäran till den e-postadress som står angiven i domänens registreringspost (WHOIS-registrant). Verifiera domänen så snart som möjligt. |
| | Domänägarskapet har verifierats och godkänts. |
| | Begäran om verifiering av domänägarskap har gått ut (kunden svarade troligtvis inte inom 6 dagar). HTTPS aktiveras inte på din domän. * |
| | Begäran om verifiering av domänägarskap avvisades av kunden. HTTPS aktiveras inte på din domän. * |
| 3 Etablering av certifikat | Certifikatutfärdaren håller på att utfärda det certifikat som krävs för att aktivera HTTPS på din domän. |
| | Certifikatet har utfärdats och håller på att distribueras till din Front Door. Den här processen kan ta upp till 1 timme. |
| | Certifikatet har distribuerats för din Front Door. |
| 4 Slutfört | HTTPS har aktiverats på din domän. |

\* Det här meddelandet visas bara om ett fel har uppstått. 

Om det uppstår ett fel innan begäran har skickats visas följande felmeddelande:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. *Vem är certifikatleverantör och vilken typ av certifikat används?*

    Ett dedikerat/enskilt certifikat som tillhandahålls av Digicert används för din anpassade domän. 

2. *Använder du IP-baserad eller SNI-baserad TLS/SSL?*

    Azure Front Door använder SNI TLS/SSL.

3. *Vad händer om jag inte får domänverifieringsmeddelandet från DigiCert?*

    Om du har en CNAME-post för din anpassade domän som pekar direkt på slutpunktens värdnamn (och du inte använder underdomännamnet afdverify) får du inget domänverifieringsmeddelande. Verifieringen sker i så fall automatiskt. Om du inte har en CNAME-post och inte har fått något e-postmeddelande inom 24 timmar kontaktar du Microsoft-supporten.

4. *Är det mindre säkert att använda ett SAN-certifikat än att använda ett dedikerat certifikat?*
    
    Ett SAN-certifikat följer samma standarder för kryptering och säkerhet som ett dedikerat certifikat. Alla utfärdade TLS/SSL-certifikat använder SHA-256 för förbättrad serversäkerhet.

5. *Behöver jag en CAA-post (Certificate Authority Authorization) med DNS-leverantören?*

    Nej, det krävs för närvarande ingen auktoriseringspost för certifikatutfärdare. Men om du har en sådan måste den innehålla DigiCert som en giltig certifikatutfärdare.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg aktiverade du HTTPS-protokollet på en anpassad domän. Om du inte längre vill använda din anpassade domän med HTTPS kan du inaktivera HTTPS genom att göra följande:

### <a name="disable-the-https-feature"></a>Inaktivera HTTPS-funktionen 

1. I [Azure Portal](https://portal.azure.com)bläddrar du till **Azure Front Door** konfiguration.

2. I listan över värdar för frontend väljer du den anpassade domän som du vill inaktivera HTTPS för.

3. Klicka på **Inaktiverad** för att inaktivera HTTPS och klicka sedan på **Spara**.

### <a name="wait-for-propagation"></a>Vänta på spridning

När HTTPS-funktionen för den anpassade domänen har inaktiverats kan det ta upp till 6–8 timmar innan ändringen börjar gälla. När processen är klar ställs den anpassade HTTPS-statusen i Azure Portal in på **Inaktiverad** och de tre åtgärdsstegen i dialogrutan för anpassad domän markeras som slutförda. Den anpassade domänen använder inte längre HTTPS.

#### <a name="operation-progress"></a>Åtgärdsförlopp

I följande tabell visas åtgärdsförloppet när du inaktiverar HTTPS. När du har inaktiverat HTTPS visas de tre åtgärdsstegen i dialogrutan Anpassad domän. När varje steg blir aktivt visas mer information under steget. När ett steg har slutförts visas en grön bock bredvid steget. 

| Åtgärdsförlopp | Åtgärdsinformation | 
| --- | --- |
| 1 Skicka begäran | Begäran skickas |
| 2 Avetablering av certifikat | Tar bort certifikat |
| 3 Slutfört | Certifikatet har tagits bort |

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Ladda upp ett certifikat till Key Vault.
* Verifiera en domän.
* Aktivera HTTPS för din anpassade domän.

Om du vill lära dig hur du ställer in en princip för geofiltrering för Front Door kan du fortsätta till nästa självstudie.

> [!div class="nextstepaction"]
> [Konfigurera en princip för geofiltrering](front-door-geo-filtering.md)
