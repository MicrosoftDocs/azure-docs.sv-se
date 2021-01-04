---
title: Konfigurera ett labb att använda Fjärrskrivbordsgateway i Azure DevTest Labs
description: Lär dig hur du konfigurerar ett labb i Azure DevTest Labs med en Fjärrskrivbordsgateway för att säkerställa säker åtkomst till de virtuella labb datorerna utan att behöva exponera RDP-porten.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dcf5191dea64c3d7bf28b9ce1c616d3d2defb73e
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695686"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurera ditt labb i Azure DevTest Labs att använda en Fjärrskrivbordsgateway
I Azure DevTest Labs kan du konfigurera en Fjärrskrivbordsgateway för ditt labb för att säkerställa säker åtkomst till de virtuella datorerna i labbet (VM) utan att behöva exponera RDP-porten. Labbet är en central plats där dina labb användare kan visa och ansluta till alla virtuella datorer som de har åtkomst till. Knappen **Anslut** på sidan **virtuell dator** skapar en datorspecifik RDP-fil som du kan öppna för att ansluta till datorn. Du kan anpassa och säkra RDP-anslutningen ytterligare genom att ansluta ditt labb till en Fjärrskrivbordsgateway. 

Den här metoden är säkrare eftersom labb användaren autentiserar direkt till gateway-datorn eller kan använda företagets autentiseringsuppgifter på en domänansluten Gateway-dator för att ansluta till sina datorer. Labbet stöder också autentisering med token till gateway-datorn som gör det möjligt för användare att ansluta till sina virtuella labb datorer utan att ha RDP-porten exponerad för Internet. Den här artikeln vägleder dig genom ett exempel på hur du konfigurerar ett labb som använder token-autentisering för att ansluta till labb datorer.

## <a name="architecture-of-the-solution"></a>Lösningens arkitektur

