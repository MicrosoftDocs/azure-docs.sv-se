---
title: Självstudie – mappa en befintlig anpassad domän till Azure våren Cloud
description: Mappa ett befintligt DNS-namn (Custom Distributed Name Service) till Azure våren Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6b2134ffd414aed3ed6561fb34544094eb52baa0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452483"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mappa en befintlig anpassad domän till Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Domain Name Service (DNS) är en teknik för att lagra namn på nätverks-noder i ett nätverk. Den här självstudien mappar en domän, till exempel www.contoso.com, med hjälp av en CNAME-post. Den säkrar den anpassade domänen med ett certifikat och visar hur du tvingar Transport Layer Security (TLS), även kallat Secure Sockets Layer (SSL). 

Certifikaten krypterar webb trafik. Dessa TLS/SSL-certifikat kan lagras i Azure Key Vault. 

## <a name="prerequisites"></a>Förutsättningar
* Ett program som distribueras till Azure våren Cloud (se [snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart.md)eller Använd en befintlig app).
* Ett domän namn med åtkomst till DNS-registret för domän leverantören, till exempel GoDaddy.
* Ett privat certifikat (det vill säga ditt självsignerade certifikat) från en tredje parts leverantör. Certifikatet måste matcha domänen.
* En distribuerad instans av [Azure Key Vault](../key-vault/general/overview.md)

