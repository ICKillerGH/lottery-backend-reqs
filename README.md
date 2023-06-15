# Requerimientos del backend (desde la app)

**POST /signin**

Inicia sesión para un usuario vendedor de lotería a través de un JWT

Requiere autenticación: No

Ejemplo de petición con curl:

```bash
curl --location --request POST 'https://backend-domain.com/signin' \
--data-raw '{
    "email": "lotovendedor@gmail.com",
    "password": "12345678"
}'
```

**Respuesta esperada:**

HTTP_CODE: 200

DATA:

```json
{
    "user": {
        "id": "fff17672-5380-42e6-a183-251db82bcdfa",
        "name": "Pedro Perez",
        "dni": "23436017",
        "birthdate": "1994-01-13",
        "email": "lotovendedor@gmail.com",
        "phoneNumber": "+584124422440",
        "affiliateCode": "123456",
    },
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
}
```

---

**POST /signup**

Registra un usuario de tipo vendedor

Requiere autenticación: No

Ejemplo de petición con curl:

```bash
curl --location --request POST 'https://backend-domain.com/signup' \
--data-raw '{
    "id": "fff17672-5380-42e6-a183-251db82bcdfa",
    "name": "Pedro Perez",
    "dni": "23436017",
    "birthdate": "1994-01-13",
    "email": "lotovendedor@gmail.com",
    "phoneNumber": "+584124422440",
    "affiliateCode": "123456",
    "password": "12345678",
    "passwordConfirmation": "12345678"
}'
```

**Respuesta esperada:**

HTTP_CODE: 200

DATA:

```json
{
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
}
```

---

**POST /forgot-password/code**

Envia un código para recuperar la contraseña a traves del email ingresado

Requiere autenticación: No

Ejemplo de petición con curl:

```bash
curl --location --request POST 'https://backend-domain.com/forgot-password/code' \
--data-raw '{
    "email": "lotovendedor@gmail.com"
}'
```

**Respuesta esperada:**

HTTP_CODE: 200

DATA:

No se espera ningún tipo de data, solo se necesita que se envíe un código de 6 dígitos
al correo electronico.

---

**POST /forgot-password/code/verification**

Verifica la validez del código enviado a través del email y lo intercambia por un token temporal para restablecer la contraseña. El token expira a los 5 minutos.

Requiere autenticación: No

Ejemplo de petición con curl:

```bash
curl --location --request POST 'https://backend-domain.com/forgot-password/code/verification' \
--data-raw '{
    "email": "lotovendedor@gmail.com",
    "code": "123456"
}'
```

**Respuesta esperada:**

HTTP_CODE: 200

DATA:
```json
{
    "tempToken": "asdf32fasgaeaefaewafeae",
}
```

---

**POST /forgot-password**

Establece la nueva contraseña

Requiere autenticación: No

Ejemplo de petición con curl:

```bash
curl --location --request POST 'https://backend-domain.com/forgot-password' \
--data-raw '{
    "password": "12345678",
    "passwordConfirmation": "12345678",
    "tempToken": "asdf32fasgaeaefaewafeae"
}'
```

**Respuesta esperada:**

HTTP_CODE: 200

DATA:

No se espera data

---

**POST /tickets**

Registra un nuevo ticket en el sistema

Requiere autenticación: Si

Ejemplo de petición con curl:

```bash
curl --location --request POST 'https://backend-domain.com/tickets' \
--data-raw '{
    "plays": [
        {
            "type": "lotto-3",
            "play": [1, 30, 83],
            "amount": 28.42,
            "currency": "VES"
        },
        {
            "type": "lotto-5",
            "play": [1, 30, 35],
            "amount": 28.42,
            "currency": "VES"
        },
    ],
    "paymentType": "card",
    "cardTransactionInfo": {
        "bankName": "Sunmi Bank",
        "bankRif": "J-309841327",
        "billType": "RECIBO DE COMPRA",
        "cardFranchise": "MasterCard",
        "commerceName1": "SUNMI TEST Store",
        "commerceName2": "",
        "commerceAddress": "Caracas, Baruta",
        "commerceRif": "1234",
        "affiliate": "0010000009",
        "terminal": "00001023",
        "batch": "000002",
        "cardNumber": "512391******7164",
        "date": "23/03/2023",
        "time": "04:39:27 PM",
        "appIdMIR": "A0000000041010 MASTERCARD",
        "approval": "798863",
        "reference": "000342",
        "trace": "000379",
        "currency": "Bs.",
        "amount": "1,85",
    }
}'
```
- paymentType: "card" | "cash"
- cardTransactionInfo: solo es requerido cuando el tipo de pago es "card"


**Respuesta esperada (si todo va bien):**

HTTP_CODE: 200

DATA:

No se espera data

**Respuesta esperada (para saldo insuficiente cuando se paga por "cash"):**

HTTP_CODE: 400

DATA:

```json
{
    "error": {
        "message": "Saldo insuficiente para realizar esta operación"
    }
}
```

### Respuesta esperada (para saldo transacción no encontrada cuando se paga por "card"):

HTTP_CODE: 400

DATA:

```json
{
    "error": {
        "message": "Transacción con el proveedor no registrada, contacte con la agencia de lotería"
    }
}
```

---

**GET /tickets**

Retorna los tickets registrados en el sistema

Requiere autenticación: Si

Ejemplo de petición con curl:

```bash
curl --location --request GET 'https://backend-domain.com/tickets?limit=10,offset=0'
```

**Query parameters**

- limit: número máximo de resultados
- offset: la cantidad de resultados que debe saltar


**Respuesta esperada:**

HTTP_CODE: 200

DATA:

```json
[
    {
        "id": "225a5e19-95ef-4667-94b4-7504bab8e94d",
        "number": 123122,
        "plays": [
            {
                "type": "lotto-3",
                "play": [1, 30, 83],
                "amount": 28.42,
                "currency": "VES"
            },
            {
                "type": "lotto-5",
                "play": [1, 30, 35],
                "amount": 28.42,
                "currency": "VES"
            },
        ],
        "paymentType": "card",
    },
    {
        "id": "9dd86fd4-b4a3-41ad-b34b-1dacc7dd7058",
        "number": 123123,
        "plays": [
            {
                "type": "lotto-3",
                "play": [1, 30, 83],
                "amount": 28.42,
                "currency": "VES"
            },
            {
                "type": "lotto-5",
                "play": [1, 30, 35],
                "amount": 28.42,
                "currency": "VES"
            },
        ],
        "paymentType": "card",
    }
]
```

---

**GET /tickets/:ticketNumber/:lastFourCodeDigits**

Retorna los tickets registrados en el sistema

Requiere autenticación: Si

Ejemplo de petición con curl:

```bash
curl --location --request GET 'https://backend-domain.com/tickets/1654654/9abc'
```


**Respuesta esperada:**

HTTP_CODE: 200

DATA:

```json
{
    "id": "225a5e19-95ef-4667-94b4-7504bab8e94d",
    "number": 123123,
    "plays": [
        {
            "type": "lotto-3",
            "play": [1, 30, 83],
            "amount": 28.42,
            "currency": "VES"
        },
        {
            "type": "lotto-5",
            "play": [1, 30, 35],
            "amount": 28.42,
            "currency": "VES"
        },
    ],
    "paymentType": "card",
    "drawId": "225a5e19-95ef-4667-94b4-7504bab8e94d",
    "status": "pending", //"pending" | "canceled" | "win" | "loss",
    "prizeMoney": { // si el estatus es "win"
        "amount": 280.42,
        "currency": "VES"
    },
    "isPrizeCollected": false,
}

```

---
