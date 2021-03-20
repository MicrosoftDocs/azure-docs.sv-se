---
title: Vad är modell och paket för Azure Cloud Service (utökad support)
description: Beskriver moln tjänst (utökad support) modell (. csdef,. cscfg) och paket (. cspkg) i Azure
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3baea92d78cf15c35d2fb36692dab1edfd950699
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744763"
---
# <a name="what-is-the-azure-cloud-service-model-and-how-do-i-package-it"></a>Vad är Azures moln tjänst modell och hur paketerar jag det?

En moln tjänst skapas från tre komponenter, tjänst definitionen *(. csdef)*, tjänst konfigurationen *(. cscfg)* och ett service paket *(. cspkg)*. Både **service definition. csdef** -och **ServiceConfig. cscfg** -filerna är XML-baserade och beskriver moln tjänst strukturen och hur den är konfigurerad. gemensamt kallat modellen. Filen **servicepack. cspkg** är en zip-fil som genereras från **service definition. csdef** och bland annat, och innehåller alla nödvändiga binära-baserade beroenden. Azure skapar en moln tjänst från både **servicepack. cspkg** och **ServiceConfig. cscfg**.

När moln tjänsten körs i Azure kan du konfigurera om den via filen **ServiceConfig. cscfg** , men du kan inte ändra definitionen.

