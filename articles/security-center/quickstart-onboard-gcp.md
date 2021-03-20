---
title: Anslut ditt GCP-konto till Azure Security Center
description: Övervaka dina GCP-resurser från Azure Security Center
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 94c7a800fc551faf6650b8e30fe7c2188f7d2dbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008391"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Anslut dina GCP-konton till Azure Security Center

Med moln arbets belastningar som ofta spänner över flera moln plattformar måste moln säkerhets tjänster göra samma sak.

Azure Security Center skyddar arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

Registrera dina GCP-konton i Security Center, integrerar GCP Security-kommandot och Azure Security Center. Security Center ger därmed insyn och skydd i båda dessa moln miljöer för att tillhandahålla:

- Identifiering av felkonfigurationer av säkerhet
- En enskild vy som visar Security Center rekommendationer och GCP Security Command Center-resultat
- Införliva dina GCP-resurser i Security Center säkra Poäng beräkningar
- Integrering av GCP Security Command Center-rekommendationer baserat på CIS-standarden i Security Centers instrument panel för kontroll av efterlevnad

I skärm bilden nedan ser du GCP-projekt som visas på instrument panelen i Security Centers översikt.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 GCP-projekt som visas på Security Center översikts instrument panel" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Nödvändiga roller och behörigheter:|**Ägare** eller **deltagare** på den aktuella Azure-prenumerationen|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="connect-your-gcp-account"></a>Anslut ditt GCP-konto

Skapa en anslutning för varje organisation som du vill övervaka från Security Center.

När du ansluter dina GCP-konton till vissa Azure-prenumerationer bör du tänka på [Google Cloud-hierarkin](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) och dessa rikt linjer:

- Du kan ansluta dina GCP-konton till ASC på *organisations* nivå
- Du kan ansluta flera organisationer till en Azure-prenumeration
- Du kan ansluta flera organisationer till flera Azure-prenumerationer
- När du ansluter en organisation läggs alla *projekt* i organisationen till i Security Center

Följ stegen nedan för att skapa din GCP Cloud Connector. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Steg 1. Konfigurera GCP Security Command Center med säkerhets hälso analys

För alla GCP-projekt i din organisation måste du också:

