---
title: Skapa en ASE med ARM
description: Lär dig hur du skapar en extern miljö eller ILB App Service miljö med hjälp av en Azure Resource Manager mall.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 10482538c819f9713e9940cffbeb5a1f966ddcf5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832058"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Skapa en ASE med en Azure Resource Manager mall

## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service(ASE) kan skapas med en internettillgänglig slutpunkt eller en slutpunkt på en intern adress i ett virtuellt Azure-nätverk (VNet). När den skapas med en intern slutpunkt tillhandahålls slutpunkten av en Azure-komponent som kallas intern lastbalanserare (ILB). ASE på en intern IP-adress kallas för en ILB ASE. ASE med en offentlig slutpunkt kallas extern ASE. 

En ASE kan skapas med hjälp av Azure Portal eller en Azure Resource Manager mall. Den här artikeln går igenom de steg och syntax som du behöver för att skapa en extern ASE eller ILB ASE med Resource Manager mallar. Information om hur du skapar en ASE i Azure Portal finns i Make an External ASE (Skapa en extern [ASE)][MakeExternalASE] eller Make an ILB ASE (Skapa [en ILB ASE).][MakeILBASE]

När du skapar en ASE i Azure Portal kan du skapa ditt VNet samtidigt eller välja ett existerande VNet att distribuera till. När du skapar en ASE från en mall måste du börja med: 

* Ett Resource Manager VNet.
* Ett undernät i det virtuella nätverket. Vi rekommenderar en ASE-undernätsstorlek `/24` på 256 adresser för framtida tillväxt- och skalningsbehov. När ASE har skapats kan du inte ändra storleken.
* Resurs-ID från ditt VNet. Du kan hämta den här informationen från Azure Portal under egenskaperna för ditt virtuella nätverk.
* Den prenumeration som du vill distribuera till.
* Den plats som du vill distribuera till.

Så här automatiserar du ase-skapandet:

1. Skapa ASE från en mall. Om du skapar en extern ASE är du klar efter det här steget. Om du skapar en ILB ASE finns det några fler saker att göra.

2. När din ILB ASE har skapats laddas ett TLS/SSL-certifikat som matchar din ILB ASE-domän upp.

