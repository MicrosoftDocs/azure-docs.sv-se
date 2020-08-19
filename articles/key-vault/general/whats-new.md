---
title: Vad är nytt för Azure Key Vault | Microsoft Docs
description: Senaste uppdateringar för Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 07/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b469ea0d0a91cc4ca7f0602dffbcc63b31c60855
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588372"
---
# <a name="whats-new-for-azure-key-vault"></a>Vad är nytt för Azure Key Vault

> [!WARNING]
> **Juli 2020**: det finns två uppdateringar av nyckel valvet som har möjlighet att påverka implementeringar av tjänsten: [Key Vault Soft-ta bort som standard](#soft-delete-on-by-default) och [Azure TLS-certifikat ändringar](#azure-tls-certificate-changes). Se nedan för mer information.

Här är what's nya med Azure Key Vault. Nya funktioner och förbättringar visas också på [Azure updates Key Vault-kanalen](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="soft-delete-on-by-default"></a>Mjuk borttagning är aktiverat som standard

I slutet av 2020 **är den mjuka borttagningen aktive rad som standard för alla nyckel valv**, både nya och redan befintliga. Fullständig information om den här potentiellt viktiga ändringen, samt steg för att hitta berörda nyckel valv och uppdatera dem i förväg, finns i artikeln [mjuk borttagning aktive ras på alla nyckel valv](soft-delete-change.md). 

## <a name="azure-tls-certificate-changes"></a>Ändringar i Azure TLS-certifikat  

Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rot certifikat utfärdare (ca: er). Den här ändringen görs eftersom de aktuella CA-certifikaten inte uppfyller något av kraven för CA/webbläsarens forum bas linje.

### <a name="when-will-this-change-happen"></a>När sker den här ändringen?

- Azure Active Directory (Azure AD)-tjänster började den här över gången den 7 juli 2020.
- Alla nyligen skapade Azure TLS/SSL-slutpunkter innehåller uppdaterade certifikat som går samman till de nya rot certifikat utfärdarna. 
- Befintliga Azure-slutpunkter övergår i ett stegvist sätt med början den 13 augusti 2020 och slutar att svara den 26 oktober 2020.

> [!IMPORTANT]
> Kunder kan behöva uppdatera sina program efter den här ändringen för att förhindra anslutnings problem vid försök att ansluta till Azure-tjänster. 

### <a name="what-is-changing"></a>Vad ändras?

Idag går de flesta TLS-certifikaten som används av Azure-tjänsterna till följande rot certifikat utfärdare:

| Eget namn på CA: n | Tumavtryck (SHA1) |
|--|--|
| [Baltimore CyberTrust-rot](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

TLS-certifikat som används av Azure-tjänster kommer att kedjas upp till någon av följande rot certifikat utfärdare:

| Eget namn på CA: n | Tumavtryck (SHA1) |
|--|--|
| [DigiCert global root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert global rot certifikat utfärdare](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust-rot](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D – förtroende rot klass 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC rot certifikat utfärdare 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>När kan jag dra tillbaka det gamla mellanliggande tumavtrycket?

De aktuella CA-certifikaten kommer *inte* att återkallas förrän den 15 februari 2021. Efter det datumet kan du ta bort den gamla tumavtrycken från din kod.

Om det här datumet ändras får du ett meddelande om det nya återkallnings datumet.

### <a name="will-this-affect-me"></a>Kommer detta att påverka mig?

Vi räknar med att **de flesta Azure-kunder inte** påverkas.  Ditt program kan dock påverkas om det uttryckligen anger en lista med acceptabla certifikat utfärdare. Den här metoden kallas certifikat fäst.

Här följer några sätt att identifiera om ditt program påverkas:

- Sök i käll koden efter tumavtryck, eget namn och andra certifikat egenskaper för någon av Microsofts IT TLS-certifikatutfärdare som du hittar [här](https://www.microsoft.com/pki/mscorp/cps/default.htm). Om det finns en matchning kommer programmet att påverkas. Lös problemet genom att uppdatera käll koden innehåller de nya certifikat utfärdarna. Bästa praxis är att se till att ca: er kan läggas till eller redige ras på kort varsel. Bransch regler kräver att CA-certifikat ersätts inom 7 dagar och kunder förlitar sig på att fästa på ett snabbt sätt.

- Om du har ett program som integreras med Azure-API: er eller andra Azure-tjänster och du är osäker på om det använder certifikat fäst, kontrollerar du med program leverantören.

- Olika operativ system och språk körningar som kommunicerar med Azure-tjänster kan kräva ytterligare åtgärder för att korrekt bygga certifikat kedjan med följande nya rötter: 
    - **Linux**: många distributioner kräver att du lägger till CAS som anges ovan till/etc/ssl/certs. För detaljerade anvisningar, se distributionens dokumentation.
    - **Java**: kontrol lera att Java-nyckelfilen innehåller de certifikat utfärdare som anges ovan.
    - **Fönster som körs i frånkopplade miljöer**: system som körs i frånkopplade miljöer måste ha de rötter som anges ovan tillagda i arkivet för betrodda rot certifikat utfärdare och de mellanliggande certifikat utfärdare som lagts till i arkivet mellanliggande certifikat utfärdare.
    - **Android**: Mer information finns i dokumentationen för enheten och Android-versionen.
    - **Andra maskin varu enheter, särskilt IoT**: kontakta enhets tillverkaren. 

- Om du har en miljö där brand Väggs regler har ställts in för att tillåta utgående anrop till enbart en speciell lista över återkallade certifikat (CRL) och/eller Online Certificate Status Protocol (OCSP) verifierings platser. Du måste tillåta följande CRL-och OCSP-URL: er:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d-förtroende&#46;net
    - http://root-c3-ca2-2009&#46; OCSP&#46;d-förtroende&#46;net
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com

## <a name="june-2020"></a>Juni 2020

Azure Monitor för Key Vault finns nu som för hands version.  Azure Monitor ger omfattande övervakning av dina nyckel valv genom att leverera en enhetlig vy över dina Key Vaults begär Anden, prestanda, problem och svars tider. Mer information finns i [Azure Monitor för Key Vault (för hands version).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maj 2020

Key Vault "ta med din egen nyckel" (BYOK) är nu allmänt tillgänglig. Se [Azure Key Vault BYOK-specifikationen](../keys/byok-specification.md)och lär dig hur du [importerar HSM-skyddade nycklar till Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Mars 2020

Privata slut punkter finns nu i för hands versionen. Med tjänsten Azure Private Link kan du komma åt Azure Key Vault och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket.  Lär dig hur du [integrerar Key Vault med en privat Azure-länk](private-link-service.md).

## <a name="2019"></a>2019

- Version av nästa generations Azure Key Vault SDK: er. Exempel på hur de används finns i Azure Key Vault hemliga snabb starter för [python](../secrets/quick-create-python.md), [.net](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)och [Node.js](../secrets/quick-create-node.md)
- Nya Azure-principer för att hantera nyckel valvs certifikat. Se [Azure policy inbyggda definitioner för Key Vault](../policy-samples.md).
- Azure Key Vault tillägget för virtuell dator är nu allmänt tillgängligt.  Se [Key Vault tillägg för virtuell dator för Linux](../../virtual-machines/extensions/key-vault-linux.md) och [Key Vault tillägg för virtuell dator för Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Händelse driven hemligheter hanteras för Azure Key Vault nu tillgängligt i Azure Event Grid. Mer information finns i [Event Grid schema för händelser i Azure Key Vault] (.. /.. /Event-Grid/Event-schema-Key-Vault.MD] och lär dig hur du [tar emot och svarar på nyckel valvs meddelanden med Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nya funktioner och integreringar som lanseras i år:

- Integrering med Azure Functions. Ett exempel scenario som utnyttjar [Azure Functions](../../azure-functions/index.yml) för nyckel valvs åtgärder finns i [Automatisera rotationen av en hemlighet](../secrets/tutorial-rotation.md). 
- [Integrering med Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Med det här alternativet stöder Azure Databricks nu två typer av hemliga omfång: Azure Key Vault-och Databricks. Mer information finns i [skapa en Azure Key Vault hemligt hemligt omfång](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nya funktioner som lanseras i år:

- Hanterade lagrings konto nycklar. Funktionen lagrings konto nycklar lades till enklare integrering med Azure Storage. I avsnittet Översikt finns mer information, [Översikt över hanterade lagrings konto nycklar](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Mjuk borttagning. Funktionen mjuk borttagning förbättrar data skyddet för nyckel valven och nyckel valvs objekt. Se översikts avsnittet för mer information, [Översikt över mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nya funktioner som lanseras i år:
- Certifikathantering. Lades till som en funktion i GA version 2015-06-01 den 26 september 2016.

Allmän tillgänglighet (version 2015-06-01) presenterades den 24 juni 2015. Följande ändringar har gjorts i den här versionen: 
- Ta bort ett fält för nyckel användning borttaget.
- Hämta information om ett nyckel-"use"-fält som har tagits bort.
- Importera en nyckel till ett valv-"use"-fält borttaget.
- Återställa en nyckel-"use"-fält som har tagits bort.     
- Ändrade "RSA_OAEP" till "RSA-OAEP" för RSA-algoritmer. Se [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).    
 
Den andra för hands versionen (version 2015-02-01-för hands version) presenterades 20 april 2015. Mer information finns i blogg inlägget [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) . Följande uppgifter har uppdaterats:
 
- Visa en lista med nycklarna i ett valv som har lagt till stöd för sid brytning.
- Visar en lista över versioner av en åtgärd som har lagts till för att visa en lista över versioner av en nyckel.  
- Lista hemligheter i ett valv – stöd för sid brytning.
- Visar en lista över versioner av en hemlighet – Lägg till-åtgärd för att visa en lista över versioner av en hemlighet.  
- Alla åtgärder som har lagt till skapade/uppdaterade tidsstämplar i attribut.  
- Skapa en hemlighet-tillagd innehålls typ för hemligheter.
- Skapa en nyckel tillagd tagg som valfri information.
- Skapa en hemligt tillagda taggar som valfri information.
- Uppdatera en nyckel – tillagda taggar som valfri information.
- Uppdatera en hemligt tillagda taggar som valfri information.
- Ändrade Max storlek för hemligheter från 10 K till 25 kB byte. Se, [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
Första för hands versionen (version 2014-12-08 – för hands version) presenterades den 8 januari 2015.  

## <a name="next-steps"></a>Nästa steg

Om du har fler frågor kan du kontakta oss via [supporten](https://azure.microsoft.com/support/options/).  
