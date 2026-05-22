# Servers API

Base URL:
[https://vpsinstall.top/api/servers](https://vpsinstall.top/api/servers)

Authentication: Bearer Token

```http
Authorization: Bearer YOUR_API_TOKEN
Accept: application/json
```

---

# List Servers

## Endpoint

```http
GET /api/servers
```

## Required Permission

```text
servers:read
```

## Query Parameters

| Parameter | Type    | Default | Max | Description              |
| --------- | ------- | ------- | --- | ------------------------ |
| per_page  | integer | 15      | 100 | Number of items per page |

## Example Request

```bash
curl --request GET \
  --url https://vpsinstall.top/api/servers?per_page=20 \
  --header "Authorization: Bearer YOUR_API_TOKEN" \
  --header "Accept: application/json"
```

## Example Response

```json
{
  "data": [
    {
      "id": "srv_682ef5a9d0f13",
      "name": "ubuntu-22",
      "ip": "192.168.1.10",
      "ssh_port": 22,
      "install_ip": "192.168.1.10",
      "install_port": 22,
      "install_username": "admin",
      "install_password": "StrongPassword123!",
      "status": "success",
      "image": {
        "id": 1,
        "name": "ubuntu-22"
      },
      "created_at": "2026-05-22T10:00:00Z",
      "updated_at": "2026-05-22T10:05:00Z",
      "links": {
        "web": "https://vpsinstall.top/servers/srv_682ef5a9d0f13"
      }
    }
  ],
  "links": {
    "first": "https://vpsinstall.top/api/servers?page=1",
    "last": "https://vpsinstall.top/api/servers?page=5",
    "prev": null,
    "next": "https://vpsinstall.top/api/servers?page=2"
  },
  "meta": {
    "current_page": 1,
    "per_page": 20,
    "total": 100
  }
}
```

---

# Get Server Detail

## Endpoint

```http
GET /api/servers/{id}
```

## Required Permission

```text
servers:read
```

## Example Request

```bash
curl --request GET \
  --url https://vpsinstall.top/api/servers/1 \
  --header "Authorization: Bearer YOUR_API_TOKEN" \
  --header "Accept: application/json"
```

## Example Response

```json
{
  "data": {
    "id": "srv_682ef5a9d0f13",
    "name": "ubuntu-22",
    "ip": "192.168.1.10",
    "ssh_port": 22,
    "install_ip": "192.168.1.10",
    "install_port": 22,
    "install_username": "admin",
    "install_password": "StrongPassword123!",
    "status": "success",
    "image": {
      "id": 1,
      "name": "ubuntu-22"
    },
    "created_at": "2026-05-22T10:00:00Z",
    "updated_at": "2026-05-22T10:05:00Z",
    "links": {
      "web": "https://vpsinstall.top/servers/srv_682ef5a9d0f13"
    }
  }
}
```

## Error Response

### Server Not Found

```json
{
  "message": "Not Found"
}
```

---

# Create Server

## Endpoint

```http
POST /api/servers
```

## Required Permission

```text
servers:create
```

## Request Body

| Field            | Type           | Required    | Description                                           |
| ---------------- | -------------- | ----------- | ----------------------------------------------------- |
| ip               | string         | Yes         | Server IP address                                     |
| port             | integer        | No          | SSH port (default: 22)                                |
| username         | string         | No          | SSH username (default: root)                          |
| password         | string         | Conditional | SSH password, required if private_key is not provided |
| private_key      | string         | Conditional | SSH private key, required if password is not provided |
| passphrase       | string         | No          | Private key passphrase                                |
| install_image    | string/integer | Yes         | Image ID or image name                                |
| install_port     | integer        | No          | Installed server SSH port (default: 22)               |
| install_username | string         | Yes         | Username for installed server                         |
| install_password | string         | Yes         | Password for installed server                         |

## Validation Rules

### ip

* Must be a valid IP address
* Cannot already exist in pending status for the same user

### port / install_port

* Range: `1 - 65535`

### username

* Maximum length: `50`

### install_username

* Maximum length: `20`
* Must pass custom username validation

### install_password

* Minimum length: `8`
* Maximum length: `150`
* Must pass password strength validation

---

## Example Request (Password Authentication)

```bash
curl --request POST \
  --url https://vpsinstall.top/api/servers \
  --header "Authorization: Bearer YOUR_API_TOKEN" \
  --header "Accept: application/json" \
  --header "Content-Type: application/json" \
  --data '{
    "ip": "192.168.1.10",
    "port": 22,
    "username": "root",
    "password": "your-ssh-password",
    "install_image": "ubuntu-22",
    "install_port": 22,
    "install_username": "admin",
    "install_password": "StrongPassword123!"
}'
```

---

## Example Request (Private Key Authentication)

```bash
curl --request POST \
  --url https://vpsinstall.top/api/servers \
  --header "Authorization: Bearer YOUR_API_TOKEN" \
  --header "Accept: application/json" \
  --header "Content-Type: application/json" \
  --data '{
    "ip": "192.168.1.10",
    "username": "root",
    "private_key": "-----BEGIN OPENSSH PRIVATE KEY-----...",
    "passphrase": "your-passphrase",
    "install_image": 1,
    "install_username": "admin",
    "install_password": "StrongPassword123!"
}'
```

---

## Success Response

```json
{
  "data": {
    "id": "srv_682ef5a9d0f13",
    "name": "ubuntu-22",
    "ip": "192.168.1.10",
    "ssh_port": 22,
    "install_ip": "192.168.1.10",
    "install_port": 22,
    "install_username": "admin",
    "install_password": "StrongPassword123!",
    "status": "pending",
    "image": {
      "id": 1,
      "name": "ubuntu-22"
    },
    "created_at": "2026-05-22T10:00:00Z",
    "updated_at": "2026-05-22T10:00:00Z",
    "links": {
      "web": "https://vpsinstall.top/servers/srv_682ef5a9d0f13"
    }
  }
}
```

---

## Error Responses

### Validation Error

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "ip": [
      "The ip field is required."
    ]
  }
}
```

### Insufficient Balance

```json
{
  "message": "Not enough balance",
  "errors": {
    "balance": [
      "Your balance is insufficient to create this server."
    ]
  }
}
```

### Invalid Image

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "install_image": [
      "The selected image is invalid."
    ]
  }
}
```
