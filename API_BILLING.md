# Getup Engine API

Este documento descreve brevemente como acessar e quais as funcionalidades básicas disponíveis na Getup Engine API.

## Endpoint de acesso

A API de acesso aos dados de conta do Getup Engine é feita através de um endpoint REST, implementado em
[django-rest-framework - DRF](https://http://www.django-rest-framework.org/), portanto segue as mesmas práticas ditadas pelo framework em questão.

O endereço do endpoint depende da configuração do mesmo durante a implantação da plataforma, geralmente seguindo o formato:

    https://gapi.${CLOUD_ZONE}/api

sendo `CLOUD_ZONE` a zona de DNS principal do cluster, usada também para o acesso do portal em `https://portal.${CLOUD_ZONE}/`.

Além da interface web oferecida pelo DRF, está disponível documentação automática da API em:

    https://gapi.${CLOUD_ZONE}/docs/

Esta última permite acesso autenticado _Basic_ e _Bearer Token_.

Chamaremos o endpoint da Getup Engine API de `GAPI` ao longo deste documento.
Para acessar o endpoint público, use a url `https://gapi.getupcloud.com`.

## Autenticação

Para acesso autenticado é necessário utilizar um dos métodos descritos abaixo:

### Autenticação Basic HTTP

Forneça email e senha do usuário na requisição.

Exemplo:

```bash
$ curl https://${GAPI}/api/v1/customer/ --user ${EMAIL}:${SENHA}
```

### Autenticação Bearer Token

Primeiro é necessário criar um token de autenticação:

```bash
$ curl https://${GAPI}/api/v1/auth/login/ -X POST --data "email=${EMAIL}" --data "password=${PASSWORD}" | python -m json.tool
{
    "created": "2018-05-27T15:32:09.738440Z",
    "expires_in": 2419200,
    "auth_type": "Bearer",
    "key": "XXXXXXXXXXXXXX"
}
```

Use os campos `auth_type` e `key` da resposta em requisições subsequentes:

```bash
$ curl https://${GAPI}/api/v1/customer/user/ -H "Authorization: ${AUTH_TYPE} ${KEY}"
```

## Dados da conta

#### GET /api/v1/auth/login

Retorna informações sobre a conta autenticada.

```json
{
    "active": true,
    "billing_alert": 100,
    "billing_contact": {
        "kind": "personal",
        "address": "Blackheath Ave",
        "city": "London",
        "country": "UK",
        "email": "halley@rgo.co",
        "name": "Edmond Halley",
        "cnpj": "",
        "cpf": "12345678901",
        "phone": "",
        "state": "",
        "zip": "SE10 9NF"
    },
    "date_joined": "1970-01-01T00:00:00Z",
    "email": "halley@rgo.co",
    "first_name": "Edmond",
    "language": "en",
    "last_name": "Halley",
    "max_projects": 50,
    "status": "regular",
    "support_plan": "free",
    "technical_contact": {
        "email": "halley@rgo.co",
        "name": "Edmond Halley",
        "phone": ""
    },
    "uid": "f42e89704b8c",
    "user_hash": "e69a961812c1ac5217db42c6429326311f73d6882299c58562854f654d887216"
}
```

## Faturas

#### GET /api/v1/invoice/

Retorna a lista de faturas do usuário autenticado.

```json
{
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
        {
            "url": "https://${GETUP_API}/api/v1/invoice/year/2016/month/4/",
            "created": "2016-05-01T00:02:00.147355Z",
            "status": "new",
            "year": 2016
            "month": 4,
            "alert_sent": null,
            "projects": [
                {
                    "cost": 293.394,
                    "name": "blog"
                },
                {
                    "cost": 21.435,
                    "name": "demo"
                }
            ],
            "services": [],
            "used_credits": [],
            "cost": 314.829,
            "cost_currency": "BRL"
        },
        {
            "url": "http://${GETUP_API}/api/v1/invoice/year/2016/month/3/",
            "created": "2016-04-01T00:02:02.486854Z",
            "status": "paid",
            "year": 2016
            "month": 3,
            "alert_sent": "2016-03-19T22:00:27.133051Z",
            "projects": [
                {
                    "cost": 37.191,
                    "name": "blog"
                },
                {
                    "cost": 10.875,
                    "name": "demo"
                }
            ],
            "services": [
                {
                    "cost": 227.0,
                    "description": "Transferêeancia de dados em CDN Azure (GB) - Região Brasil",
                    "details": "",
                    "end": null,
                    "name": "Transferência CDN Azure (BR)",
                    "start": "2016-12-01T00:00:00Z"
                }
            ],
            "used_credits": [
                {
                    "amount": 275.066,
                    "amount_currency": "BRL",
                    "credit": {
                        "amount": "500.000",
                        "available": "224.934",
                        "recurring": true,
                        "scope_data": "",
                        "scope_description": "",
                        "scope_type": "manual",
                        "status": "using"
                    }
                }
            ],
            "cost": 1892.609,
            "cost_currency": "BRL"
        },
        {
            "url": "http://gapi.getupcloud.com/api/v1/invoice/year/2016/month/2/",
            "created": "2016-03-01T02:00:00.345662Z",
            "status": "paid",
            "year": 2016
            "month": 2,
            "alert_sent": "2016-01-19T22:00:27.133051Z",
            "cost": 1892.609,
            "cost_currency": "BRL",
            "projects": [
                {
                    "cost": 432.023,
                    "name": "blog"
                },
                {
                    "cost": 33.522,
                    "name": "demo"
                }
            ],
            "services": [
                {
                    "cost": 227.0,
                    "description": "Transferência de dados em CDN Azure (GB) - Região Brasil",
                    "details": "",
                    "end": null,
                    "name": "Transferência CDN Azure (BR)",
                    "start": "2016-04-01T00:00:00Z"
                }
            ],
            "used_credits": [
                {
                    "amount": 275.066,
                    "amount_currency": "BRL",
                    "credit": {
                        "amount": "500.000",
                        "available": "224.934",
                        "recurring": true,
                        "scope_data": "",
                        "scope_description": "",
                        "scope_type": "manual",
                        "status": "using"
                    }
                }
            ]
        }
    ]
}
```

#### GET /api/v1/invoice/year/${YEAR}/month/${MONTH}/

Retorna a fatura detalhada do mês/ano especifico.

```json
{
    "created": "2016-05-01T00:02:00.147355Z",
    "status": "new",
    "year": 2016
    "month": 4,
    "alert_sent": null,
    "billing_data": {
        "address": "Blackheath Ave",
        "city": "London",
        "country": "UK",
        "email": "halley@rgo.co.uk",
        "id_no": "12345678901",
        "kind": "personal",
        "name": "Badanha Ltda.",
        "phone": "(51)82980026",
        "state": "RS",
        "zip": "91751452"
    },
    "projects": [
        {
            "start": "2016-11-10T18:33:47Z"
            "end": null,
            "family": "default",
            "name": "demo",
            "namespace_uid": "3802a7e8-a774-11e6-91aa-000d3ac0312b",
            "payer": null,
            "cost": 7.090,
            "records": [
                {
                    "cost": 3.545,
                    "cpu": 293,
                    "duration": 510542,
                    "end": "2017-01-06T21:49:02Z",
                    "memory": 536870912,
                    "offer": "dev-small",
                    "resource_name": "mysql-4-froms",
                    "resource_scope": "mysql",
                    "resource_type": "pod_container",
                    "start": "2017-01-01T00:00:00Z"
                },
                {
                    "cost": 3.545,
                    "cpu": 293,
                    "duration": 510542,
                    "end": "2017-01-06T21:49:02Z",
                    "memory": 536870912,
                    "offer": "dev-small",
                    "resource_name": "app-1-tsjx3",
                    "resource_scope": "php",
                    "resource_type": "pod_container",
                    "start": "2017-01-01T00:00:00Z"
                },
            ],
        },
        {
            "start": "2016-09-10T12:10:29Z"
            "end": "2017-03-20T19:57:53.663109Z",
            "family": "default",
            "name": "blog",
            "namespace_uid": "90914e7f-774f-11e6-9481-000d3ac01d22",
            "payer": null,
            "cost": 10.875,
            "records": [
                {
                    "start": "2017-04-01T00:00:00Z"
                    "end": "2017-04-31T23:59:59Z",
                    "duration": 2678400,
                    "offer": "prod",
                    "cpu": 1000,
                    "memory": 2147483648,
                    "resource_type": "pod_container",
                    "resource_name": "blog-1-0ud7z",
                    "resource_scope": "php",
                    "cost": 10.874,
                }
            ]
        },
    ],
    "services": [
        {
            "cost": 227.0,
            "description": "Transferência de dados em CDN Azure (GB) - Região Brasil",
            "details": "",
            "end": null,
            "name": "Transferência CDN Azure (BR)",
            "start": "2016-12-01T00:00:00Z"
        }
    ],
    "used_credits": [
        {
            "amount": 227.0,
            "amount_currency": "BRL",
            "credit": {
                "amount": "500.000",
                "available": "1.546",
                "recurring": true,
                "scope_data": "",
                "scope_description": "",
                "scope_type": "signup",
                "status": "using"
            }
        }
    ],
    "cost": 314.829,
    "cost_currency": "BRL"
}
```

## Acesso Adminstrativo

O acesso adminstrativo permite listar as faturas dos usuários.

> Este acesso exige um usuário com poder administrativo.

### GET /api/admin/v1/billing/invoice/

Listar as faturas do mês atual. Os filtros `year`,`month`, `user` e `status` podem ser utilizados
na _querystring_:

- **year**: Ano, com 4 dígitos (AAAA). Default: ano atual
- **month**: Mês, com 2 dígitos (MM). Default: mês atual
- **user**: Email do usuário. Default: todos os usuários
- **status**: Um dos seguintes valores: `partial`, `new`, `paid` ou `failed`. Default: todos os status
- **all**: Retorna todos os registros do banco. Esta operação pode ser longa

Exemplo:

```
$ curl "https://${GAPI}/api/admin/v1/billing/invoice/?status=paid&month=01&year=2018" --user ${ADMIN_USER}:${ADMIN_PASS}
[
    {
        "user_id": 1968,
        "user": "bob.kennedy@gmail.com",
        "year": 2018,
        "month": 1,
        "cost": 59.194,
        "status": "paid"
    },
    {
        "user_id": 1963,
        "user": "john.kennedy@gmail.com",
        "year": 2018,
        "month": 1,
        "cost": 38.349,
        "status": "paid"
    },
    {
        "user_id": 2009,
        "user": "ted.kennedy@gmail.com",
        "year": 2018,
        "month": 1,
        "cost": 0.0,
        "status": "paid"
    }
]
```
