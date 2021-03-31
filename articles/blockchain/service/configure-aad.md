---
title: Konfigurera Azure Active Directory åtkomst – Azure blockchain-tjänsten
description: Så här konfigurerar du Azure blockchain service med Azure Active Directory åtkomst
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85208781"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Så här konfigurerar du Azure Active Directory åtkomst för Azure blockchain-tjänsten

I den här artikeln får du lära dig hur du beviljar åtkomst och ansluter till Azure blockchain service-noder med Azure Active Directory (Azure AD) användare, grupp eller program-ID: n.

Azure AD tillhandahåller molnbaserad identitets hantering och gör att du kan använda en enda identitet i hela företags-och åtkomst program i Azure. Azure blockchain-tjänsten är integrerad med Azure AD och erbjuder fördelar som ID-Federation, enkel inloggning och Multi-Factor Authentication.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md)

## <a name="grant-access"></a>Bevilja åtkomst

Du kan bevilja åtkomst på både medlems nivå och Node-nivå. Om du beviljar åtkomst behörighet på medlems nivån ger det behörighet att ge alla noder under medlemmen åtkomst.

### <a name="grant-member-level-access"></a>Bevilja åtkomst till medlems nivå

Bevilja behörighet på medlems nivå.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till **åtkomst kontroll (IAM) > Lägg till > Lägg till roll tilldelning**.
1. Välj rollen **blockchain member Node Access (för hands version)** och Lägg till det Azure AD ID-objekt som du vill bevilja åtkomst till. Azure AD ID-objekt kan vara:

    | Azure AD-objekt | Exempel |
    |-----------------|---------|
    | Azure AD-användare   | `kim@contoso.onmicrosoft.com` |
    | Azure AD-grupp  | `sales@contoso.onmicrosoft.com` |
    | Program-ID  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Lägg till rolltilldelning](./media/configure-aad/add-role-assignment.png)

1. Välj **Spara**.

### <a name="grant-node-level-access"></a>Bevilja åtkomst till nodnivå

Du kan bevilja åtkomst på nodnivå genom att gå till noden säkerhet och klicka på det nodnamn som du vill bevilja åtkomst.

Välj rollen blockchain member Node Access (för hands version) och Lägg till det Azure AD ID-objekt som du vill bevilja åtkomst till.

Mer information finns i [Konfigurera noder i Azure blockchain service Transactions](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Anslut med Azure blockchain Connector

Hämta eller klona [Azure blockchain-anslutningen från GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Följ anvisningarna i avsnittet snabb start i **README** för att bygga anslutningen från käll koden.

### <a name="connect-using-an-azure-ad-user-account"></a>Anslut med ett användar konto i Azure AD

1. Kör följande kommando för att autentisera med ett användar konto i Azure AD. Ersätt \<myAADDirectory\> med en Azure AD-domän. Till exempel `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD uppmanas att ange autentiseringsuppgifter.
1. Logga in med ditt användar namn och lösen ord.
1. Vid lyckad autentisering ansluter din lokala proxy till din blockchain-nod. Nu kan du koppla din Geth-klient till den lokala slut punkten.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Anslut med ett program-ID

Många program autentiseras med Azure AD med ett program-ID i stället för ett Azure AD-användarkonto.

Om du vill ansluta till din nod med ett program-ID ersätter du **aadauthcode** med **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parameter | Beskrivning |
|-----------|-------------|
| klient organisations-ID | Azure AD-domän, till exempel `yourdomain.onmicrosoft.com`
| klient-ID | Klient-ID för det registrerade programmet i Azure AD
| klient hemlighet | Klient hemlighet för det registrerade programmet i Azure AD

Mer information om hur du registrerar ett program i Azure AD finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Ansluta en mobil enhet eller text webbläsare

För en mobil enhet eller text baserad webbläsare där popup-visningen av Azure AD-autentisering inte är möjlig, genererar Azure AD ett eng ång slö sen ord. Du kan kopiera lösen ordet och fortsätta med Azure AD-autentisering i en annan miljö.

Om du vill generera lösen ordet ersätter du **aadauthcode** med **aaddevice**. Ersätt \<myAADDirectory\> med en Azure AD-domän. Till exempel `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Nästa steg

Mer information om data säkerhet i Azure blockchain-tjänsten finns i [säkerhet för Azure blockchain-tjänster](data-security.md).