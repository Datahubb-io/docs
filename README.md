<img src="https://datahubb.io/wp-content/uploads/2024/06/Logo.png" alt="Datahubb.io" height="80px" />

This document provides all the information needed to successfully implement datahubb's lead ingest api

### Public API Information

**General API Information:**

- The following base endpoints are available for test
  - [https://test.datahubb.io](https://test.datahubb.io)
- All endpoints return either a JSON object or array. So. highly recommended to use request `Accept` header to `application/json`

**HTTP Return Codes:**

- HTTP `4XX` return codes are used for malformed requests; the issue is on the sender's side.
- HTTP `403` return code is used when the WAF Limit (Web Application Firewall) has been violated.
- HTTP `5XX` return codes are used for internal errors; the issue is on Datahubb's side. It is important to NOT treat this as a failure operation; the execution status is UNKNOWN and could have been a success.

**General Information on Endpoints:**

- For `POST` endpoints, the parameters may be sent in the `request body` with content type `application/x-www-form-urlencoded` or `application/json`, we strongly recommend sending as `application/json`. You may mix parameters between both the `query string` and `request body` if you wish to do so.
- Parameters may be sent in any order.
- If a parameter sent in both the `query string` and `request body`, the `request body` parameter will be used.

**Api Endpoint:**

`POST /api/test-ingest`

Accepted Method: `POST`
Response Type: `application/json`

**Parameters:**

| Name | Type | Mandatory | Description |
|------|------|-----------|-------------|
| `api_key` | STRING | YES | Test api key: `9d242b7136564b279d16a29d6d74e81e` |
| `supplier_id` | STRING | YES | The supplier unique id. Use the following supplier id to Test: `9d1a3197-2a1f-4a1f-bf89-a4c77bb2d50d` |
| `is_test` | BOOLEAN | NO | To send lead as test |
| `payload` | JSON | YES | Contains the lead data provided by supplier |

**Payload:**
Lead payload contains dynamic key value pair which can be different by campaigns. Below is an example of payload with both required and optional fields

| Name | Type | Mandatory | Accepted Values |
|------|------|-----------|-----------------|
| `have_kidney_cancer` | STRING | YES | `Childhood Leukemia`, `Thyroid Cancer`, `Kidney Cancer` |
| `myself_or_lovedone` | STRING | YES | `Myself`, `Loved One` |
| `have_attorney` | STRING | YES | `Yes`, `No` |
| `first_name` | STRING | NO | |
| `last_name` | STRING | NO | |
| `email` | STRING | NO | |
| `phone` | STRING | NO | |
| `description` | STRING | NO | |
| `ip_address` | STRING | NO | |

**Response Status:**

- `SUCCESS` - Indicates the lead has accepted
- `PENDING` - Indicates the lead is accepted and requires a review
- `DUPLICATED` - Indicates the lead is duplicated
- `REJECTED` - Indicates the lead is rejected
- `ERROR` - Indicates the validation error or system error happend during processing data

**Responses:**

<details open>
    <summary>ACCEPTED</summary>

```json
{
    "success": true,
    "status": "ACCEPTED",
    "lead_id": "9d2476bc-0e04-458a-9d5b-efadfbd39201",
    "payout": 50,
    "buyer_type": "CPL",
    "stage": "Pass all the stage",
    "fraud_score": 0,
    "health_percentage": 100
}
```

</details>
<details>
    <summary>PENDING</summary>

```json
{
    "success": true,
    "status": "PENDING",
    "lead_id": "9d2476bc-0e04-458a-9d5b-efadfbd39201",
    "payout": 50,
    "buyer_type": "CPL",
    "stage": "Pass all the stage",
    "fraud_score": 0,
    "health_percentage": 100
}
```

</details>
<details>
    <summary>DUPLICATED</summary>

```json
{
    "success": false,
    "status": "DUPLICATED",
    "payout": 0,
    "stage": "DUPLICATED LEADS"
}
```

</details>
<details>
    <summary>REJECTED</summary>

```json
{
    "success": false,
    "status": "REJECTED",
    "lead_id": "9d2476bc-0e04-458a-9d5b-efadfbd39201",
    "payout": 0,
    "buyer_type": null,
    "stage": "NOT SOLD",
    "fraud_score": 0,
    "health_percentage": 100
}
```

</details>
<details>
    <summary>ERROR</summary>

```json
{
    "success": false,
    "message": "Missing fields: have_kidney_cancer"
}
```

</details>


### Example

`POST https://test.datahubb.io/api/test-ingest`

Request Body: `application/json`

```json
{
    "api_key": "9d242b7136564b279d16a29d6d74e81e",
    "supplier_id": "9d1a3197-2a1f-4a1f-bf89-a4c77bb2d50d",
    "payload": {
        "zip_code": "39771",
        "first_name": "Ryan",
        "last_name": "Reynolds",
        "email": "ryanreynolds@greenlantern.com",
        "phone": "(724)-543-1254",
        "description": "Test connection",
        "ip_address": "86.93.190.230",
        "jornaya_lead_id": "A86DC100-DF97-1875-DA2C-399AC646B34F",
        "have_kidney_cancer": "Childhood Leukemia",
        "myself_or_lovedone": "Myself",
        "have_attorney": "No",
        "affiliate_id": "001",
        "traffic_source": "7"
    }
}
```
