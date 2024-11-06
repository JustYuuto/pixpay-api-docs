# Pixpay API docs

Cette page répertorie les routes sur l'api interne utilisée au sein de l'application bancaire "Pixpay"

* base url: `https://prod-bo1.pixpay.app`
* user agents: 
  * `Dart/3.2 (dart:io)`
  * `Dalvik/2.1.0 (Linux; U; Android 13; Subsystem for Android(TM) Build/TQ3A.230901.001)`
* bucket amazon s3 : https://pixpay-public.s3.eu-west-3.amazonaws.com/

> [!TIP]
> À part pour s'authentifier toutes les requêtes utilisent GraphQL sur `https://prod-bo1.pixpay.app/graphql` en post.

## Routes

### Authentification

> [!NOTE]
> Attention pour cette section l'api utilise le ndd `prod-auth.pixpay.app`

#### `POST /user/check_login`

content type : `application/x-www-form-urlencoded`

params :
* ``login``: numéro de tel, ex : `+33@111111111`
* ``chosenServer`` : c'est vide
* `deviceId`: id unique de l'appareil

réponse :
```json
{
  "step": "PIN"
}
```

---

ensuite un fois que le pin est entré :

params :
* ``login``: numéro de tel, ex : `+33@111111111`
* `password`: mdp du compte
* `deviceId`: id unique de l'appareil
* `scaToken` : je sais pas
* `auth_type`: `pwd` si connexion via mdp
* `checkOnly`: je sais pas
* `publicKey`: clé rsa

réponse : 
```json
{
  "step": "LOGIN",
  "blocked": false,
  "session": {
    "servers": [
      {
        "domain": "https://prod-bo1.pixpay.app",
        "status": 3,
        "tenantId": "........",
        "isDefault": true,
        "ownerPhone": "<numéro de ce qu'il semble être le parent qui 'gère' le compte>"
      }
    ],
    "token": "un token jwt",
    "domain": "https://prod-bo1.pixpay.app",
    "auth": "https://prod-auth.pixpay.app",
    "expires_in": 900,
    "expires_at": "2024-11-00T00:00:15.000Z",
    "session_id": "un uuid",
    "auth_type": "pwd"
  }
}
```

### Récupérer ses infos

requête :
```json
{
  "operationName": null,
  "variables": {
    "deviceId": "...",
    "deviceUuid": "la même qu'au dessus"
  },
  "query": "query MeQuery($deviceId: String!, $deviceUuid: String) {\n  __typename\n  me(deviceId: $deviceId, deviceUuid: $deviceUuid) {\n    __typename\n    id\n    login\n    email\n    device {\n      __typename\n      id\n      pushToken\n      pushDesactivatedInSystem\n    }\n    isUnregistered\n    pendingRegistrationId\n    processingOrderId\n    pushActivated\n    parent {\n      __typename\n      id\n      firstname\n      lastname\n      email\n      phone\n      birthdate\n      civility\n      mainWalletId\n      address\n      addressComplement\n      addressState\n      city\n      zipcode\n      createdDate\n      country\n      countryCode\n      placeOfBirth\n      countryOfBirthCode\n      blocked\n      verified\n      blockedDate\n      blockedStatus\n      subscriptionStatus\n      terminated\n      updatedDate\n      createdDate\n      geo\n      taxIdentificationNumber\n      workFunction\n      salaryIncome\n      taxResidence\n      personalAssets\n      blockedKyc\n      lastAddressModificationDate\n      numberOfChildren\n      nickname\n    }\n    parentUser {\n      __typename\n      id\n      applicationUserId\n      parentId\n      isMainUser\n      phone\n      firstname\n      lastname\n      parentType\n      email\n      roles\n      isSubscriptionFinished\n      civility\n      birthdate\n      zendeskId\n      avatarId\n      isDeleted\n      autoTopupEnabled\n      allowRating\n      picture\n      lang\n      hasDoneTopup\n      nickname\n      createdDate\n    }\n    children {\n      __typename\n      id\n      firstname\n      lastname\n      birthdate\n      phone\n      email\n      picture\n      mainWalletId\n      civility\n      allowRating\n      isOnboardingFinished\n      isSelfOnboardingFinished\n      unregistered\n      isGift\n      schoolId\n      schoolGrade\n      lang\n      pixpayGo\n      hasDoneMoneyRequest\n      allowRating\n      applicationUserId\n      planId\n      createdDate\n      hasFreeCard\n    }\n    child {\n      __typename\n      id\n      firstname\n      lastname\n      birthdate\n      phone\n      email\n      picture\n      mainWalletId\n      civility\n      allowRating\n      isOnboardingFinished\n      isSelfOnboardingFinished\n      unregistered\n      isGift\n      schoolId\n      schoolGrade\n      lang\n      pixpayGo\n      hasDoneMoneyRequest\n      allowRating\n      applicationUserId\n      planId\n      createdDate\n      hasFreeCard\n    }\n    childProspect {\n      __typename\n      id\n      tenantId\n      firstname\n      lastname\n      birthdate\n      phone\n      email\n      userToken\n      pushActivated\n      isChildRegistrationDone\n      countryCode\n      cardPrint\n      tailorMadeCardId\n    }\n  }\n}"
}
```