1. Konfigurera **GCP Security Command Center** med hjälp av [dessa instruktioner från GCP-dokumentationen](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Aktivera **säkerhets hälso analys** med hjälp av [dessa instruktioner från GCP-dokumentationen](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Kontrol lera att det finns data som flödar till säkerhets kommando centret.

Anvisningarna för att ansluta din GCP-miljö för säkerhets konfiguration följer Googles rekommendationer för att använda rekommendationer för säkerhets konfiguration. Integrationen utnyttjar Google Security Command Center och använder ytterligare resurser som kan påverka din fakturering.

När du först aktiverar säkerhets hälso analys kan det ta flera timmar innan data är tillgängliga.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Steg 2. Aktivera GCP Security Command Center API

1. Från Googles **moln konsol API-bibliotek** väljer du varje projekt i organisationen som du vill ansluta till Azure Security Center.
1. I API-biblioteket letar du reda på och väljer **Security Command Center API**.
1. På sidan API väljer du **Aktivera**.

Läs mer om [Security Command Center API](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Steg 3. Skapa ett dedikerat tjänst konto för integrering av säkerhets konfiguration

1. I **GCP-konsolen** väljer du ett projekt från organisationen där du skapar det tjänst konto som krävs. 

    > [!NOTE]
    > När det här tjänst kontot läggs till på organisations nivå används det för att komma åt data som samlats in av Security Command Center från alla andra aktiverade projekt i organisationen. 

1. I **navigerings menyn** under **IAM & admin** -alternativ väljer du **tjänst konton**.
1. Välj **skapa tjänst konto**.
1. Ange ett konto namn och välj **skapa**.
1. Ange **rollen** som **Security Center admin Viewer** och välj **Fortsätt**.
1. Avsnittet **bevilja användare åtkomst till det här tjänst kontot** är valfritt. Välj **Klar**.
1. Kopiera **e-postvärdet** för det skapade tjänst kontot och spara det för senare användning.
1. I **navigerings menyn** under **IAM & admin** -alternativ väljer du **IAM**
    1. Växla till organisations nivå.
    1. Välj **Lägg till**.
    1. I fältet **nya medlemmar** klistrar du in det **e-Postvärde** som du kopierade tidigare.
    1. Ange rollen som **Security Center admin Viewer** och välj sedan **Spara**.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Ange relevanta GCP-behörigheter":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Steg 4. Skapa en privat nyckel för dedikerat tjänst konto
1. Växla till projekt nivå.
1. I **navigerings menyn** under **IAM & admin** -alternativ väljer du **tjänst konton**.
1. Öppna det dedikerade tjänst kontot och välj Redigera.
1. I avsnittet **nycklar** väljer du **Lägg till nyckel** och **skapar sedan ny nyckel**.
1. På skärmen skapa privat nyckel väljer du **JSON** och väljer sedan **skapa**.
1. Spara denna JSON-fil för senare användning.


### <a name="step-5-connect-gcp-to-security-center"></a>Steg 5. Anslut GCP till Security Center
1. Från Security Center menyn väljer du **moln anslutningar**.
1. Välj Lägg till GCP-konto.
1. På sidan onboarding, gör du följande och väljer sedan **Nästa**.
    1. Verifiera den valda prenumerationen.
    1. I fältet **visnings namn** anger du ett visnings namn för kopplingen.
    1. I fältet **organisations-ID** anger du organisationens ID. Om du inte vet det, se [skapa och hantera organisationer](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. I rutan **privat nyckel** fil bläddrar du till den JSON-fil som du laddade ned i [steg 4. Skapa en privat nyckel för dedikerat tjänst konto](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Steg 6. Bekräftelse

När anslutningen har skapats och GCP Security Command Center har kon figurer ATS korrekt:

- GCP CIS-standarden visas i Security Centerens instrument panel för kontroll av efterlevnad.
- Säkerhets rekommendationer för dina GCP-resurser visas i Security Center portalen och instrument panelen för övervakning av efterlevnad 5-10 minuter efter att publiceringen är klar:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="GCP-resurser och rekommendationer på Security Centers rekommendations sida":::


## <a name="monitoring-your-gcp-resources"></a>Övervaka dina GCP-resurser

Som du ser ovan visar Azure Security Center sidan med säkerhets rekommendationer dina GCP-resurser tillsammans med dina Azure-och AWS-resurser för en verklig vy med flera moln.

Om du vill visa alla aktiva rekommendationer för dina resurser efter resurs typ, använder du Security Center till gångs inventerings sidan och filtrerar till den GCP som du är intresse rad av:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Resurs typs filtret för till gångs lager visar GCP alternativ"::: 


## <a name="faq-for-connecting-gcp-accounts-to-azure-security-center"></a>Vanliga frågor och svar om att ansluta GCP-konton till Azure Security Center

### <a name="can-i-connect-multiple-gcp-organizations-to-security-center"></a>Kan jag ansluta flera GCP-organisationer till Security Center?
Ja. Security Center GCP Connector ansluter dina Google Cloud-resurser på *organisations* nivå. 

Skapa en anslutning för varje GCP-organisation som du vill övervaka från Security Center. När du ansluter en organisation läggs alla projekt i organisationen till Security Center.

Lär dig mer om Google Cloud-hierarkistrukturen i [Googles online-dokument](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-security-center"></a>Finns det ett API för att ansluta mina GCP-resurser till Security Center?
Ja. Om du vill skapa, redigera eller ta bort Security Center moln anslutningar med en REST API kan du läsa mer i [anslutnings-API: et](/rest/api/securitycenter/connectors).

## <a name="next-steps"></a>Nästa steg

Att ansluta ditt GCP-konto är en del av den multi-Cloud-upplevelse som finns i Azure Security Center. Relaterad information finns på följande sida:

- [Anslut dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md)
- [Google Cloud-resurs-hierarki](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)– lär dig mer om Google Cloud-mappstrukturen i Googles online-dokument