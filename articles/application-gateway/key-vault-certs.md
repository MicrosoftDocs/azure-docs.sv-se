---
title: TLS-avslutning med Azure Key Vault certifikat
description: Lär dig hur du kan integrera Azure Application Gateway med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775754"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-avslutning med Key Vault-certifikat

[Azure Key Vault](../key-vault/general/overview.md) är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och TLS/SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till v2-SKU: n för Application Gateway.

Key Vault integration erbjuder två modeller för att avsluta TLS:

- Du kan uttryckligen tillhandahålla TLS/SSL-certifikat som är kopplade till lyssnaren. Den här modellen är det traditionella sättet att skicka TLS/SSL-certifikat till Application Gateway för TLS-avslutning.
- Du kan också ange en referens till ett befintligt Key Vault certifikat eller hemlighet när du skapar en HTTPS-aktiverad lyssnare.

Application Gateway integration med Key Vault erbjuder många fördelar, inklusive:

- Starkare säkerhet, eftersom TLS/SSL-certifikat inte hanteras direkt av program utvecklings teamet. Integrationen gör att ett separat säkerhets team kan:
  * Konfigurera programgatewayer.
  * Kontrol lera Application Gateway-livscykler.
  * Bevilja behörighet till valda programgatewayer för att komma åt certifikat som lagras i ditt nyckel valv.
- Stöd för import av befintliga certifikat i nyckel valvet. Eller Använd Key Vault API: er för att skapa och hantera nya certifikat med någon av de betrodda Key Vault partnerna.
- Stöd för automatisk förnyelse av certifikat som lagras i ditt nyckel valv.

Application Gateway stöder för närvarande endast program varu validerade certifikat. HSM (Hardware Security Module) – verifierade certifikat stöds inte. När Application Gateway har kon figurer ATS för att använda Key Vault certifikat, hämtar dess instanser certifikatet från Key Vault och installerar dem lokalt för TLS-avslutning. Instanserna avsöker också Key Vault med intervall om fyra timmar för att hämta en förnyad version av certifikatet, om det finns. Om ett uppdaterat certifikat hittas, roteras TLS/SSL-certifikatet som för närvarande är associerat med HTTPS-lyssnaren automatiskt.

> [!NOTE]
> Azure Portal stöder bara certifikat för nyckel valv, inte hemligheter. Application Gateway stöder fortfarande referenser till hemligheter från nyckel valv, men endast via icke-Portal resurser som PowerShell, CLI, API, ARM-mallar osv. 

## <a name="how-integration-works"></a>Så här fungerar integreringen

Application Gateway-integrering med Key Vault kräver en konfigurations process i tre steg:

1. **Skapa en användartilldelad hanterad identitet**

   Du skapar eller återanvänder en befintlig användare som tilldelats en hanterad identitet, som Application Gateway använder för att hämta certifikat från Key Vault åt dig. Mer information finns i [skapa, lista, ta bort eller tilldela en roll till en användardefinierad hanterad identitet med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). I det här steget skapas en ny identitet i Azure Active Directory-klienten. Identiteten är betrodd av den prenumeration som används för att skapa identiteten.

1. **Konfigurera nyckel valvet**

   Du kan antingen importera ett befintligt certifikat eller skapa ett nytt i ditt nyckel valv. Certifikatet kommer att användas av program som körs via programgatewayen. I det här steget kan du också använda en Key Vault hemlighet som också tillåter lagring av en lösen ords fri, Base-64-kodad PFX-fil. Vi rekommenderar att du använder en "certifikat"-typ på grund av den funktion för förnyad förnyelse som är tillgänglig för den här typen av objekt i Key Vault. När du har skapat ett certifikat eller en hemlighet måste du definiera åtkomst principer i Key Vault så att identiteten kan beviljas åtkomst till hemligheten.
   
   > [!IMPORTANT]
   > Från den 15 mars 2021 känner Key Vault igen Azure Application Gateway som en av de betrodda tjänsterna, så att du kan bygga en säker nätverks gränser i Azure. På så sätt kan du neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) till Key Vault men ändå göra den tillgänglig för Application Gateway resurs under din prenumeration. 

   > Du kan konfigurera Application Gateway i ett begränsat nätverk av Key Vault på följande sätt. <br />
   > a) under Key Vaultens nätverks blad <br />
   > b) Välj privat slut punkt och valda nätverk på fliken "brand väggar och virtuella nätverk" <br/>
   > c) sedan använder du virtuella nätverk och lägger till din Application Gateways virtuella nätverk och undernät. Under processen konfigurerar du också tjänst slut punkten Microsoft. nyckel valv genom att markera dess kryss ruta. <br/>
   > d) Slutligen väljer du "Ja" om du vill tillåta att betrodda tjänster kringgår Key Vaults brand vägg. <br/>
   > 
   > ![Key Vault brand vägg](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Om du distribuerar programgatewayen via en ARM-mall, antingen med hjälp av Azure CLI eller PowerShell eller via ett Azure-program som distribueras från Azure Portal, lagras SSL-certifikatet i nyckel valvet som en Base64-kodad PFX-fil. Du måste slutföra stegen i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > Det är särskilt viktigt att ställa in `enabledForTemplateDeployment` på `true` . Certifikatet kan vara ett lösen ord eller ha ett lösen ord. Om det gäller ett certifikat med ett lösen ord visar följande exempel en möjlig konfiguration för `sslCertificates` posten i `properties` för konfigurationen av arm-mal len för en app-Gateway. Värdena för `appGatewaySSLCertificateData` och `appGatewaySSLCertificatePassword` slås upp från nyckel valvet, enligt beskrivningen i avsnittet [referens hemligheter med dynamiskt ID](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Följ referenserna bakåt från `parameters('secretName')` för att se hur sökningen sker. Om certifikatet är lösenordsskyddat utelämnar du `password` posten.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Konfigurera progamgatewayen**

   När du har slutfört de två föregående stegen kan du konfigurera eller ändra en befintlig Programgateway för att använda den tilldelade hanterade identiteten. Mer information finns i [set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   Du kan också konfigurera HTTP-lyssnarens TLS/SSL-certifikat så att det pekar på den fullständiga URI: n för Key Vault certifikat eller hemligt ID.

   ![Key Vault-certifikat](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera TLS-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md)