exemple de réponse :
```json
{
  "data": {
    "__typename": "Query",
    "me": {
      "__typename": "User",
      "id": "id utilisateur qui est connecté",
      "login": "+33@......",
      "email": null,
      "device": {
        "__typename": "Device",
        "id": null,
        "pushToken": null,
        "pushDesactivatedInSystem": null
      },
      "isUnregistered": false,
      "pendingRegistrationId": null,
      "processingOrderId": null,
      "pushActivated": true,
      "parent": {
        "__typename": "Parent",
        "id": "... id du parent",
        "firstname": "...",
        "lastname": "...",
        "email": "..........@.......",
        "phone": "+336....",
        "birthdate": "DDMMYYYY",
        "civility": "M",
        "mainWalletId": "uuid",
        "address": "...",
        "addressComplement": null,
        "addressState": null,
        "city": "....",
        "zipcode": "12345",
        "createdDate": "2000-00-00T00:00:00.000Z",
        "country": "France",
        "countryCode": "FR",
        "placeOfBirth": "...",
        "countryOfBirthCode": "FR",
        "blocked": false,
        "verified": true,
        "blockedDate": null,
        "blockedStatus": null,
        "subscriptionStatus": "active",
        "terminated": false,
        "updatedDate": "2000-00-00T00:00:00.000Z",
        "geo": "FR",
        "taxIdentificationNumber": "",
        "workFunction": "...",
        "salaryIncome": "...",
        "taxResidence": "FR",
        "personalAssets": "000-000",
        "blockedKyc": false,
        "lastAddressModificationDate": "2000-00-00T00:00:00.000Z",
        "numberOfChildren": null, // oui c'est vraiment à null genre pixpay mettez le nb d'enfants jsp 😭😭
        "nickname": null
      },
      "parentUser": [
        {
          "__typename": "ParentUser",
          "id": "...",
          "applicationUserId": "....",
          "parentId": "....",
          "isMainUser": true, // true = c'est le parent au dessus
          "phone": "+336.......",
          "firstname": "...",
          "lastname": "...",
          "parentType": null,
          "email": ".......@.......",
          "roles": [],
          "isSubscriptionFinished": true,
          "civility": "M",
          "birthdate": "DDMMYYYY",
          "zendeskId": "id user zendesk",
          "avatarId": 1,
          "isDeleted": false,
          "autoTopupEnabled": null,
          "allowRating": false,
          "picture": null,
          "lang": null,
          "hasDoneTopup": true,
          "nickname": null,
          "createdDate": "timestamp de création du compte"
        },
        // ...
      ],
      "children": [
        {
          "__typename": "Child",
          "id": ".......",
          "firstname": "....",
          "lastname": "....",
          "birthdate": "DDMMYYYY",
          "phone": "+33......",
          "email": ".......@........",
          "picture": null,
          "mainWalletId": "uuid",
          "civility": "M",
          "allowRating": false,
          "isOnboardingFinished": true,
          "isSelfOnboardingFinished": true,
          "unregistered": null,
          "isGift": true,
          "schoolId": "uuid",
          "schoolGrade": "3EME", // ah ouais lourde l'update
          "lang": null,
          "pixpayGo": false,
          "hasDoneMoneyRequest": true,
          "applicationUserId": "....",
          "planId": "plan_.......",
          "createdDate": "2000-00-00T00:00:00.000Z",
          "hasFreeCard": false
        }
        // ....
      ],
      "child": {
        "__typename": "Child",
        "id": "....",
        "firstname": "....",
        "lastname": ".......",
        "birthdate": "DDMMYYYY",
        "phone": "+++3........",
        "email": ".....@........",
        "picture": null,
        "mainWalletId": "..........",
        "civility": "M",
        "allowRating": false,
        "isOnboardingFinished": true,
        "isSelfOnboardingFinished": true,
        "unregistered": null,
        "isGift": true,
        "schoolId": "........",
        "schoolGrade": "3EME",
        "lang": null,
        "pixpayGo": false,
        "hasDoneMoneyRequest": true,
        "applicationUserId": ".......",
        "planId": "plan_.....",
        "createdDate": "2000-00-00T00:00:00.000Z",
        "hasFreeCard": false
      },
      "childProspect": null
    }
  }
}
```

