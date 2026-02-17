# 📌 Q-TOTP

**Quantum-Linked Time-based One-Time Password**

---

## 🇧🇷 / 🇺🇸

| 🇧🇷 Português                                                                                                                                                                                                                                                                                                                                                                                                                                                      | 🇺🇸 English                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Um **protocolo de autenticação moderno, robusto e escalável** que estende o TOTP tradicional (RFC-6238) ao introduzir:<br><br>✅ *Seed* dinâmico e mutável — elimina o problema do segredo estático<br>✅ Identidade baseada em continuidade biológica e comportamental<br>✅ Suporte a múltiplos dispositivos com sincronização segura<br>✅ Mecanismos de recuperação e tolerância à variação natural do usuário<br>✅ Modos de operação simples (v1) e paranoico (v2) | A **modern, robust, and scalable authentication protocol** that extends traditional TOTP (RFC-6238) by introducing:<br><br>✅ Dynamic and mutable *seed* — eliminates the static secret problem<br>✅ Identity based on biological and behavioral continuity<br>✅ Multi-device support with secure synchronization<br>✅ Recovery mechanisms and tolerance to natural user variation<br>✅ Simple (v1) and paranoid (v2) operation modes |

---

# 🚀 Visão Geral / Overview

| 🇧🇷 Português                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | 🇺🇸 English                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| O Q-TOTP transforma a autenticação de um evento isolado em um **processo contínuo de identidade**, combinando:<br><br>🔹 Identidade externa (IdP certificado, ex.: Google, Gov.br, Open Finance)<br>🔹 Biometria comportamental (como ritmo de digitação)<br>🔹 Dados biológicos estáveis (como aniversário e tipo sanguíneo)<br>🔹 PIN secreto e histórico de uso<br>🔹 Geração de códigos compatíveis com TOTP (RFC-6238)<br><br>Isso torna o protocolo:<br><br>✔ mais seguro que TOTP clássico<br>✔ resiliente a roubo de dispositivo<br>✔ resiliente a vazamento de base de dados estática<br>✔ adequado para ambientes corporativos e distribuídos | Q-TOTP transforms authentication from an isolated event into a **continuous identity process**, combining:<br><br>🔹 External identity (certified IdP, e.g., Google, Gov.br, Open Finance)<br>🔹 Behavioral biometrics (such as typing rhythm)<br>🔹 Stable biological data (such as birthday and blood type)<br>🔹 Secret PIN and usage history<br>🔹 Generation of TOTP-compatible codes (RFC-6238)<br><br>This makes the protocol:<br><br>✔ more secure than classic TOTP<br>✔ resilient to device theft<br>✔ resilient to static database leakage<br>✔ suitable for corporate and distributed environments |

---

# 💡 Principais Funcionalidades / Key Features

## 🔹 Identity Chain

| 🇧🇷 Português                                                                                                                      | 🇺🇸 English                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Uma cadeia hash encadeada que mapeia a evolução da identidade ao longo do tempo, tornando cada autenticação dependente da anterior. | A hash-linked chain that maps identity evolution over time, making each authentication dependent on the previous one. |

---

## 🔹 Seed Dinâmico / Dynamic Seed

| 🇧🇷 Português                                                                                                      | 🇺🇸 English                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Cada código TOTP é gerado com seed calculado a partir do estado mais recente, impedindo replay de segredos antigos. | Each TOTP code is generated with a seed derived from the most recent state, preventing replay of old secrets. |

---

## 🔹 Modos Operacionais / Operation Modes

| Versão            | 🇧🇷 Descrição                                                                      | 🇺🇸 Description                                                   |
| ----------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| **v1 (básico)**   | Implementação simples com geração dinâmica de seed                                  | Simple implementation with dynamic seed generation                 |
| **v1-assisted**   | Recuperação assistida via revalidação externa                                       | Assisted recovery via external revalidation                        |
| **v2 (paranoic)** | Sincronização multi-dispositivo, fragmentação de segredo, Zero-Knowledge Biometrics | Multi-device sync, secret fragmentation, Zero-Knowledge Biometrics |