![Lösningens arkitektur](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Åtgärden [Hämta RDP-filinnehåll](/rest/api/dtl/virtualmachines/getrdpfilecontents) anropas när du väljer knappen **Anslut** . 1. 
1. Åtgärden hämta RDP-filinnehåll anropar `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` för att begära en autentiseringstoken.
    1. `{gateway-hostname}` är Gateway-värdnamnet som anges på sidan **labb inställningar** för ditt labb i Azure Portal. 
    1. `{lab-machine-name}` är namnet på den dator som du försöker ansluta till.
    1. `{port-number}` är porten som anslutningen måste göras på. Vanligt vis är den här porten 3389. Om den virtuella labb datorn använder den [delade IP-](devtest-lab-shared-ip.md) funktionen i DevTest Labs är porten annorlunda.
1. Gatewayen för fjärrskrivbordsgateway överlåter anropet från `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` till en Azure-funktion för att generera autentiseringstoken. DevTest Labs-tjänsten innehåller automatiskt funktions nyckeln i begär ande huvudet. Funktions nyckeln ska sparas i Labbets nyckel valv. Namnet på hemligheten visas som **hemlighet för gateway-token** på sidan **labb inställningar** för labbet.
1. Azure-funktionen förväntas returnera en token för certifikatbaserad autentisering mot gateway-datorn.  
1. Åtgärden hämta RDP-filinnehåll returnerar sedan den fullständiga RDP-filen, inklusive autentiseringsinformationen.
1. Du öppnar RDP-filen med det önskade RDP-anslutnings programmet. Kom ihåg att inte alla RDP-anslutningar stöder token-autentisering. Autentiseringstoken har ett förfallo datum som anges av Function-appen. Upprätta anslutningen till den virtuella labb datorn innan token upphör att gälla.
1. När den fjärranslutna datorn för fjärrskrivbordsgateway autentiserar token i RDP-filen vidarebefordras anslutningen till din labb dator.

### <a name="solution-requirements"></a>Lösningskrav
För att arbeta med DevTest Labs token-autentisering finns det några konfigurations krav för gateway-datorerna, Domain Name Services (DNS) och functions.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Krav för datorer med Fjärrskrivbordsgateway
- TLS/SSL-certifikatet måste vara installerat på gateway-datorn för att du ska kunna hantera HTTPS-trafik. Certifikatet måste matcha det fullständigt kvalificerade domän namnet (FQDN) för belastningsutjämnaren för gateway-servergruppen eller datorns FQDN om det bara finns en dator. Jokertecken för jokertecken/SSL-certifikat fungerar inte.  
- Ett signerings certifikat som installerats på gateway-datorer. Skapa ett signerings certifikat med hjälp av [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) skript.
- Installera modulen för [pluggable-autentisering](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) som stöder token-autentisering för fjärrskrivbordsgateway. Ett exempel på en sådan modul är `RDGatewayFedAuth.msi` som medföljer [System Center Virtual Machine Manager (VMM) avbildningar](/system-center/vmm/install-console?view=sc-vmm-1807). Mer information om System Center finns i [dokumentationen för System Center](/system-center/) och [pris information](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Gateway-servern kan hantera begär Anden som görs till `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    Gateway-hostname är FQDN för belastningsutjämnaren för gateway-servergruppen eller FQDN för själva datorn om det bara finns en dator. `{lab-machine-name}`Är namnet på labb datorn som du försöker ansluta till och `{port-number}` är porten som anslutningen ska göras på.  Som standard är den här porten 3389.  Men om den virtuella datorn använder den [delade IP-](devtest-lab-shared-ip.md) funktionen i DevTest Labs är porten annorlunda.
- Modulen för [programbegäran för programbegäran](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) för Internet Information Server (IIS) kan användas för att omdirigera `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` begär anden till Azure-funktionen som hanterar begäran om att hämta en token för autentisering.


## <a name="requirements-for-azure-function"></a>Krav för Azure Function
Azure Function hanterar begäran med formatet `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` och returnerar autentiseringstoken baserat på samma signerings certifikat som installerats på Gateway-datorerna. `{function-app-uri}`Är den URI som används för att få åtkomst till funktionen. Funktions nyckeln skickas automatiskt i rubriken för begäran. En exempel funktion finns i [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>Krav för nätverk

- DNS för det FQDN som är associerat med TLS/SSL-certifikatet som är installerat på Gateway-datorerna måste dirigera trafiken till gateway-datorn eller belastningsutjämnaren för gateway-datorns grupp.
- Om labb datorn använder privata IP-adresser måste det finnas en nätverks Sök väg från gateway-datorn till labb datorn, antingen genom att dela samma virtuella nätverk eller använda peer-tilldelade virtuella nätverk.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurera labbet att använda token-autentisering 
Det här avsnittet visar hur du konfigurerar ett labb för att använda en dator för fjärrskrivbordsgateway som stöder token-autentisering. Det här avsnittet beskriver inte hur du konfigurerar en servergrupp för fjärrskrivbordsgateway. Mer information finns i [exemplet på att skapa en](#sample-to-create-a-remote-desktop-gateway) Fjärrskrivbordsgateway-sektion i slutet av den här artikeln. 

Innan du uppdaterar Lab-inställningarna lagrar du nyckeln som behövs för att kunna köra funktionen för att returnera en autentiseringstoken i Labbets nyckel valv. Du kan hämta funktions nyckel svärdet på sidan **Hantera** för funktionen i Azure Portal. Mer information om hur du sparar en hemlighet i ett nyckel valv finns i [lägga till en hemlighet till Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Spara namnet på hemligheten för senare användning.

Kör följande Azure CLI-kommando för att hitta ID: t för labbets nyckel valv: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurera labbet att använda token-autentisering med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du ditt **labb**.
1. På labb sidan väljer du **konfiguration och principer**.
1. På den vänstra menyn går du till avsnittet **Inställningar** och väljer **labb inställningar**.
1. I avsnittet **fjärr skrivbord** anger du det fullständigt kvalificerade domän namnet (FQDN) eller IP-adressen för gateway-datorn eller Server gruppen för **Gateway-värdnamn** . Värdet måste matcha det fullständiga domän namnet för TLS/SSL-certifikatet som används på gateway-datorer.

    ![Fjärr skrivbords alternativ i labb inställningar](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. I avsnittet **fjärr skrivbord** , för **Gateway-token** hemlighet, anger du namnet på hemligheten som skapades tidigare. Det här värdet är inte själva funktions tangenten, men namnet på hemligheten i labb nyckel valvet som innehåller funktions nyckeln.

    ![Hemlighet för gateway-token i labb inställningar](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Spara** Något.

    > [!NOTE] 
    > Genom att klicka på **Spara** godkänner du [licens villkoren](https://www.microsoft.com/licensing/product-licensing/products)för fjärrskrivbordsgateway. Mer information om fjärr-Gateway finns i [Välkommen till Fjärrskrivbordstjänster](/windows-server/remote/remote-desktop-services/Welcome-to-rds) och [distribuera din fjärr skrivbords miljö](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Om du vill konfigurera labbet via Automation, se [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) för ett PowerShell-skript för att ange **Gateway-värdnamn** och inställningar för gateway- **token** . [Azure DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab) innehåller också en Azure Resource Manager-mall som skapar eller uppdaterar ett labb med **Gateway-värdnamn** och inställningar för gateway- **token** .

## <a name="configure-network-security-group"></a>Konfigurera nätverks säkerhets grupp
För att ytterligare skydda labbet kan en nätverks säkerhets grupp (NSG) läggas till i det virtuella nätverk som används av virtuella labb datorer. Instruktioner för hur du konfigurerar en NSG finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](../virtual-network/manage-network-security-group.md).

Här är ett exempel på en NSG som endast tillåter trafik som först går igenom gatewayen för att komma åt labb datorer. Källan i den här regeln är IP-adressen för den enskilda gateway-datorn eller belastnings utjämningens IP-adress framför Gateway-datorerna.

![Nätverks säkerhets grupp – regler](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Exempel för att skapa en gateway för fjärr skrivbord

> [!NOTE] 
> Genom att använda exempel mallarna godkänner du [licens villkoren](https://www.microsoft.com/licensing/product-licensing/products)för fjärrskrivbordsgateway. Mer information om fjärr-Gateway finns i [Välkommen till Fjärrskrivbordstjänster](/windows-server/remote/remote-desktop-services/Welcome-to-rds) och [distribuera din fjärr skrivbords miljö](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Azure DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab) innehåller några exempel som hjälper dig att konfigurera de resurser som behövs för att använda token-autentisering och fjärr skrivbords-Gateway med DevTest Labs. Dessa exempel innehåller Azure Resource Manager mallar för gateway-datorer, Lab-inställningar och Function-appar.

Följ dessa steg om du vill konfigurera en exempel lösning för Server gruppen för fjärrskrivbordsgateway.

1. Skapa ett signerings certifikat.  Kör [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Spara tumavtrycket, lösen ordet och base64-kodningen för det skapade certifikatet.
2. Hämta ett TLS/SSL-certifikat. FQDN som är associerat med TLS/SSL-certifikatet måste vara för den domän som du styr. Spara tumavtryck, lösen ord och base64-kodning för det här certifikatet. Använd följande kommandon för att hämta tumavtryck med PowerShell.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Använd följande kommando för att hämta base64-kodning med PowerShell.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Hämta filer från [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    Mallen kräver åtkomst till några andra Resource Manager-mallar och relaterade resurser på samma bas-URI. Kopiera alla filer från [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) och RDGatewayFedAuth.msi till en BLOB-behållare i ett lagrings konto.  
4. Distribuera **azuredeploy.js** från [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . Mallen använder följande parametrar:
    - adminUsername – krävs.  Administratörens användar namn för gateway-datorerna.
    - adminPassword – krävs. Lösen ordet för administratörs kontot för gateway-datorerna.
    - instanceCount – antal gateway-datorer som ska skapas.  
    - alwaysOn – anger om du vill behålla den skapade Azure Functions-appen i ett varmt tillstånd eller inte. Att behålla Azure Functions-appen kommer att undvika fördröjningar när användarna först försöker ansluta till sin virtuella labb dator, men det kostar ett kostnads konsekvenser.  
    - tokenLifetime – hur lång tid som den skapade token är giltig. Formatet är HH: MM: SS.
    - Elementet sslcertificate – base64-kodningen för TLS/SSL-certifikatet för gateway-datorn.
    - sslCertificatePassword – lösen ordet för TLS/SSL-certifikatet för gateway-datorn.
    - sslCertificateThumbprint – certifikatets tumavtryck för identifiering i det lokala certifikat arkivet för TLS/SSL-certifikatet.
    - signCertificate – base64-kodning för signerings certifikat för gateway-datorn.
    - signCertificatePassword – lösen ordet för signerings certifikat för gateway-datorn.
    - signCertificateThumbprint – certifikatets tumavtryck för identifiering i det lokala certifikat arkivet för signerings certifikatet.
    - _artifactsLocation – URI-plats där alla stöd resurser kan hittas. Värdet måste vara ett fullständigt kvalificerat UIR, inte en relativ sökväg.
    - _artifactsLocationSasToken – den SAS-token (signatur för delad åtkomst) som används för att komma åt stöd resurser, om platsen är ett Azure Storage-konto.

    Mallen kan distribueras med hjälp av Azure CLI med hjälp av följande kommando:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Här följer beskrivningar av parametrarna:

    - {Storage-Account-Endpoint} kan hämtas genom att köra `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  {Storage-acct-Name} är namnet på det lagrings konto som innehåller filer som du har överfört.  
    - {Container-Name} är namnet på behållaren i {Storage-acct-Name} som innehåller filer som du har överfört.  
    - {SAS-token} kan hämtas genom att köra `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - {Storage-acct-Name} är namnet på det lagrings konto som innehåller filer som du har överfört.  
        - {Container-Name} är namnet på behållaren i {Storage-acct-Name} som innehåller filer som du har överfört.  
        - {UTC-expire-date} är datumet, i UTC, då SAS-token upphör att gälla och SAS-token inte längre kan användas för att komma åt lagrings kontot.

    Registrera värdena för gatewayFQDN och gatewayIP från mallens distributions data. Du måste också spara värdet för funktions nyckeln för den nyligen skapade funktionen, som du hittar på fliken [Inställningar i appen](../azure-functions/functions-how-to-use-azure-function-app-settings.md) .
5. Konfigurera DNS så att FQDN för TLS/SSL-certifikat dirigeras till IP-adressen för gatewayIP från föregående steg.

    När gruppen för fjärrskrivbordsgateway har skapats och lämpliga DNS-uppdateringar har gjorts, är den redo att användas av ett labb i DevTest Labs. Inställningarna för **Gateway-värdnamn** och gateway- **token** måste konfigureras för att använda de gateway-datorer som du har distribuerat. 

    > [!NOTE]
    > Om labb datorn använder privata IP-adresser måste det finnas en nätverks Sök väg från gateway-datorn till labb datorn, antingen genom att dela samma virtuella nätverk eller använda ett peer-kopplat virtuellt nätverk.

    När både gateway och labb har kon figurer ATS innehåller anslutnings filen som skapades när labb användaren klickar på **Connect** automatiskt information som krävs för att ansluta med hjälp av token-autentisering.     

## <a name="next-steps"></a>Nästa steg
I följande artikel finns mer information om Fjärrskrivbordstjänster: [Fjärrskrivbordstjänster dokumentation](/windows-server/remote/remote-desktop-services/Welcome-to-rds)