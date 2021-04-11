---
title: Exempel på en Azure-attesterings princip
description: Exempel på principer för Azure-attestering.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 9c29ec3dbc4d4f7d0a7abe0ff9a90fc0b7565272
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442566"
---
# <a name="examples-of-an-attestation-policy"></a>Exempel på en attesterings princip

Attesterings policyn används för att bearbeta attesterings beviset och avgöra om Azure-attesteringen ska utfärda en attesterings-token. Genereringen av attesterings-token kan kontrol leras med anpassade principer. Nedan visas några exempel på en attesterings princip. 

## <a name="sample-custom-policy-for-an-sgx-enclave"></a>Exempel på en anpassad princip för en SGX-enklaven 

```
version= 1.0;
authorizationrules
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==<product-id> ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="<mrsigner>"]
    => permit();
};
issuancerules {
c:[type=="x-ms-sgx-mrsigner"] => issue(type="<custom-name>", value=c.value);
};

```
Mer information om inkommande anspråk som genereras av Azure-attestering finns i [anspråks uppsättningar](./claim-sets.md). Inkommande anspråk kan användas av princip författare för att definiera auktoriseringsregler i en anpassad princip. 

Avsnittet utfärdande regler är inte obligatoriskt. Det här avsnittet kan användas av användare för att få ytterligare utgående anspråk som genereras i attesterings-token med anpassade namn. Mer information om utgående anspråk som genereras av tjänsten i attesterings-token finns i [anspråks uppsättningar](./claim-sets.md).

## <a name="default-policy-for-an-sgx-enclave"></a>Standard princip för en SGX-enklaven

```
version= 1.0;
authorizationrules {
    => permit();
};
issuancerules {
    c:[type=="x-ms-sgx-is-debuggable"] => issue(type="is-debuggable", value=c.value);
    c:[type=="x-ms-sgx-mrsigner"] => issue(type="sgx-mrsigner", value=c.value);
    c:[type=="x-ms-sgx-mrenclave"] => issue(type="sgx-mrenclave", value=c.value);
    c:[type=="x-ms-sgx-product-id"] => issue(type="product-id", value=c.value);
    c:[type=="x-ms-sgx-svn"] => issue(type="svn", value=c.value);
    c:[type=="x-ms-attestation-type"] => issue(type="tee", value=c.value);
};
```

Anspråk som används i standard principen anses vara föråldrade men stöds fullt ut och kommer även fortsättnings vis att ingå i framtiden. Vi rekommenderar att du använder anspråks namnen som inte är föråldrade. Mer information om rekommenderade anspråks namn finns i [anspråks uppsättningar](./claim-sets.md). 

## <a name="sample-custom-policy-to-support-multiple-sgx-enclaves"></a>Exempel på anpassad princip för att stödja flera SGX-enclaves

```
version= 1.0;
authorizationrules 
{
    [ type=="x-ms-sgx-is-debuggable", value==true ]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    [ type=="x-ms-sgx-is-debuggable", value==true ]&& 
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
};
```

## <a name="unsigned-policy-for-an-sgx-enclave-with-policyformatjwt"></a>Osignerad princip för en SGX-enklaven med PolicyFormat = JWT

