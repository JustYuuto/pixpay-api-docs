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

### 
