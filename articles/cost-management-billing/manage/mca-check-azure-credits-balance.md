---
title: Spåra Azure-kreditbalansen för ett Microsoft-kundavtal
description: Lär dig hur du spårar Azure-kreditbalansen för ett Microsoft-kundavtal.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: 96c37ded2750ad3e4882b203d71c75c9f4608e9d
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285917"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Spåra Azure-kreditbalansen för Microsoft-kundavtal

Du kan kontrollera Azure-kreditsaldot för ditt faktureringskonto för ett Microsoft-kundavtal på Azure-portalen eller via REST API:er.

Krediter tilldelas till en faktureringsprofil på faktureringskontot för ett Microsoft-kundavtal. Varje faktureringsprofil har sina egna krediter, som tillämpas automatiskt på kostnaderna på fakturan. Du måste ha rollen ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen eller rollen ägare, deltagare eller läsare för faktureringskontot för att kunna visa Azure-kreditsaldot för en faktureringsprofil. Du kan läsa mer om rollerna i [Förstå administrativa roller för Microsoft-kundavtal i Azure](understand-mca-roles.md).

> [!NOTE]
> Det kan ta upp till 24 timmar innan en ny kredit visas på Azure-portalen. Vänta 24 timmar om du har fått en ny kredit som inte visas på portalen.

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Kontrollera ditt kreditsaldo

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar en sökning i portalen efter kostnadshantering + fakturering.](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. På sidan för faktureringsomfång väljer du det faktureringskonto som du vill spåra kreditsaldot för. Faktureringskontot bör vara ett **Microsoft-kundavtal**.

    ![Skärmbild som visar faktureringsomfång.](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure-portalen kommer ihåg det senaste faktureringsomfånget som du använder och visar omfånget nästa gång du kommer till sidan Kostnadshantering + fakturering. Sidan för faktureringsomfång visas inte om du har besökt sidan Kostnadshantering + fakturering. I så fall kontrollerar du att du befinner dig i [rätt omfång](#check-access-to-a-microsoft-customer-agreement). Annars [byter du omfång](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) och väljer faktureringskontot för ett Microsoft-kundavtal.

3. Välj **Betalningsmetoder** till vänster och välj sedan **Azure-krediter**.

   ![Skärmbild av beräknade och aktuella saldon.](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. Sidan Azure-krediter innehåller följande avsnitt:

   #### <a name="balance"></a>Saldo

   I saldoavsnittet visas en översikt över ditt Azure-saldo.

   ![Skärmbild av kreditsaldo för en faktureringsprofil](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Period               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Uppskattat saldo  | Uppskattat belopp med krediter som du har när alla fakturerade och väntande transaktioner har tagits med i beräkningen |
   | Aktuellt saldo    | Summan av krediterna från din senaste faktura. Inga väntande transaktioner ingår |

   När ditt beräknade saldo sjunker till 0 debiteras du för all användning, inklusive för produkter som omfattas av krediter.

   #### <a name="credits-list"></a>Kreditlista

   I kreditlistan visas en lista över Azure-krediter.

   ![Skärmbild av kreditlistor för en faktureringsprofil](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Period | Definition |
   |---|---|
   | Källa | Kreditens förvärvskälla |
   | Startdatum | Det datum då du skaffade krediten |
   | Förfallodatum | Det datum då krediten upphör att gälla |
   | Aktuellt saldo | Saldot från din senaste faktura |
   | Ursprungligt belopp | Den ursprungliga mängden kredit |
   | Status | Aktuell kreditstatus. Statusen kan vara aktiv, används, upphörd eller håller på att upphöra |

   #### <a name="transactions"></a>Transaktioner

   I avsnittet om transaktioner visas alla transaktioner som påverkat ditt kreditsaldo.

   ![Skärmbild av kredittransaktioner för en faktureringsprofil](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Period | Definition |
   |---|---|
   | Transaktionsdatum | Datumet då transaktionen gjordes |
   | Beskrivning | En beskrivning av transaktionen |
   | Belopp| Transaktionsbeloppet |
   | Saldo | Saldot efter transaktionen |

    > [!NOTE]
    >
    > Om du inte ser Azure-krediter på sidan för betalningsmetoder beror det på att du inte har krediter eller att du inte har valt rätt omfång. Välj det faktureringskonto som har krediter eller någon av dess faktureringsprofiler. Information om hur du byter omfång finns i avsnittet om hur du [byter faktureringsomfång på Azure-portalen](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Om du visar Azure-krediter i faktureringskontoomfånget och faktureringskontot har mer än en faktureringsprofil visar sidan Azure-krediter en tabell med en översikt över Azure-krediterna för varje faktureringsprofil. Välj en faktureringsprofil i listan, välj betalningsmetoder och sedan Azure-krediter för att visa information om en faktureringsprofil.

    ![Skärmbild av kreditlista för ett faktureringskonto](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST-API](#tab/rest)

Du kan använda API:erna [Azure Billing](/rest/api/billing/) och [Consumption](/rest/api/consumption/) till att kontrollera kreditsaldot för ditt faktureringskonto programmatiskt.

I exemplen nedan används REST-API:er. PowerShell och Azure CLI stöds inte för närvarande.

### <a name="find-billing-profiles-you-have-access-to"></a>Kontrollera vilka faktureringsprofiler du har åtkomst till

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
I API-svaret ser du en lista med faktureringskonton och tillhörande faktureringsprofiler.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Använd egenskapen `displayName` för faktureringsprofilen för att identifiera den faktureringsprofil som du vill kontrollera kreditsaldot för. Kopiera egenskapen `id` för faktureringsprofilen. Om du till exempel vill kontrollera kreditsaldot för faktureringsprofilen **Development** kopierar du ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Klistra in det här värdet någonstans så att du kan använda det i nästa steg.

### <a name="get-azure-credit-balance"></a>Hämta kreditsaldot för Azure

Gör följande begäran och ersätt `<billingProfileId>` med värdet för `id` som du kopierade i det första steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Du ser uppskattat och aktuellt saldo för faktureringsprofilen i API-svaret.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Elementnamn  | Beskrivning                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Uppskattat kreditsaldo när alla fakturerade och väntande transaktioner har tagits med i beräkningen. |
| `currentBalance`   | Summan av krediterna från din senaste faktura. Här ingår inga väntande transaktioner.    |
| `pendingCreditAdjustments`      | justeringar, som återbetalningar som ännu inte har fakturerats.  |
| `expiredCredit`      |  Den kredit som har upphört att gälla sedan din senaste faktura.  |
| `pendingEligibleCharges`  | De debiteringar som omfattas av krediter som ännu inte har fakturerats.   |

### <a name="get-list-of-credits"></a>Hämta en lista med krediter

Gör följande begäran och ersätt `<billingProfileId>` med värdet för `id` som du kopierade i det första steget (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Du ser en lista med Azure-krediter för en faktureringsprofil i API-svaret.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Elementnamn  | Beskrivning                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Det ursprungliga kreditbeloppet. |
| `closedBalance`   | Saldot sedan din senaste faktura.    |
| `source`      | Källan som definierar vem som har köpt krediten. |
| `startDate`      |  Datumet då krediten började gälla.  |
| `expirationDate`  | Datumet när krediten upphör att gälla.   |
| `poNumber`  | Inköpsordernumret på fakturan som krediten räknades av mot.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Hämta transaktioner som påverkat kreditsaldot

Gör följande begäran och ersätt `<billingProfileId>` med värdet för `id` som du kopierade i det första steget (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Du måste skicka ett värde för **startDate** och **endDate** för att få transaktioner för din begärda tidsperiod.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Du ser alla transaktioner som påverkat kreditsaldot för din faktureringsprofil i API-svaret.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Elementnamn  | Beskrivning                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Datumet när transaktionen ägde rum. |
| `description` | En beskrivning av transaktionen. |
| `adjustments`   | Kreditjusteringen för transaktionen.    |
| `creditExpired`      | Kreditbeloppet som har upphört att gälla. |
| `charges`      |  Avgifterna för transaktionen.  |
| `closedBalance`  | Saldot efter transaktionen.   |
| `eventType`  | Typen av transaktion.   |
| `invoiceNumber`  | Fakturanumret för fakturan som transaktionen satts upp på. Det här är tomt för väntande transaktioner.   |

---

## <a name="how-credits-are-used"></a>Så används krediter

I ett faktureringskonto för ett Microsoft-kundavtal använder du faktureringsprofiler för att hantera fakturor och betalningsmetoder. En månadsfaktura skapas för varje faktureringsprofil, och du använder betalningsmetoderna för att betala fakturan.

Du kan tilldela krediter som du har köpt till en fakturerings profil. När en faktura genereras för faktureringsprofilen räknas krediterna automatiskt av från de totala avgifterna så att du ser vilket belopp du behöver betala. Du betalar återstående belopp med vald betalningsmetod, som check/banköverföring eller kreditkort.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkter som inte omfattas av Azure-krediter

 Följande produkter omfattas inte av dina Azure-krediter. Du debiteras för användning av dessa produkter oavsett ditt kreditsaldo:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registrerad användare
- Openlogic
- Registrerad användare för Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (månatligen)
- Visual Studio Enterprise (årsvis)
- Visual Studio Professional (månatligen)
- Visual Studio Professional (årsvis)
- Azure Marketplace-produkter
- Azure-supportplaner

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonton för Microsofts-kundavtal](../understand/mca-overview.md)
- [Förstå termerna på fakturan för ditt Microsoft-kundavtal](../understand/mca-understand-your-invoice.md)