```
eyJhbGciOiJub25lIn0.eyJBdHRlc3RhdGlvblBvbGljeSI6ICJkbVZ5YzJsdmJqMGdNUzR3TzJGMWRHaHZjbWw2WVhScGIyNXlkV3hsYzN0ak9sdDBlWEJsUFQwaUpHbHpMV1JsWW5WbloyRmliR1VpWFNBOVBpQndaWEp0YVhRb0tUdDlPMmx6YzNWaGJtTmxjblZzWlhON1l6cGJkSGx3WlQwOUlpUnBjeTFrWldKMVoyZGhZbXhsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYVhNdFpHVmlkV2RuWVdKc1pTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjMmQ0TFcxeWMybG5ibVZ5SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXljMmxuYm1WeUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN1l6cGJkSGx3WlQwOUlpUnpaM2d0YlhKbGJtTnNZWFpsSWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzJkNExXMXlaVzVqYkdGMlpTSXNJSFpoYkhWbFBXTXVkbUZzZFdVcE8yTTZXM1I1Y0dVOVBTSWtjSEp2WkhWamRDMXBaQ0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbkJ5YjJSMVkzUXRhV1FpTENCMllXeDFaVDFqTG5aaGJIVmxLVHRqT2x0MGVYQmxQVDBpSkhOMmJpSmRJRDAtSUdsemMzVmxLSFI1Y0dVOUluTjJiaUlzSUhaaGJIVmxQV011ZG1Gc2RXVXBPMk02VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.
```

## <a name="signed-policy-for-an-sgx-enclave-with-policyformatjwt"></a>Signerad princip för en SGX-enklaven med PolicyFormat = JWT