### Récupérer le timestamp serveur

`GET https://prod-bo1.pixpay.app/timestamp`

```json
{
  "timestamp": "2024-11-06T18:50:17.385Z"
}
```

### Récupérer les perms d'un enfant par son ID

requête :
```json
{
  "operationName": null,
  "variables": {
    "childId": "..."
  },
  "query": "query findPermissionsByChildId($childId: String!) {\n  __typename\n  findPermissionsByChildId(childId: $childId) {\n    __typename\n    feature\n    isAllowed\n  }\n}"
}
```

exemple de réponse : 
```json
{
  "data": {
    "__typename": "Query",
    "findPermissionsByChildId": [
      {
        "__typename": "PlanPermission",
        "feature": "virtual_card",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "vip_support",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "travel",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "insurance_ultimate",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "cashback_v_two",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "free_abroad_card_transaction",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "free_card_reorder",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "tailor_made",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "loan",
        "isAllowed": true
      },
      {
        "__typename": "PlanPermission",
        "feature": "free_withdrawal",
        "isAllowed": true
      },
      {
        "__typename": "PlanPermission",
        "feature": "custom_app",
        "isAllowed": true
      },
      {
        "__typename": "PlanPermission",
        "feature": "insurrance",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "carbon_footprint",
        "isAllowed": true
      },
      {
        "__typename": "PlanPermission",
        "feature": "custom_card",
        "isAllowed": false
      },
      {
        "__typename": "PlanPermission",
        "feature": "missions",
        "isAllowed": true
      },
      {
        "__typename": "PlanPermission",
        "feature": "projects",
        "isAllowed": true
      },
      {
        "__typename": "PlanPermission",
        "feature": "vault",
        "isAllowed": true
      }
    ]
  }
}
```

### Récupérer les "wallets"

requête :
```json
{
  "operationName": null,
  "variables": {
    "lastSynchroDate": null
  },
  "query": "query SynchroWallets($lastSynchroDate: Date) {\n  __typename\n  synchroWallets(lastSynchroDate: $lastSynchroDate) {\n    __typename\n    wallets {\n      __typename\n      id\n      balance\n      currency\n      authorizedBalance\n      authorizedDisplayed\n      nextBalance\n      isMain\n      isSaving\n      isCashback\n      isTravel\n      name\n      childUserId\n      createdDate\n      iban\n      bic\n      isDeleted\n      externalId\n      updatedDate\n    }\n    date\n  }\n}"
}
```

exemple de réponse :
```json
{
  "data": {
    "__typename": "Query",
    "synchroWallets": {
      "__typename": "WalletsSynchro",
      "wallets": [
        {
          "__typename": "Wallet",
          "id": "....",
          "balance": 0,
          "currency": "EUR",
          "authorizedBalance": 0,
          "authorizedDisplayed": null,
          "nextBalance": 0,
          "isMain": false,
          "isSaving": false,
          "isCashback": true,
          "isTravel": false,
          "name": "[Cashback] : ......",
          "childUserId": "....",
          "createdDate": "2000-00-00T00:00:00.000Z",
          "iban": null,
          "bic": null,
          "isDeleted": false,
          "externalId": "...",
          "updatedDate": "2000-00-00T00:00:00.000Z"
        },
        { // ce wallet est la cb
          "__typename": "Wallet",
          "id": "......",
          "balance": 12.71, // oui en effet c'est la hess
          "currency": "EUR",
          "authorizedBalance": 12.71,
          "authorizedDisplayed": null,
          "nextBalance": 0,
          "isMain": true,
          "isSaving": false,
          "isCashback": false,
          "isTravel": false,
          "name": "[Child Checking] ....",
          "childUserId": "....",
          "createdDate": "2000-00-00T00:00:00.000Z",
          "iban": "iban du compte bancaire",
          "bic": "bic du compte bancaire",
          "isDeleted": false,
          "externalId": "...",
          "updatedDate": "2000-00-00T00:00:00.000Z"
        },
        { // ce wallet est le coffre fort je crois
          "__typename": "Wallet",
          "id": "......",
          "balance": 0,
          "currency": "EUR",
          "authorizedBalance": 0,
          "authorizedDisplayed": null,
          "nextBalance": 0,
          "isMain": true,
          "isSaving": true,
          "isCashback": false,
          "isTravel": false,
          "name": "[Child Vault] : .......",
          "childUserId": "...",
          "createdDate": "2000-00-00T00:00:00.000Z",
          "iban": null,
          "bic": null,
          "isDeleted": false,
          "externalId": "000000000",
          "updatedDate": "2000-00-00T00:00:00.000Z"
        }
      ],
      "date": "2000-00-00T00:00:00.000Z"
    }
  }
}
```

