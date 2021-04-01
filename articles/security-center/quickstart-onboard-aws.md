---
title: Anslut ditt AWS-konto till Azure Security Center
description: Övervaka dina AWS-resurser från Azure Security Center
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ad3ab8c39d9f9a29e74bd139fb5ddd1e5faeff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755153"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Anslut dina AWS-konton till Azure Security Center

Med moln arbets belastningar som ofta spänner över flera moln plattformar måste moln säkerhets tjänster göra samma sak.

Azure Security Center skyddar arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

När du registrerar ditt AWS-konto i Security Center integreras AWS-säkerhetshubben och Azure Security Center. Security Center ger därmed insyn och skydd i båda dessa moln miljöer för att tillhandahålla:

- Automatisk agent etablering (Security Center använder [Azure Arc](../azure-arc/servers/overview.md) för att distribuera Log Analytics-agenten till dina AWS-instanser)
- Principhantering
- Sårbarhetshantering
- Identifiering och svar för inbäddad slut punkt (EDR)
- Identifiering av felkonfigurationer av säkerhet
- En enskild vy som visar Security Center rekommendationer och AWS-rön om säkerhetshubbar
- Införliva dina AWS-resurser i Security Center säkra Poäng beräkningar
- Regelefterlevnad för AWS-resurser

I skärm bilden nedan ser du AWS-konton som visas på instrument panelen i Security Centers översikt.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Tre GCP-projekt som visas på Security Center översikts instrument panel" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Nödvändiga roller och behörigheter:|**Ägare** för den aktuella Azure-prenumerationen<br>**Deltagare** kan också ansluta ett AWS-konto om en ägare tillhandahåller information om tjänstens huvud namn|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Inga](./media/icons/no-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||



## <a name="connect-your-aws-account"></a>Anslut ditt AWS-konto

Följ stegen nedan för att skapa din AWS Cloud Connector. 

### <a name="step-1-set-up-aws-security-hub"></a>Steg 1. Konfigurera AWS Security Hub:

