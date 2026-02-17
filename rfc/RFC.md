# 📜 Q-TOTP — Quantum-Linked Time-based One-Time Password

**RFC Draft — Bilíngue PT-BR / EN-US — Version 1.0**

---

## 1. Status of This Memo

| 🇧🇷 Português                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | 🇺🇸 English                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Este documento especifica o protocolo Q-TOTP (“Quantum-Linked TOTP”). Ele define os mecanismos, formatos de dados, fluxos de mensagens, considerações de segurança e Threat Model de um protocolo de autenticação compatível com TOTP (RFC-6238), porém com:<br><br>• Seed dinâmico mutável<br>• Identidade evolutiva (Identity Chain)<br>• Suporte multi-dispositivo<br>• Mecanismos de recuperação e tolerância ao drift biométrico<br>• Estrutura clara de assinatura de estado e rotação de chaves<br><br>Este é um **draft de especificação técnica** que pode ser usado como base de implementação e validação. | This document specifies the Q-TOTP (“Quantum-Linked TOTP”) protocol. It defines the mechanisms, data formats, message flows, security considerations, and Threat Model of an authentication protocol compatible with TOTP (RFC-6238), but with:<br><br>• Mutable dynamic seed<br>• Evolutionary identity (Identity Chain)<br>• Multi-device support<br>• Recovery and biometric drift tolerance mechanisms<br>• Clear structure for state signing and key rotation<br><br>This is a **technical specification draft** intended as a basis for implementation and validation. |

---

## 2. Terminology and Conventions

| 🇧🇷 Português                                                                                                                                                                                | 🇺🇸 English                                                                                                                                                                                            |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| As palavras-chave **MUST**, **SHOULD**, **MAY**, **MUST NOT**, **SHOULD NOT** e **RECOMMENDED** são usadas em conformidade com RFC-2119 e RFC-8174 para descrever requisitos e recomendações. | The keywords **MUST**, **SHOULD**, **MAY**, **MUST NOT**, **SHOULD NOT**, and **RECOMMENDED** are to be interpreted as described in RFC-2119 and RFC-8174 to describe requirements and recommendations. |

---

## 3. Definitions

| Termo / Term       | 🇧🇷 Português                                                | 🇺🇸 English                                             |
| ------------------ | ------------------------------------------------------------- | -------------------------------------------------------- |
| **IdP**            | Identity Provider confiável (ex: Google, Gov.br, Banco).      | Trusted Identity Provider (e.g., Google, Gov.br, Bank).  |
| **Genesis**        | Evento inicial que cria identidade validada por IdP.          | Initial event creating IdP-validated identity.           |
| **S_user**         | Segredo mestre do usuário, derivado de fatores humanos.       | User master secret derived from human factors.           |
| **Identity Chain** | Cadeia encadeada de hashes de estado representando evolução.  | Hash-linked state chain representing identity evolution. |
| **state_hash**     | Hash do estado i da cadeia (biometria e histórico).           | Hash of state i in the chain (biometrics and history).   |
| **SEED_i**         | Seed dinâmico para TOTP derivado de S_user e state_hash i.    | Dynamic TOTP seed derived from S_user and state_hash i.  |
| **TOTP**           | Código temporário gerado via RFC-6238 usando SEED_i.          | Time code generated via RFC-6238 using SEED_i.           |
| **Vault**          | Criptograma que armazena S_user protegido por chave derivada. | Cryptogram storing S_user protected by derived key.      |
| **ICSH**           | Identity Chain Synchronization Handshake, protocolo de sync.  | Identity Chain Synchronization Handshake protocol.       |

---

## 4. Overview and Goals

| 🇧🇷 Português                                                                                                                                                                                                                                                                                                                                                                        | 🇺🇸 English                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| O Q-TOTP estende conceitos de TOTP clássico para:<br><br>• Resolver o problema de segredo estático<br>• Autenticar via continuidade de identidade<br>• Permitir sincronização entre dispositivos<br>• Fornecer modos escaláveis de proteção (v1, v2)<br>• Suportar recuperação e tolerância natural de uso humano<br><br>Ele é compatível com RFC-6238 para geração final de códigos. | Q-TOTP extends classical TOTP concepts to:<br><br>• Solve the static secret problem<br>• Authenticate via identity continuity<br>• Enable synchronization across devices<br>• Provide scalable protection modes (v1, v2)<br>• Support recovery and natural human usage tolerance<br><br>It remains compatible with RFC-6238 for final code generation. |

---

## 5. Architecture