```
eyJhbGciOiJSU0EyNTYiLCJ4NWMiOlsiTUlJQzFqQ0NBYjZnQXdJQkFnSUlTUUdEOUVGakJcdTAwMkJZd0RRWUpLb1pJaHZjTkFRRUxCUUF3SWpFZ01CNEdBMVVFQXhNWFFYUjBaWE4wWVhScGIyNURaWEowYVdacFkyRjBaVEF3SGhjTk1qQXhNVEl6TVRneU1EVXpXaGNOTWpFeE1USXpNVGd5TURVeldqQWlNU0F3SGdZRFZRUURFeGRCZEhSbGMzUmhkR2x2YmtObGNuUnBabWxqWVhSbE1EQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQQURDQ0FRb0NnZ0VCQUpyRWVNSlo3UE01VUJFbThoaUNLRGh6YVA2Y2xYdkhmd0RIUXJ5L3V0L3lHMUFuMGJ3MVU2blNvUEVtY2FyMEc1WmYxTUR4alZOdEF5QjZJWThKLzhaQUd4eFFnVVZsd1dHVmtFelpGWEJVQTdpN1B0NURWQTRWNlx1MDAyQkJnanhTZTBCWVpGYmhOcU5zdHhraUNybjYwVTYwQUU1WFx1MDAyQkE1M1JvZjFUUkNyTXNLbDRQVDRQeXAzUUtNVVlDaW9GU3d6TkFQaU8vTy9cdTAwMkJIcWJIMXprU0taUXh6bm5WUGVyYUFyMXNNWkptRHlyUU8vUFlMTHByMXFxSUY2SmJsbjZEenIzcG5uMXk0Wi9OTzJpdFBxMk5Nalx1MDAyQnE2N1FDblNXOC9xYlpuV3ZTNXh2S1F6QVR5VXFaOG1PSnNtSThUU05rLzBMMlBpeS9NQnlpeDdmMTYxQ2tjRm1LU3kwQ0F3RUFBYU1RTUE0d0RBWURWUjBUQkFVd0F3RUIvekFOQmdrcWhraUc5dzBCQVFzRkFBT0NBUUVBZ1ZKVWRCaXRud3ZNdDdvci9UMlo4dEtCUUZsejFVcVVSRlRUTTBBcjY2YWx2Y2l4VWJZR3gxVHlTSk5pbm9XSUJROU9QamdMa1dQMkVRRUtvUnhxN1NidGxqNWE1RUQ2VjRyOHRsejRISjY0N3MyM2V0blJFa2o5dE9Gb3ZNZjhOdFNVeDNGTnBhRUdabDJMUlZHd3dcdTAwMkJsVThQd0gzL2IzUmVCZHRhQTdrZmFWNVx1MDAyQml4ZWRjZFN5S1F1VkFUbXZNSTcxM1A4VlBsNk1XbXNNSnRrVjNYVi9ZTUVzUVx1MDAyQkdZcU1yN2tLWGwxM3lldUVmVTJWVkVRc1ovMXRnb29iZVZLaVFcdTAwMkJUcWIwdTJOZHNcdTAwMkJLamRIdmFNYngyUjh6TDNZdTdpR0pRZnd1aU1tdUxSQlJwSUFxTWxRRktLNmRYOXF6Nk9iT01zUjlpczZ6UDZDdmxGcEV6bzVGUT09Il19.eyJBdHRlc3RhdGlvblBvbGljeSI6ImRtVnljMmx2YmoweExqQTdZWFYwYUc5eWFYcGhkR2x2Ym5KMWJHVnpJSHRqT2x0MGVYQmxQVDBpSkdsekxXUmxZblZuWjJGaWJHVWlYU0FtSmlCYmRtRnNkV1U5UFhSeWRXVmRJRDAtSUdSbGJua29LVHM5UGlCd1pYSnRhWFFvS1R0OU8ybHpjM1ZoYm1ObGNuVnNaWE1nZXlBZ0lDQmpPbHQwZVhCbFBUMGlKR2x6TFdSbFluVm5aMkZpYkdVaVhTQTlQaUJwYzNOMVpTaDBlWEJsUFNKT2IzUkVaV0oxWjJkaFlteGxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdJQ0FnSUdNNlczUjVjR1U5UFNJa2FYTXRaR1ZpZFdkbllXSnNaU0pkSUQwLUlHbHpjM1ZsS0hSNWNHVTlJbWx6TFdSbFluVm5aMkZpYkdVaUxDQjJZV3gxWlQxakxuWmhiSFZsS1RzZ0lDQWdZenBiZEhsd1pUMDlJaVJ6WjNndGJYSnphV2R1WlhJaVhTQTlQaUJwYzNOMVpTaDBlWEJsUFNKelozZ3RiWEp6YVdkdVpYSWlMQ0IyWVd4MVpUMWpMblpoYkhWbEtUc2dJQ0FnWXpwYmRIbHdaVDA5SWlSelozZ3RiWEpsYm1Oc1lYWmxJbDBnUFQ0Z2FYTnpkV1VvZEhsd1pUMGljMmQ0TFcxeVpXNWpiR0YyWlNJc0lIWmhiSFZsUFdNdWRtRnNkV1VwT3lBZ0lDQmpPbHQwZVhCbFBUMGlKSEJ5YjJSMVkzUXRhV1FpWFNBOVBpQnBjM04xWlNoMGVYQmxQU0p3Y205a2RXTjBMV2xrSWl3Z2RtRnNkV1U5WXk1MllXeDFaU2s3SUNBZ0lHTTZXM1I1Y0dVOVBTSWtjM1p1SWwwZ1BUNGdhWE56ZFdVb2RIbHdaVDBpYzNadUlpd2dkbUZzZFdVOVl5NTJZV3gxWlNrN0lDQWdJR002VzNSNWNHVTlQU0lrZEdWbElsMGdQVDRnYVhOemRXVW9kSGx3WlQwaWRHVmxJaXdnZG1Gc2RXVTlZeTUyWVd4MVpTazdmVHMifQ.c0l-xqGDFQ8_kCiQ0_vvmDQYG_u544CYmoiucPNxd9MU8ZXT69UD59UgSuya2yl241NoVXA_0LaMEB2re0JnTbPD_dliJn96HnIOqnxXxRh7rKbu65ECUOMWPXbyKQMZ0I3Wjhgt_XyyhfEiQGfJfGzA95-wm6yWqrmW7dMI7JkczG9ideztnr0bsw5NRsIWBXOjVy7Bg66qooTnODS_OqeQ4iaNsN-xjMElHABUxXhpBt2htbhemDU1X41o8clQgG84aEHCgkE07pR-7IL_Fn2gWuPVC66yxAp00W1ib2L-96q78D9J52HPdeDCSFio2RL7r5lOtz8YkQnjacb6xA       
```

</br>

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
