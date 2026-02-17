# 📜 Q-TOTP — Quantum-Linked Time-based One-Time Password

**RFC Draft — Versão 1.0 em Markdown**

---

## 1. Status of This Memo

Este documento especifica o protocolo Q-TOTP (“Quantum-Linked TOTP”). Ele define os mecanismos, formatos de dados, fluxos de mensagens, considerações de segurança e Threat Model de um protocolo de autenticação compatível com TOTP (RFC-6238), porém com:

* Seed dinâmico mutável
* Identidade evolutiva (Identity Chain)
* Suporte multi-dispositivo
* Mecanismos de recuperação e tolerância ao drift biometricamente
* Estrutura clara de assinatura de estado e rotação de chaves

Este é um **draft de especificação técnica** que pode ser usado como base de implementação e validação.

---

## 2. Terminology and Conventions

As palavras-chave **MUST**, **SHOULD**, **MAY**, **MUST NOT**, **SHOULD NOT** e **RECOMMENDED** são usadas em conformidade com RFC-2119 e RFC-8174 para descrever requisitos e recomendações.

---

## 3. Definitions

| Termo              | Definição                                                     |
| ------------------ | ------------------------------------------------------------- |
| **IdP**            | Identity Provider confiável (ex: Google, Gov.br, Banco).      |
| **Genesis**        | Evento inicial que cria identidade validada por IdP.          |
| **S_user**         | Segredo mestre do usuário, derivado de fatores humanos.       |
| **Identity Chain** | Cadeia encadeada de hashes de estado representando evolução.  |
| **state_hash**     | Hash do estado i da cadeia (biometria e histórico).           |
| **SEED_i**         | Seed dinâmico para TOTP derivado de S_user e state_hash i.    |
| **TOTP**           | Código temporário gerado via RFC-6238 usando SEED_i.          |
| **Vault**          | Criptograma que armazena S_user protegido por chave derivada. |
| **ICSH**           | Identity Chain Synchronization Handshake, protocolo de sync.  |

---

## 4. Overview and Goals

O Q-TOTP estende conceitos de TOTP clássico para:

* Resolver o problema de segredo estático
* Autenticar via continuidade de identidade
* Permitir sincronização entre dispositivos
* Fornecer modos escaláveis de proteção (v1, v2)
* Suportar recuperação e tolerância natural de uso humano

Ele é compatível com RFC-6238 para geração final de códigos.

---

## 5. Architecture

O protocolo tem três camadas principais:

```
┌───────────────────────────────────┐
|        Q-TOTP Core                |
|  • Identity Chain                 |
|  • Genesis                        |
|  • Seed Derivation                |
|  • TOTP Final (RFC-6238)         |
└───────────────────────────────────┘
        ↑                  ↑
        |                  |
        v                  v
  Q-TOTP v1 (simple)   Q-TOTP v2 (paranoic)
                  • Fragmented secrets
                  • Zero-knowledge biometric
                  • Sync multi-device
```

---

## 6. Data Structures

### 6.1 User Record (JSON)

```json
{
  "user_id": "string (UUID)",
  "public_name": "string",
  "pin_hint_hash": "string (SHA-256)",
  "genesis": { … },
  "identity_chain": [ … ],
  "qtotp": { … },
  "policy": { … }
}
```

### 6.2 Genesis Block

```json
"genesis": {
  "idp": "string",
  "idp_subject_hash": "string",
  "verified": true,
  "verified_at": "timestamp"
}
```

### 6.3 Identity Chain Entry

```json
{
  "iteration": n,
  "timestamp": "UNIX seconds",
  "prev_hash": "string",
  "state_hash": "string",
  "bio_templates": { … }
}
```

### 6.4 Q-TOTP Metadata

```json
"qtotp": {
  "S_user": "base64",
  "last_counter": 0
}
```

### 6.5 Policy

```json
"policy": {
  "min_score": 0.85,
  "required": ["keyboard"],
  "optional": ["voice","face","fingerprint","cognitive"]
}
```

---

## 7. Identity Chain

A cada autenticação válida:

```
state_hash_i =
    SHA256( biometric_vector_i || prev_state_hash )
```

O primeiro estado i=1 é derivado de Genesis.

---

## 8. Seed and TOTP

### 8.1 SEED Derivation

```
SEED_i = HMAC( S_user , last_state_hash )
```

