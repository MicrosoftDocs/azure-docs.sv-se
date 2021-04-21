---
title: Skapa ILB ASE v1
description: Skapa en App Service miljö med en intern lastbalanserare (ILB ASE). Det här dokumentet tillhandahålls endast för kunder som använder äldre v1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a6cc1cae640b97ecb3d95ee1e4f8ec34750e32d2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833027"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Skapa en ILB ASE med hjälp av Azure Resource Manager-mallar

> [!NOTE] 
> Den här artikeln handlar om App Service-miljön v1. Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med Introduktion till App Service-miljön](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
App Service miljöer kan skapas med en intern adress för ett virtuellt nätverk i stället för en offentlig VIP.  Den här interna adressen tillhandahålls av en Azure-komponent som kallas intern lastbalanserare (ILB).  En ILB ASE kan skapas med hjälp av Azure Portal.  Den kan också skapas med hjälp av automatisering via Azure Resource Manager mallar.  Den här artikeln går igenom de steg och den syntax som krävs för att skapa en ILB ASE med Azure Resource Manager mallar.

Det finns tre steg som krävs för att automatisera skapandet av en ILB ASE:

1. Först skapas bas-ASE i ett virtuellt nätverk med hjälp av en intern lastbalanseringsadress i stället för en offentlig VIP.  Som en del av det här steget tilldelas ett rotdomännamn till ILB ASE.
2. När ILB ASE har skapats laddas ett TLS/SSL-certifikat upp.  
3. Det uppladdade TLS/SSL-certifikatet tilldelas uttryckligen till ILB ASE som dess "standard-TLS/SSL-certifikat".  Det här TLS/SSL-certifikatet används för TLS-trafik till appar på ILB ASE när apparna adresseras med hjälp av den gemensamma rotdomänen som tilldelats ASE (t.ex. `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>Skapa bas-ILB ASE
Ett exempel Azure Resource Manager mall och dess associerade parameterfil finns på GitHub [här.][quickstartilbasecreate]

De flesta parametrarna i filen *azuredeploy.parameters.js* är gemensamma för att skapa både ILB ASE:er och ASE:er som är bundna till en offentlig VIP.  I listan nedan visas parametrar med särskild anteckning eller som är unika när du skapar en ILB ASE:

* *internalLoadBalancingMode:* I de flesta fall anger du detta till 3, vilket innebär att både HTTP/HTTPS-trafik på portarna 80/443 och kontroll-/datakanalportarna som lyssnar av FTP-tjänsten på ASE kommer att bindas till en intern adress för ett ILB-allokerat virtuellt nätverk.  Om den här egenskapen i stället anges till 2 kommer endast DE FTP-tjänstrelaterade portarna (både kontroll- och datakanaler) att bindas till en ILB-adress, medan HTTP/HTTPS-trafiken finns kvar på den offentliga VIP:en.
* *dnsSuffix:* Den här parametern definierar standardrotdomänen som ska tilldelas TILL ASE.  I den offentliga variationen av Azure App Service är standardrotdomänen för alla webbappar *azurewebsites.net*.  Men eftersom en ILB ASE är intern i en kunds virtuella nätverk är det inte meningsfullt att använda den offentliga tjänstens standardrotdomän.  I stället bör en ILB ASE ha en standardrotdomän som är meningsfull för användning i ett företags interna virtuella nätverk.  Ett hypotetiskt Contoso Corporation kan till exempel använda en standardrotdomän på *internal-contoso.com* för appar som endast är avsedda att matchas och vara tillgängliga i Contosos virtuella nätverk. 
* *ipSslAddressCount:* Den här parametern är automatiskt standardvärdet 0 i *filenazuredeploy.json* eftersom ILB ASE:er bara har en enda ILB-adress.  Det finns inga explicita IP-SSL-adresser för en ILB ASE och därför måste IP-SSL-adresspoolen för en ILB ASE anges till noll, annars uppstår ett etableringsfel. 

Närazuredeploy.parameters.js *på* filen har fyllts i för en ILB ASE kan ILB ASE sedan skapas med hjälp av följande PowerShell-kodfragment.  Ändra sökvägarna så att de matchar var Azure Resource Manager-mallfilerna finns på datorn.  Kom också ihåg att ange dina egna värden Azure Resource Manager namnet på distributionen och resursgruppens namn.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

När Azure Resource Manager mallen har skickats tar det några timmar innan ILB ASE skapas.  När skapandet är klart visas ILB ASE i portalens UX i listan över App Service-miljöer för prenumerationen som utlöste distributionen.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Ladda upp och konfigurera TLS/SSL-standardcertifikatet
När ILB ASE har skapats ska ett TLS/SSL-certifikat associeras med ASE som "standard"-TLS/SSL-certifikatanvändning för att upprätta TLS-/SSL-anslutningar till appar.  Om ase-standard-DNS-suffixet är *internal-contoso.com* och fortsätter med det hypotetiska Exemplet contoso Corporation kräver en anslutning till ett TLS/SSL-certifikat som är giltigt för *`https://some-random-app.internal-contoso.com`* **.internal-contoso.com*. 

Det finns en mängd olika sätt att hämta ett giltigt TLS/SSL-certifikat, inklusive interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare och använda ett själv signerat certifikat.  Oavsett källan för TLS/SSL-certifikatet måste följande certifikatattribut konfigureras korrekt:

* *Ämne:* Det här attributet måste vara inställt på **.your-root-domain-here.com*
* *Alternativt namn för ämne:* Det här attributet måste innehålla både **.your-root-domain-here.com* och **.scm.your-root-domain-here.com*.  Orsaken till den andra posten är att TLS-anslutningar till SCM/Kudu-webbplatsen som är associerad med varje app görs med hjälp av en adress i *formuläret your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt TLS/SSL-certifikat krävs två ytterligare förberedande steg.  TLS/SSL-certifikatet måste konverteras/sparas som en PFX-fil.  Kom ihåg att PFX-filen måste innehålla alla mellanliggande certifikat och rotcertifikat och även måste skyddas med ett lösenord.

Den resulterande PFX-filen måste konverteras till en base64-sträng eftersom TLS/SSL-certifikatet laddas upp med hjälp av en Azure Resource Manager mall.  Eftersom Azure Resource Manager mallar är textfiler måste PFX-filen konverteras till en base64-sträng så att den kan inkluderas som en parameter i mallen.

PowerShell-kodfragmentet nedan visar ett exempel på hur du genererar ett själv signerat certifikat, exporterar certifikatet som en PFX-fil, konverterar PFX-filen till en base64-kodad sträng och sedan sparar den base64-kodade strängen i en separat fil.  PowerShell-koden för base64-kodning har anpassats från [PowerShell-skriptbloggen][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

När TLS/SSL-certifikatet har genererats och konverterats till en base64-kodad sträng kan exempelmallen Azure Resource Manager på GitHub för att konfigurera [TLS/SSL-standardcertifikatet][configuringDefaultSSLCertificate] användas.

Parametrarna i *azuredeploy.parameters.jspå* filen visas nedan:

* *appServiceEnvironmentName:* Namnet på den ILB ASE som konfigureras.
* *existingAseLocation:* Textsträng som innehåller den Azure-region där ILB ASE distribuerades.  Till exempel: "USA, södra centrala".
* *pfxBlobString:* Den based64-kodade strängrepresentationen av PFX-filen.  Med hjälp av kodfragmentet som visades tidigare kopierar du strängen i "exportedcert.pfx.b64" och klistrar in den som värdet för *attributet pfxBlobString.*
* *password*: Lösenordet som används för att skydda PFX-filen.
* *certificateThumbprint:* Certifikatets tumavtryck.  Om du hämtar det här värdet från PowerShell (t.ex. *$certificate. Tumavtryck* från det tidigare kodfragmentet) kan du använda värdet som det är.  Men om du kopierar värdet från dialogrutan Windows-certifikat, kom ihåg att ta bort överflödiga blanksteg.  *CertificateThumbprint* bör se ut ungefär så här: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName:* En egen egen strängidentifierare som du väljer för att identifiera certifikatet.  Namnet används som en del av den unika Azure Resource Manager för *entiteten Microsoft.Web/certificates* som representerar TLS/SSL-certifikatet.  Namnet måste **sluta** med följande suffix:  \_ yourASENameHere_InternalLoadBalancingASE.  Det här suffixet används av portalen som en indikator på att certifikatet används för att skydda en ILB-aktiverad ASE.

Ett förkortat exempel på *azuredeploy.parameters.jspå* visas nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appServiceEnvironmentName": {
            "value": "yourASENameHere"
        },
        "existingAseLocation": {
            "value": "East US 2"
        },
        "pfxBlobString": {
            "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
        },
        "password": {
            "value": "PASSWORDGOESHERE"
        },
        "certificateThumbprint": {
            "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
        },
        "certificateName": {
            "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
        }
    }
}
```

Närazuredeploy.parameters.js *på* filen har fyllts i kan TLS/SSL-standardcertifikatet konfigureras med hjälp av följande PowerShell-kodfragment.  Ändra sökvägarna så att de Azure Resource Manager på datorn.  Kom också ihåg att ange dina egna värden Azure Resource Manager namnet på distributionen och resursgruppens namn.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

När Azure Resource Manager mallen har skickats tar det ungefär 40 minuter per ASE-frontend att tillämpa ändringen.  Med en ase-standardstorlek som använder två frontend-ändar tar det till exempel ungefär en timme och tjugo minuter att slutföra mallen.  När mallen körs kommer ASE inte att kunna skalas.  

När mallen har slutförts kan appar på ILB ASE nås via HTTPS och anslutningarna skyddas med TLS/SSL-standardcertifikatet.  TLS/SSL-standardcertifikatet används när appar på ILB ASE adresseras med en kombination av programnamnet plus standardvärdnamnet.  Till exempel *`https://mycustomapp.internal-contoso.com`* använder TLS/SSL-standardcertifikatet för **.internal-contoso.com*.

Men precis som appar som körs på den offentliga tjänsten för flera innehavare kan utvecklare också konfigurera anpassade värdnamn för enskilda appar och sedan konfigurera unika SNI TLS/SSL-certifikatbindningar för enskilda appar.  

## <a name="getting-started"></a>Komma igång
Information om hur du kommer App Service miljöer finns i [Introduktion till App Service-miljön](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

