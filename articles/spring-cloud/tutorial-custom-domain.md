---
title: 'Självstudie: Mappa en befintlig anpassad domän till Azure Spring Cloud'
description: Mappa ett befintligt anpassat DNS-namn (Distributed Name Service) till Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 493752a3857b80b43668b6bf1b20480604442955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568003"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Självstudie: Mappa en befintlig anpassad domän till Azure Spring Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Domain Name Service (DNS) är en teknik för att lagra nätverksnodnamn i ett nätverk. Den här självstudien mappar en domän, till www.contoso.com, med hjälp av en CNAME-post. Den skyddar den anpassade domänen med ett certifikat och visar hur du tillämpar Transport Layer Security (TLS), även kallat Secure Sockets Layer (SSL). 

Certifikat krypterar webbtrafik. Dessa TLS/SSL-certifikat kan lagras i Azure Key Vault. 

## <a name="prerequisites"></a>Förutsättningar
* Ett program som distribuerats till Azure Spring Cloud (se Snabbstart: Starta ett [befintligt Azure Spring Cloud-program](spring-cloud-quickstart.md)med hjälp av Azure Portal eller använda en befintlig app).
* Ett domännamn med åtkomst till DNS-registret för domänleverantör, till exempel GoDaddy.
* Ett privat certifikat (det vill säga ditt själv signerade certifikat) från en tredjepartsleverantör. Certifikatet måste matcha domänen.
* En distribuerad instans av [Azure Key Vault](../key-vault/general/overview.md)

## <a name="keyvault-private-link-considerations"></a>Keyvault Private Link Att tänka på

