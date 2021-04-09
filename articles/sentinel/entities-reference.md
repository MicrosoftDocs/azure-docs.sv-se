---
title: Referens för Azure Sentinel-enhets typer | Microsoft Docs
description: I den här artikeln visas enhets typerna för Azure Sentinel och de identifierare som krävs.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456342"
---
# <a name="azure-sentinel-entity-types-reference"></a>Referens för Azure Sentinel-enhets typer

## <a name="entity-types-and-identifiers"></a>Entitetstyper och identifierare

I följande tabell visas de **entitetstyper** som för närvarande är tillgängliga för mappning i Azure Sentinel, och de **attribut** som är tillgängliga som **identifierare** för varje entitetstyp – som visas i list rutan **identifierare** i avsnittet för [enhets mappning](map-data-fields-to-entities.md) i [guiden Analytics-regel](tutorial-detect-threats-custom.md).

Var och en av identifierarna i kolumnen **obligatoriska identifierare** är minimalt nödvändiga för att identifiera dess entitet. Men en identifierare som krävs kanske inte själva är tillräcklig för att ge *unik* identifiering. De fler identifierare som används, desto större sannolikheten för unik identifiering. Du kan använda upp till tre identifierare för en enda enhets mappning.

För bästa resultat – du bör använda identifierare från kolumnen **starkast identifierare** närhelst det är möjligt. Användningen av flera starka identifierare möjliggör korrelation mellan starka identifierare från varierande data källor och scheman. På så sätt kan Azure Sentinel tillhandahålla mer omfattande insikter för en specifik entitet.