---

# 📁 Estrutura do Repositório / Repository Structure

```
Q-TOTP/
├── LICENSE
├── README.md
├── rfc/
│   ├── RFC.md
├── docs/
│   ├── api-specs.md
│   ├── ascii-diagrams.md
│   └── poc-plan.md
├── examples/
│   ├── requests/
│   └── responses/
├── server/
│   ├── harbour/
│   └── nodejs/
├── client/
│   └── cli-examples/
└── tests/
```

---

# 🛠️ APIs e Exemplos / APIs and Examples

| 🇧🇷 Português                                                                                                                                                                                                             | 🇺🇸 English                                                                                                                                                                                                 |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Este projeto inclui:<br><br>🔹 Endpoints REST com payloads completos<br>🔹 Diagramas de sequência ASCII para fluxos de handshake<br>🔹 Exemplos de request/response detalhados<br>🔹 Scripts de teste CLI para uso prático | This project includes:<br><br>🔹 REST endpoints with complete payloads<br>🔹 ASCII sequence diagrams for handshake flows<br>🔹 Detailed request/response examples<br>🔹 CLI test scripts for practical usage |

---

# 🧪 Prova de Conceito (PoC) / Proof of Concept

| 🇧🇷 Português                                                                                                                                                                        | 🇺🇸 English                                                                                                                                                         |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Inclui planos de teste para:<br><br>🔸 Ajustar α (drift biométrico)<br>🔸 Calibrar k (MAX_STATE_LAG)<br>🔸 Simular uso em múltiplos dispositivos<br>🔸 Avaliar tolerância e segurança | Includes test plans to:<br><br>🔸 Tune α (biometric drift)<br>🔸 Calibrate k (MAX_STATE_LAG)<br>🔸 Simulate multi-device usage<br>🔸 Evaluate tolerance and security |

---

# 📜 Licença / License

| 🇧🇷 Português                                                                                                                                                            | 🇺🇸 English                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| O Q-TOTP é licenciado sob a **MIT License**, garantindo:<br><br>✔ liberdade de uso<br>✔ uso comercial permitido<br>✔ permissões completas de modificação e redistribuição | Q-TOTP is licensed under the **MIT License**, ensuring:<br><br>✔ freedom of use<br>✔ commercial use allowed<br>✔ full modification and redistribution permissions |

---

# 📌 Como Começar / Getting Started

| 🇧🇷 Português                                                                                                                                                                   | 🇺🇸 English                                                                                                                                                               |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1. Leia o `rfc/RFC.md` para entender o protocolo completo<br>2. Explore `docs/` para APIs e diagramas<br>3. Utilize os exemplos em `examples/`<br>4. Teste com a PoC em `tests/` | 1. Read `rfc/RFC.md` to understand the full protocol<br>2. Explore `docs/` for APIs and diagrams<br>3. Use the examples in `examples/`<br>4. Test with the PoC in `tests/` |

---

# 🙌 Contribuições / Contributions

| 🇧🇷 Português                                                                                                                                                                                                                                                              | 🇺🇸 English                                                                                                                                                                                                                                         |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Este é um projeto **aberto e colaborativo**!<br><br>Se você quiser:<br><br>⭐ adicionar suporte a novas linguagens<br>📈 melhorar o modelo de sincronização<br>🧬 expandir casos de uso<br>📘 escrever documentação complementar<br><br>…sinta-se à vontade para contribuir! | This is an **open and collaborative** project!<br><br>If you want to:<br><br>⭐ add support for new languages<br>📈 improve the synchronization model<br>🧬 expand use cases<br>📘 write complementary documentation<br><br>…feel free to contribute! |

---

# 📩 Contato / Contact

| 🇧🇷 Português                                                                            | 🇺🇸 English                                                                                     |
| ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| Para dúvidas, sugestões ou validações, abra uma *issue* ou *pull request* no repositório. | For questions, suggestions, or validations, open an *issue* or *pull request* in the repository. |

---
