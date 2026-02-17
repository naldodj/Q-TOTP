# 📌 Q-TOTP

**Quantum-Linked Time-based One-Time Password**

Um **protocolo de autenticação moderno, robusto e escalável** que estende o TOTP tradicional (RFC-6238) ao introduzir:

✅ *Seed* dinâmico e mutável — elimina o problema do segredo estático
✅ Identidade baseada em continuidade biológica e comportamental
✅ Suporte a múltiplos dispositivos com sincronização segura
✅ Mecanismos de recuperação e tolerância à variação natural do usuário
✅ Modos de operação simples (v1) e paranoico (v2)

---

## 🚀 Visão Geral

O Q-TOTP transforma a autenticação de um evento isolado em um **processo contínuo de identidade**, combinando:

🔹 Identidade externa (IdP certificado, ex.: Google, Gov.br, Open Finance)
🔹 Biometria comportamental (como ritmo de digitação)
🔹 Dados biológicos estáveis (como aniversário e tipo sanguíneo)
🔹 PIN secreto e histórico de uso
🔹 Geração de códigos compatíveis com TOTP (RFC-6238)

Isso torna o protocolo:

✔ mais seguro que TOTP clássico
✔ resiliente a roubo de dispositivo
✔ resiliente a vazamento de base de dados estática
✔ adequado para ambientes corporativos e distribuídos

---

## 💡 Principais Funcionalidades

### 🔹 Identity Chain

Uma cadeia hash encadeada que mapeia a evolução da identidade ao longo do tempo, tornando cada autenticação dependente da anterior.

### 🔹 Seed Dinâmico

Cada código TOTP é gerado com seed calculado a partir do estado mais recente, impedindo replay de segredos antigos.

### 🔹 Modos Operacionais

| Versão            | Descrição                                                                           |
| ----------------- | ----------------------------------------------------------------------------------- |
| **v1 (básico)**   | Implementação simples com geração dinâmica de seed                                  |
| **v1-assisted**   | Recuperação assistida via revalidação externa                                       |
| **v2 (paranoic)** | Sincronização multi-dispositivo, fragmentação de segredo, Zero-Knowledge Biometrics |

---

## 📁 Estrutura do Repositório

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

## 🛠️ APIs e Exemplos

Este projeto inclui:

🔹 Endpoints REST com payloads completos
🔹 Diagramas de sequência ASCII para fluxos de handshake
🔹 Exemplos de request/response detalhados
🔹 Scripts de teste CLI para uso prático

---

## 🧪 Prova de Conceito (PoC)

Inclui planos de teste para:

🔸 Ajustar α (drift biométrico)
🔸 Calibrar k (MAX_STATE_LAG)
🔸 Simular uso em múltiplos dispositivos
🔸 Avaliar tolerância e segurança

---

## 📜 Licença

O Q-TOTP é licenciado sob a **MIT License**, garantindo:

✔ liberdade de uso
✔ uso comercial permitido
✔ permissões completas de modificação e redistribuição

---

## 📌 Como Começar

1. Leia o `rfc/RFC.md` para entender o protocolo completo
2. Explore `docs/` para APIs e diagramas
3. Utilize os exemplos em `examples/`
4. Teste com a PoC em `tests/`

---

## 🙌 Contribuições

Este é um projeto **aberto e colaborativo**!

Se você quiser:

⭐ adicionar suporte a novas linguagens
📈 melhorar o modelo de sincronização
🧬 expandir casos de uso
📘 escrever documentação complementar

…sinta-se à vontade para contribuir!

---

## 📩 Contato

Para dúvidas, sugestões ou validações, abra uma *issue* ou *pull request* no repositório.

---