3. Det uppladdade TLS/SSL-certifikatet tilldelas till ILB ASE som dess "standard"-TLS/SSL-certifikat.  Det här certifikatet används för TLS/SSL-trafik till appar på ILB ASE när de använder den gemensamma rotdomän som har tilldelats ASE (till exempel `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Skapa ASE
En Resource Manager mall som skapar en ASE och dess associerade parameterfil finns [i ett exempel][quickstartasev2create] på GitHub.

Om du vill skapa en ILB ASE använder du följande Resource Manager [exempelmallen][quickstartilbasecreate]. De tillgodoser det användningsfallet. De flesta parametrarna i filen *azuredeploy.parameters.jsär* gemensamma för skapandet av ILB ASE:er och externa ASE:er. I följande lista visas parametrar med särskild anteckning eller som är unika när du skapar en ILB ASE:

* *internalLoadBalancingMode:* Ange i de flesta fall till 3, vilket innebär att både HTTP/HTTPS-trafik på portarna 80/443 och kontroll-/datakanalportarna som lyssnar av FTP-tjänsten på ASE kommer att bindas till en intern adress för ett ILB-allokerat virtuellt nätverk. Om den här egenskapen är inställd på 2 är det bara DE FTP-tjänstrelaterade portarna (både kontroll- och datakanaler) som är bundna till en ILB-adress. HTTP/HTTPS-trafiken finns kvar på den offentliga VIP:en.
* *dnsSuffix:* Den här parametern definierar standardrotdomänen som har tilldelats TILL ASE. I den offentliga variationen av Azure App Service är standardrotdomänen för alla webbappar *azurewebsites.net*. Eftersom en ILB ASE är intern i en kunds virtuella nätverk är det inte meningsfullt att använda den offentliga tjänstens standardrotdomän. I stället bör en ILB ASE ha en standardrotdomän som är meningsfull för användning i ett företags interna virtuella nätverk. Contoso Corporation kan till exempel använda en *standardrotdomän* på internal-contoso.com för appar som är avsedda att vara matchningsbara och endast tillgängliga i Contosos virtuella nätverk. 
* *ipSslAddressCount:* Den här parametern får automatiskt värdet 0 i *filenazuredeploy.jspå* eftersom ILB ASE:er bara har en enda ILB-adress. Det finns inga explicita IP-SSL-adresser för en ILB ASE. Ip-SSL-adresspoolen för en ILB ASE måste därför vara inställd på noll. Annars uppstår ett etableringsfel. 

När koden *azuredeploy.parameters.jspå* filen har fyllts i skapar du ASE med hjälp av PowerShell-kodfragmentet. Ändra sökvägarna så att de Resource Manager mallfilsplatserna på datorn. Kom ihåg att ange dina egna värden Resource Manager namnet på distributionen och resursgruppens namn:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär en timme för ASE att skapas. Sedan visas ASE i portalen i listan över ASE:er för den prenumeration som utlöste distributionen.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Ladda upp och konfigurera TLS/SSL-standardcertifikatet
Ett TLS/SSL-certifikat måste associeras med ASE som TLS/SSL-standardcertifikat som används för att upprätta TLS-anslutningar till appar. Om ASE:s DNS-standardsuffix *är internal-contoso.com*, kräver en anslutning till ett TLS/SSL-certifikat som är giltigt `https://some-random-app.internal-contoso.com` för **.internal-contoso.com*. 

Skaffa ett giltigt TLS-/SSL-certifikat genom att använda interna certifikatutfärdare, köpa ett certifikat från en extern utfärdare eller använda ett själv signerat certifikat. Oavsett källan för TLS/SSL-certifikatet måste följande certifikatattribut vara korrekt konfigurerade:

* **Ämne:** Det här attributet måste anges till **.your-root-domain-here.com*.
* **Alternativt namn för ämne:** Det här attributet måste innehålla både **.your-root-domain-here.com* och **.scm.your-root-domain-here.com*. TLS-anslutningar till den SCM/Kudu-webbplats som är associerad med varje app använder en adress för formuläret *your-app-name.scm.your-root-domain-here.com*.

Med ett giltigt TLS/SSL-certifikat krävs ytterligare två förberedande steg. Konvertera/spara TLS/SSL-certifikatet som en PFX-fil. Kom ihåg att PFX-filen måste innehålla alla mellanliggande certifikat och rotcertifikat. Skydda den med ett lösenord.

PFX-filen måste konverteras till en base64-sträng eftersom TLS/SSL-certifikatet laddas upp med hjälp av en Resource Manager mall. Eftersom Resource Manager mallar är textfiler måste PFX-filen konverteras till en base64-sträng. På så sätt kan den inkluderas som en parameter i mallen.

Använd följande PowerShell-kodfragment för att:

* Generera ett själv signerat certifikat.
* Exportera certifikatet som en PFX-fil.
* Konvertera PFX-filen till en base64-kodad sträng.
* Spara den base64-kodade strängen i en separat fil. 

Den här PowerShell-koden för base64-kodning har anpassats från [PowerShell-skriptbloggen][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com&quot;,&quot;*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

När TLS/SSL-certifikatet har genererats och konverterats till en base64-kodad sträng [][quickstartconfiguressl] använder du exempelmallen Resource Manager Konfigurera ssl-standardcertifikatet på GitHub. 

Parametrarna i *azuredeploy.parameters.jspå* filen visas här:

* *appServiceEnvironmentName:* Namnet på den ILB ASE som konfigureras.
* *existingAseLocation:* Textsträng som innehåller den Azure-region där ILB ASE distribuerades.  Till exempel: "USA, södra centrala".
* *pfxBlobString:* Den based64-kodade strängrepresentationen av PFX-filen. Använd kodfragmentet som visades tidigare och kopiera strängen i "exportedcert.pfx.b64". Klistra in det som värdet för *attributet pfxBlobString.*
* *password*: Lösenordet som används för att skydda PFX-filen.
* *certificateThumbprint:* Certifikatets tumavtryck. Om du hämtar det här värdet från PowerShell (till exempel *$certificate. Tumavtryck* från det tidigare kodfragmentet) kan du använda värdet som det är. Om du kopierar värdet från dialogrutan Windows-certifikat bör du komma ihåg att ta bort de överflödiga blankstegen. *CertificateThumbprint* bör se ut ungefär så här: AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName:* Ett eget eget sträng-ID som du väljer för att identifiera certifikatet. Namnet används som en del av den unika Resource Manager för entiteten *Microsoft.Web/certificates* som representerar TLS/SSL-certifikatet. Namnet måste *sluta* med följande suffix: \_ yourASENameHere_InternalLoadBalancingASE. Den Azure Portal använder det här suffixet som en indikator på att certifikatet används för att skydda en ILB-aktiverad ASE.

Ett förkortat exempel på *azuredeploy.parameters.jspå* visas här:

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

När filen *azuredeploy.parameters.jshar* fyllts i konfigurerar du TLS/SSL-standardcertifikatet med hjälp av PowerShell-kodfragmentet. Ändra filsökvägarna så att de Resource Manager filerna finns på datorn. Kom ihåg att ange dina egna värden Resource Manager namnet på distributionen och resursgruppens namn:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Det tar ungefär 40 minuter per ASE-frontend att tillämpa ändringen. För en ASE av standardstorlek som använder två frontend-ändarna tar det till exempel cirka en timme och 20 minuter att slutföra mallen. Medan mallen körs kan ASE inte skalas.  

När mallen är klar kan appar på ILB ASE nås via HTTPS. Anslutningarna skyddas med hjälp av TLS/SSL-standardcertifikatet. TLS/SSL-standardcertifikatet används när appar på ILB ASE hanteras med hjälp av en kombination av programnamnet och standardvärdnamnet. Använder till exempel `https://mycustomapp.internal-contoso.com` TLS/SSL-standardcertifikatet för **.internal-contoso.com*.

Men precis som appar som körs på den offentliga multitenant-tjänsten kan utvecklare konfigurera anpassade värdnamn för enskilda appar. De kan också konfigurera unika SNI TLS/SSL-certifikatbindningar för enskilda appar.

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##
App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2.

I ASEv1 hanterar du alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut App Service plan måste du skala ut den arbetspool som ska vara värd för den.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Det omfattar virtuella processorer som används för frontend-datorer eller arbetare som inte är värdar för några arbetsbelastningar. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

Information om hur du skapar en ASEv1 Resource Manager en mall finns i Skapa en [ILB ASE v1][ILBASEv1Template]med en Resource Manager mall .


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