| 🇧🇷 Português                           | 🇺🇸 English                        |
| ---------------------------------------- | ----------------------------------- |
| O protocolo tem três camadas principais: | The protocol has three main layers: |

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

| 🇧🇷 Português      | 🇺🇸 English |
| ------------------- | ------------ |
| Registro do usuário | User record  |

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

---

### 6.2 Genesis Block

| 🇧🇷 Português | 🇺🇸 English  |
| -------------- | ------------- |
| Bloco Genesis  | Genesis block |

```json
"genesis": {
  "idp": "string",
  "idp_subject_hash": "string",
  "verified": true,
  "verified_at": "timestamp"
}
```

---

### 6.3 Identity Chain Entry

| 🇧🇷 Português                  | 🇺🇸 English         |
| ------------------------------- | -------------------- |
| Entrada da cadeia de identidade | Identity chain entry |

```json
{
  "iteration": n,
  "timestamp": "UNIX seconds",
  "prev_hash": "string",
  "state_hash": "string",
  "bio_templates": { … }
}
```

---

### 6.4 Q-TOTP Metadata

| 🇧🇷 Português   | 🇺🇸 English    |
| ---------------- | --------------- |
| Metadados Q-TOTP | Q-TOTP metadata |

```json
"qtotp": {
  "S_user": "base64",
  "last_counter": 0
}
```

---

### 6.5 Policy

| 🇧🇷 Português | 🇺🇸 English |
| -------------- | ------------ |
| Política       | Policy       |

```json
"policy": {
  "min_score": 0.85,
  "required": ["keyboard"],
  "optional": ["voice","face","fingerprint","cognitive"]
}
```

---

## 7. Identity Chain

| 🇧🇷 Português              | 🇺🇸 English                  |
| --------------------------- | ----------------------------- |
| A cada autenticação válida: | At each valid authentication: |

```
state_hash_i =
    SHA256( biometric_vector_i || prev_state_hash )
```

| 🇧🇷 Português                               | 🇺🇸 English                                 |
| -------------------------------------------- | -------------------------------------------- |
| O primeiro estado i=1 é derivado de Genesis. | The first state i=1 is derived from Genesis. |

---

## 8. Seed and TOTP

### 8.1 SEED Derivation

```
SEED_i = HMAC( S_user , last_state_hash )
```

### 8.2 TOTP Final

| 🇧🇷 Português                                    | 🇺🇸 English                                          |
| ------------------------------------------------- | ----------------------------------------------------- |
| Usa RFC-6238 com SEED_i e timestamp (30s window). | Uses RFC-6238 with SEED_i and timestamp (30s window). |

---

## 9. Drift Biometric Update

```
new_template =
   (1 − α) * old_template +
   α * new_capture
```

| 🇧🇷 Português                                  | 🇺🇸 English                                    |
| ----------------------------------------------- | ----------------------------------------------- |
| Com α ∈ [0.05, 0.15] por defeito, configurável. | With α ∈ [0.05, 0.15] by default, configurable. |

---

## 10. Threshold Adaptation

| 🇧🇷 Português                                     | 🇺🇸 English                                    |
| -------------------------------------------------- | ----------------------------------------------- |
| Threshold dinâmico baseado em variância histórica. | Dynamic threshold based on historical variance. |

```
threshold = f(historical_variance)
```

---

## 11. API Endpoints (Complete REST Examples)

### 11.1 POST /auth/enroll

| 🇧🇷 Português | 🇺🇸 English |
| -------------- | ------------ |
| **Request**    | **Request**  |

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

| 🇧🇷 Português              | 🇺🇸 English                |
| --------------------------- | --------------------------- |
| **Response (202 Accepted)** | **Response (202 Accepted)** |

```json
{
  "status": "pending",
  "email_verification_link": "url",
  "temp_id": "string"
}
```

---

### 11.2 GET /auth/verify

| 🇧🇷 Português                                            | 🇺🇸 English                                    |
| --------------------------------------------------------- | ----------------------------------------------- |
| **Chamado quando o cliente clica no link de verificação** | **Called when client clicks verification link** |

| 🇧🇷 Português        | 🇺🇸 English          |
| --------------------- | --------------------- |
| **Response (200 OK)** | **Response (200 OK)** |

```json
{
  "status": "verified",
  "user_id": "string"
}
```

---

### 11.3 POST /auth/challenge

| 🇧🇷 Português | 🇺🇸 English |
| -------------- | ------------ |
| **Request**    | **Request**  |

```json
{
  "public_name": "string",
  "pin_hint": "string"
}
```

