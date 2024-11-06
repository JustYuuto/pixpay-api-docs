# Pixpay API docs

Cette page répertorie les routes sur l'api interne utilisée au sein de l'application bancaire "Pixpay"

* base url: `https://prod-bo1.pixpay.app`
* user agents: 
  * `Dart/3.2 (dart:io)`
  * `Dalvik/2.1.0 (Linux; U; Android 13; Subsystem for Android(TM) Build/TQ3A.230901.001)`
* bucket amazon s3 : https://pixpay-public.s3.eu-west-3.amazonaws.com/

À part pour s'authentifier toutes les requêtes utilisent GraphQL sur `https://prod-bo1.pixpay.app/graphql` en post.

## Routes

### Authentification

Attention pour cette section l'api utilise le ndd `prod-auth.pixpay.app`

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