### 8.2 TOTP Final

Usa RFC-6238 com SEED_i e timestamp (30s window).

---

## 9. Drift Biometric Update

```
new_template =
   (1 − α) * old_template +
   α * new_capture
```

Com α ∈ [0.05, 0.15] por defeito, configurável.

---

## 10. Threshold Adaptation

Threshold dinâmico baseado em variância histórica.

```
threshold = f(historical_variance)
```

---

## 11. API Endpoints (Complete REST Examples)

### 11.1 POST /auth/enroll

**Request**

```
POST /auth/enroll
Content-Type: application/json
```

```json
{
  "public_name": "string",
  "pin_hint": "string",
  "birthday": "YYYY-MM-DD",
  "blood_type": "string",
  "keyboard_profile": { … },
  "idp_token": "string"
}
```

**Response (202 Accepted)**

```json
{
  "status": "pending",
  "email_verification_link": "url",
  "temp_id": "string"
}
```

---

### 11.2 GET /auth/verify

**Called when client clicks verification link**

**Response (200 OK)**

```json
{
  "status": "verified",
  "user_id": "string"
}
```

---

### 11.3 POST /auth/challenge

**Request**

```json
{
  "public_name": "string",
  "pin_hint": "string"
}
```

**Response (200 OK)**

```json
{
  "salt": "string",
  "time": 1234567890,
  "q_server": "string"
}
```

---

### 11.4 POST /auth/verify

**Request**

```json
{
  "user_id": "...",
  "F": "string",
  "otp": "string",
  "q_client": "string",
  "state_hash": "string"
}
```

**Response (200 OK)**

```json
{
  "status": "success",
  "next_state_hash": "string"
}
```

---

### 11.5 GET /qtotp/certs

**Response (200 OK)**

```json
[
  {
    "cert_id": "string",
    "public_key": "PEM string",
    "valid_from": "timestamp",
    "valid_to": "timestamp"
  }
]
```

---

### 11.6 POST /auth/sync

**Request**

```json
{
  "user_id": "string",
  "local_state_hash": "string",
  "local_iteration": 4
}
```

**Response (200 OK)**

```json
{
  "server_iteration": 6,
  "state_delta_proof": {
     "signature": "base64",
     "new_state_hash": "string"
  },
  "MAX_STATE_LAG": 3
}
```

---

## 12. ASCII Diagrams

### 12.1 Enroll + Genesis

```
Client           Server           IdP
   |               |               |
   | -- enroll --> |               |
   |               | -- validate IdP --> |
   |               |               | -- return idp_subject
   |               | <-- store genesis --|
   |               |               |
   |<-- verify link sent -----------|
```

---

### 12.2 Auth Sync (ICSH)

```
Client (B)                    Server
    |   POST /auth/sync         |
    | ------------------------> |
    |                            |
    |     200: SYNC_REQUIRED     |
    | <------------------------ |
    | verify signature   |
    | update local state           |
```

---

## 13. Identity Chain Sync (ICSH)

### 13.1 Model

Servidor é fonte de verdade:

```
server_iteration, last_state_hash
```

Cliente compara:

```
local_iteration < server_iteration
```

---

## 14. Security Considerations

* Proteção de servidor signing key (HSM, TPM)
* Revogação de certificados
* Drift limits
* Threshold adaptation
* Fork detection

---

## 15. Threat Model

Considera:

* Vazamento de DB
* Roubo isolado
* Ataque de interpolação gradual
* Fork malicioso

---

## 16. Key Rotation

Servidor publica certificados via `/qtotp/certs`.
Clientes devem validar assinatura com o certificado apropriado.

---

## 17. Cryptographic Primitives

Recomendações:

* SHA-256
* HMAC-SHA-256
* AES-256 GCM / ChaCha20
* ECDSA / Ed25519

---

## 18. PoC

Descreve modelos de teste para calibrar:

```
α (drift parameter),
k (MAX_STATE_LAG)
```

com dados reais.

---

## 19. Implementation Notes

* Identity Chain must be immutable
* Sync must be robust
* APIs must reject forked states

---

## 20. IANA Considerations

Nenhuma no momento

---

## 21. References

* RFC-6238 — TOTP
* RFC-4226 — HOTP
* RFC-2119 / 8174
* Shamir Secret Sharing
* Zero-Knowledge Proofs (general)

---
