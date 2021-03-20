---
title: Konfigurera TLS för en moln tjänst | Microsoft Docs
description: Lär dig hur du anger en HTTPS-slutpunkt för en webbroll och hur du överför ett TLS/SSL-certifikat för att skydda ditt program. I de här exemplen används Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742649"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Konfigurera TLS för ett program i Azure

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Transport Layer Security (TLS), som tidigare kallades SSL-kryptering (Secure Socket Layer), är den vanligaste metoden för att skydda data som skickas via Internet. Den här vanliga åtgärden beskriver hur du anger en HTTPS-slutpunkt för en webbroll och hur du överför ett TLS/SSL-certifikat för att skydda ditt program.

> [!NOTE]
> Procedurerna i den här uppgiften gäller Azure Cloud Services; för App Services, se [detta](../app-service/configure-ssl-bindings.md).
>

Den här uppgiften använder en produktions distribution. Information om hur du använder en mellanlagrings distribution finns i slutet av det här avsnittet.

Läs [detta](cloud-services-how-to-create-deploy-portal.md) först om du inte redan har skapat en moln tjänst.

## <a name="step-1-get-a-tlsssl-certificate"></a>Steg 1: Hämta ett TLS/SSL-certifikat
Om du vill konfigurera TLS för ett program måste du först skaffa ett TLS/SSL-certifikat som har signerats av en certifikat utfärdare (CA), en betrodd tredje part som utfärdar certifikat för detta ändamål. Om du inte redan har en måste du skaffa ett från ett företag som säljer TLS/SSL-certifikat.

Certifikatet måste uppfylla följande krav för TLS/SSL-certifikat i Azure:

* Certifikatet måste innehålla en offentlig nyckel.
* Certifikatet måste skapas för nyckel utbyte, exporteras till en personal information Exchange-fil (. pfx).
* Certifikatets ämnes namn måste matcha den domän som används för att få åtkomst till moln tjänsten. Det går inte att hämta ett TLS/SSL-certifikat från en certifikat utfärdare (CA) för cloudapp.net-domänen. Du måste skaffa ett anpassat domän namn som ska användas vid åtkomst till tjänsten. När du begär ett certifikat från en certifikat utfärdare måste certifikatets ämnes namn matcha det anpassade domän namn som används för att få åtkomst till ditt program. Om ditt anpassade domän namn till exempel är **contoso.com** kan du begära ett certifikat från din certifikat utfärdare för **_. contoso.com_* eller **www \. contoso.com**.
* Certifikatet måste ha minst 2048-bitars kryptering.

I test syfte kan du [skapa](cloud-services-certs-create.md) och använda ett självsignerat certifikat. Ett självsignerat certifikat autentiseras inte via en CA och kan använda cloudapp.net-domänen som webbplats-URL. Följande uppgift använder till exempel ett självsignerat certifikat där det egna namnet (CN) som används i certifikatet är **sslexample.cloudapp.net**.

Därefter måste du inkludera information om certifikatet i tjänst definitions-och tjänstekonfigurationsfiler.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Steg 2: ändra tjänst definition och konfigurationsfiler
Ditt program måste vara konfigurerat för att använda certifikatet och en HTTPS-slutpunkt måste läggas till. Därför måste tjänst definitions-och tjänst konfigurations filerna uppdateras.

1. Öppna tjänst definitions filen (CSDEF) i utvecklings miljön, Lägg till avsnittet **certifikat** i avsnittet **webrole** och ta med följande information om certifikatet (och mellanliggande certifikat):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   Avsnittet **certifikat** definierar namnet på vårt certifikat, dess plats och namnet på butiken där det finns.

   Behörigheter ( `permissionLevel` attribut) kan anges till något av följande värden:

   | Behörighets värde | Beskrivning |
   | --- | --- |
   | limitedOrElevated |**(Standard)** Alla roll processer kan komma åt den privata nyckeln. |
   | med |Endast utökade processer kan komma åt den privata nyckeln. |

2. I tjänst definitions filen lägger du till ett **InputEndpoint** -element i avsnittet **slut punkter** för att aktivera https:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Lägg till ett **bindnings** element i avsnittet **platser** i tjänst definitions filen. Det här elementet lägger till en HTTPS-bindning för att mappa slut punkten till platsen:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Alla nödvändiga ändringar av tjänst definitions filen har slutförts. men du behöver fortfarande lägga till certifikat informationen i tjänst konfigurations filen.
4. I tjänst konfigurations filen (CSCFG), ServiceConfiguration. Cloud. cscfg, lägger du till ett **certifikat** värde med certifikatet. I följande kod exempel visas information om **certifikat** avsnittet, förutom tumavtrycket.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Det här exemplet använder **SHA1** för tumavtrycket. Ange lämpligt värde för certifikatets tumavtryck-algoritm.)

Nu när tjänst definitions-och tjänstekonfigurationsfiler har uppdaterats kan du paketera distributionen för att ladda upp till Azure. Om du använder **CSPack** ska du inte använda flaggan **/generateConfigurationFile** , eftersom den kommer att skriva över den certifikat information som du nyss infogade.

## <a name="step-3-upload-a-certificate"></a>Steg 3: Ladda upp ett certifikat
Anslut till Azure Portal och...

1. I avsnittet **alla resurser** i portalen väljer du din moln tjänst.

    ![Publicera din moln tjänst](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klicka på **Certifikat**.

    ![Klicka på certifikat ikonen](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klicka på **Ladda upp** överst i avsnittet certifikat.

    ![Klicka på meny alternativet överför](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Ange **filen**, **lösen ordet** och klicka sedan på **överför** längst ned i data inmatnings området.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Steg 4: Anslut till roll instansen med hjälp av HTTPS
Nu när din distribution är igång i Azure kan du ansluta till den med hjälp av HTTPS.

1. Klicka på webb **adressen för webbplatsen** för att öppna webbläsaren.

   ![Klicka på webbplatsens URL](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. I webbläsaren ändrar du länken för att använda **https** i stället för **http** och besöker sidan.

   > [!NOTE]
   > Om du använder ett självsignerat certifikat kan du se ett certifikat fel i webbläsaren när du bläddrar till en HTTPS-slutpunkt som är associerad med det självsignerade certifikatet. Om du använder ett certifikat som signerats av en betrodd certifikat utfärdare elimineras problemet. under tiden kan du ignorera felet. (Ett annat alternativ är att lägga till det självsignerade certifikatet i användarens certifikat Arkiv för betrodd certifikat utfärdare.)
   >
   >

   ![Förhandsgranska webbplats](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Om du vill använda TLS för en mellanlagrings distribution i stället för en produktions distribution måste du först fastställa den URL som används för mellanlagrings distributionen. När moln tjänsten har distribuerats bestäms URL: en till mellanlagringsplatsen av **distributions-ID-** GUID i detta format: `https://deployment-id.cloudapp.net/`  
   >
   > Skapa ett certifikat med eget namn (CN) som motsvarar den GUID-baserade URL: en (till exempel **328187776e774ceda8fc57609d404462.cloudapp.net**). Använd portalen för att lägga till certifikatet i den mellanlagrade moln tjänsten. Lägg sedan till certifikat informationen i CSDEF-och CSCFG-filerna, paketera om programmet och uppdatera din mellanlagrade distribution för att använda det nya paketet.
   >

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av din moln tjänst](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuerar en moln tjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera ett [anpassat domän namn](cloud-services-custom-domain-name-portal.md).
* [Hantera din moln tjänst](cloud-services-how-to-manage-portal.md).