| 🇧🇷 Português        | 🇺🇸 English          |
| --------------------- | --------------------- |
| **Response (200 OK)** | **Response (200 OK)** |

```json
{
  "salt": "string",
  "time": 1234567890,
  "q_server": "string"
}
```

---

### 11.4 POST /auth/verify

| 🇧🇷 Português | 🇺🇸 English |
| -------------- | ------------ |
| **Request**    | **Request**  |

```json
{
  "user_id": "...",
  "F": "string",
  "otp": "string",
  "q_client": "string",
  "state_hash": "string"
}
```

| 🇧🇷 Português        | 🇺🇸 English          |
| --------------------- | --------------------- |
| **Response (200 OK)** | **Response (200 OK)** |

```json
{
  "status": "success",
  "next_state_hash": "string"
}
```

---

### 11.5 GET /qtotp/certs

| 🇧🇷 Português        | 🇺🇸 English          |
| --------------------- | --------------------- |
| **Response (200 OK)** | **Response (200 OK)** |

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

| 🇧🇷 Português | 🇺🇸 English |
| -------------- | ------------ |
| **Request**    | **Request**  |

```json
{
  "user_id": "string",
  "local_state_hash": "string",
  "local_iteration": 4
}
```

| 🇧🇷 Português        | 🇺🇸 English          |
| --------------------- | --------------------- |
| **Response (200 OK)** | **Response (200 OK)** |

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
    | verify signature           |
    | update local state         |
```

---

## 13. Identity Chain Sync (ICSH)

| 🇧🇷 Português               | 🇺🇸 English               |
| ---------------------------- | -------------------------- |
| Servidor é fonte de verdade: | Server is source of truth: |

```
server_iteration, last_state_hash
```

| 🇧🇷 Português   | 🇺🇸 English     |
| ---------------- | ---------------- |
| Cliente compara: | Client compares: |

```
local_iteration < server_iteration
```

---

## 14. Security Considerations

| 🇧🇷 Português                                                                                                                               | 🇺🇸 English                                                                                                                           |
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| • Proteção de servidor signing key (HSM, TPM)<br>• Revogação de certificados<br>• Drift limits<br>• Threshold adaptation<br>• Fork detection | • Server signing key protection (HSM, TPM)<br>• Certificate revocation<br>• Drift limits<br>• Threshold adaptation<br>• Fork detection |

---

## 15. Threat Model

| 🇧🇷 Português                                                                                             | 🇺🇸 English                                                                                                      |
| ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Considera:<br>• Vazamento de DB<br>• Roubo isolado<br>• Ataque de interpolação gradual<br>• Fork malicioso | Considers:<br>• Database leakage<br>• Isolated device theft<br>• Gradual interpolation attack<br>• Malicious fork |

---

## 16. Key Rotation

| 🇧🇷 Português                                                                                                    | 🇺🇸 English                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Servidor publica certificados via `/qtotp/certs`. Clientes devem validar assinatura com o certificado apropriado. | Server publishes certificates via `/qtotp/certs`. Clients MUST validate signatures using the appropriate certificate. |

---

## 17. Cryptographic Primitives

| 🇧🇷 Português                                                                                 | 🇺🇸 English                                                                                     |
| ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| Recomendações:<br>• SHA-256<br>• HMAC-SHA-256<br>• AES-256 GCM / ChaCha20<br>• ECDSA / Ed25519 | Recommendations:<br>• SHA-256<br>• HMAC-SHA-256<br>• AES-256-GCM / ChaCha20<br>• ECDSA / Ed25519 |

---

## 18. PoC

| 🇧🇷 Português                                                                                                    | 🇺🇸 English                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Descreve modelos de teste para calibrar:<br><br>α (drift parameter),<br>k (MAX_STATE_LAG)<br><br>com dados reais. | Describes test models to calibrate:<br><br>α (drift parameter),<br>k (MAX_STATE_LAG)<br><br>using real data. |

---

## 19. Implementation Notes

| 🇧🇷 Português                                                                                  | 🇺🇸 English                                                                                    |
| ----------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| • Identity Chain must be immutable<br>• Sync must be robust<br>• APIs must reject forked states | • Identity Chain MUST be immutable<br>• Sync MUST be robust<br>• APIs MUST reject forked states |

---

## 20. IANA Considerations

| 🇧🇷 Português     | 🇺🇸 English      |
| ------------------ | ----------------- |
| Nenhuma no momento | None at this time |

---

## 21. References

* RFC-6238 — TOTP
* RFC-4226 — HOTP
* RFC-2119 / 8174
* Shamir Secret Sharing
* Zero-Knowledge Proofs

---
