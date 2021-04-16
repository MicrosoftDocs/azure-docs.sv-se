---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 3da4f8f946b11985d93be35fa2748e7f25015a71
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564461"
---
Azure Instance Metadata Service (IMDS) innehåller information om instanser av virtuella datorer som körs för närvarande. Du kan använda den för att hantera och konfigurera dina virtuella datorer.
Den här informationen omfattar SKU, lagring, nätverkskonfigurationer och kommande underhållshändelser. En fullständig lista över tillgängliga data finns i Sammanfattning [av slutpunktskategorier.](#endpoint-categories)

IMDS är tillgängligt för att köra instanser av virtuella datorer (VM) och VM-skalningsuppsättningsinstanser. Alla slutpunkter stöder virtuella datorer som skapats och hanteras med hjälp [av Azure Resource Manager](/rest/api/resources/). Endast kategorin Attested (Attesterad) och Network (Nätverk) i kategorin Instance (Instans) stöder virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen. Den attesterade slutpunkten gör detta endast i begränsad utsträckning.

IMDS är REST API som är tillgänglig på en välkänd, icke-dirigerbar IP-adress ( `169.254.169.254` ). Du kan bara komma åt den från den virtuella datorn. Kommunikationen mellan den virtuella datorn och IMDS lämnar aldrig värden.
Se till att HTTP-klienter kringgår webbproxies i den virtuella datorn när de frågar IMDS och `169.254.169.254` behandlar samma som [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="usage"></a>Användning

### <a name="access-azure-instance-metadata-service"></a>Få åtkomst till Azure Instance Metadata Service

Om du vill komma åt IMDS [skapar du en virtuell dator Azure Resource Manager](/rest/api/resources/) eller [Azure Portal](https://portal.azure.com)och använder följande exempel.
Fler exempel finns i [Azure Instance Metadata Samples](https://github.com/microsoft/azureimds).

Här är exempelkod för att hämta alla metadata för en instans. En översikt över alla tillgängliga funktioner finns i [Slutpunktskategorier](#endpoint-categories) för att få åtkomst till en specifik datakälla.

**Förfrågan**

> [!IMPORTANT]
> I det här exemplet kringgås proxys. Du **måste** kringgå proxy när du frågar IMDS. Mer information [finns i Proxys.](#proxies)

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Säkerhet och autentisering

Den Instance Metadata Service är endast tillgänglig från en instans av en virtuell dator som körs på en icke-dirigerbar IP-adress. Virtuella datorer är begränsade till att interagera med metadata/funktioner som hör till sig själva. API:et är endast HTTP och lämnar aldrig värden.

För att säkerställa att begäranden är direkt avsedda för IMDS och förhindra oavsiktlig eller oönskad omdirigering av begäranden, begäranden:
- **Måste** innehålla rubriken `Metadata: true`
- Får **inte** innehålla en `X-Forwarded-For` rubrik

Alla förfrågningar som inte **uppfyller båda** dessa krav avvisas av tjänsten.

> [!IMPORTANT]
> IMDS är **inte** en kanal för känsliga data. API:et är oauthenticerat och öppet för alla processer på den virtuella datorn. Information som exponeras via den här tjänsten bör betraktas som delad information för alla program som körs på den virtuella datorn.

## <a name="proxies"></a>Proxyservrar

IMDS **är inte** avsett att användas bakom en proxy och detta stöds inte. De flesta HTTP-klienter ger dig ett alternativ för att inaktivera proxy för dina begäranden, och den här funktionen måste användas vid kommunikation med IMDS. Mer information finns i klientens dokumentation.

> [!IMPORTANT]
> Även om du inte känner till någon proxykonfiguration i din miljö, måste du fortfarande **åsidosätta alla standardinställningar för klientproxy.** Proxykonfigurationer kan identifieras automatiskt och om du inte kringgår sådana konfigurationer exponeras du för avbrottsrisker om datorns konfiguration ändras i framtiden.

## <a name="rate-limiting"></a>Frekvensbegränsning

I allmänhet är begäranden till IMDS begränsade till 5 begäranden per sekund. Begäranden som överskrider det här tröskelvärdet avvisas med 429 svar. Begäranden till kategorin [Hanterad](#managed-identity) identitet är begränsade till 20 begäranden per sekund och 5 samtidiga begäranden.

## <a name="http-verbs"></a>HTTP-verb

Följande HTTP-verb stöds för närvarande:

| Verb | Description |
|------|-------------|
| `GET` | Hämta den begärda resursen

## <a name="parameters"></a>Parametrar

Slutpunkter kan ha stöd för obligatoriska och/eller valfria parametrar. Mer [information](#schema) finns i Schema och dokumentationen för den aktuella slutpunkten.

### <a name="query-parameters"></a>Frågeparametrar

IMDS-slutpunkter har stöd för HTTP-frågesträngsparametrar. Exempel: 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

Anger parametrarna:

| Name | Värde |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

Begäranden med dubbla frågeparameternamn avvisas.

### <a name="route-parameters"></a>Vägparametrar

För vissa slutpunkter som returnerar större json-blobar har vi stöd för att lägga till vägparametrar till begärandeslutpunkten för att filtrera ned till en delmängd av svaret: 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Parametrarna motsvarar de index/nycklar som skulle användas för att gå igenom json-objektet om du interagerar med en parsad representation.

Returnerar till `/metatadata/instance` exempel json-objektet:
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Om vi vill filtrera svaret till bara beräkningsegenskapen skickar vi begäran: 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

Om vi vill filtrera på en kapslad egenskap eller ett specifikt matriselement fortsätter vi på samma sätt att lägga till nycklar: 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
filtrerar till det första elementet från `Network.interface` egenskapen och returnerar:

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> När du filtrerar till en `format=json` lövnod fungerar inte det. För dessa frågor `format=text` måste anges uttryckligen eftersom standardformatet är json.

## <a name="schema"></a>Schema

### <a name="data-format"></a>Dataformat

Som standard returnerar IMDS data i JSON-format ( `Content-Type: application/json` ). Slutpunkter som stöder svarsfiltrering (se [Vägparametrar)](#route-parameters)har dock även stöd för formatet `text` .

Om du vill komma åt ett icke-standardsvarsformat anger du det begärda formatet som en frågesträngsparameter i begäran. Exempel:

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

I json-svar kommer alla primitiver att vara av typen , och värden som saknas eller inte kan användas ingår alltid, men de anges `string` till en tom sträng.

### <a name="versioning"></a>Versionshantering

IMDS är versionshantering och det är obligatoriskt att ange API-versionen i HTTP-begäran. Det enda undantaget till det här kravet är [versionsslutpunkten,](#versions) som kan användas för att dynamiskt hämta tillgängliga API-versioner.

När nyare versioner läggs till kan äldre versioner fortfarande användas för kompatibilitet om skripten har beroenden av specifika dataformat.

När du inte anger en version visas ett fel med en lista över de senaste versionerna som stöds:
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>API-versioner som stöds
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01

### <a name="swagger"></a>Swagger

En fullständig Swagger-definition för IMDS finns på: https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>Regional tillgänglighet

Tjänsten är allmänt **tillgänglig i** alla Azure-moln.

## <a name="root-endpoint"></a>Rotslutpunkt

Rotslutpunkten är `http://169.254.169.254/metadata` .

## <a name="endpoint-categories"></a>Slutpunktskategorier

IMDS-API:et innehåller flera slutpunktskategorier som representerar olika datakällor, som var och en innehåller en eller flera slutpunkter. Mer information finns i varje kategori.

| Kategorirot | Description | Version introducerad |
|---------------|-------------|--------------------|
| `/metadata/attested` | Se [Attesterade data](#attested-data) | 2018-10-01
| `/metadata/identity` | Se [Hanterad identitet via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Se [Instansmetadata](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Se [Hämta Load Balancer metadata via IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Se [Schemalagda händelser via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Se [versioner](#versions) | Ej tillämpligt

## <a name="versions"></a>Versioner

> [!NOTE]
> Den här funktionen släpptes tillsammans med version 2020-10-01, som för närvarande distribueras och kanske inte är tillgänglig i alla regioner än.

### <a name="list-api-versions"></a>Lista API-versioner

Returnerar uppsättningen API-versioner som stöds.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Parametrar

Ingen (den här slutpunkten är icke-versionerad).

#### <a name="response"></a>Svarsåtgärder

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Instansmetadata

### <a name="get-vm-metadata"></a>Hämta VM-metadata

Exponerar viktiga metadata för den virtuella datorinstansen, inklusive beräkning, nätverk och lagring. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Parametrar

| Name | Obligatorisk/valfri | Beskrivning |
|------|-------------------|-------------|
| `api-version` | Krävs | Den version som används för att använda begäran.
| `format` | Valfritt* | Formatet ( `json` eller ) för `text` svaret. *Obs! Kan krävas när du använder begärandeparametrar

Den här slutpunkten stöder svarsfiltrering via [vägparametrar](#route-parameters).

#### <a name="response"></a>Svarsåtgärder

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Schemauppdelning:

**Beräkning**

| Data | Description | Version introducerad |
|------|-------------|--------------------|
| `azEnvironment` | Azure-miljö där den virtuella datorn körs i | 2018-10-01
| `customData` | Den här funktionen är inaktuell och inaktiverad. Den har ersatts av `userData` | 2019-02-01
| `evictionPolicy` | Anger hur en [virtuell spot-dator](../articles/virtual-machines/spot-vms.md) ska avlägsnas. | 2020-12-01
| `isHostCompatibilityLayerVm` | Anger om den virtuella datorn körs på värdkompatibilitetsskiktet | 2020-06-01
| `licenseType` | Typ av licens för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit). Detta gäller endast för AHB-aktiverade virtuella datorer | 2020-09-01
| `location` | Azure-region som den virtuella datorn körs i | 2017-04-02
| `name` | Namnet på den virtuella datorn | 2017-04-02
| `offer` | Erbjudandeinformation för VM-avbildningen och finns endast för avbildningar som distribuerats från Azure-avbildningsgalleriet | 2017-04-02
| `osProfile.adminUsername` | Anger namnet på administratörskontot | 2020-07-15
| `osProfile.computerName` | Anger namnet på datorn | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Anger om lösenordsautentisering är inaktiverat. Detta gäller endast för virtuella Linux-datorer | 2020-10-01
| `osType` | Linux eller Windows | 2017-04-02
| `placementGroupId` | [Placeringsgrupp för](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) VM-skalningsuppsättningen | 2017-08-01
| `plan` | [Planera](/rest/api/compute/virtualmachines/createorupdate#plan) med namn, produkt och utgivare för en virtuell dator om det är en Azure Marketplace avbildning | 2018-04-02
| `platformUpdateDomain` |  [Uppdateringsdomän](../articles/virtual-machines/availability.md) som den virtuella datorn körs i | 2017-04-02
| `platformFaultDomain` | [Feldomän](../articles/virtual-machines/availability.md) som den virtuella datorn körs i | 2017-04-02
| `priority` | Prioritet för den virtuella datorn. Mer information [finns i Virtuella spot-datorer](../articles/virtual-machines/spot-vms.md) | 2020-12-01
| `provider` | Provider för den virtuella datorn | 2018-10-01
| `publicKeys` | [Samling offentliga nycklar tilldelade till](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) den virtuella datorn och sökvägar | 2018-04-02
| `publisher` | Utgivare av VM-avbildningen | 2017-04-02
| `resourceGroupName` | [Resursgrupp](../articles/azure-resource-manager/management/overview.md) för den virtuella datorn | 2017-08-01
| `resourceId` | Resursens [fullständigt](/rest/api/resources/resources/getbyid) kvalificerade ID | 2019-03-11
| `sku` | Specifik SKU för VM-avbildningen | 2017-04-02
| `securityProfile.secureBootEnabled` | Anger om säker UEFI-start är aktiverat på den virtuella datorn | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Anger om den virtuella Trusted Platform Module (TPM) är aktiverad på den virtuella datorn | 2020-06-01
| `storageProfile` | Se Lagringsprofil nedan | 2019-06-01
| `subscriptionId` | Azure-prenumeration för den virtuella datorn | 2017-08-01
| `tags` | [Taggar](../articles/azure-resource-manager/management/tag-resources.md) för din virtuella dator  | 2017-08-01
| `tagsList` | Taggar formaterade som en JSON-matris för enklare programmatisk parsning  | 2019-06-04
| `userData` | Den datauppsättning som angavs när den virtuella datorn skapades för användning under eller efter etableringen (Base64-kodad)  | 2021-01-01
| `version` | Version av VM-avbildningen | 2017-04-02
| `vmId` | [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
| `vmScaleSetName` | [VM-skalningsuppsättningEns namn](../articles/virtual-machine-scale-sets/overview.md) på VM-skalningsuppsättningen | 2017-12-01
| `vmSize` | [VM-storlek](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Tillgänglighetszon](../articles/availability-zones/az-overview.md) för den virtuella datorn | 2017-12-01

**Lagringsprofil**

Lagringsprofilen för en virtuell dator är indelad i tre kategorier: avbildningsreferens, OS-disk och datadiskar.

Avbildningsreferensobjektet innehåller följande information om OS-avbildningen:

| Data | Description |
|------|-------------|
| `id` | Resurs-ID
| `offer` | Erbjudande om plattforms- eller marketplace-avbildning
| `publisher` | Avbildningsutgivare
| `sku` | Bild-SKU
| `version` | Version av plattformen eller Marketplace-avbildningen

OS-diskobjektet innehåller följande information om OS-disken som används av den virtuella datorn:

| Data | Description |
|------|-------------|
| `caching` | Krav för cachelagring
| `createOption` | Information om hur den virtuella datorn skapades
| `diffDiskSettings` | Tillfälliga diskinställningar
| `diskSizeGB` | Diskens storlek i GB
| `image`   | Virtuell hårddisk för källanvändaravbildning
| `lun`     | Diskens logiska enhetsnummer
| `managedDisk` | Parametrar för hanterade diskar
| `name`    | Disknamn
| `vhd`     | Virtuell hårddisk
| `writeAcceleratorEnabled` | Huruvida writeAccelerator är aktiverat på disken eller inte

Matrisen för datadiskar innehåller en lista över datadiskar som är anslutna till den virtuella datorn. Varje datadiskobjekt innehåller följande information:

Data | Description |
-----|-------------|
| `caching` | Krav för cachelagring
| `createOption` | Information om hur den virtuella datorn skapades
| `diffDiskSettings` | Tillfälliga diskinställningar
| `diskSizeGB` | Diskens storlek i GB
| `encryptionSettings` | Krypteringsinställningar för disken
| `image` | Virtuell hårddisk för källanvändaravbildning
| `managedDisk` | Parametrar för hanterade diskar
| `name` | Disknamn
| `osType` | Typ av operativsystem som ingår i disken
| `vhd` | Virtuell hårddisk
| `writeAcceleratorEnabled` | Huruvida writeAccelerator är aktiverat på disken eller inte

**Nätverk**

| Data | Description | Version introducerad |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Den virtuella datorns lokala IPv4-adress | 2017-04-02
| `ipv4.publicIpAddress` | Den virtuella datorns offentliga IPv4-adress | 2017-04-02
| `subnet.address` | Den virtuella datorns undernätsadress | 2017-04-02
| `subnet.prefix` | Undernätsprefix, exempel 24 | 2017-04-02
| `ipv6.ipAddress` | Den virtuella datorns lokala IPv6-adress | 2017-04-02
| `macAddress` | Mac-adress för virtuell dator | 2017-04-02

### <a name="get-user-data"></a>Hämta användardata

När du skapar en ny virtuell dator kan du ange en uppsättning data som ska användas under eller efter etableringen av den virtuella datorn och hämta dem via IMDS. Om du vill konfigurera användardata använder du snabbstartsmallen [här](https://aka.ms/ImdsUserDataArmTemplate). Exemplet nedan visar hur du hämtar dessa data via IMDS.

> [!NOTE]
> Den här funktionen lanseras med version och är beroende av en uppdatering av Azure-plattformen, som för närvarande distribueras och kanske ännu inte `2021-01-01` är tillgänglig i varje region.

> [!NOTE]
> Säkerhetsmeddelande: IMDS är öppet för alla program på den virtuella datorn, känsliga data ska inte placeras i användardata.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Exempel 1: Spåra virtuella datorer som körs på Azure

Som tjänstleverantör kan du behöva spåra antalet virtuella datorer som kör din programvara eller ha agenter som behöver spåra den virtuella datorns unika egenskaper. Om du vill kunna hämta ett unikt ID för en virtuell dator använder du `vmId` fältet från Instance Metadata Service.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Response**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Exempel 2: Placering av olika datarepliker

För vissa scenarier är placering av olika datarepliker av största vikt. [HdFS-replikplacering eller](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) containerplacering via [](https://kubernetes.io/docs/user-guide/node-selection/) en initierare kan till exempel kräva att du känner till och `platformFaultDomain` att den virtuella datorn körs `platformUpdateDomain` på.
Du kan också använda [Tillgänglighetszoner](../articles/availability-zones/az-overview.md) för instanserna för att fatta dessa beslut.
Du kan köra frågor mot dessa data direkt via IMDS.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Exempel 3: Hämta VM-taggar

VM-taggar ingår i instans-API:et under slutpunkten instans/beräkning/taggar.
Taggar kan ha tillämpats på din virtuella Azure-dator för att logiskt ordna dem i en taxonomi. Taggarna som tilldelats en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Response**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

Fältet `tags` är en sträng med taggar som avgränsas med semikolon. Dessa utdata kan vara ett problem om semikolon används i själva taggarna. Om en parser skrivs för att programmatiskt extrahera taggarna bör du förlita dig på `tagsList` fältet . Fältet `tagsList` är en JSON-matris utan avgränsare och därför enklare att parsa. TagsList som tilldelats en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Response**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Exempel 4: Få mer information om den virtuella datorn under supportfall

Som tjänstleverantör kan du få ett supportsamtal där du vill veta mer om den virtuella datorn. Att be kunden att dela beräkningsmetadata kan ge supporten grundläggande information om vilken typ av virtuell dator som finns i Azure.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är ganska utskrivet för läsbarhet.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Exempel 5: Hämta Azure-miljön där den virtuella datorn körs

Azure har olika nationella moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Ibland behöver du Azure-miljön för att fatta vissa körningsbeslut. I följande exempel visas hur du kan uppnå det här beteendet.

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Response**

```
AzurePublicCloud
```

Molnet och värdena för Azure-miljön visas här.

| Moln | Azure-miljö |
|-------|-------------------|
| [Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Exempel 6: Hämta nätverksinformation

**Förfrågan**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Exempel 7: Hämta offentlig IP-adress

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Attesterade data

### <a name="get-attested-data"></a>Hämta attesterade data

IMDS hjälper till att ge garantier om att de data som tillhandahålls kommer från Azure. Microsoft signerar en del av den här informationen så att du kan bekräfta att Azure Marketplace avbildningen är den som du kör i Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Parametrar

| Name | Obligatorisk/valfri | Beskrivning |
|------|-------------------|-------------|
| `api-version` | Krävs | Den version som används för att skicka begäran.
| `nonce` | Valfritt | En 10-siffrig sträng som fungerar som en kryptografisk nonce. Om inget värde anges använder IMDS den aktuella UTC-tidsstämpeln.

#### <a name="response"></a>Svarsåtgärder

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> På grund av IMDS:s cachelagringsmekanism kan ett tidigare cachelagrat nonce-värde returneras.

Signaturbloben är en [pkcs7-signerad](https://aka.ms/pkcs7)version av dokumentet. Den innehåller certifikatet som används för signering tillsammans med viss VM-specifik information.

För virtuella datorer som skapats med Azure Resource Manager innehåller dokumentet , , , , för att skapa och förfalla för dokumentet och `vmId` `sku` `nonce` `subscriptionId` `timeStamp` planinformation om avbildningen. Planinformationen fylls bara i för Azure Marketplace bilder.

För virtuella datorer som skapats med hjälp av den klassiska `vmId` distributionsmodellen fylls endast i. Du kan extrahera certifikatet från svaret och använda det för att bekräfta att svaret är giltigt och kommer från Azure.

Det avkodade dokumentet innehåller följande fält:

| Data | Description | Version introducerad |
|------|-------------|--------------------|
| `licenseType` | Typ av licens för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit). Detta gäller endast för AHB-aktiverade virtuella datorer. | 2020-09-01
| `nonce` | En sträng som kan anges med begäran om du vill. Om inget `nonce` har angetts används den Coordinated Universal Time tidsstämpeln. | 2018-10-01
| `plan` | [Avbildningsplanen Azure Marketplace .](/rest/api/compute/virtualmachines/createorupdate#plan) Innehåller plan-ID (namn), produktavbildning eller erbjudande (produkt) och utgivar-ID (utgivare). | 2018-10-01
| `timestamp.createdOn` | UTC-tidsstämpeln för när det signerade dokumentet skapades | 2018-20-01
| `timestamp.expiresOn` | UTC-tidsstämpeln för när det signerade dokumentet upphör att gälla | 2018-10-01
| `vmId` | [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2018-10-01
| `subscriptionId` | Azure-prenumeration för den virtuella datorn | 2019-04-30
| `sku` | Specifik SKU för VM-avbildningen | 2019-11-01

> [!NOTE]
> För klassiska (icke-Azure Resource Manager) virtuella datorer är det garanterat bara vmId som fylls i.

Exempeldokument:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Exempel 1: Verifiera att den virtuella datorn körs i Azure

Leverantörer i Azure Marketplace vill se till att deras programvara är licensierad att endast köras i Azure. Om någon kopierar den virtuella hårddisken till en lokal miljö måste leverantören kunna identifiera den. Via IMDS kan dessa leverantörer hämta signerade data som endast garanterar svar från Azure.

> [!NOTE]
> Det här exemplet kräver att jq-verktyget installeras.

**Validering**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Kontrollera att signaturen kommer Microsoft Azure och kontrollera om certifikatkedjan innehåller fel.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Resultat**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Kontrollera att signaturen kommer Microsoft Azure och kontrollera om det finns fel i certifikatkedjan.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> På grund av IMDS:s cachelagringsmekanism kan ett tidigare `nonce` cachelagrat värde returneras.

I `nonce` det signerade dokumentet kan jämföras om du har angett en `nonce` parameter i den första begäran.

> [!NOTE]
> Certifikatet för det offentliga molnet och varje suveränt moln kommer att vara olika.

| Moln | Certifikat |
|-------|-------------|
| [Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Certifikaten kanske inte matchar det `metadata.azure.com` offentliga molnet exakt. Därför bör certifieringsverifieringen tillåta ett eget namn från alla `.metadata.azure.com` underdomäner.

I de fall där det mellanliggande certifikatet inte kan laddas ned på grund av nätverksbegränsningar under verifieringen kan du fästa det mellanliggande certifikatet. Azure rullar över certifikaten, vilket är PKI-standardpraxis. Du måste uppdatera de fästa certifikaten när en rollover sker. När en ändring av mellanliggande certifikat planeras uppdateras Azure-bloggen och Azure-kunder meddelas. 

Du hittar mellanliggande certifikat på [PKI-lagringsplatsen](https://www.microsoft.com/pki/mscorp/cps/default.htm). De mellanliggande certifikaten för var och en av regionerna kan vara olika.

> [!NOTE]
> Det mellanliggande certifikatet för Azure China 21Vianet kommer från DigiCert Global Root CA i stället för Baltimore.
Om du har fäst mellanliggande certifikat för Azure Kina som en del av en ändring av rotkedjeutfärdaren måste mellanliggande certifikat uppdateras.


## <a name="managed-identity"></a>Hanterad identitet

En hanterad identitet som tilldelats av systemet kan aktiveras på den virtuella datorn. Du kan också tilldela en eller flera användar tilldelade hanterade identiteter till den virtuella datorn.
Du kan sedan begära token för hanterade identiteter från IMDS. Använd dessa token för att autentisera med andra Azure-tjänster, till exempel Azure Key Vault.

Detaljerade anvisningar för hur du aktiverar den här funktionen finns i [Hämta en åtkomsttoken.](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

## <a name="load-balancer-metadata"></a>Load Balancer Metadata
När du placerar instanser av virtuella datorer eller virtuella datoruppsättningsinstanser bakom en Azure Standard Load Balancer kan du använda IMDS för att hämta metadata relaterade till lastbalanseraren och instanserna. Mer information finns i Hämta [information om lastbalanserare.](../articles/load-balancer/instance-metadata-service-load-balancer.md)

## <a name="scheduled-events"></a>Schemalagda händelser
Du kan hämta status för schemalagda händelser med hjälp av IMDS. Användaren kan sedan ange en uppsättning åtgärder som ska köras på dessa händelser. Mer information finns i [Schemalagda händelser för Linux eller](../articles/virtual-machines/linux/scheduled-events.md) [Schemalagda händelser för Windows.](../articles/virtual-machines/windows/scheduled-events.md)


## <a name="sample-code-in-different-languages"></a>Exempelkod på olika språk

I följande tabell visas exempel på anrop av IMDS med hjälp av olika språk i den virtuella datorn:

| Språk | Exempel |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Fel och felsökning

Om det inte finns ett dataelement som inte hittas eller en felaktig begäran returnerar Instance Metadata Service HTTP-standardfel. Exempel:

| HTTP-statuskod | Anledning |
|------------------|--------|
| `200 OK` | Begäran lyckades.
| `400 Bad Request` | Saknar `Metadata: true` rubrik eller parameter som saknas när du frågar en `format=json` lövnod
| `404 Not Found` | Det begärda elementet finns inte
| `405 Method Not Allowed` | HTTP-metoden (verb) stöds inte på slutpunkten.
| `410 Gone` | Försök igen efter en stund i högst 70 sekunder
| `429 Too Many Requests` | [API-hastighetsbegränsningar](#rate-limiting) har överskridits
| `500 Service Error` | Försök igen efter en stund

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

- Jag får felet `400 Bad Request, Required metadata header not specified` . Vad betyder detta?
  - IMDS kräver att `Metadata: true` -huvudet skickas i begäran. Genom att skicka det här huvudet i REST-anropet får du åtkomst till IMDS.

- Varför får jag inte beräkningsinformation för min virtuella dator?
  - IMDS stöder för närvarande endast instanser som skapats med Azure Resource Manager.

- Jag skapade min virtuella dator via Azure Resource Manager för en tid sedan. Varför ser jag inte information om beräkningsmetadata?
  - Om du har skapat den virtuella datorn efter september 2016 lägger du till en [tagg för att](../articles/azure-resource-manager/management/tag-resources.md) börja se beräkningsmetadata. Om du har skapat den virtuella datorn före september 2016 lägger du till eller tar bort tillägg eller datadiskar till VM-instansen för att uppdatera metadata.

- Är användardata samma som anpassade data?
  - Användardata har liknande funktioner som anpassade data, så att du kan skicka dina egna metadata till den virtuella datorinstansen. Skillnaden är att användardata hämtas via IMDS och är beständiga under hela livslängden för den virtuella datorinstansen. Befintlig anpassad datafunktion fortsätter att fungera enligt beskrivningen i [den här artikeln.](https://docs.microsoft.com/azure/virtual-machines/custom-data) Du kan dock bara hämta anpassade data via den lokala systemmappen, inte via IMDS.

- Varför ser jag inte alla data ifyllda för en ny version?
  - Om du har skapat den virtuella datorn efter september 2016 lägger du till en [tagg för att](../articles/azure-resource-manager/management/tag-resources.md) börja se beräkningsmetadata. Om du har skapat den virtuella datorn före september 2016 lägger du till eller tar bort tillägg eller datadiskar till VM-instansen för att uppdatera metadata.

- Varför får jag felet `500 Internal Server Error` eller `410 Resource Gone` ?
  - Försök igen med din begäran. Mer information finns i Hantering [av tillfälliga fel.](/azure/architecture/best-practices/transient-faults) Om problemet kvarstår skapar du ett supportproblem i Azure Portal för den virtuella datorn.

- Skulle detta fungera för VM-skalningsuppsättningsinstanser?
  - Ja, IMDS är tillgängligt för vm-skalningsuppsättningsinstanser.

- Jag har uppdaterat mina taggar i VM-skalningsuppsättningar, men de visas inte i instanserna (till skillnad från virtuella datorer med en instans). Gör jag något fel?
  - Taggar för VM-skalningsuppsättningar visas för närvarande endast för den virtuella datorn vid en omstart, avbildning eller diskändring av instansen.

- Varför ser jag inte SKU-informationen för min virtuella dator i `instance/compute` detalj?
  - För anpassade avbildningar Azure Marketplace behåller Azure-plattformen inte SKU-informationen för den anpassade avbildningen och informationen för virtuella datorer som skapats från den anpassade avbildningen. Detta är enligt design och kan därför inte visas i informationen om den virtuella `instance/compute` datorn.

- Varför har min begäran time out för mitt anrop till tjänsten?
  - Metadata-anrop måste göras från den primära IP-adressen som tilldelats till den virtuella datorns primära nätverkskort. Om du har ändrat vägarna måste det dessutom finnas en väg för adressen 169.254.169.254/32 i den virtuella datorns lokala routningstabell.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Dumpa din lokala routningstabell och leta efter IMDS-posten. Exempel:
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Kontrollera att det finns en väg för `169.254.169.254` och notera motsvarande nätverksgränssnitt (till exempel `172.16.69.7` ).
    1. Dumpa gränssnittskonfigurationen och leta upp det gränssnitt som motsvarar det som refereras till i routningstabellen och se MAC-adressen (fysisk).
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Bekräfta att gränssnittet motsvarar den virtuella datorns primära nätverkskort och primära IP-adress. Du hittar det primära nätverkskortet och IP-adressen genom att titta på nätverkskonfigurationen i Azure Portal, eller genom att leta upp det med Azure CLI. Anteckna de privata IP-adresserna (och MAC-adressen om du använder CLI). Här är ett PowerShell CLI-exempel:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Om de inte matchar uppdaterar du routningstabellen så att det primära nätverkskortet och IP-adressen är mål.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Dumpa din lokala routningstabell med ett kommando som `netstat -r` och leta efter IMDS-posten (t.ex.):
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Kontrollera att det finns en väg `169.254.169.254` för och notera motsvarande nätverksgränssnitt (t.ex. `eth0` ).
    1. Dumpa gränssnittskonfigurationen för motsvarande gränssnitt i routningstabellen (observera att det exakta namnet på konfigurationsfilen kan variera)
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Observera MAC-adressen om du använder en dynamisk IP-adress. Om du använder en statisk IP-adress kan du notera de angivna IP-adresserna och/eller MAC-adressen.
    1. Kontrollera att gränssnittet motsvarar den virtuella datorns primära nätverkskort och primära IP-adress. Du hittar det primära nätverkskortet och IP-adressen genom att titta på nätverkskonfigurationen i Azure Portal eller genom att leta upp den med Azure CLI. Anteckna de privata IP-adresserna (och MAC-adressen om du använder CLI). Här är ett PowerShell CLI-exempel:
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Om de inte matchar uppdaterar du routningstabellen så att det primära nätverkskortet/IP-adressen är mål.

    ---

- Redundansklustring i Windows Server
  - När du kör frågor mot IMDS med redundansklustring är det ibland nödvändigt att lägga till en väg i routningstabellen. Gör så här:

    1. Öppna en kommandotolk med administratörsbehörighet.

    1. Kör följande kommando och anteckna adressen för gränssnittet för nätverksmål ( `0.0.0.0` ) i IPv4-vägtabellen.

    ```bat
    route print
    ```

    > [!NOTE]
    > Följande exempelutdata kommer från en virtuell Windows Server-dator med aktiverat redundanskluster. För enkelhetens skull innehåller utdata endast IPv4-vägtabellen.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Kör följande kommando och använd adressen för Gränssnittet för nätverksmål ( `0.0.0.0` ), som är ( ) i det här `10.0.1.10` exemplet.

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Support

Om du inte kan få ett metadatasvar efter flera försök kan du skapa ett supportproblem i Azure Portal.

## <a name="product-feedback"></a>Produktfeedback

Du kan ge produktfeedback och idéer till vår kanal för användarfeedback under Virtual Machines > Instance Metadata Service [här](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)

## <a name="next-steps"></a>Nästa steg

- [Hämta en åtkomsttoken för den virtuella datorn](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Schemalagda händelser för Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md)