1. Om du vill visa säkerhets rekommendationer för flera regioner upprepar du följande steg för varje relevant region.

    > [!IMPORTANT]
    > Om du använder ett AWS huvud konto upprepar du följande tre steg för att konfigurera huvud kontot och alla anslutna medlems konton i alla relevanta regioner

    1. Aktivera [AWS-konfiguration](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Aktivera [AWS-Säkerhetshubben](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Kontrol lera att det finns data som flödar till säkerhets hubben.

        Första gången du aktiverar säkerhetshubben kan det ta flera timmar innan data är tillgängliga.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Steg 2. Konfigurera autentisering för Security Center i AWS

Det finns två sätt att tillåta Security Center att autentisera till AWS:

- **Skapa en IAM-roll för Security Center** – det här är den säkraste metoden och rekommenderas
- **AWS-användare för Security Center** – ett mindre säkert alternativ om du inte har IAM aktiverat

#### <a name="create-an-iam-role-for-security-center"></a>Skapa en IAM-roll för Security Center
1. Välj **IAM** i Amazon Web Services-konsolen under **säkerhet, identitet & efterlevnad**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS-tjänster":::

1. Välj **roller** och **skapa roll**.
1. Välj **ett annat AWS-konto**.
1. Ange följande information:

    - **Konto-ID** – ange Microsoft-konto-ID: t (**158177204117**) som visas på AWS connector-sidan i Security Center.
    - **Kräv externt ID** – ska väljas
    - **Externt ID** – ange prenumerations-ID: t enligt vad som visas på AWS Connector-sidan i Security Center 

1. Välj **Nästa**.
1. I avsnittet **bifoga behörighets principer** väljer du följande principer:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Du kan också lägga till taggar. Om du lägger till taggar till användaren påverkas inte anslutningen.
1. Välj **Nästa**.

1. I listan Roller väljer du den roll som du skapade

1. Spara Amazon-resursens namn (ARN) för senare. 

#### <a name="create-an-aws-user-for-security-center"></a>Skapa en AWS-användare för Security Center 
1. Öppna fliken **användare** och välj **Lägg till användare**.
1. I **informations** steget anger du ett användar namn för Security Center och ser till att du väljer **programmatisk åtkomst** för åtkomst typen AWS. 
1. Välj **Nästa behörigheter**.
1. Välj **bifoga befintliga principer direkt** och tillämpa följande principer:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Välj **Nästa: Taggar**. Du kan också lägga till taggar. Om du lägger till taggar till användaren påverkas inte anslutningen.
1. Välj **Granska**.
1. Spara det automatiskt genererade **åtkomst nyckel-ID: t** och filen med **hemlig åtkomst nyckel** för senare versioner.
1. Granska sammanfattningen och klicka på **skapa användare**.


### <a name="step-3-configure-the-ssm-agent"></a>Steg 3. Konfigurera SSM-agenten

AWS Systems Manager krävs för att automatisera uppgifter över dina AWS-resurser. Om EC2-instanserna inte har SSM-agenten följer du de relevanta anvisningarna från Amazon:

- [Installera och konfigurera SSM-agenten i Windows-instanser](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installera och konfigurera SSM-agenten på Amazon EC2 Linux-instanser](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Steg 4. Slutför nödvändiga komponenter för Azure båg
1. Kontrol lera att rätt [Azure-adressresurser](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) är registrerade:
    - Microsoft. HybridCompute
    - Microsoft. GuestConfiguration

1. Skapa ett huvud namn för tjänsten för onboarding i stor skala. Som **ägare** till den prenumeration som du vill använda för onboarding skapar du ett tjänst huvud namn för Azure Arc onboarding enligt beskrivningen i [skapa ett tjänst huvud namn för onboarding i stor skala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale).


### <a name="step-5-connect-aws-to-security-center"></a>Steg 5. Anslut AWS till Security Center

1. Från Security Center menyn väljer du **flera moln anslutningar**.
1. Välj **Lägg till AWS-konto**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knappen Lägg till AWS-konto på Security Center sidan med flera moln anslutningar":::
1. Konfigurera alternativen på fliken **AWS-autentisering** :
    1. Ange ett **visnings namn** för kopplingen.
    1. Bekräfta att prenumerationen är korrekt. Det är den prenumeration som omfattar anslutnings-och AWS för säkerhets nav.
    1. Beroende på alternativet autentisering valde du i [steg 2. Konfigurera autentisering för Security Center i AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Välj  **anta roll** och klistra in ARN från [skapa en IAM-roll för Security Center](#create-an-iam-role-for-security-center).
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Inklistring av ARN-filen i relevant fält i guiden AWS-anslutning i Azure Portal":::

            ELLER

        - Välj **autentiseringsuppgifter** och klistra in **åtkomst nyckeln** och den **hemliga nyckeln** från CSV-filen som du sparade i [skapa en AWS-användare för Security Center](#create-an-aws-user-for-security-center).
1. Välj **Nästa**.
1. Konfigurera alternativen på fliken **Azure Arc-konfiguration** :

    Security Center identifierar EC2-instanserna i det anslutna AWS-kontot och använder SSM för att publicera dem till Azure-bågen. 

    > [!TIP]
    > En lista över operativ system som stöds finns i [vilka operativ system för mina EC2-instanser stöds?](#what-operating-systems-for-my-ec2-instances-are-supported) i vanliga frågor och svar.

    1. Välj den **resurs grupp** och den **Azure-region** som identifierade AWS-EC2s kommer att ingå i den valda prenumerationen.
    1. Ange **tjänstens huvud namns-ID** och **tjänstens huvud namn** för Azure-bågen enligt beskrivningen här [skapa ett tjänst huvud namn för onboarding i skala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Om datorn ansluter till Internet via en proxyserver anger du IP-adressen för proxyservern eller namnet och port numret som datorn använder för att kommunicera med proxyservern. Ange värdet i formatet ```http://<proxyURL>:<proxyport>```
    1. Välj **Granska + skapa**.

        Granska sammanfattnings informationen

        I avsnittet taggar visas en lista över alla Azure-taggar som skapas automatiskt för varje onboarded EC2 med sin egen relevanta information för att enkelt kunna identifiera den i Azure. 

        Lär dig mer om Azure-Taggar i [use-taggar för att organisera dina Azure-resurser och-hanterings hierarkier](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>Steg 6. Bekräftelse

När anslutningen har skapats och AWS-säkerhetshubben har kon figurer ATS korrekt:

- Security Center genomsöker miljön efter AWS EC2-instanser, registrera dem på Azure Arc, aktivera för att installera Log Analytics agent och tillhandahålla hot skydd och säkerhets rekommendationer. 
- ASC-tjänsten söker efter nya AWS EC2-instanser var 6: e timme och registrerar dem enligt konfigurationen.
- AWS CIS-standarden visas i Security Centerens instrument panel för kontroll av efterlevnad.
- Om säkerhets NAVs principen är aktive rad visas rekommendationerna i Security Center portalen och instrument panelen för övervakning av efterlevnad 5-10 minuter efter att den har slutförts.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS-resurser och rekommendationer på sidan rekommendationer för Security Center":::



## <a name="monitoring-your-aws-resources"></a>Övervaka dina AWS-resurser

Som du ser ovan visar Azure Security Center sidan med säkerhets rekommendationer dina AWS-resurser tillsammans med dina Azure-och GCP-resurser för en verklig vy med flera moln.

Om du vill visa alla aktiva rekommendationer för dina resurser efter resurs typ, använder du Security Center till gångs inventerings sidan och filtrerar till den AWS som du är intresse rad av:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Resurs typs filtret för till gångs lager visar AWS alternativ"::: 


## <a name="aws-in-security-center-faq"></a>AWS i Security Center vanliga frågor och svar

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Vilka operativ system för mina EC2-instanser stöds?

Operativ system som stöds för automatisk onboarding till Azure Arc för AWS-datorer

- Ubuntu 16,04-SSM-agenten är förinstallerad som standard
- Ubuntu 18,04-SSM-agenten är förinstallerad som standard
- Windows Server-SSM agent är förinstallerad som standard
- CentOS Linux 7 – SSM ska installeras manuellt eller separat
- SUSE Linux Enterprise Server (SLES) 15 (x64) – SSM ska installeras manuellt eller separat
- Red Hat Enterprise Linux (RHEL) 7 (x64) – SSM ska installeras manuellt eller separat


## <a name="next-steps"></a>Nästa steg

Att ansluta ditt AWS-konto är en del av den multi-Cloud-upplevelse som finns i Azure Security Center. Relaterad information finns på följande sida:

- [Anslut dina GCP-konton till Azure Security Center](quickstart-onboard-gcp.md)