## <a name="import-certificate"></a>Importera certifikatet
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Förbered din certifikat fil i PFX (valfritt)
Azure Key Vault stöd för import av privat certifikat i PEM och PFX-format. Om PEM-filen som du fick från din certifikat leverantör inte fungerar i avsnittet nedan: [Spara certifikat i Key Vault](#save-certificate-in-key-vault)följer du stegen här för att generera en PFX-fil för Azure Key Vault.

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

Exportera det kopplade TLS/SSL-certifikatet med den privata nyckel som din certifikatbegäran genererades med.

Om du genererade din certifikatbegäran med hjälp av OpenSSL, har du skapat en privat nyckelfil. Kör följande kommando för att exportera certifikatet till PFX. Ersätt plats hållarna för den _&lt; privata nyckel filen>_ och _&lt; sammanfogade certifikat fil>_ med Sök vägarna till din privata nyckel och den sammanslagna certifikat filen.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

När du uppmanas till det anger du ett exportlösenord. Du använder det här lösen ordet när du överför TLS/SSL-certifikatet till Azure Key Vault senare.

Om du använder IIS eller _Certreq.exe_ till att generera din certifikatbegäran, installerar du certifikatet på den lokala datorn och [exporterar sedan certifikatet till PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Spara certifikat i Key Vault
Proceduren för att importera ett certifikat kräver att PEM-eller PFX-filen finns på disk och att du måste ha en privat nyckel. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Så här överför du ditt certifikat till nyckel valvet:
1. Gå till din Key Vault-instans.
1. I det vänstra navigerings fönstret klickar du på **certifikat**.
1. Klicka på **generera/importera** på den övre menyn.
1. I dialog rutan **skapa ett certifikat** under **metoden för att skapa certifikat** väljer du `Import` .
1. Under **överför certifikat fil**, navigerar du till certifikat plats och väljer den.
1. Under **lösen ord** anger du den privata nyckeln för certifikatet.
1. Klicka på **Skapa**.

    ![Importera certifikat 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Ge Azure våren Cloud åtkomst till ditt nyckel valv

Du måste ge Azure våren Cloud åtkomst till ditt nyckel valv innan du importerar certifikat:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Gå till din Key Vault-instans.
1. Klicka på **åtkomst principer** i det vänstra navigerings fönstret.
1. I den övre menyn klickar du på **Lägg till åtkomst princip**.
1. Fyll i informationen och klicka på knappen **Lägg till** och **Spara** sedan åtkomst principer.

| Hemlig behörighet | Certifikat behörighet | Välj huvud konto |
|--|--|--|
| Hämta, Lista | Hämta, Lista | Azure våren Cloud Domain-Management |

![Importera certifikat 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Ge Azure våren Cloud Läs åtkomst till Key Vault, Ersätt `<key vault resource group>` och `<key vault name>` i följande kommando.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importera certifikat till Azure våren Cloud
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Gå till din tjänst instans. 
1. I det vänstra navigerings fönstret i appen väljer du **TLS/SSL-inställningar**.
1. Klicka sedan på **importera Key Vault certifikat**.

    ![Importera certifikatet](./media/custom-dns-tutorial/import-certificate.png)

1. När du har importerat certifikatet ser du det i listan över **certifikat för privat nyckel**.

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
> Om du vill skydda en anpassad domän med det här certifikatet måste du fortfarande binda certifikatet till en speciell domän. Följ stegen i det här avsnittet: [Lägg till SSL-bindning](#add-ssl-binding).

## <a name="add-custom-domain"></a>Lägg till anpassad domän
Du kan använda en CNAME-post för att mappa ett anpassat DNS-namn till Azure våren Cloud. 

> [!NOTE] 
> A-posten stöds inte. 

### <a name="create-the-cname-record"></a>Skapa CNAME-posten
Gå till DNS-providern och Lägg till en CNAME-post för att mappa din domän till <service_name>. azuremicroservices.io. Här <service_name> är namnet på din Azure våren Cloud-instans. Vi stöder domänen med jokertecken och under domänen. När du har lagt till CNAME kommer sidan DNS-poster att likna följande exempel: 

![Sida för DNS-poster](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mappa din anpassade domän till Azure våren Cloud-appen
Om du inte har ett program i Azure våren-molnet följer du anvisningarna i [snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Gå till program sidan.

1. Välj **anpassad domän**.
2. **Lägg sedan till anpassad domän**. 

    ![Anpassad domän](./media/custom-dns-tutorial/custom-domain.png)

3. Ange det fullständigt kvalificerade domän namnet som du har lagt till en CNAME-post för, till exempel www.contoso.com. Kontrol lera att post typen hostname är inställd på CNAME (<service_name>. azuremicroservices.io)
4. Klicka på **validera** för att aktivera knappen **Lägg till** .
5. Klicka på **Lägg till**.

    ![Lägg till anpassad domän](./media/custom-dns-tutorial/add-custom-domain.png)

En app kan ha flera domäner, men en domän kan bara mappas till en enda app. När du har mappat din anpassade domän till appen visas den i den anpassade domän tabellen.

![Anpassad domän tabell](./media/custom-dns-tutorial/custom-domain-table.png)

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
> En **osäker etikett för** din anpassade domän innebär att den inte har bundits till något SSL-certifikat än. Eventuella HTTPS-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning.

## <a name="add-ssl-binding"></a>Lägg till SSL-bindning

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
I tabellen anpassad domän väljer du **Lägg till SSL-bindning** som visas i föregående bild.  
1. Välj ditt **certifikat** eller importera det.
1. Klicka på **Spara**.

    ![Lägg till SSL-bindning 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

När du har lagt till SSL-bindningen är domän tillståndet säker: **felfri**. 

![Lägg till SSL-bindning 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Använda HTTPS
Som standard kan alla fortfarande komma åt din app med HTTP, men du kan omdirigera alla HTTP-förfrågningar till HTTPS-porten.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
På din app-sida väljer du **anpassad domän** i det vänstra navigerings fältet. Ange sedan **https** till *True*.

![Lägg till SSL-bindning 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
När åtgärden har slutförts navigerar du till någon av HTTPS-URL: erna som pekar på din app. Observera att HTTP-URL: er inte fungerar.

## <a name="see-also"></a>Se även
* [Vad är Azure Key Vault?](../key-vault/general/overview.md)
* [Importera ett certifikat](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Starta din våren Cloud-App med hjälp av Azure CLI](./spring-cloud-quickstart.md)