### Récupérer les "wallets" (des autres enfants de la famille)

requête : 
```json
{
  "operationName": null,
  "variables": {},
  "query": "query getSiblingWallet {\n  __typename\n  getSiblingWallet {\n    __typename\n    id\n    balance\n    currency\n    authorizedBalance\n    authorizedDisplayed\n    nextBalance\n    isMain\n    isSaving\n    isCashback\n    name\n    childUserId\n    createdDate\n    iban\n    bic\n    isDeleted\n    externalId\n    updatedDate\n  }\n}"
}
```

réponse : voir [Récupérer les "wallets"](#récupérer-les-wallets)

### Récupérer le wallet du parent

requête :
```json
{
  "operationName": null,
  "variables": {},
  "query": "query getParentWallet {\n  __typename\n  getParentWallet {\n    __typename\n    id\n    balance\n    currency\n    authorizedBalance\n    authorizedDisplayed\n    nextBalance\n    isMain\n    isSaving\n    isCashback\n    name\n    childUserId\n    createdDate\n    iban\n    bic\n    isDeleted\n    externalId\n    updatedDate\n  }\n}"
}
```

exemple de réponse :
```json
{
  "data": {
    "__typename": "Query",
    "getParentWallet": {
      // référrez vous à la section au-dessus x2
    }
  }
}
```

### Argent chaque semaine/mois

requête :
```json
{
  "operationName": null,
  "variables": {
    "childId": "..."
  },
  "query": "query getAllowance($childId: ID!) {\n  __typename\n  getAllowance(childId: $childId) {\n    __typename\n    id\n    childId\n    amount\n    periodicity\n    periodicityDay\n    isSuspended\n    nextDistributionAmount\n    nextDistributionDate\n  }\n}"
}
```

exemple de réponse : 
```json
{
  "data": {
    "__typename": "Query",
    "getAllowance": {
      "__typename": "Allowance",
      "id": "...",
      "childId": "...",
      "amount": 30,
      "periodicity": "MONTHLY",
      "periodicityDay": 1,
      "isSuspended": false,
      "nextDistributionAmount": 30,
      "nextDistributionDate": "2000-00-00T00:00:00.000Z"
    }
  }
}
```

### Récupérer les infos de la cb

En fait vous avez les infos de votre cb mais aussi des autres membres de la famille, me demandez pas pourquoi, je sais pas

requête :
```json
{
  "operationName": null,
  "variables": {
    "lastSynchroDate": null,
    "includeRenewalCards": true,
    "includeVirtual": true,
    "includeDeleted": false
  },
  "query": "query SynchroCards($lastSynchroDate: Date, $includeRenewalCards: Boolean, $includeVirtual: Boolean, $includeDeleted: Boolean) {\n  __typename\n  synchroCards(lastSynchroDate: $lastSynchroDate, includeRenewalCards: $includeRenewalCards, includeVirtual: $includeVirtual, includeDeleted: $includeDeleted) {\n    __typename\n    date\n    cards {\n      __typename\n      id\n      embossedName\n      expiryMonth\n      expiryYear\n      statusCode\n      creationStatus\n      maskedPan\n      childHolderId\n      isLive\n      limitAtmEnabled\n      limitAtmPeriodicity\n      limitAtmAmount\n      limitPaymentEnabled\n      limitPaymentPeriodicity\n      limitPaymentAmount\n      isBlocked\n      isBlockedByParent\n      isBlockedByOperator\n      totalAtmWeek\n      totalAtmMonth\n      totalPaymentWeek\n      totalPaymentMonth\n      pinLocked\n      optionAtm\n      optionForeign\n      optionOnline\n      optionNfc\n      deliveryBatchDate\n      cardPrint\n      parentOnboardingFinished\n      childOnboardingFinished\n      authorizedColors\n      approxDeliveryDate\n      latitude\n      longitude\n      displayDeliveryDate\n      displayPinChosenDate\n      isDigitalizable\n      isRenewalCard\n      isDeleted\n      isVirtual\n      tailorMadeCardId\n      tailorMadeCardValidationStatus\n      tenantId\n      trackingId\n    }\n  }\n}"
}
```

exemple de réponse :
```json
{
  "data": {
    "__typename": "Query",
    "synchroCards": {
      "__typename": "CardsSynchro",
      "date": "2000-00-00T00:00:00.000Z",
      "cards": [
        {
          "__typename": "Card",
          "id": "...",
          "embossedName": "... nom prénom",
          "expiryMonth": "00",
          "expiryYear": "2000",
          "statusCode": "UNLOCK",
          "creationStatus": 4,
          "maskedPan": "000000******0000",
          "childHolderId": "...",
          "isLive": "1",
          "limitAtmEnabled": null,
          "limitAtmPeriodicity": "MONTHLY",
          "limitAtmAmount": 00,
          "limitPaymentEnabled": null,
          "limitPaymentPeriodicity": "WEEKLY",
          "limitPaymentAmount": 00,
          "isBlocked": false,
          "isBlockedByParent": false,
          "isBlockedByOperator": false,
          "totalAtmWeek": 0,
          "totalAtmMonth": 0,
          "totalPaymentWeek": 00.08,
          "totalPaymentMonth": 00.02,
          "pinLocked": false,
          "optionAtm": true,
          "optionForeign": true,
          "optionOnline": true,
          "optionNfc": true,
          "deliveryBatchDate": "2000-00-00",
          "cardPrint": "BLUE",
          "parentOnboardingFinished": true,
          "childOnboardingFinished": true,
          "authorizedColors": [],
          "approxDeliveryDate": "2000-00-00T00:00:00.000Z",
          "latitude": 0.00000,
          "longitude": 0.00000,
          "displayDeliveryDate": "2000-00-00T00:00:00.000Z",
          "displayPinChosenDate": "2000-00-00T00:00:00.000Z",
          "isDigitalizable": true,
          "isRenewalCard": false,
          "isDeleted": false,
          "isVirtual": false,
          "tailorMadeCardId": null,
          "tailorMadeCardValidationStatus": null,
          "tenantId": "...",
          "trackingId": null
        },
        // ...
      ]
    }
  }
}
```

### Mettre à jour les infos de l'appareil

exemple de requête :
```json
{
  "operationName": null,
  "variables": {
    "input": {
      "uuid": "...",
      "id": null,
      "pushToken": null,
      "manufacturer": "Microsoft Corporation",
      "model": "Subsystem for Android(TM)",
      "os": "Android",
      "osVersion": "13",
      "sdkVersion": "33",
      "language": "en_US",
      "region": null,
      "pushActivated": null,
      "pushDesactivatedInSystem": null,
      "applicationVersion": "5.87.15"
    }
  },
  "query": "mutation updateDevice($input: DeviceInput!) {\n  __typename\n  updateDevice(device: $input) {\n    __typename\n    id\n  }\n}"
}
```

réponse : 
```json
{
  "data": {
    "__typename": "Mutation",
    "updateDevice": null
  }
}
```

### Récupérer les notifications in-app

requête :
```json
{
  "operationName": null,
  "variables": {},
  "query": "query findInappNotification {\n  __typename\n  findInappNotification {\n    __typename\n    id\n    title\n    description\n    linkTarget\n    linkLabel\n    modalId\n    ctaTitle\n    emoji\n    storyId\n    type\n    readDate\n    createdDate\n  }\n}"
}
```

exemple de réponse :
```json
{
  "data": {
    "__typename": "Query",
    "findInappNotification": []
  }
}
```

### Savoir si la carte est digitale

requête :
```json
{
  "operationName": null,
  "variables": {
    "cardId": "..."
  },
  "query": "query isCardDigitalized($cardId: ID!) {\n  __typename\n  isCardDigitalized(cardId: $cardId)\n}"
}
```

réponse :
```json
{
  "data": {
    "__typename": "Query",
    "isCardDigitalized": true // ou false si c'est pas le cas
  }
}
```

## Fichiers du bucket S3

Cette section indexe juste les urls du bucket Amazon S3 utilisé par Pixpay, n"anmoins j'ai la flemme (pour le moment de documenter chaque URL)

* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/collections.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/marketing_communication/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/plan/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/share_template/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/background_wallet/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/pixpay_go/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/association/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/broker_coach/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/news/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/bloc_feature/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/child_task/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/insurance/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/advantage-marketing-news/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/gift/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/parent_offer_offers/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/parent_offer_services/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/hub/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/custom_card_preview/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/saving_feature/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/custom_card_category/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/hard_page/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/story/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/pixpay_throwback/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/content/prod/fr-fr/story/content.json
* https://pixpay-public.s3.eu-west-3.amazonaws.com/cards/Classic/blue.png