Ip Azure Spring Cloud hanterings-IP-adresser är ännu inte en del av Azure Trusted Microsoft-tjänster. För att tillåta Azure Spring Cloud läsa in certifikat från en Key Vault som skyddas med privata slutpunktsanslutningar måste du därför lägga till följande IP-adresser i Azure Key Vault Firewall:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Importera certifikatet
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Förbered certifikatfilen i PFX (valfritt)
Azure Key Vault stöd för import av privata certifikat i PEM- och PFX-format. Om PEM-filen som du fick från certifikatprovidern inte fungerar i avsnittet nedan: Spara certifikat i [Key Vault](#save-certificate-in-key-vault)följer du stegen här för att generera en PFX för Azure Key Vault.

#### <a name="merge-intermediate-certificates"></a>Sammanfoga mellanliggande certifikat

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

#### <a name="export-certificate-to-pfx"></a>Exportera certifikat till PFX

Exportera ditt sammanslagna TLS/SSL-certifikat med den privata nyckel som certifikatbegäran genererades med.

Om du genererade din certifikatbegäran med hjälp av OpenSSL, har du skapat en privat nyckelfil. Kör följande kommando för att exportera certifikatet till PFX. Ersätt platshållarna _&lt; private-key-file>_ _&lt; och merged-certificate-file>_ sökvägarna till din privata nyckel och den sammanslagna certifikatfilen.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

När du uppmanas till det anger du ett exportlösenord. Du använder det här lösenordet när du laddar upp TLS/SSL-certifikatet till Azure Key Vault senare.

Om du använder IIS eller _Certreq.exe_ till att generera din certifikatbegäran, installerar du certifikatet på den lokala datorn och [exporterar sedan certifikatet till PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Spara certifikatet i Key Vault
Proceduren för att importera ett certifikat kräver att PEM- eller PFX-kodad fil finns på disken och att du måste ha den privata nyckeln. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Så här laddar du upp certifikatet till key vault:
1. Gå till nyckelvalvsinstansen.
1. I det vänstra navigeringsfönstret klickar du på **Certifikat.**
1. Klicka på **Generera/importera** på den övre menyn.
1. I dialogrutan **Skapa ett certifikat** under Metod för att skapa **certifikat** väljer du `Import` .
1. Under **Ladda upp certifikatfil** navigerar du till certifikatplatsen och väljer den.
1. Under **Lösenord** anger du den privata nyckeln för certifikatet.
1. Klicka på **Skapa**.

    ![Importera certifikat 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Bevilja Azure Spring Cloud åtkomst till ditt nyckelvalv

Du måste ge Azure Spring Cloud åtkomst till nyckelvalvet innan du importerar certifikatet:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Gå till nyckelvalvsinstansen.
1. I det vänstra navigeringsfönstret klickar du på **Åtkomst-police**.
1. Klicka på Lägg till åtkomstprincip **på den övre menyn.**
1. Fyll i informationen och klicka på knappen **Lägg till** och sedan **på Spara** åtkomst-police.

| Behörigheten Hemlighet | Certifikatbehörighet | Välj huvudnamn |
|--|--|--|
| Hämta, Lista | Hämta, Lista | Azure Spring Cloud Domain-Management |

![Importera certifikat 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Ge Azure Spring Cloud läsbehörighet till nyckelvalvet och `<key vault resource group>` ersätt och i följande `<key vault name>` kommando.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importera certifikat till Azure Spring Cloud
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Gå till tjänstinstansen. 
1. I det vänstra navigeringsfönstret i appen väljer du **TLS/SSL-inställningar.**
1. Klicka sedan **på Importera Key Vault certifikat**.

    ![Importera certifikatet](./media/custom-dns-tutorial/import-certificate.png)

1. När du har importerat certifikatet visas det i listan över privata **nyckelcertifikat.**

    ![Certifikat för privat nyckel](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Så här visar du en lista över importerade certifikat:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande binda certifikatet till en specifik domän. Följ stegen i det här avsnittet: Lägg [till SSL-bindning.](#add-ssl-binding)

## <a name="add-custom-domain"></a>Lägga till Custom Domain
Du kan använda en CNAME-post för att mappa ett anpassat DNS-namn till Azure Spring Cloud. 

> [!NOTE] 
> A-posten stöds inte. 

### <a name="create-the-cname-record"></a>Skapa CNAME-posten
Gå till DIN DNS-provider och lägg till en CNAME-post för att mappa domänen till <service_name>.azuremicroservices.io. Här <service_name> är namnet på din Azure Spring Cloud instans. Vi stöder jokerteckendomän och underdomän. När du har lagt till CNAME liknar sidan DNS-poster följande exempel: 

![Sida för DNS-poster](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mappa din anpassade domän till Azure Spring Cloud app
Om du inte har något program i Azure Spring Cloud följer du anvisningarna i [Snabbstart: Starta ett befintligt Azure Spring Cloud-program med](./spring-cloud-quickstart.md)hjälp av Azure Portal .

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Gå till programsidan.

1. Välj **Custom Domain**.
2. Lägg **sedan till Custom Domain**. 

    ![Anpassad domän](./media/custom-dns-tutorial/custom-domain.png)

3. Ange det fullständigt kvalificerade domännamnet som du har lagt till en CNAME-post för, till exempel www.contoso.com. Kontrollera att Posttypen Värdnamn har angetts till CNAME (<service_name>.azuremicroservices.io)
4. Klicka **på Verifiera** för att aktivera knappen **Lägg** till.
5. Klicka på **Lägg till**.

    ![Lägga till anpassad domän](./media/custom-dns-tutorial/add-custom-domain.png)

En app kan ha flera domäner, men en domän kan bara mappa till en app. När du har mappat din anpassade domän till appen visas den i den anpassade domäntabellen.

![Anpassad domäntabell](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Så här visar du listan över anpassade domäner:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Etiketten **Inte säker** för din anpassade domän innebär att den ännu inte är bunden till ett SSL-certifikat. Alla HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett felmeddelande eller en varning.

## <a name="add-ssl-binding"></a>Lägga till SSL-bindning

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
I den anpassade domäntabellen väljer du **Lägg till SSL-bindning** enligt föregående bild.  
1. Välj ditt **certifikat** eller importera det.
1. Klicka på **Spara**.

    ![Lägg till SSL-bindning 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

När du har lagt till SSL-bindning är domäntillståndet säkert: **Felfritt**. 

![Lägga till SSL-bindning 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Använda HTTPS
Som standard kan vem som helst fortfarande komma åt din app via HTTP, men du kan omdirigera alla HTTP-begäranden till HTTPS-porten.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
På appsidan går du till det vänstra navigeringsfönstret och **väljer Custom Domain**. Ange sedan **ENDAST HTTPS** till *Sant.*

![Lägga till SSL-bindning 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
När åtgärden har slutförts navigerar du till någon av de HTTPS-webbadresser som pekar på din app. Observera att HTTP-URL:er inte fungerar.

## <a name="see-also"></a>Se även
* [Vad är Azure Key Vault?](../key-vault/general/overview.md)
* [Importera ett certifikat](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Starta din Spring Cloud med hjälp av Azure CLI](./spring-cloud-quickstart.md)