| Entitetstyp | Identifierare | Identifierare som krävs | Starkast identifierare |
| - | - | - | - |
| [**Användar konto**](#user-account)<br>*Redovisning* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>Name<br>AadUserId<br>PUID<br>ObjectGuid | Namn + NTDomain<br>Namn + UPNSuffix<br>AADUserId<br>Sid |
| [**Värd**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosNamn<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosNamn<br>AzureID<br>OMSAgentID | HostName + NTDomain<br>HostName + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP-adress**](#ip-address)<br>*SÖKNING* | Adress | Adress | |
| [**Skadlig kod**](#malware) | Name<br>Kategori | Name | |
| [**Fil**](#file) | Katalog<br>Namn | Namn | |
| [**Process**](#process) | ProcessId<br>Raden<br>ElevationToken<br>CreationTimeUtc | Raden<br>ProcessId | |
| [**Moln program**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Domän namn**](#domain-name)<br>*DNS* | DomainName | DomainName | |
| [**Azure-resurs**](#azure-resource) | ResourceId | ResourceId | |
| [**Filhash-värde**](#file-hash)<br>*(FileHash)* | Integritetsalgoritm<br>Värde | Algoritm + värde | |
| [**Register nyckel**](#registry-key) | Hive<br>Nyckel | Hive<br>Nyckel | Hive + nyckel |
| [**Register värde**](#registry-value) | Name<br>Värde<br>Värdetyp | Name | |
| [**Säkerhets grupp**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | URL | URL | |
| [**IoT-enhet**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Källa<br>SourceRef<br>Tillverkare<br>Modell<br>OperatingSystem<br>Adresser<br>MacAddress<br>Protokoll<br>Serienummer | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Kvoter**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>UPN<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**E-postkluster**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Hot<br>Fråga<br>QueryTime<br>Antal återförsök<br>IsVolumeAnomaly<br>Källa<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Fråga<br>Källa | Fråga + källa |
| [**E-postmeddelande**](#mail-message) | Mottagare<br>Er<br>Hot<br>Avsändare<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>Ämne<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Språk<br>ThreatDetectionMethods | NetworkMessageId<br>Mottagare | NetworkMessageId + mottagare |
| [**Skicka e-post**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Jobbets avsändare<br>NetworkMessageId<br>Timestamp<br>Mottagare<br>Avsändare<br>SenderIp<br>Ämne<br>ReportType | SubmissionId<br>NetworkMessageId<br>Mottagare<br>Jobbets avsändare |  |
|

## <a name="entity-type-schemas"></a>Enhets typ scheman

Följande är en mer ingående titt på de fullständiga scheman för varje entitetstyp. Du ser att många av dessa scheman innehåller länkar till andra entitetstyper, till exempel om schemat för användar kontot innehåller en länk till typen av entitetstyp, eftersom ett-attribut för ett användar konto är den värd som den är definierad på. Dessa externt länkade entiteter kan inte användas som identifierare för enhets mappning, men de är mycket användbara när du vill ge en komplett bild av entiteter på entitetsformulär och i undersöknings diagrammet.

> [!NOTE]
> Ett frågetecken som följer värdet i kolumnen **typ** anger att fältet är nullbar.

## <a name="user-account"></a>Användar konto

*Entitetsnamn: konto*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | redovisning |
| Name | Sträng | Namnet på kontot. Det här fältet ska bara innehålla namnet utan att någon domän har lagts till. |
| *FullName* | *EJ TILLÄMPLIGT* | *Ingår inte i schemat, ingår för bakåtkompatibilitet med en gammal version av enhets mappning.*
| NTDomain | Sträng | NETBIOS-domännamnet som det visas i aviserings formatet – domain\username. Exempel: ekonomi, NT-auktoritet |
| DnsDomain | Sträng | Det fullständigt kvalificerade domänens DNS-namn. Exempel: finance.contoso.com |
| UPNSuffix | Sträng | User Principal Name suffix för kontot. I vissa fall är detta även domän namnet. Exempel: contoso.com |
| Värd | Entitet | Värden som innehåller kontot, om det är ett lokalt konto. |
| Sid | Sträng | Konto säkerhets identifieraren, till exempel S-1-5-18. |
| AadTenantId | LED? | Azure AD-klient-ID, om det är känt. |
| AadUserId | LED? | Objekt-ID för Azure AD-kontot, om det är känt. |
| PUID | LED? | Användar-ID för Azure AD Passport, om det är känt. |
| IsDomainJoined | Booleska? | Anger om det här är ett domän konto. |
| DisplayName | Sträng | Kontots visnings namn. |
| ObjectGuid | LED? | Attributet objectGUID är ett attribut med en enda värde som är den unika identifieraren för objektet som tilldelas av Active Directory. |
|

Starka identifierare för en konto enhet:

- Namn + UPNSuffix
- AadUserId
- Sid + värd (krävs för sid för inbyggda konton)
- Sid (förutom sid för inbyggda konton)
- Namn + NTDomain (om inte NTDomain är en Builtin domän, till exempel "arbets grupp")
- Namn + värd (om NTDomain är en Builtin domän, till exempel "arbets grupp")
- Namn + DnsDomain
- PUID
- ObjectGuid

Svaga identifierare för en konto enhet:

- Name

## <a name="host"></a>Värd

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | värd |
| DnsDomain | Sträng | Den DNS-domän som den här värden tillhör. Ska innehålla det fullständiga DNS-suffixet för domänen, om det är känt. |
| NTDomain | Sträng | Den NT-domän som denna värd tillhör. |
| HostName | Sträng | Värdnamn utan domänsuffix. |
| *FullName* | *EJ TILLÄMPLIGT* | *Ingår inte i schemat, ingår för bakåtkompatibilitet med en gammal version av enhets mappning.*
| NetBiosNamn | Sträng | Värd namnet (före Windows 2000). |
| IoTDevice | Entitet | IoT Device-entiteten (om denna värd representerar en IoT-enhet). |
| AzureID | Sträng | Azure-resurs-ID för den virtuella datorn, om det är känt. |
| OMSAgentID | Sträng | OMS-agentens ID, om värden har OMS-agent installerad. |
| OSFamily | Räkning? | En av följande värden: <li>Linux<li>Windows<li>Android<li>iOS |
| OSVersion | Sträng | En fri text representation av operativ systemet.<br>Det här fältet är avsett att innehålla vissa versioner som är mer detaljerade än OSFamily eller framtida värden som inte stöds av OSFamily-uppräkning. |
| IsDomainJoined | Bool | Anger om värden tillhör en domän. |
|

Starka identifierare för en värd enhet:
- HostName + NTDomain
- HostName + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (stöds inte för enhets mappning)

Svaga identifierare för en värd enhet:
- HostName
- NetBiosNamn

## <a name="ip-address"></a>IP-adress

*Enhets namn: IP*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | sökning |
| Adress | Sträng | IP-adressen som sträng, t. ex. 127.0.0.1 (antingen i IPv4 eller IPv6). |
| Location | Geoplats | Den Geo-Location-kontext som är kopplad till IP-entiteten. |
|

Starka identifierare för en IP-entitet:
- Adress

## <a name="malware"></a>Skadlig kod

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | skadlig kod |
| Name | Sträng | Namnet på skadlig kod av leverantören, till exempel `Win32/Toga!rfn` . |
| Kategori | Sträng | Kategorin skadlig kod av leverantören, t. ex. Trojan. |
| Filer | Lista\<Entity> | Lista över länkade filentiteter där skadlig kod hittades. Kan innehålla filen entiteter i rad eller som referens.<br>Mer information om strukturen finns i entiteten fil. |
| Processer | Lista\<Entity> | Lista över länkade process enheter där skadlig kod hittades. Detta används ofta när aviseringen utlöses vid en fil lös aktivitet.<br>Mer information om strukturen finns i entiteten [process](#process) . |
|

Starka identifierare för en entitet med skadlig kod:

- Namn + kategori

## <a name="file"></a>Fil

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | Arkiv |
| Katalog | Sträng | Den fullständiga sökvägen till filen. |
| Name | Sträng | Fil namnet utan sökvägen (vissa aviseringar kanske inte innehåller någon sökväg). |
| Värd | Entitet | Värden som filen lagrades på. |
| FileHashes | Lista &lt; entitet&gt; | De filhashar som är associerade med den här filen. |
|

Starka identifierare för en filentitet:
- Namn + katalog
- Namn + FileHash
- Namn + katalog + FileHash

## <a name="process"></a>Process

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | uppgraderingen |
| ProcessId | Sträng | Process-ID. |
| Raden | Sträng | Den kommando rad som används för att skapa processen. |
| ElevationToken | Räkning? | Höjnings-token som är associerad med processen.<br>Möjliga värden:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | Tiden då processen började köras. |
| ImageFile | Entitet (fil) | Kan innehålla filens enhets infogade eller som referens.<br>Mer information om strukturen finns i entiteten fil. |
| Konto | Entitet | Kontot som kör processerna.<br>Kan innehålla en infogad [konto](#user-account) enhet eller som referens.<br>Mer information om strukturen finns i entiteten [konto](#user-account) . |
| ParentProcess | Entitet (process) | Den överordnade process enheten. <br>Kan innehålla ofullständiga data, d.v.s. bara PID. |
| Värd | Entitet | Värden som processen kördes på. |
| LogonSession | Entitet (HostLogonSession) | Sessionen där processen kördes. |
|

Starka identifierare för en process enhet:

- Värd + ProcessId + CreationTimeUtc
- Värd + ParentProcessId + CreationTimeUtc + kommando rad
- Värd + ProcessId + CreationTimeUtc + ImageFile
- Värd + ProcessId + CreationTimeUtc + ImageFile. FileHash

Svaga identifierare för en process-entitet:

- ProcessId + CreationTimeUtc + kommando rad (och ingen värd)
- ProcessId + CreationTimeUtc + ImageFile (och ingen värd)

## <a name="cloud-application"></a>Moln program

*Entitetsnamn: CloudApplication*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | ' Cloud-Application ' |
| AppId | Int | Programmets tekniska ID. Detta ska vara ett av de värden som definierats i listan över [moln program identifierare](#cloud-application-identifiers). Värdet för AppId-fältet är valfritt. |
| Name | Sträng | Namnet på det relaterade moln programmet. Värdet för program namn är valfritt. |
| InstanceName | Sträng | Det användardefinierade instans namnet för moln programmet. Den används ofta för att skilja mellan olika program av samma typ som en kund har. |
|

Starka identifierare för en molnbaserad program enhet:
 - AppId (utan instans namn)
 - Namn (utan instans namn)
 - AppId + InstanceName
 - Namn + instans namn

## <a name="domain-name"></a>Domännamn

*Enhets namn: DNS*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | DNS |
| DomainName | Sträng | Namnet på DNS-posten som är associerad med aviseringen. |
| Adresser | Lista &lt; entitet (IP)&gt; | Entiteter som motsvarar de matchade IP-adresserna. |
| DnsServerIp | Entitet (IP) | En entitet som representerar DNS-servern som matchar begäran. |
| HostIpAddress | Entitet (IP) | En entitet som representerar klienten för DNS-begäran. |
|

Starka identifierare för en DNS-entitet:
- Domän \ DnsServerIp + HostIpAddress

Svaga identifierare för en DNS-entitet:
- Domän \ HostIpAddress

## <a name="azure-resource"></a>Azure-resurs

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | "Azure-Resource" |
| ResourceId | Sträng | Resurs-ID: t för resursen. |
| SubscriptionId | Sträng | Resursens prenumerations-ID. |
| TryGetResourceGroup | Bool | Resurs grupp svärdet om det finns. |
| TryGetProvider | Bool | Värdet Provider om det finns. |
| TryGetName | Bool | Värdet Name om det finns. |
|

Starka identifierare för en Azure-resurs-entitet:
- ResourceId

## <a name="file-hash"></a>Filhash-värde

*Entitetsnamn: FileHash*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | 'filehash' |
| Integritetsalgoritm | Enum | Typen av hash-algoritm. Möjliga värden:<li>Okänt<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Värde | Sträng | Hash-värdet. |
|

Starka identifierare för en filhash-entitet:
- Algoritm + värde

## <a name="registry-key"></a>Registernyckel

*Entitetsnamn: RegistryKey*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | "register nyckel" |
| Hive | Räkning? | En av följande värden:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Nyckel | Sträng | Sökvägen till register nyckeln. |
|

Starka identifierare för en register nyckel entitet:
- Hive + nyckel

## <a name="registry-value"></a>Registervärde

*Entitetsnamn: RegistryValue*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | "register värde" |
| Nyckel | Entitet (RegistryKey) | Entiteten register nyckel. |
| Name | Sträng | Register värdes namnet. |
| Värde | Sträng | Sträng formaterad representation av värde data. |
| Värdetyp | Räkning? | En av följande värden:<li>Sträng<li>Binär<li>DWord<li>QWORD<li>Multisträng<li>ExpandString<li>Inget<li>Okänt<br>Värdena bör följas av uppräkningen Microsoft. Win32. RegistryValueKind. |
|

Starka identifierare för en register värdes enhet:
- Nyckel + namn

Svaga identifierare för en register värdes enhet:
- Namn (utan nyckel)

## <a name="security-group"></a>Säkerhets grupp

*Entitetsnamn: SecurityGroup*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | säkerhets grupp |
| DistinguishedName | Sträng | Gruppens unika namn. |
| SID | Sträng | SID-attributet är ett attribut med en enda värde som anger gruppens säkerhets identifierare (SID). |
| ObjectGuid | LED? | Attributet objectGUID är ett attribut med en enda värde som är den unika identifieraren för objektet som tilldelas av Active Directory. |
|

Starka identifierare för en säkerhets grupp entitet:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | adresser |
| URL | URI | En fullständig URL som entiteten pekar på. |
|

Starka identifierare för en URL-entitet:
- URL (när en absolut URL)

Svaga identifierare för en URL-entitet:
- URL (när en relativ URL)

## <a name="iot-device"></a>IoT-enhet

*Entitetsnamn: IoTDevice*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | 'iotdevice' |
| IoTHub | Entitet (AzureResource) | AzureResource-entiteten som representerar IoT Hub enheten tillhör. |
| DeviceId | Sträng | ID för enheten i IoT Hubs kontext. |
| DeviceName | Sträng | Det egna namnet på enheten. |
| IoTSecurityAgentId | LED? | ID: t för den *Defender för IoT* -agent som körs på enheten. |
| DeviceType | Sträng | Enhetens typ ("temperatur sensor", "frysning", "lindning turbin" osv.). |
| Källa | Sträng | Källan (Microsoft/leverantören) för enhets enheten. |
| SourceRef | Entitet (URL) | En URL-referens till det käll objekt där enheten hanteras. |
| Tillverkare | Sträng | Enhetstillverkaren. |
| Modell | Sträng | Enhetsmodellen. |
| OperatingSystem | Sträng | Operativ systemet som enheten körs på. |
| Adresser | Entitet (IP) | Enhetens aktuella IP-adress. |
| MacAddress | Sträng | Enhetens MAC-adress. |
| Protokoll | List &lt; sträng&gt; | En lista över protokoll som enheten stöder. |
| Serienummer | Sträng | Enhetens serienummer. |
|

Starka identifierare för en IoT Device-entitet:
- IoTHub + DeviceId

Svaga identifierare för en IoT Device-entitet:
- DeviceId (utan IoTHub)

## <a name="mailbox"></a>Mailbox

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | kvoter |
| MailboxPrimaryAddress | Sträng | Post lådans primära adress. |
| DisplayName | Sträng | Post lådans visnings namn. |
| UPN | Sträng | Post lådans UPN. |
| RiskLevel | Räkning? | Post lådans risk nivå. Möjliga värden:<li>Inget<li>Låg<li>Medel<li>Högt |
| ExternalDirectoryObjectId | LED? | AzureAD-ID för post låda. Liknar AadUserId i entiteten Account, men den här egenskapen är bara unik för post lådans objekt på Office-sidan. |
|

Starka identifierare för en e-postentitet:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>E-postkluster

*Entitetsnamn: ett kluster*

> [!NOTE]
> **MDO**  =  **Microsoft Defender för Office 365**, tidigare kallat Office 365 Advanced Threat Protection (O365 ATP).

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | ' e-kluster ' |
| NetworkMessageIds | IList- &lt; sträng&gt; | E-postmeddelande-ID: n som ingår i e-postklustret. |
| CountByDeliveryStatus | IDictionary &lt; -sträng, int&gt; | Antal e-postmeddelanden per DeliveryStatus-sträng representation. |
| CountByThreatType | IDictionary &lt; -sträng, int&gt; | Antal e-postmeddelanden per ThreatType-sträng representation. |
| CountByProtectionStatus | IDictionary &lt; -sträng, lång&gt; | Antal e-postmeddelanden efter hot skydds status. |
| Hot | IList- &lt; sträng&gt; | Hot från e-postmeddelanden som ingår i e-postklustret. |
| Fråga | Sträng | Frågan som användes för att identifiera e-postklustrets meddelanden. |
| QueryTime | DateTime? | Frågans tid. |
| Antal återförsök | Int? | Antalet e-postmeddelanden som ingår i e-postklustret. |
| IsVolumeAnomaly | Booleska? | Avgör om detta är ett volym avvikande e-postkluster. |
| Källa | Sträng | E-postklustrets källa (standard är O365 ATP). |
| ClusterSourceIdentifier | Sträng | Nätverks meddelande-ID: t för e-postmeddelandet som är källan till det här e-postklustret. |
| ClusterSourceType | Sträng | Käll typen för e-postklustret. Detta mappar till MailClusterSourceType-inställningen från MDO (se anmärkning ovan). |
| ClusterQueryStartTime | DateTime? | Klustrets start tid – används som start tid för frågan antal kluster antal. Vanligt vis datum till slut tiden minus DaysToLookBack-inställningen från MDO (se anmärkning ovan). |
| ClusterQueryEndTime | DateTime? | Klustrets slut tid – används som slut tid för frågan antal kluster antal. Vanligt vis tas e-postdata emot. |
| ClusterGroup | Sträng | Motsvarar den Kusto som används på MDO (se anmärkning ovan). |
|

Starka identifierare för en e-postkluster-entitet:
- Fråga + källa

## <a name="mail-message"></a>E-postmeddelande

*Entitetsnamn: meddelande*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | "e-postmeddelande" |
| Filer | IList- &lt; fil&gt; | Filentiteter för det här e-postmeddelandets bifogade filer. |
| Mottagare | Sträng | Mottagaren av det här e-postmeddelandet. Om det finns flera mottagare kopieras e-postmeddelandet och varje kopia har en mottagare. |
| Er | IList- &lt; sträng&gt; | URL: erna som finns i det här e-postmeddelandet. |
| Hot | IList- &lt; sträng&gt; | Hoten som finns i det här e-postmeddelandet. |
| Avsändare | Sträng | Avsändarens e-postadress. |
| P1Sender | Sträng | E-post-ID för (delegerad) användare som skickade den här e-postmeddelandet "å-vägnar" (primär) användare ". Om e-post inte skickas av delegaten är detta värde lika med P2Sender. |
| P1SenderDisplayName | Sträng | Visnings namn för den (delegerade) användare som skickade det här e-postmeddelandet "på vägnar av P2 (primär) användare". Representeras i e-posthuvudet med egenskapen "OnbehalfofSenderDisplayName". |
| P1SenderDomain | Sträng | E-postdomän för den (delegerade) användare som skickade det här e-postmeddelandet för P2-användare (primär). Om e-post inte skickas av delegaten är detta värde lika med P2SenderDomain. |
| P2Sender | Sträng | E-post för den (primära) användare för vilken det här e-postmeddelandet skickades. |
| P2SenderDisplayName | Sträng | Visnings namn för den (primära) användare för vilken det här e-postmeddelandet skickades. Om e-post inte skickas av ombud representerar det visnings namnet på avsändaren. |
| P2SenderDomain | Sträng | E-postdomän för den (primära) användare för vilken det här e-postmeddelandet skickades. Om e-post inte skickas av ombud representerar det här avsändarens domän. |
| SenderIP | Sträng | Avsändarens IP-adress. |
| ReceivedDate | DateTime | Mottaget datum för det här meddelandet. |
| NetworkMessageId | LED? | Nätverks meddelande-ID för det här e-postmeddelandet. |
| InternetMessageId | Sträng | E-postmeddelandets Internet meddelande-ID. |
| Ämne | Sträng | Ämnet för det här e-postmeddelandet. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Används av Microsoft Defender för Office 365 för att hitta matchande eller liknande e-postmeddelanden. |
| AntispamDirection | Räkning? | Det här e-postmeddelandets riktning. Möjliga värden:<li>Okänt<li>Inkommande<li>Utgående<li>Intraorg (intern) |
| DeliveryAction | Räkning? | Leverans åtgärden för det här e-postmeddelandet. Möjliga värden:<li>Okänt<li>DeliveredAsSpam<li>Levererade<li>Blockerad<li>Ersätter |
| DeliveryLocation | Räkning? | Leverans platsen för det här e-postmeddelandet. Möjliga värden:<li>Okänt<li>Inkorgen<li>JunkFolder<li>DeletedFolder<li>Karantän<li>Extern<li>Misslyckad<li>Släpper<li>Vidarebefordras |
| Språk | Sträng | Det språk som används för att skriva e-postmeddelandets innehåll. |
| ThreatDetectionMethods | IList- &lt; sträng&gt; | Listan över hot identifierings metoder som tillämpas på det här e-postmeddelandet. |
|

Starka identifierare för en e-postmeddelande enhet:
- NetworkMessageId + mottagare

## <a name="submission-mail"></a>Skicka e-post

*Entitetsnamn: SubmissionMail*

| Fält | Typ | Beskrivning |
| ----- | ---- | ----------- |
| Typ | Sträng | 'SubmissionMail' |
| SubmissionId | LED? | Överförings-ID. |
| SubmissionDate | DateTime? | Rapporterade datum och tid för den här sändningen. |
| Jobbets avsändare | Sträng | E-postadressen för sändnings tjänsten. |
| NetworkMessageId | LED? | Nätverks meddelande-ID: t för e-post som överföringen tillhör. |
| Timestamp | DateTime? | Tidsstämpeln när meddelandet tas emot (mail). |
| Mottagare | Sträng | Mottagaren av e-postmeddelandet. |
| Avsändare | Sträng | E-postmeddelandets avsändare. |
| SenderIp | Sträng | Avsändarens IP-adress. |
| Ämne | Sträng | Ämnet för e-post. |
| ReportType | Sträng | Sändnings typ för den aktuella instansen. Detta mappar till skräp, Phish, skadlig kod eller NotJunk. |
|

Starka identifierare för en SubmissionMail-entitet:
- SubmissionId, sändning, NetworkMessageId, mottagare

## <a name="cloud-application-identifiers"></a>Cloud program-ID: n

I följande lista definieras identifierare för kända moln program. App-ID-värdet används som ett ID för [molnbaserad program](#cloud-application) enhet.

|App-ID|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jives-programvara|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive för företag|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk fusion-livscykel|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype för företag|
|25988|Google-dokument|
|26055|Microsoft Office 365 administrations Center|
|26060|OPSWAT växlar|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS Proxy-emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics-personal|
|

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig om enhets struktur, identifierare och schema i Azure Sentinel.

Lär dig mer om [entiteter](entities-in-azure-sentinel.md) och [enhets mappning](map-data-fields-to-entities.md). 