## <a name="what-would-you-like-to-know-more-about"></a>Vad vill du veta mer om?
* Jag vill veta mer om filerna [service definition. csdef](#csdef) och [ServiceConfig. cscfg](#cscfg) .
* Jag vet redan om det, ge mig [några exempel](#next-steps) på vad jag kan konfigurera.
* Jag vill skapa filen [servicepack. cspkg](#cspkg).
* Jag använder Visual Studio och jag vill...
  * [Skapa en moln tjänst] [vs_create]
  * [Konfigurera om en befintlig moln tjänst] [vs_reconfigure]
  * [Distribuera ett moln tjänst projekt] [vs_deploy]
  * [Fjärr skrivbord till en moln tjänst instans] [remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>Service definition. csdef
Filen **service definition. csdef** anger de inställningar som används av Azure för att konfigurera en moln tjänst. [Azure-tjänstens definitions schema (. csdef-fil)](schema-csdef-file.md) innehåller det tillåtna formatet för en tjänst definitions fil. I följande exempel visas de inställningar som kan definieras för webb-och arbets roller:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Du kan referera till [tjänst definitions schemat](schema-csdef-file.md)) för att få en bättre förståelse för det XML-schema som används här, men det här är en snabb förklaring av några av elementen:

**Webbplatser**  
Innehåller definitionerna för webbplatser eller webb program som finns i IIS7.

**InputEndpoints**  
Innehåller definitionerna för slut punkter som används för att kontakta moln tjänsten.

**InternalEndpoints**  
Innehåller definitionerna för slut punkter som används av roll instanser för att kommunicera med varandra.

**ConfigurationSettings**  
Innehåller inställnings definitioner för funktioner i en speciell roll.

**Certifikat**  
Innehåller definitionerna för certifikat som behövs för en roll. I föregående kod exempel visas ett certifikat som används för konfigurationen av Azure Connect.

**LocalResources**  
Innehåller definitionerna för lokala lagrings resurser. En lokal lagrings resurs är en reserverad katalog på fil systemet på den virtuella datorn där en instans av en roll körs.

**Kina**  
Innehåller definitionerna för importerade moduler. I föregående kod exempel visas modulerna för Anslutning till fjärrskrivbord och Azure Connect.

**Start**  
Innehåller aktiviteter som körs när rollen startas. Aktiviteterna definieras i en. cmd-eller körbar fil.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
Konfigurationen av inställningarna för din moln tjänst bestäms av värdena i **ServiceConfiguration. cscfg** -filen. Du anger antalet instanser som du vill distribuera för varje roll i den här filen. Värdena för de konfigurations inställningar som du definierade i tjänst definitions filen läggs till i tjänst konfigurations filen. Tumavtrycken för alla hanterings certifikat som är associerade med moln tjänsten läggs också till i filen. [Konfigurations schema för Azure-tjänsten (. cscfg-filen)](schema-cscfg-file.md) innehåller det tillåtna formatet för en tjänst konfigurations fil.

Tjänst konfigurations filen paketeras inte med programmet, men överförs till Azure som en separat fil och används för att konfigurera moln tjänsten. Du kan ladda upp en ny tjänst konfigurations fil utan att distribuera om moln tjänsten. Konfigurations värden för moln tjänsten kan ändras när moln tjänsten körs. I följande exempel visas de konfigurations inställningar som kan definieras för webb-och arbets roller:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Du kan referera till [tjänstens konfigurations schema](schema-cscfg-file.md) för bättre förståelse av det XML-schema som används här, men det här är en snabb förklaring av elementen:

**Instanser**  
Konfigurerar antalet instanser som körs för rollen. För att förhindra att moln tjänsten kan bli otillgänglig under uppgraderingar, rekommenderar vi att du distribuerar fler än en instans av dina webbaserade roller. Genom att distribuera fler än en instans följer du rikt linjerna i [Azure compute serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/), som garanterar 99,95% extern anslutning för roller som riktas mot Internet när två eller fler roll instanser distribueras för en tjänst.

**ConfigurationSettings**  
Konfigurerar inställningarna för de instanser som körs för en roll. Namnet på `<Setting>` elementen måste matcha inställnings definitionerna i tjänst definitions filen.

**Certifikat**  
Konfigurerar de certifikat som används av tjänsten. I föregående kod exempel visas hur du definierar certifikatet för RemoteAccess-modulen. Värdet för attributet *tumavtryck* måste anges till tumavtrycket för det certifikat som ska användas.

<p/>

> [!NOTE]
> Du kan lägga till tumavtrycket för certifikatet i konfigurations filen med hjälp av en text redigerare. Du kan också lägga till värdet på fliken **certifikat** på **egenskaps** sidan för rollen i Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiera portar för roll instanser
Azure tillåter endast en start punkt till en webb roll. Innebär att all trafik sker via en IP-adress. Du kan konfigurera dina webbplatser så att de delar en port genom att konfigurera värd rubriken för att dirigera begäran till rätt plats. Du kan också konfigurera dina program så att de lyssnar på välkända portar på IP-adressen.

I följande exempel visas konfigurationen för en webb roll med en webbplats och ett webb program. Webbplatsen konfigureras som standard plats för inmatnings platsen på port 80 och webb programmen konfigureras för att ta emot begär Anden från ett alternativt värd huvud som kallas "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Ändra konfigurationen för en roll
Du kan uppdatera konfigurationen av moln tjänsten medan den körs i Azure, utan att ta tjänsten offline. Om du vill ändra konfigurations information kan du antingen ladda upp en ny konfigurations fil eller redigera konfigurations filen på plats och tillämpa den på tjänsten som körs. Följande ändringar kan göras i konfigurationen av en tjänst:

* **Ändra värdena för konfigurations inställningar**  
  När en konfigurations inställning ändras kan en roll instans välja att tillämpa ändringen medan instansen är online, eller för att återvinna instansen korrekt och tillämpa ändringen när instansen är offline.
* **Ändra tjänst sto pol Ogin för roll instanser**  
  Ändringar i topologin påverkar inte instanser som körs, förutom var en instans tas bort. Alla återstående instanser behöver vanligt vis inte återvinnas. Du kan dock välja att återvinna roll instanser som svar på en ändring i topologin.
* **Ändra tumavtryck för certifikatet**  
  Du kan bara uppdatera ett certifikat när en roll instans är offline. Om ett certifikat läggs till, tas bort eller ändras när en roll instans är online, tar Azure på ett smidigt sätt instansen offline för att uppdatera certifikatet och ta tillbaka det igen när ändringen har slutförts.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Hantera konfigurations ändringar med service runtime-händelser
Azure runtime-biblioteket innehåller namn området Microsoft. WindowsAzure. ServiceRuntime, som innehåller klasser för att interagera med Azure-miljön från en roll. RoleEnvironment-klassen definierar följande händelser som aktive ras före och efter en konfigurations ändring:

* **Händelsen ändring**  
  Detta inträffar innan konfigurations ändringen tillämpas på en angiven instans av en roll, vilket ger dig möjlighet att ta bort roll instanserna vid behov.
* **Ändrings händelse**  
  Inträffar efter att konfigurations ändringen har tillämpats på en angiven instans av en roll.

> [!NOTE]
> Eftersom certifikat ändringar alltid tar instanser av en roll offline, ökar de inte RoleEnvironment. ändrar eller RoleEnvironment. ändrade händelser.
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePack. cspkg
> [!NOTE]
> Den maximala paket storleken som kan distribueras är 600MB

Om du vill distribuera ett program som en moln tjänst i Azure måste du först paketera programmet i rätt format. Du kan använda kommando rads verktyget **CSPack** (installerat med [Azure SDK](https://azure.microsoft.com/downloads/)) för att skapa paket filen som ett alternativ till Visual Studio.

**CSPack** använder innehållet i tjänst definitions filen och tjänst konfigurations filen för att definiera paketets innehåll. **CSPack** genererar en programpaket fil (. cspkg) som du kan överföra till Azure med hjälp av [Azure Portal](../cloud-services/cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Som standard heter paketet `[ServiceDefinitionFileName].cspkg` , men du kan ange ett annat namn genom att använda `/out` alternativet för **CSPack**.

**CSPack** finns på  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (i Windows) är tillgängligt genom att köra **Microsoft Azure kommando tolkens** genväg som installeras med SDK: n.  
> 
> Kör själva programmet CSPack.exe för att se dokumentation om alla möjliga växlar och kommandon.
> 
> 

<p />

> [!TIP]
> Kör din moln tjänst lokalt i **Microsoft Azure Compute-emulatorn**, Använd alternativet **/copyonly** . Med det här alternativet kopieras binärfilerna för programmet till en katalog-layout som de kan köras från i beräknings-emulatorn.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Exempel kommando för att paketera en moln tjänst
I följande exempel skapas ett programpaket som innehåller informationen för en webb roll. Kommandot anger vilken tjänst definitions fil som ska användas, katalogen där de binära filerna kan hittas och namnet på paket filen.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Om programmet innehåller både en webb roll och en arbets roll används följande kommando:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Var variablerna definieras enligt följande:

| Variabel | Värde |
| --- | --- |
| \[DirectoryName\] |Under katalogen under rot projekt katalogen som innehåller. csdef-filen för Azure-projektet. |
| \[Service definition\] |Namnet på tjänst definitions filen. Som standard heter filen service definition. csdef. |
| \[OutputFileName\] |Namnet på den genererade paket filen. Detta är vanligt vis inställt på namnet på programmet. Om inget fil namn anges skapas programpaketet som \[ ApplicationName \] . cspkg. |
| \[RoleName\] |Namnet på rollen som det definieras i tjänst definitions filen. |
| \[RoleBinariesDirectory] |Platsen för de binära filerna för rollen. |
| \[VirtualPath\] |De fysiska katalogerna för varje virtuell sökväg som definieras i avsnittet platser i tjänst definitionen. |
| \[FysiskSökväg\] |Fysiska kataloger för innehållet för varje virtuell sökväg som definierats i noden plats i tjänst definitionen. |
| \[RoleAssemblyName\] |Namnet på den binära filen för rollen. |

## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).

