---
title: Lägga till och hantera TLS/SSL-certifikat
description: Skapa ett kostnadsfritt certifikat, importera App Service ett certifikat, importera ett Key Vault certifikat eller köp ett App Service-certifikat i Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1e05435f364cc30b351275439a04caff47c35512
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871803"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Lägg till ett TLS-/SSL-certifikat i Azure App Service

[Azure App Service](overview.md) en mycket skalbar och självkorrigeringstjänst för webbvärdtjänster. Den här artikeln visar hur du skapar, laddar upp eller importerar ett privat certifikat eller ett offentligt certifikat till App Service. 

När certifikatet har lagts till i din App Service-app eller [funktionsapp](../azure-functions/index.yml)kan du skydda ett anpassat [DNS-namn](configure-ssl-bindings.md) med det eller använda det [i programkoden](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Ett certifikat som laddas upp till en app lagras i en distributionsenhet som är bunden till appens kombination av resursgrupp och region (internt kallat *ett webbområde).* Detta gör certifikatet tillgängligt för andra appar i samma kombination av resursgrupp och region. 

I följande tabell visas de alternativ du har för att lägga till certifikat i App Service:

|Alternativ|Beskrivning|
|-|-|
| Skapa ett kostnadsfritt App Service -certifikat (förhandsversion) | Ett privat certifikat som är kostnadsfritt och enkelt att använda om du bara behöver skydda din [anpassade domän](app-service-web-tutorial-custom-domain.md) i App Service. |
| Köpa ett App Service certifikat | Ett privat certifikat som hanteras av Azure. Den kombinerar enkelheten i automatiserad certifikathantering och flexibiliteten med förnyelse- och exportalternativ. |
| Importera ett certifikat från Key Vault | Användbart om du [använder Azure Key Vault](../key-vault/index.yml) för att hantera [dina PKCS12-certifikat.](https://wikipedia.org/wiki/PKCS_12) Se [Krav för privata certifikat.](#private-certificate-requirements) |
| Ladda upp ett privat certifikat | Om du redan har ett privat certifikat från en tredjepartsleverantör kan du ladda upp det. Se [Krav för privata certifikat.](#private-certificate-requirements) |
| Ladda upp ett offentligt certifikat | Offentliga certifikat används inte för att skydda anpassade domäner, men du kan läsa in dem i koden om du behöver dem för att komma åt fjärrresurser. |

## <a name="prerequisites"></a>Förutsättningar

- [Skapa en App Service app](./index.yml).
- För ett privat certifikat kontrollerar du att det uppfyller alla [krav från App Service](#private-certificate-requirements).
- **Endast kostnadsfritt certifikat:**
    - Mappa den domän som du vill att ett certifikat för ska App Service. Mer information finns i [Självstudie: Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md).
    - För en rotdomän (till exempel contoso.com) kontrollerar du att appen inte har några [konfigurerade IP-begränsningar.](app-service-ip-restrictions.md) Både skapandet av certifikat och den periodiska förnyelsen för en rotdomän beror på att din app kan nås från Internet.

## <a name="private-certificate-requirements"></a>Krav för privata certifikat

Det [kostnadsfria App Service hanterade](#create-a-free-managed-certificate-preview) certifikatet [och det App Service certifikatet](#import-an-app-service-certificate) uppfyller redan kraven i App Service. Om du väljer att ladda upp eller importera ett privat certifikat till App Service måste certifikatet uppfylla följande krav:

* Exporteras som en [lösenordsskyddad PFX-fil](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions), krypterad med trippel DES.
* Innehålla en privat nyckel som är minst 2 048 bitar lång
* Innehålla alla mellanliggande certifikat i certifikatkedjan

Certifikatet har ytterligare krav för att skydda en anpassad domän i en TLS-bindning:

* Innehåller utökad [nyckelanvändning för](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) serverautentisering (OID = 1.3.6.1.5.5.7.3.1)
* Vara signerat av en betrodd certifikatutfärdare

> [!NOTE]
> **ECC-certifikat (Elliptic Curve Cryptography)** kan fungera med App Service, men behandlas inte i den här artikeln. Din certifikatutfärdare kan visa hur du skapar ECC-certifikat.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Skapa ett kostnadsfritt hanterat certifikat (förhandsversion)

> [!NOTE]
> Innan du skapar ett kostnadsfritt hanterat certifikat kontrollerar du [att du uppfyller kraven](#prerequisites) för din app.

Det kostnadsfria App Service Managed Certificate är en nyckelnyckellösning för att skydda ditt anpassade DNS-namn i App Service. Det är ett fullt funktionellt TLS/SSL-certifikat som hanteras av App Service förnyas automatiskt. Det kostnadsfria certifikatet har följande begränsningar:

- Stöder inte jokerteckencertifikat.
- Kan inte exporteras.
- Stöds inte på App Service-miljön (ASE).
- Stöds inte med rotdomäner som är integrerade med Traffic Manager.

> [!NOTE]
> Det kostnadsfria certifikatet utfärdas av DigiCert. För vissa toppnivådomäner måste du uttryckligen tillåta DigiCert som certifikatutfärdare genom att skapa en [CAA-domänpost](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) med värdet: `0 issue digicert.com` .
> 

I den <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menyn till vänster väljer du **App Services**  >  **\<app-name>** .

I appens vänstra navigeringsfält väljer du **TLS/SSL-inställningar** Private  >  **Key Certificates (.pfx)**  >  **Create App Service Managed Certificate**.

![Skapa ett kostnadsfritt certifikat i App Service](./media/configure-ssl-certificate/create-free-cert.png)

Välj den anpassade domänen för att skapa ett kostnadsfritt certifikat för och välj **Skapa**. Du kan bara skapa ett certifikat för varje anpassad domän som stöds.

När åtgärden har slutförts visas certifikatet i listan Certifikat **för privat** nyckel.

![Skapa ett kostnadsfritt certifikat slutfört](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importera en App Service Certificate

Om du köper App Service Certificate från Azure hanterar Azure följande uppgifter:

- Tar hand om inköpsprocessen från GoDaddy.
- Utför domänverifiering av certifikatet.
- Underhåller certifikatet i [Azure Key Vault](../key-vault/general/overview.md).
- Hanterar certifikatförnyelse (se [Förnya certifikat).](#renew-certificate)
- Synkronisera certifikatet automatiskt med de importerade kopiorna i App Service appar.

Om du vill App Service ett certifikat går du till [Starta certifikatbeställning](#start-certificate-order).

Om du redan har ett App Service certifikat kan du:

- [Importera certifikatet till App Service](#import-certificate-into-app-service).
- [Hantera certifikatet,](#manage-app-service-certificates)till exempel förnya, nyckela om och exportera det.
> [!NOTE]
> App Service certifikat stöds inte i nationella Azure-moln för närvarande.

### <a name="start-certificate-order"></a>Starta certifikatordning

Starta en App Service certifikatordning på <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">sidan App Service Certificate skapa</a>.

![Starta App Service köp av certifikat](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Använd följande tabell för att konfigurera certifikatet. Klicka på **Skapa** när du är klar.

| Inställning | Beskrivning |
|-|-|
| Name | Ett eget namn för ditt App Service certifikat. |
| Domänvärdnamn för domän | Ange rotdomänen här. Det utfärdade certifikatet skyddar *både* rotdomänen och `www` underdomänen. I det utfärdade certifikatet innehåller fältet Eget namn rotdomänen och fältet Alternativt namn för certifikatutfärdare innehåller `www` domänen. Om du endast vill skydda en underdomän anger du det fullständigt kvalificerade domännamnet för underdomänen här (till exempel `mysubdomain.contoso.com` ).|
| Prenumeration | Den prenumeration som ska innehålla certifikatet. |
| Resursgrupp | Den resursgrupp som ska innehålla certifikatet. Du kan till exempel använda en ny resursgrupp eller välja samma resursgrupp App Service din app. |
| Certifikat-SKU | Anger vilken typ av certifikat som ska skapas, oavsett om det är ett standardcertifikat eller [ett jokerteckencertifikat](https://wikipedia.org/wiki/Wildcard_certificate). |
| Juridiska villkor | Klicka för att bekräfta att du accepterar de juridiska villkoren. Certifikaten hämtas från GoDaddy. |

> [!NOTE]
> App Service certifikat som köpts från Azure utfärdas av GoDaddy. För vissa toppnivådomäner måste du uttryckligen tillåta GoDaddy som certifikatutfärdare genom att skapa en [CAA-domänpost](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) med värdet : `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Lagra i Azure Key Vault

När certifikatinköpsprocessen är klar finns det några fler steg som du måste utföra innan du kan börja använda det här certifikatet. 

Välj certifikatet på sidan [App Service certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och klicka sedan på **Certifikatkonfiguration Steg**  >  **1: Lagra**.

![Konfigurera Key Vault lagring av App Service certifikat](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) är en Azure-tjänst som hjälper till att skydda kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster. Det är lagringsvalet för App Service certifikat.

På sidan **Key Vault status klickar** du på Key Vault **lagringsplats för** att skapa ett nytt valv eller väljer ett befintligt valv. Om du väljer att skapa ett nytt valv använder du följande tabell för att konfigurera valvet och klickar på Skapa. Skapa den nya Key Vault i samma prenumeration och resursgrupp som din App Service appen.

| Inställning | Beskrivning |
|-|-|
| Name | Ett unikt namn som består av alfanumeriska tecken och bindestreck. |
| Resursgrupp | Som en rekommendation väljer du samma resursgrupp som ditt App Service certifikat. |
| Location | Välj samma plats som din App Service appen. |
| Prisnivå | Mer information finns i [Azure Key Vault prisinformation.](https://azure.microsoft.com/pricing/details/key-vault/) |
| Åtkomstprinciper| Definierar programmen och den tillåtna åtkomsten till valvresurserna. Du kan konfigurera den senare genom att följa stegen i [Tilldela en Key Vault åtkomstprincip](../key-vault/general/assign-access-policy-portal.md). |
| Virtual Network Access | Begränsa valvåtkomst till vissa virtuella Azure-nätverk. Du kan konfigurera den senare genom att följa stegen [i Konfigurera Azure Key Vault-brandväggar och virtuella nätverk](../key-vault/general/network-security.md) |

När du har valt valvet stänger du sidan **Key Vault Lagringsplats.** Alternativet **Steg 1: Butik** bör visa en grön bockmarkering för att det ska lyckas. Låt sidan vara öppen för nästa steg.

### <a name="verify-domain-ownership"></a>Verifiera domänägarskap

Från samma **certifikatkonfigurationssida** som du använde i det senaste steget klickar du **på Steg 2: Verifiera**.

![Verifiera domänen för App Service certifikat](./media/configure-ssl-certificate/verify-domain.png)

Välj **App Service Verifiering**. Eftersom du redan har mappat domänen till din webbapp (se [Krav](#prerequisites)) är den redan verifierad. Slutför det **här steget** genom att klicka på Verifiera. Klicka på **knappen Uppdatera** tills meddelandet Certifikat **är Domänverifierade** visas.

> [!NOTE]
> Fyra typer av domänverifieringsmetoder stöds: 
> 
> - **App Service** – Det enaste alternativet när domänen redan har mappats till en App Service i samma prenumeration. Det drar nytta av det faktum att App Service redan har verifierat domänägarskapet.
> - **Domän** – Verifiera en [App Service domän som du har köpt från Azure](manage-custom-dns-buy-domain.md). Azure lägger automatiskt till TXT-verifieringsposten åt dig och slutför processen.
> - **E-post** – Verifiera domänen genom att skicka ett e-postmeddelande till domänadministratören. Instruktioner ges när du väljer alternativet.
> - **Manuell** – Verifiera domänen med hjälp av antingen en HTML-sida **(endast standardcertifikat)** eller en DNS TXT-post. Instruktioner ges när du väljer alternativet.

### <a name="import-certificate-into-app-service"></a>Importera certifikat till App Service

Välj <a href="https://portal.azure.com" target="_blank">Azure Portal</a>på menyn till vänster i **App Services**  >  **\<app-name>** .

I appens vänstra navigeringsfält väljer du **TLS/SSL-inställningar** Privat nyckelcertifikat  >  **(.pfx)**  >  **Importera App Service Certificate**.

![Importera App Service certifikat i App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Välj det certifikat som du precis har köpt och välj **OK.**

När åtgärden har slutförts visas certifikatet i listan Certifikat **för privat** nyckel.

![Import App Service certifikatet har slutförts](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importera ett certifikat från Key Vault

Om du använder Azure Key Vault för att hantera dina certifikat kan du importera ett PKCS12-certifikat från Key Vault till App Service så länge det [uppfyller kraven](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>Auktorisera App Service att läsa från valvet
Som standard har App Service resursprovidern inte åtkomst till Key Vault. För att kunna använda en Key Vault för en certifikatdistribution måste du auktorisera resursproviderns [läsbehörighet till KeyVault](../key-vault/general/assign-access-policy-cli.md). 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  är resursproviderns tjänsthuvudnamn för App Service och det är detsamma för alla Azure-prenumerationer. För Azure Government molnmiljö använder du i `6a02c803-dafd-4136-b4c3-5a6f318b4714` stället som resursproviderns huvudnamn för tjänstens huvudnamn.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Importera ett certifikat från valvet till din app

Välj <a href="https://portal.azure.com" target="_blank">Azure Portal</a>på menyn till vänster i **App Services**  >  **\<app-name>** .

I appens vänstra navigeringsfält väljer du **TLS/SSL-inställningar** Privat nyckelcertifikat  >  **(.pfx)**  >  **Importera Key Vault certifikat**.

![Importera Key Vault certifikat i App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Använd följande tabell som hjälp när du väljer certifikatet.

| Inställning | Beskrivning |
|-|-|
| Prenumeration | Den prenumeration som Key Vault tillhör. |
| Key Vault | Valvet med det certifikat som du vill importera. |
| Certifikat | Välj i listan över PKCS12-certifikat i valvet. Alla PKCS12-certifikat i valvet visas med tumavtryck, men alla stöds inte i App Service. |

När åtgärden har slutförts visas certifikatet i listan Certifikat **för privat** nyckel. Om importen misslyckas med ett fel uppfyller certifikatet inte kraven [för att App Service](#private-certificate-requirements).

![Import Key Vault certifikatet har slutförts](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Om du uppdaterar certifikatet i Key Vault med ett nytt certifikat App Service automatiskt certifikatet inom 24 timmar.

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

När du har skaffat ett certifikat från certifikatprovidern följer du stegen i det här avsnittet för att göra det App Service.

### <a name="merge-intermediate-certificates"></a>Sammanfoga mellanliggande certifikat

Om du får flera certifikat av din certifikatutfärdare i certifikatkedjan, måste du sammanfoga certifikaten i ordning.

Gör detta genom att öppna varje certifikat som du fick i ett redigeringsprogram.

Skapa en fil för det sammanfogade certifikatet med namnet _mergedcertificate.crt_. I redigeringsprogrammet kopierar du innehållet i varje certifikat till den här filen. Ordningen på dina certifikat ska följa ordningen i certifikatkedjan, först med ditt certifikat och sist med rotcertifikatet. Det ser ut som i följande exempel:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportera certifikat till PFX

Exportera ditt sammanslagna TLS/SSL-certifikat med den privata nyckel som certifikatbegäran genererades med.

Om du genererade din certifikatbegäran med hjälp av OpenSSL, har du skapat en privat nyckelfil. Kör följande kommando för att exportera certifikatet till PFX. Ersätt platshållarna _&lt; private-key-file>_ _&lt; och merged-certificate-file>_ sökvägarna till din privata nyckel och den sammanslagna certifikatfilen.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

När du uppmanas till det anger du ett exportlösenord. Du använder det här lösenordet när du laddar upp TLS/SSL-certifikatet till App Service senare.

Om du använder IIS eller _Certreq.exe_ till att generera din certifikatbegäran, installerar du certifikatet på den lokala datorn och [exporterar sedan certifikatet till PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Ladda upp certifikat till App Service

Nu är du redo att ladda upp certifikatet till App Service.

Välj <a href="https://portal.azure.com" target="_blank">Azure Portal</a>på menyn till vänster i **App Services**  >  **\<app-name>** .

I appens vänstra navigeringsfält väljer du **TLS/SSL-inställningar** Certifikat för privat nyckel  >  **(.pfx)**  >  **Ladda upp certifikat.**

![Ladda upp privat certifikat i App Service](./media/configure-ssl-certificate/upload-private-cert.png)

I **PFX-certifikatsfil** väljer du din PFX-fil. I **Certifikatslösenord** skriver du lösenordet som du skapade när du exporterade PFX-filen. När du är klar klickar du på **Ladda upp**. 

När åtgärden har slutförts visas certifikatet i listan Certifikat **för privat** nyckel.

![Ladda upp certifikatet har slutförts](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande skapa en certifikatbindning. Följ stegen i [Skapa bindning](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Offentliga certifikat stöds i *CER-format.* 

Välj <a href="https://portal.azure.com" target="_blank">Azure Portal</a>på menyn till vänster i **App Services**  >  **\<app-name>** .

I appens vänstra navigeringsfält klickar du på **TLS/SSL-inställningar**  >  **Offentliga certifikat (.cer) Ladda** upp certifikat för offentlig  >  **nyckel.**

I **Namn** anger du ett namn för certifikatet. I **CER-certifikatfilen** väljer du cer-filen.

Klicka på **Överför**.

![Ladda upp offentligt certifikat i App Service](./media/configure-ssl-certificate/upload-public-cert.png)

När certifikatet har laddats upp kopierar du certifikatets tumavtryck och [ser Gör certifikatet tillgängligt.](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)

## <a name="manage-app-service-certificates"></a>Hantera App Service certifikat

Det här avsnittet visar hur du hanterar ett App Service-certifikat som du köpte i [Importera ett App Service certifikat](#import-an-app-service-certificate).

- [Nyckel nytt certifikat](#rekey-certificate)
- [Förnya certifikat](#renew-certificate)
- [Exportera certifikatet](#export-certificate)
- [Ta bort certifikat](#delete-certificate)

### <a name="rekey-certificate"></a>Nyckelfärdigt certifikat

Om du tror att certifikatets privata nyckel har komprometterats kan du nyckela om certifikatet. Välj certifikatet på sidan [App Service certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och välj sedan Nyckel nytt och **Synkronisera i** det vänstra navigeringsfönstret.

Klicka **på Nyckel igen** för att starta processen. Den här processen kan ta 1–10 minuter att slutföra.

![Nyckelfärdiga ett App Service certifikat](./media/configure-ssl-certificate/rekey-app-service-cert.png)

När du gör en ny nyckel för certifikatet återställs certifikatet med ett nytt certifikat som utfärdats från certifikatutfärdaren.

När nyckelåtgärden är klar klickar du på **Synkronisera.** Synkroniseringsåtgärden uppdaterar automatiskt värdnamnsbindningarna för certifikatet i App Service utan att orsaka driftstopp för dina appar.

> [!NOTE]
> Om du inte klickar på **Synkronisera** App Service automatiskt certifikatet inom 24 timmar.

### <a name="renew-certificate"></a>Förnya certifikat

Om du vill aktivera automatisk förnyelse av certifikatet när som helst väljer du  certifikatet [på sidan App Service Certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och klickar sedan på Förnya inställningar automatiskt i det vänstra navigeringsfönstret. Som standard App Service certifikat en ettårig giltighetsperiod.

Välj **På** och klicka på **Spara.** Certifikat kan börja förnyas automatiskt 30 dagar innan de upphör att gälla om du har aktiverat automatisk förnyelse.

![Förnya App Service certifikat automatiskt](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Om du i stället vill förnya certifikatet manuellt klickar du på **Förnya manuellt.** Du kan begära att certifikatet förnyas manuellt 60 dagar innan det upphör att gälla.

När förnyelseåtgärden är klar klickar du på **Synkronisera.** Synkroniseringsåtgärden uppdaterar automatiskt värdnamnsbindningarna för certifikatet i App Service utan att orsaka driftstopp för dina appar.

> [!NOTE]
> Om du inte klickar på **Synkronisera** App Service automatiskt certifikatet inom 24 timmar.

### <a name="export-certificate"></a>Exportera certifikatet

Eftersom en App Service Certificate är [en Key Vault hemlighet](../key-vault/general/about-keys-secrets-certificates.md)kan du exportera en PFX-kopia av den och använda den för andra Azure-tjänster eller utanför Azure.

Om du vill App Service Certificate som en PFX-fil kör du följande kommandon i [Cloud Shell](https://shell.azure.com). Du kan också köra det lokalt om du [har installerat Azure CLI.](/cli/azure/install-azure-cli) Ersätt platshållarna med de namn som du använde när [du skapade App Service certifikat](#start-certificate-order).

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Den nedladdade *filen appservicecertificate.pfx* är en rå PKCS12-fil som innehåller både offentliga och privata certifikat. Använd en tom sträng för importlösenordet och PEM-passfrasen i varje prompt.

### <a name="delete-certificate"></a>Ta bort certifikat 

Borttagningen av ett App Service certifikat är slutgiltig och går inte att ångra. Borttagning av en App Service Certificate-resurs resulterar i att certifikatet återkallas. Alla bindningar i App Service med det här certifikatet blir ogiltiga. För att förhindra oavsiktlig borttagning låser Azure certifikatet. Om du vill App Service ett certifikat måste du först ta bort borttagningslåset för certifikatet.

Välj certifikatet på sidan [App Service certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och välj lås **i** det vänstra navigeringsfönstret.

Leta reda på låset på certifikatet med låstypen **Ta bort.** Välj Ta bort till höger **om den.**

![Ta bort lås App Service certifikat](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Nu kan du ta bort App Service certifikat. Välj Översikt Ta bort i det **vänstra**  >  **navigeringsfönstret.** I bekräftelsedialogrutan skriver du certifikatets namn och väljer **OK.**

## <a name="automate-with-scripts&quot;></a>Automatisera med skript

### <a name=&quot;azure-cli&quot;></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Använda TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Använda ett TLS-/SSL-certifikat i koden i Azure App Service](configure-ssl-certificate-in-code.md)
* [Vanliga frågor och svar: App Service certifikat](./faq-configuration-and-management.md)
