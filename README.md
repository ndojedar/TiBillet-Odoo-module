# Odoo
Odoo for TiBillet financial report

# RoadMap :
- [x] XMLRPX http API
- [x] Api Check Version
- [x] Api Check Login
- [x] API Création de fiche contact Odoo
- [x] Nouvelle adhésion TiBillet -> Odoo
- [x] Création d'un nouvel article d'adhésion s'il n'existe pas
- [x] Facturation en brouillon de l'adhésion
- [x] Enregistrement d'un paiement de facture
- [ ] Selection du moyen de paiement

# API :

Postman documentation : https://documenter.getpostman.com/view/17519122/UVypzHCa

### Version :

	POST http://localhost:8069/tibillet-api/common/version

#### POST DATA
```json
{
    "params": {}
}
```

#### RESPONSE
```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "server_version": "15.0-20220307",
        "server_version_info": [
            15, 0, 0, "final", 0, ""
        ],
        "server_serie": "15.0",
        "protocol_version": 1,
        "random": "0.5241139307469328"
    }
}
```


### Login :

	POST http://localhost:8069/tibillet-api/xmlrpc/login

#### Parameters

Attribute | Type | Required | Description
--- | --- | --- | ---
`db` | string | yes | Odoo server DB name
`login` | string | yes | Odoo User
`api_key` | string | yes | Odoo User ApiKey


#### POST DATA
```json
{
    "params": {
        "db": "{{db}}",
        "login": "{{login}}",
        "apikey": "{{api_key}}"
    }
}
```

#### Response :

```json
{
    "jsonrpc": "2.0",
    "id": null,
    "result": {
        "random": "0.7985724403682478",
        "user_uid": 2,
        "authentification": true,
        "permissions": {
            "show_full_accounting_features": 25,
            "manager": 31
        }
    }
}
```


# Install :

## From scratch :


```
git clone --recurse-submodules https://github.com/TiBillet/Odoo.git
```

Copier le fichier ```docker/env_example``` vers ```docker/.env```.
Modifiez le fichier ```.env``` pour changer les variables.

Lancez ```docker-compose config``` pour vérifier que tout est bon.
Créez le réseau comme indiqué si besoin.


Lancez les conteneurs : ```docker-compose up -d```

Aller sur ```http://localhost:8069``` et suivez les instructions.
Notez bien le mot de passe master, il ne vous sera plus présenté nulle part.
Notez aussi le nom que vous avez donné à la base de donnée odoo.

## Avec un Odoo 15 déjà installé

Copiez et/ou montez ces dossier dans le dossier addon de votre installation.

- extra-addons/tibillet:/mnt/extra-addons/tibillet
- submodule/odoo_api:/mnt/extra-addons/odoo_api
- submodule/vertical-association/membership_extension:/mnt/extra-addons/membership_extension
- submodule/vertical-association/membership_variable_period:/mnt/extra-addons/membership_variable_period

## Inside Odoo

Allez sur Odoo / Application. Retirez le tag "application" dans la barre de recherche. Tapez "tibillet" et installez.
Cela va vérifier si les application suivantes sont installées et les installer si besoin.

```
# __manifest__.py
'depends': [
    'base',
    'account',
    'contacts',
    'membership',
    'membership_extension',
    'membership_variable_period',
    'odoo_api'
], 
```

## Testez l'API et l'Authentification

```shell
curl --location --request POST 'http://localhost:8069/tibillet-api/xmlrpc/login' \
--data-raw '{
    "params": {
        "db": "${ODOO_DATABASE}",
        "login": "${LOGIN}>",
        "apikey": "${API_KEY}"
    }
}'
```

### Permissions :

Si None dans les permissions :
```js
        "permissions": {
            "show_full_accounting_features": None,
            "manager": none
        }
```

Rajoutez les droits d'utilisateur dans Odoo :
Paramètres -> utilisateurs et sociétés -> Groupes
- Technique / Montrer les fonctions de comptabilité complètes
- Adhésion / Responsable