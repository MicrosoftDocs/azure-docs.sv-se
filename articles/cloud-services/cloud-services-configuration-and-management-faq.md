---
title: Vanliga frågor och svar om konfiguration och hantering
description: Den här artikeln innehåller vanliga frågor och svar om konfiguration och hantering av Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c5dd09292897d69f90606e8661b4e6cb28090612
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742598"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Konfigurations-och hanterings problem för Azure Cloud Services (klassisk): vanliga frågor och svar

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln innehåller vanliga frågor om konfigurations-och hanterings problem för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också se storleks information på [sidan Cloud Services virtuell dator storlek](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certifikat**

- [Varför är certifikat kedjan för mitt moln tjänst TLS/SSL-certifikat ofullständig?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Vad är syftet med krypterings certifikatet "Windows Azure-verktyg för tillägg"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hur kan jag skapa en certifikat signerings förfrågan utan "RDP-ing" i-instansen?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mitt moln tjänst hanterings certifikat upphör att gälla. Hur förnyar jag det?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Hur automatiserar du installationen av huvudtls/SSL-certifikat (. pfx) och mellanliggande certifikat (. p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Vad är syftet med certifikatet "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Övervakning och loggning**

- [Vilka är de kommande moln tjänst funktionerna i Azure Portal som kan hjälpa dig att hantera och övervaka program?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Varför slutar IIS skriva till logg katalogen?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hur gör jag för att aktivera loggning av WAD för Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfiguration av nätverk**

- [Hur gör jag för att ange tids gränsen för inaktivitet för Azure Load Balancer?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hur gör jag för att associera en statisk IP-adress till min moln tjänst?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Vilka är de funktioner och funktioner som Azure Basic IP-adresser och DDOS tillhandahåller?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Hur aktiverar jag HTTP/2 på Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Behörigheter**

- [Kan Microsoft Internal Engineers fjärr skrivbord till moln tjänst instanser utan behörighet?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Jag kan inte fjärr skrivbord till moln tjänstens virtuella dator med hjälp av RDP-filen. Jag får följande fel meddelande: ett autentiseringsfel har inträffat (kod: 0x80004005 visas)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalning**

- [Det går inte att skala mer än X instanser](#i-cannot-scale-beyond-x-instances)
- [Hur kan jag konfigurera automatisk skalning baserat på minnes mått?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Allmänna**

- [Hur gör jag för att lägga till `nosniff` på min webbplats?](#how-do-i-add-nosniff-to-my-website)
- [Hur gör jag för att anpassa IIS för en webb roll?](#how-do-i-customize-iis-for-a-web-role)
- [Vilken är kvot gränsen för min moln tjänst?](#what-is-the-quota-limit-for-my-cloud-service)
- [Varför visar enheten i min moln tjänst VM mycket ledigt disk utrymme?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hur kan jag lägga till ett tillägg för program mot skadlig kod för mina Cloud Services på ett automatiserat sätt?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Hur aktiverar du Servernamnindikator (SNI) för Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hur kan jag lägga till taggar i min Azure Cloud service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Azure Portal visar inte SDK-versionen av min moln tjänst. Hur kan jag få det?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Jag vill stänga av moln tjänsten under flera månader. Hur minskar du fakturerings kostnaden för moln tjänsten utan att förlora IP-adressen?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certifikat

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Varför är certifikat kedjan för mitt moln tjänst TLS/SSL-certifikat ofullständig?
    
Vi rekommenderar att kunderna installerar hela certifikat kedjan (löv certifikat, mellanliggande certifikat och rot certifikat) i stället för bara löv certifikatet. När du installerar bara löv certifikatet förlitar du dig på Windows för att bygga certifikat kedjan genom att gå till listan över betrodda certifikat. Om tillfälliga nätverks-eller DNS-problem inträffar i Azure eller Windows Update när Windows försöker validera certifikatet, kan certifikatet anses vara ogiltigt. Genom att installera den fullständiga certifikat kedjan kan du undvika det här problemet. Bloggen för [att installera ett länkat SSL-certifikat](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) visar hur du gör detta.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Vad är syftet med krypterings certifikatet "Windows Azure-verktyg för tillägg"?

Dessa certifikat skapas automatiskt när ett tillägg läggs till i moln tjänsten. Oftast är detta WAD-tillägget eller RDP-tillägget, men det kan vara andra, till exempel tillägget för program mot skadlig kod eller logg insamlare. Dessa certifikat används bara för kryptering och dekryptering av den privata konfigurationen för tillägget. Utgångs datumet kontrol leras aldrig, så det spelar ingen roll om certifikatet har upphört att gälla. 

Du kan ignorera dessa certifikat. Om du vill rensa certifikaten kan du prova att ta bort alla. Azure kommer att utlösa ett fel om du försöker ta bort ett certifikat som används.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hur kan jag skapa en certifikat signerings förfrågan utan "RDP-ing" i-instansen?

Se följande rikt linjer:

[Hämta ett certifikat för användning med Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR är bara en textfil. Den behöver inte skapas från den dator där certifikatet slutligen ska användas.Även om det här dokumentet skrivs för en App Service, är CSR-skapandet generiskt och gäller även för Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mitt moln tjänst hanterings certifikat upphör att gälla. Hur förnyar jag det?

Du kan använda följande PowerShell-kommandon för att förnya hanterings certifikat:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

**Get-AzurePublishSettingsFile** skapar ett nytt hanterings certifikat i **prenumerations**  >  **hanterings certifikaten** i Azure Portal. Namnet på det nya certifikatet ser ut som "YourSubscriptionNam]-[CurrentDate]-credentials".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Hur automatiserar du installationen av huvudtls/SSL-certifikat (. pfx) och mellanliggande certifikat (. p7b)?

Du kan automatisera den här uppgiften med hjälp av ett start skript (batch/cmd/PowerShell) och registrera start skriptet i tjänst definitions filen. Lägg till både start skriptet och certifikatet (. P7B-filen) i projektmappen i samma katalog som start skriptet.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Vad är syftet med certifikatet "Microsoft Azure Service Management for MachineKey"?

Det här certifikatet används för att kryptera dator nycklar på Azure-webbroller. Mer information finns i [den här rekommendationen](/security-updates/securityadvisories/2018/4092731).

Mer information finns i följande artiklar:
- [Konfigurera och köra start åtgärder för en moln tjänst](./cloud-services-startup-tasks.md)
- [Vanliga start uppgifter för moln tjänster](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Övervakning och loggning

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Vilka är de kommande moln tjänst funktionerna i Azure Portal som kan hjälpa dig att hantera och övervaka program?

Möjlighet att generera ett nytt certifikat för Remote Desktop Protocol (RDP) kommer snart. Du kan också köra det här skriptet:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Det kommer snart att välja BLOB eller lokalt för csdef-och cscfg-uppladdnings platsen. Med [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-4.0.0&preserve-view=true)kan du ange varje plats värde.

Möjlighet att övervaka mått på instans nivå. Ytterligare övervakningsfunktioner finns i [så här övervakar du Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Varför slutar IIS skriva till logg katalogen?
Du har förbrukat den lokala lagrings kvoten för skrivning till logg katalogen.Du kan åtgärda detta genom att göra något av tre saker:
* Aktivera diagnostik för IIS och låt diagnostiken regelbundet flyttas till Blob Storage.
* Ta bort loggfiler manuellt från loggnings katalogen.
* Öka kvot gränsen för lokala resurser.

Mer information finns i följande dokument:
* [Lagra och visa diagnostikdata i Azure Storage](../storage/common/storage-introduction.md)
* [IIS-loggar slutar att skriva i moln tjänsten](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hur gör jag för att aktivera loggning av WAD för Cloud Services?
Du kan aktivera Windows Azure-diagnostik (WAD)-loggning genom följande alternativ:
1. [Aktivera från Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Aktivera via .NET-kod](./cloud-services-dotnet-diagnostics.md)
3. [Aktivera via PowerShell](./cloud-services-diagnostics-powershell.md)

För att kunna hämta de aktuella WAD-inställningarna för moln tjänsten kan du använda [Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) PowerShell cmd eller så kan du Visa den via portalen från bladet "Cloud Services--> tillägg".


## <a name="network-configuration"></a>Konfiguration av nätverk

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hur gör jag för att ange tids gränsen för inaktivitet för Azure Load Balancer?
Du kan ange timeout i csdef-filen (service definition) så här:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Se [ny: konfigurerbar tids gräns för inaktivitet för Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) för mer information.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hur gör jag för att associera en statisk IP-adress till min moln tjänst?
Om du vill konfigurera en statisk IP-adress måste du skapa en reserverad IP-adress. Den här reserverade IP-adressen kan kopplas till en ny moln tjänst eller till en befintlig distribution. Mer information finns i följande dokument:
* [Så här skapar du en reserverad IP-adress](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Reservera IP-adressen för en befintlig moln tjänst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Koppla en reserverad IP-adress till en ny moln tjänst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Koppla en reserverad IP-adress till en distribution som körs](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Koppla en reserverad IP-adress till en moln tjänst med hjälp av en tjänst konfigurations fil](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Vilka är de funktioner och funktioner som Azure Basic IP-adresser och DDOS tillhandahåller?
Azure har IP-adresser/ID: n i Data Center fysiska servrar för att skydda mot hot. Dessutom kan kunderna distribuera säkerhetslösningar från tredje part, till exempel brand väggar för webb program, nätverks brand väggar, program mot skadlig kod, intrångs identifiering, skydds system (ID/IP-adresser) med mera. Mer information finns i [skydda dina data och till gångar och följa globala säkerhets standarder](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft övervakar kontinuerligt servrar, nätverk och program för att identifiera hot. Azures multipronged Threat-Management-metod använder intrångs identifiering, distribuerad denial-of-service (DDoS), inträngande test, beteende analys, avvikelse identifiering och maskin inlärning för att ständigt förstärka sitt försvar och minska riskerna. Microsoft Antimalware för Azure skyddar Azure-Cloud Services och virtuella datorer. Du har möjlighet att distribuera säkerhetslösningar från tredje part, till exempel brand väggar för webb program, nätverks brand väggar, program mot skadlig kod, intrångs identifiering och skydds system (ID/IP-adresser) med mera.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Hur aktiverar jag HTTP/2 på Cloud Services VM?

Windows 10 och Windows Server 2016 levereras med stöd för HTTP/2 på både klient-och Server sidan. Om klienten (webbläsaren) ansluter till IIS-servern via TLS som förhandlar om HTTP/2 via TLS-tillägg behöver du inte göra några ändringar på Server sidan. Detta beror på att TLS-14-huvudet som anger användningen av HTTP/2 skickas som standard via TLS. Om den andra klienten skickar ett uppgraderings huvud för att uppgradera till HTTP/2, måste du göra ändringen nedan på Server sidan för att säkerställa att uppgraderingen fungerar och att du har slut på en HTTP/2-anslutning. 

1. Kör regedit.exe.
2. Bläddra till register nyckel: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Skapa ett nytt DWORD-värde med namnet **DuoEnabled**.
4. Ange värdet 1.
5. Starta om servern.
6. Gå till **standard webbplatsen** och under **bindningar** skapar du en ny TLS-bindning med det självsignerade certifikatet som du nyss skapade. 

Mer information finns i:

- [HTTP/2 i IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 i Windows 10: webbläsare, appar och webb server](https://channel9.msdn.com/Events/Build/2015/3-88)
         

De här stegen kan automatiseras via en start åtgärd, så när en ny PaaS-instans skapas, kan ändringarna placeras ovan i system registret. Mer information finns i [så här konfigurerar och kör du Start åtgärder för en moln tjänst](cloud-services-startup-tasks.md).

 
När detta har gjorts kan du kontrol lera om HTTP/2 har Aktiver ATS eller inte genom att använda någon av följande metoder:

- Aktivera protokoll version i IIS-loggar och leta i IIS-loggarna. Då visas HTTP/2 i loggarna. 
- Aktivera verktyget F12-utvecklare i Internet Explorer eller Microsoft Edge och växla till fliken nätverk för att verifiera protokollet. 

Mer information finns i [http/2 i IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Behörigheter

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hur kan jag implementera rollbaserad åtkomst för Cloud Services?
Cloud Services stöder inte Azure RBAC-modellen (rollbaserad åtkomst kontroll) i Azure, eftersom den inte är en Azure Resource Manager baserad tjänst.

Se [förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Fjärr skrivbord

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Kan Microsoft Internal Engineers fjärr skrivbord till moln tjänst instanser utan behörighet?
Microsoft följer en strikt process som inte tillåter interna tekniker att använda fjärr skrivbord i moln tjänsten utan skriftligt tillstånd (e-post eller annan skriftlig kommunikation) från ägaren eller deras utformning.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Jag kan inte fjärr skrivbord till moln tjänstens virtuella dator med hjälp av RDP-filen. Jag får följande fel meddelande: ett autentiseringsfel har inträffat (kod: 0x80004005 visas)

Det här felet kan inträffa om du använder RDP-filen från en dator som är ansluten till Azure Active Directory. Följ dessa anvisningar för att lösa problemet:

1. Högerklicka på den RDP-fil som du laddade ned och välj sedan **Redigera**.
2. Lägg till "&#92;" som prefix före användar namnet. Använd till exempel **.\username** i stället för  **användar namn**.

## <a name="scaling"></a>Skalning

### <a name="i-cannot-scale-beyond-x-instances"></a>Det går inte att skala mer än X instanser
Din Azure-prenumeration har en gräns för antalet kärnor som du kan använda. Skalning fungerar inte om du har använt alla kärnor som är tillgängliga. Om du till exempel har en gräns på 100 kärnor, innebär det att du kan ha 100 a1-storlek för virtuella dator instanser för din moln tjänst eller 50 a2 storlek på virtuella dator instanser.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hur kan jag konfigurera automatisk skalning baserat på minnes mått?

Automatisk skalning baserat på minnes mått för en Cloud Services stöds inte för närvarande. 

Du kan lösa problemet genom att använda Application Insights. Autoskalning stöder Application Insights som en mått källa och kan skala roll instans antalet baserat på gäst måttet, till exempel "minne".  Du måste konfigurera Application Insights i din moln tjänst projekt paket fil (*. cspkg) och aktivera Azure-diagnostik-tillägget på tjänsten för att implementera den här Feat.

Mer information om hur du använder ett anpassat mått via Application Insights för att konfigurera automatisk skalning på Cloud Services finns i [Kom igång med automatisk skalning efter anpassad mått i Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Mer information om hur du integrerar Azure-diagnostik med Application Insights för Cloud Services finns i [Skicka moln tjänst, virtuell dator eller Service Fabric diagnostikdata till Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Mer information om hur du aktiverar Application Insights för Cloud Services finns i [Application Insights för Azure Cloud Services](../azure-monitor/app/cloudservices.md)

Mer information om hur du aktiverar Azure-diagnostik loggning för Cloud Services finns i [Konfigurera diagnostik för Azure Cloud Services och virtuella datorer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Allmänna

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hur gör jag för att lägga till `nosniff` på min webbplats?
Om du vill förhindra att klienter kan använda MIME-typer lägger du till en inställning i *web.config* -filen.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Du kan också lägga till den som en inställning i IIS. Använd följande kommando med artikeln [vanliga start åtgärder](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hur gör jag för att anpassa IIS för en webb roll?
Använd start skriptet i IIS från artikeln [vanliga start åtgärder](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Vilken är kvot gränsen för min moln tjänst?
Se [tjänstspecifika gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Varför visar enheten i min moln tjänst VM mycket ledigt disk utrymme?
Detta är ett förväntat beteende och det bör inte orsaka något problem med ditt program. Journalering har Aktiver ATS för% appens rot%-enheten i virtuella Azure PaaS-datorer, vilket i princip förbrukar dubbelt så mycket utrymme som filer normalt tar upp. Det finns dock flera saker som du bör känna till, vilket innebär att det innebär ett icke-problem.

Enhets storleken% appens rot% beräknas som <storlek för. cspkg + Max Journal storlek + en marginal på det lediga utrymmet> eller 1,5 GB, beroende på vilket som är störst. Den virtuella datorns storlek har ingen betydelse för den här beräkningen. (Storleken på den virtuella datorn påverkar endast storleken på den temporära enheten C:.) 

Det går inte att skriva till% appens rot%-enheten. Om du skriver till den virtuella Azure-datorn måste du göra det i en tillfällig LocalStorage-resurs (eller något annat alternativ, till exempel Blob Storage, Azure Files osv.). Därför är mängden ledigt utrymme i mappen% appens rot% inte meningsfull. Om du inte är säker på om ditt program skriver till% appens rot%-enheten, kan du alltid låta tjänsten köras i några dagar och sedan jämföra storlekarna "före" och "efter". 

Azure skriver inte något till% appens rot%-enheten. När den virtuella hård disken har skapats från `.cspkg` och monterats i den virtuella Azure-datorn är programmet det enda som kan skriva till den här enheten. 

Journal inställningarna kan inte konfigureras, så du kan inte stänga av den.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hur kan jag lägga till ett tillägg för program mot skadlig kod för mina Cloud Services på ett automatiserat sätt?

Du kan aktivera tillägg för program mot skadlig kod med hjälp av PowerShell-skript i Start aktiviteten. Följ stegen i de här artiklarna för att implementera det: 
 
- [Skapa en start åtgärd för PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0&preserve-view=true)

Mer information om scenarier för distribution av program mot skadlig kod och hur du aktiverar det från portalen finns i [scenarier för distribution av program mot skadlig kod](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Hur aktiverar du Servernamnindikator (SNI) för Cloud Services?

Du kan aktivera SNI i Cloud Services med någon av följande metoder:

**Metod 1: Använd PowerShell**

SNI-bindningen kan konfigureras med PowerShell **-cmdlet New-webbinding** i en start åtgärd för en moln tjänst roll instans enligt nedan:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Som beskrivs [här](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10))kan $sslFlags vara ett av värdena som följande:

|Värde|Innebörd|
------|------
|0|Ingen SNI|
|1|SNI aktiverat|
|2|Icke-SNI bindning som använder centralt certifikat Arkiv|
|3|SNI-bindning som använder centralt certifikat Arkiv|
 
**Metod 2: Använd kod**

SNI-bindningen kan också konfigureras via kod i roll starten enligt beskrivningen i det här [blogg inlägget](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service):

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
Med hjälp av någon av metoderna ovan måste respektive certifikat (*. pfx) för de angivna värdarna installeras först på roll instanserna med en start uppgift eller via kod för att SNI-bindningen ska vara effektiv.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hur kan jag lägga till taggar i min Azure Cloud service? 

Moln tjänsten är en klassisk resurs. Endast resurser som skapats med hjälp av Azure Resource Manager Support taggar. Du kan inte använda taggar för klassiska resurser, till exempel en moln tjänst. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Azure Portal visar inte SDK-versionen av min moln tjänst. Hur kan jag få det?

Vi arbetar med att ta med den här funktionen på Azure Portal. Under tiden kan du Hämta SDK-versionen med följande PowerShell-kommandon:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Jag vill stänga av moln tjänsten under flera månader. Hur minskar du fakturerings kostnaden för moln tjänsten utan att förlora IP-adressen?

En redan distribuerad moln tjänst faktureras för den beräkning och lagring som används. Så även om du stänger av den virtuella Azure-datorn debiteras du fortfarande för lagringen. 

Det här kan du göra för att minska faktureringen utan att förlora IP-adressen för din tjänst:

1. [Reservera IP-adressen](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) innan du tar bort distributionerna.  Du debiteras bara för den här IP-adressen. Mer information om fakturering av IP-adresser finns i [priser för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Ta bort distributionerna. Ta inte bort xxx.cloudapp.net, så att du kan använda den i framtiden.
3. Om du vill distribuera om moln tjänsten med hjälp av samma reserverade IP-adress som du har reserverat i din prenumeration, se [reserverad IP adresser för Cloud Services och Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).