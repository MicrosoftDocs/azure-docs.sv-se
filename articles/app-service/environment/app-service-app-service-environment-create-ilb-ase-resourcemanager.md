---
title: Skapa ILB ASE v1
description: Skapa en App Service-miljö med en intern belastningsutjämnare (ILB ASE). Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a03b791f37868010e107214ddcb7cf42174e4e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833561"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Skapa en ILB ASE med hjälp av Azure Resource Manager-mallar

> [!NOTE] 
> Den här artikeln gäller App Service-miljön v1. Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
App Service miljöer kan skapas med en intern virtuell nätverks adress i stället för en offentlig VIP.  Den här interna adressen tillhandahålls av en Azure-komponent som kallas intern belastningsutjämnare (ILB).  Du kan skapa en ILB-ASE med hjälp av Azure Portal.  Det kan också skapas med Automation med hjälp av Azure Resource Manager mallar.  Den här artikeln beskriver de steg och den syntax som krävs för att skapa en ILB-ASE med Azure Resource Manager mallar.

Det finns tre steg när du vill automatisera skapandet av en ILB-ASE:

1. Först skapas bas-ASE i ett virtuellt nätverk med hjälp av en intern belastnings Utjämnings adress i stället för en offentlig VIP.  Som en del av det här steget tilldelas ett rot domän namn till ILB-ASE.
2. När ILB-ASE har skapats laddas ett TLS/SSL-certifikat.  
3. Det uppladdade TLS/SSL-certifikatet tilldelas uttryckligen till ILB-ASE som "standard" TLS/SSL-certifikat.  Detta TLS/SSL-certifikat används för TLS-trafik till appar på ILB-ASE när apparna adresseras med den gemensamma rot domän som tilldelats till ASE (t. ex. `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>Skapa bas ILB-ASE
Ett exempel på Azure Resource Manager mall, och dess tillhör ande parameter fil, finns på GitHub [här][quickstartilbasecreate].

De flesta av parametrarna i *azuredeploy.parameters.jsi* filen är vanliga för att skapa både ILB-ASE, samt ASE som är kopplade till en offentlig VIP.  I listan nedan anropas parametrar för särskild anteckning, eller som är unika, när du skapar en ILB-ASE:

* *internalLoadBalancingMode*: i de flesta fall anges detta till 3, vilket innebär både http/https-trafik på portarna 80/443 och kontroll-och data kanals portarna som har avlyssnats av FTP-tjänsten på ASE, binds till en ILB allokerad virtuell nätverks adress.  Om den här egenskapen anges i stället till 2, kommer bara FTP-tjänstens relaterade portar (både kontroll-och data kanaler) att bindas till en ILB-adress, medan HTTP/HTTPS-trafiken finns kvar på den offentliga VIP.
* *dnsSuffix*: den här parametern definierar den standard rot domän som ska tilldelas ASE.  I den offentliga variationen av Azure App Service, är standard rot domänen för alla webbappar *azurewebsites.net*.  Eftersom en ILB-ASE är intern för kundens virtuella nätverk är det dock inte klokt att använda den offentliga tjänstens standard rot domän.  I stället bör en ILB-ASE ha en standard rot domän som är bra att använda i företagets interna virtuella nätverk.  Ett hypotetisk contoso-företag kan till exempel använda en standard rot domän för *Internal-contoso.com* för appar som endast är avsedda att matchas och vara tillgängliga i Contosos virtuella nätverk. 
* *ipSslAddressCount*: den här parametern tilldelas automatiskt värdet 0 i *azuredeploy.jspå* filen eftersom ILB ASE bara har en enda ILB-adress.  Det finns inga explicita IP-SSL-adresser för en ILB-ASE, och därför måste IP-SSL-adresspoolen för en ILB-ASE anges till noll, annars uppstår ett etablerings fel. 

När *azuredeploy.parameters.jsi* filen har fyllts i för en ILB-ASE kan ILB ASE sedan skapas med hjälp av följande PowerShell-kodfragment.  Ändra fil Sök vägarna så att de matchar var Azure Resource Manager mallfiler finns på datorn.  Kom även ihåg att ange dina egna värden för Azure Resource Manager distributions namn och resurs grupp namn.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

När Azure Resource Manager-mallen har skickats tar det några timmar innan ILB-ASE skapas.  När processen har skapats visas ILB-ASE i portalens UX i listan över App Service miljöer för den prenumeration som utlöste distributionen.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Ladda upp och konfigurera "standard" TLS/SSL-certifikatet
När ILB-ASE har skapats ska ett TLS/SSL-certifikat associeras med ASE som "standard" TLS/SSL-certifikat används för att upprätta TLS/SSL-anslutningar till appar.  Genom att fortsätta med det hypotetiska Contoso Corporation-exemplet, om ASE standard-DNS-suffixet är *Internal-contoso.com*, så kräver en anslutning till *`https://some-random-app.internal-contoso.com`* ett TLS/SSL-certifikat som är giltigt för **. Internal-contoso.com*. 

Det finns flera olika sätt att skaffa ett giltigt TLS/SSL-certifikat, inklusive interna certifikat utfärdare, köpa ett certifikat från en extern utfärdare och använda ett självsignerat certifikat.  Oberoende av källan till TLS/SSL-certifikatet måste följande attribut för certifikaten konfigureras korrekt:

* *Subject*: det här attributet måste anges till **. your-Root-Domain-here.com*
* *Alternativt namn för certifikat mottagare*: det här attributet måste innehålla både **. your-Root-Domain-here.com* och **. scm.your-Root-Domain-here.com*.  Orsaken till den andra posten är att TLS-anslutningar till den SCM/kudu-webbplats som är kopplad till varje app kommer att göras med hjälp av en adress i formatet *Your-App-Name.scm.your-Root-Domain-here.com*.

Med ett giltigt TLS/SSL-certifikat i handen krävs ytterligare två förberedande steg.  TLS/SSL-certifikatet måste konverteras/sparas som en PFX-fil.  Kom ihåg att. pfx-filen måste innehålla alla mellanliggande och rot certifikat och måste också skyddas med ett lösen ord.

Sedan måste den resulterande PFX-filen konverteras till en Base64-sträng eftersom TLS/SSL-certifikatet överförs med hjälp av en Azure Resource Manager-mall.  Eftersom Azure Resource Manager mallar är Textfiler måste. pfx-filen konverteras till en Base64-sträng så att den kan inkluderas som en parameter för mallen.

I PowerShell-kodfragmentet nedan visas ett exempel på att skapa ett självsignerat certifikat, exportera certifikatet som en. pfx-fil, konvertera. pfx-filen till en Base64-kodad sträng och sedan spara den base64-kodade strängen i en separat fil.  PowerShell-koden för base64-kodning har anpassats från [PowerShell-skriptets blogg][examplebase64encoding].

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

När TLS/SSL-certifikatet har genererats och konverterats till en Base64-kodad sträng kan du använda exemplet Azure Resource Manager mall på GitHub för att [Konfigurera standard TLS/SSL-certifikatet][configuringDefaultSSLCertificate] .

Parametrarna i *azuredeploy.parameters.jsi* filen visas nedan:

* *appServiceEnvironmentName*: namnet på den ILB ASE som konfigureras.
* *existingAseLocation*: text sträng som innehåller den Azure-region där ILB-ASE har distribuerats.  Till exempel: "södra centrala USA".
* *pfxBlobString*: den based64-kodade sträng representationen för. pfx-filen.  Med hjälp av kodfragmentet som visas tidigare kopierar du strängen som finns i "exportedcert. pfx. B64" och klistrar in den som värdet för attributet *pfxBlobString* .
* *lösen ord*: lösen ordet som används för att skydda. pfx-filen.
* *certificateThumbprint*: certifikatets tumavtryck.  Om du hämtar det här värdet från PowerShell (t. ex. *$Certificate. Tumavtryck* från det tidigare kodfragmentet) kan du använda värdet som-är.  Om du däremot kopierar värdet från dialog rutan Windows-certifikat, måste du komma ihåg att utesluta de extra utrymmena.  *CertificateThumbprint* bör se ut ungefär så här: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: ett eget sträng-ID som du väljer används för att identifiera certifikatet.  Namnet används som en del av det unika Azure Resource Manager ID: t för entiteten *Microsoft. Web/Certificate* som representerar TLS/SSL-certifikatet.  Namnet **måste** sluta med följande suffix:  \_ yourASENameHere_InternalLoadBalancingASE.  Det här suffixet används av portalen som en indikator att certifikatet används för att skydda en ILB-aktiverad ASE.

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

När *azuredeploy.parameters.jspå* filen har fyllts i kan standard-TLS/SSL-certifikatet konfigureras med hjälp av följande PowerShell-kodfragment.  Ändra fil Sök vägarna så att de matchar var Azure Resource Manager mallfiler finns på datorn.  Kom även ihåg att ange dina egna värden för Azure Resource Manager distributions namn och resurs grupp namn.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

När Azure Resource Manager-mallen har skickats tar det ungefär 40 minuter per ASE-klient för att tillämpa ändringen.  Med en standard storleks ASE som använder två frontend-platser tar mallen till exempel cirka en timme och tjugo minuter att slutföra.  När mallen körs kan ASE inte skalas.  

När mallen har slutförts kan appar på ILB-ASE nås via HTTPS och anslutningarna skyddas med hjälp av standard TLS/SSL-certifikatet.  Standard TLS/SSL-certifikatet kommer att användas när appar på ILB-ASE adresseras med en kombination av program namnet plus standard värd namnet.  Använd till exempel *`https://mycustomapp.internal-contoso.com`* standard TLS/SSL-certifikat för **. Internal-contoso.com*.

Men precis som appar som körs på den offentliga tjänsten för flera innehavare kan utvecklare också konfigurera anpassade värdnamn för enskilda appar och sedan konfigurera unika SNI TLS/SSL-certifikat bindningar för enskilda appar.  

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [Introduktion till App Service-miljön](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

