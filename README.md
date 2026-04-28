# 🤖 AIAD — Agente Inteligente de Análise Documental

![Status](https://img.shields.io/badge/Status-MVP%20Ágil-orange)
![Stack](https://img.shields.io/badge/Stack-Python%20|%20Node.js%20|%20LLM-blue)
![Segurança](https://img.shields.io/badge/Segurança-JWT%20|%20LGPD%20|%20AES--256-green)
![Metodologia](https://img.shields.io/badge/Metodologia-Manifesto%20Ágil-purple)
![Licença](https://img.shields.io/badge/Licença-MIT-lightgrey)

> Agente inteligente de análise documental com separação estrita de pipelines de **Compliance/LGPD** e **Antifraude**, trilha de auditoria imutável, token JWT em todas as telas e score de risco 0–100.

---

## 📌 Visão geral

O AIAD automatiza a triagem documental para times de **Jurídico, Compliance e Segurança**, eliminando gargalos manuais e mitigando riscos de fraude em contratos, documentos KYC e assinaturas digitais.

### Diferenciais em relação ao CONFER e BRFLOW

| Funcionalidade | **AIAD** |
|---|---|---|---|
| Separação compliance × fraude | ❌ | Parcial | ✅ Pipelines independentes |
| Detecção de autofraude | ❌ | ❌ | ✅ Módulo dedicado |
| Token de segurança por tela | ❌ | ❌ | ✅ JWT RS256 em todas as rotas |
| Auditoria interna automatizada | ❌ | Parcial | ✅ Trilha imutável + IA |
| Score de risco 0–100 | Parcial | ❌ | ✅ Motor com explicabilidade |
| Conformidade LGPD nativa | Parcial | ❌ | ✅ Análise semântica por artigo |
| Análise de assinatura digital | ❌ | ❌ | ✅ ICP-Brasil + biometria |
| Relatório PDF auditável | Parcial | ✅ | ✅ Assinado + hash na trilha |

---

## 🎯 Objetivos

- Automatizar análises repetitivas de contratos e documentos
- Separar claramente compliance LGPD da detecção de fraude (pipelines independentes)
- Detectar inconsistências cadastrais, tentativas de fraude e autofraude
- Suportar auditoria interna com trilha de evidências rastreável e imutável
- Proteger dados em todas as camadas (JWT, AES-256, logs imutáveis)
- Gerar relatórios com score de risco, achados e recomendações

---

## 🏗️ Arquitetura

```
┌─────────────────────────────────────────────────────────┐
│                   CAMADA DE APRESENTAÇÃO                │
│        React SPA  ←→  Token JWT em cada requisição      │
└──────────────────────────┬──────────────────────────────┘
                           │ HTTPS / TLS 1.3
┌──────────────────────────▼──────────────────────────────┐
│                  API GATEWAY (Node.js)                  │
│   Rate limiting · Autenticação JWT · Logging de acesso  │
└───┬──────────────────────┬──────────────────────────────┘
    │                      │
┌───▼────────┐   ┌─────────▼───────────────────────────────┐
│   Auth     │   │        ORQUESTRADOR (Python/FastAPI)     │
│  Service   │   │   Fila de tarefas · Roteamento IA        │
└────────────┘   └──┬──────────┬──────────┬────────────────┘
                    │          │          │
          ┌─────────▼──┐ ┌─────▼────┐ ┌──▼──────────┐
          │  Módulo    │ │  Módulo  │ │   Módulo    │
          │ Compliance │ │  Fraude  │ │  Assinatura │
          │  (LGPD)    │ │ + Auto   │ │  Digital    │
          └─────────┬──┘ └─────┬────┘ └──┬──────────┘
                    └──────────┼──────────┘
                    ┌──────────▼──────────┐
                    │    SCORE ENGINE     │
                    │   (0–100 · Risco)   │
                    └──────────┬──────────┘
                    ┌──────────▼──────────┐
                    │  DB + Trilha Audit. │
                    │  PostgreSQL + Redis │
                    └─────────────────────┘
```

```mermaid
graph TD
    A[Upload do Documento] --> B[API Gateway + JWT]
    B --> C[Orquestrador / Fila]
    C --> D1[Módulo Compliance LGPD]
    C --> D2[Módulo Antifraude + Autofraude]
    C --> D3[Módulo Assinatura Digital]
    D1 & D2 & D3 --> E[Score Engine 0-100]
    E --> F[DB + Trilha de Auditoria]
    F --> G[Dashboard do Analista]
    G --> H[Relatório PDF Auditável]
```

---

## 🚀 Funcionalidades por etapa

### Etapa 1 — Autenticação
- JWT RS256 obrigatório em todas as telas e rotas
- Roles: `ANALISTA_FRAUDE`, `AUDITOR`, `GESTOR_COMPLIANCE`, `ADMIN`
- Bloqueio após 3 tentativas falhas com notificação ao ADMIN
- Invalidação de token no logout via blocklist Redis

### Etapa 2 — Ingestão de Documentos
- Formatos: PDF, DOCX, PNG, JPG, TIFF (até 50 MB)
- OCR automático + extração de metadados
- Hash SHA-256 gravado antes de qualquer processamento
- Detecção de adulteração EXIF

### Etapa 3 — Compliance LGPD (pipeline independente)
- Checklist por artigo da LGPD (Art. 5º ao Art. 48º)
- Detecção de cláusulas abusivas via NLP + SBERT
- Mascaramento de dados pessoais sensíveis
- Score de compliance 0–100 com nível: `CONFORME` / `PENDENTE` / `NÃO CONFORME`

### Etapa 4 — Antifraude (pipeline independente)
- Cruzamento cadastral entre documentos do processo
- Detecção de **autofraude** (beneficiário = solicitante ou partes relacionadas)
- Verificação de assinatura ICP-Brasil + biometria comportamental
- Score de fraude 0–100 com explicabilidade dos fatores

### Etapa 5 — Score de Risco
- Score composto (compliance 40% + fraude 60%)
- Classificação: `BAIXO` · `MÉDIO` · `ALTO` · `CRÍTICO`
- Histórico de variação por processo

### Etapa 6 — Auditoria Interna
- Trilha imutável com hash SHA-256 encadeado
- Assistente IA para perguntas em linguagem natural
- Exportação de relatório PDF assinado digitalmente
- Alertas automáticos para scores ALTO e CRÍTICO (SLA ≤ 2 min)

---

## 📁 Estrutura do repositório

```
aiad/
├── README.md
├── docs/
│   ├── requisitos/
│   │   ├── requisitos-funcionais.md
│   │   └── requisitos-nao-funcionais.md
│   ├── diagramas/
│   │   ├── casos-de-uso.md
│   │   ├── diagrama-estados.md
│   │   └── diagrama-sequencia.md
│   └── telas/
│       └── telas-descricao.md
├── stories/
│   └── user-stories.md
└── infra/
    └── docker-compose.yml
```

---

## 🔐 Segurança

| Camada | Mecanismo |
|---|---|
| Autenticação | JWT RS256, expiração 8h, refresh token |
| Transporte | TLS 1.3, HSTS |
| Armazenamento | AES-256 para documentos em repouso |
| Logs | Imutáveis, hash SHA-256 encadeado |
| Dados pessoais | Pseudoanonimização conforme LGPD |
| API | Rate limiting, CORS restrito, Helmet.js |
| Auditoria | Trilha completa, sem possibilidade de deleção |

---

## 🌀 Manifesto Ágil — aplicação no projeto

O AIAD segue os **4 valores** e os **12 princípios** do Manifesto Ágil:

1. **Indivíduos e interações** acima de processos → cerimônias de sprint com analistas antifraude como stakeholders diretos
2. **Software funcionando** acima de documentação → entregas incrementais a cada sprint de 2 semanas
3. **Colaboração com o cliente** → analistas e auditores participam do refinamento de backlog
4. **Responder a mudanças** → arquitetura modular permite adicionar novos detectores sem refatoração

---

## 🛠️ Stack tecnológica

| Componente | Tecnologia |
|---|---|
| Frontend | React 18 + TypeScript + TailwindCSS |
| API Gateway | Node.js + Express + JWT |
| Orquestrador | Python 3.11 + FastAPI + Celery |
| NLP / LLM | spaCy (NER) + SBERT + LLM API |
| OCR | Tesseract + PDF.js |
| Banco de dados | PostgreSQL 15 + Redis |
| Infraestrutura | Docker + Kubernetes |
| CI/CD | GitHub Actions |

---

## 👥 Papéis (Scrum)

| Papel | Responsabilidade |
|---|---|
| Product Owner | Analista sênior antifraude / gestor de compliance |
| Scrum Master | Facilitar sprints, remover impedimentos |
| Dev Team | Backend, Frontend, IA, DevOps |

---

## 📜 Licença

MIT License — veja `LICENSE` para detalhes.

# 📖 User Stories — AIAD

> **Formato:** `Como [papel], quero [funcionalidade], para [valor de negócio]`
> **Metodologia:** Manifesto Ágil · Estimativas em Story Points (Fibonacci)
> **Velocidade estimada:** 30 story points por sprint · Sprints de 2 semanas

---

## 🔐 Épico 1 — Autenticação e segurança de acesso

---

### US-001 — Login com token de segurança

**Como** analista antifraude,
**quero** fazer login com meu e-mail corporativo e senha e receber um token JWT,
**para** que apenas usuários autorizados acessem o sistema e todas as minhas ações sejam rastreáveis.

**Critérios de aceitação:**

- [ ] Formulário de login com campo de e-mail e campo de senha com máscara e botão de visibilidade
- [ ] Token JWT RS256 gerado com claims: `sub`, `role`, `exp`, `iat`
- [ ] Token armazenado em memória — nunca em localStorage ou cookie sem flag HTTPOnly
- [ ] Mensagem de erro genérica em caso de credenciais inválidas (sem revelar qual campo está errado)
- [ ] Redirecionamento para dashboard após login bem-sucedido
- [ ] Login registrado na trilha de auditoria com IP e timestamp

**Story Points:** 5 | **Sprint:** 1 | **Rastreabilidade:** RF-01, RNF-01

---

### US-002 — Controle de acesso por role (RBAC)

**Como** administrador do sistema,
**quero** que cada role tenha acesso somente às telas e recursos autorizados,
**para** garantir o princípio do menor privilégio e proteger dados sensíveis.

**Critérios de aceitação:**

- [ ] `ANALISTA_FRAUDE`: acesso a upload, análise fraude, assinatura, score, histórico
- [ ] `GESTOR_COMPLIANCE`: acesso a compliance, contratos, score, relatórios, painel executivo
- [ ] `AUDITOR`: acesso a auditoria, trilha, relatórios, assistente IA
- [ ] `ADMIN`: acesso total + gestão de usuários + configurações de segurança + logs + status do sistema
- [ ] Tentativa de acesso não autorizado retorna tela 18 (Acesso negado) e registra evento na trilha
- [ ] Bloqueio automático após 3 falhas de login com notificação ao ADMIN por e-mail

**Story Points:** 8 | **Sprint:** 1 | **Rastreabilidade:** RF-01, RNF-01

---

## 📄 Épico 2 — Ingestão de documentos

---

### US-003 — Upload de documentos com validação e hash

**Como** analista antifraude,
**quero** fazer upload de documentos nos formatos PDF, DOCX e imagem com validação automática,
**para** iniciar o processo de análise com garantia de integridade do documento original.

**Critérios de aceitação:**

- [ ] Área de drag-and-drop com feedback visual (borda colorida ao arrastar)
- [ ] Validação de formato (PDF, DOCX, PNG, JPG, TIFF) e tamanho (≤ 50 MB) antes do envio
- [ ] Barra de progresso individual por arquivo durante o upload
- [ ] Hash SHA-256 calculado no servidor e exibido ao analista antes de qualquer processamento
- [ ] Mensagem de erro clara para formatos não suportados ou arquivos corrompidos
- [ ] Suporte a múltiplos documentos por processo

**Story Points:** 5 | **Sprint:** 1 | **Rastreabilidade:** RF-02

---

### US-004 — OCR e extração de metadados

**Como** sistema AIAD,
**quero** executar OCR automaticamente e extrair metadados de todos os documentos,
**para** tornar o conteúdo de documentos escaneados analisável e detectar possíveis adulterações.

**Critérios de aceitação:**

- [ ] OCR executado para PDFs com imagens e arquivos de imagem (Tesseract)
- [ ] Texto extraído com percentual de confiança de reconhecimento reportado
- [ ] Metadados extraídos: autor, data de criação, data de modificação, software utilizado
- [ ] Alerta quando data de modificação é posterior à data declarada no documento
- [ ] Detecção de inconsistências em metadados EXIF (ex.: câmera, localização, timestamps)
- [ ] Resultado do OCR armazenado vinculado ao hash SHA-256 do documento original

**Story Points:** 8 | **Sprint:** 2 | **Rastreabilidade:** RF-02

---

## ✅ Épico 3 — Análise de compliance (etapa LGPD)

---

### US-005 — Checklist LGPD automático por artigo

**Como** gestor de compliance,
**quero** que o sistema verifique automaticamente a conformidade do documento com a LGPD artigo por artigo,
**para** identificar riscos legais antes da formalização do contrato sem depender de revisão manual completa.

**Critérios de aceitação:**

- [ ] Checklist com itens por artigo: base legal (Art. 7º), consentimento, finalidade, retenção (Art. 15º), transferência (Art. 33º), direitos do titular (Art. 9º), DPO (Art. 41º), incidentes (Art. 48º)
- [ ] Cada item com status: `CONFORME` / `PENDENTE` / `NÃO CONFORME` e artigo referenciado
- [ ] Descrição do achado e sugestão de adequação para itens não conformes
- [ ] Score de compliance gerado ao final com nível geral
- [ ] Tela de compliance com paleta azul/verde, visualmente distinta da tela de fraude
- [ ] Banner explícito informando que este pipeline é independente do antifraude

**Story Points:** 13 | **Sprint:** 2 | **Rastreabilidade:** RF-03, RNF-05

---

### US-006 — Detecção e mascaramento de dados pessoais sensíveis

**Como** gestor de compliance,
**quero** que o sistema identifique dados pessoais sensíveis não protegidos no documento,
**para** evitar exposição indevida e possíveis sanções da ANPD.

**Critérios de aceitação:**

- [ ] Detecção de: CPF, RG, data de nascimento, dados de saúde, dados biométricos, e-mail, telefone
- [ ] Dados identificados destacados com localização exata (página e linha)
- [ ] Opção de mascaramento direto na interface com confirmação obrigatória
- [ ] Ação de mascaramento registrada na trilha de auditoria com usuário e timestamp
- [ ] Visualização de dados sensíveis sem mascaramento requer confirmação e é registrada na trilha

**Story Points:** 8 | **Sprint:** 2 | **Rastreabilidade:** RF-03, RNF-03

---

### US-007 — Formalização e análise semântica de contratos

**Como** analista jurídico,
**quero** que o sistema extraia automaticamente as cláusulas principais e identifique cláusulas atípicas,
**para** acelerar a revisão formal e detectar condições abusivas sem leitura integral manual.

**Critérios de aceitação:**

- [ ] Extração automática: partes contratantes, CNPJ/CPF, vigência, valor, objeto, obrigações
- [ ] Alerta para cláusulas com similaridade semântica a modelos conhecidos de cláusulas abusivas (SBERT)
- [ ] Comparação com templates pré-aprovados da organização (quando configurados)
- [ ] Score de conformidade contratual separado e independente do score de fraude
- [ ] Relatório de compliance gerado com todos os achados organizados por categoria

**Story Points:** 13 | **Sprint:** 3 | **Rastreabilidade:** RF-03

---

## 🚨 Épico 4 — Análise antifraude (etapa fraude)

---

### US-008 — Detecção de inconsistências cadastrais

**Como** analista antifraude,
**quero** que o sistema identifique inconsistências entre os dados cadastrais dos documentos do processo,
**para** detectar tentativas de fraude por dados conflitantes sem precisar comparar manualmente.

**Critérios de aceitação:**

- [ ] Cruzamento automático de CPF, CNPJ, nome, endereço, data de nascimento entre todos os documentos
- [ ] Alerta quando o mesmo CPF aparece com nomes ou razões sociais diferentes
- [ ] Alerta quando datas de nascimento conflitam entre documentos
- [ ] Nível de severidade por inconsistência: `LEVE` / `MODERADA` / `GRAVE`
- [ ] Exibição do trecho exato de cada documento onde a inconsistência foi encontrada
- [ ] Tela de antifraude com paleta laranja/vermelho, banner de separação de pipeline

**Story Points:** 13 | **Sprint:** 3 | **Rastreabilidade:** RF-04, RNF-05

---

### US-009 — Detecção de autofraude

**Como** analista antifraude,
**quero** que o sistema identifique quando o beneficiário é o próprio solicitante ou partes relacionadas disfarçadas,
**para** prevenir prejuízos financeiros por autofraude antes da aprovação do contrato.

**Critérios de aceitação:**

- [ ] Cruzamento de CPF/CNPJ entre solicitante, beneficiário e partes relacionadas
- [ ] Identificação de vínculos societários ou familiares que configurem autofraude (consulta Receita Federal)
- [ ] Alerta específico `AUTOFRAUDE_SUSPEITA` com evidências do vínculo identificado
- [ ] Flag `AUTOFRAUDE` no score e no relatório final com explicação do vínculo
- [ ] Botões de confirmação e descarte da suspeita com registro obrigatório na trilha

**Story Points:** 13 | **Sprint:** 3 | **Rastreabilidade:** RF-04

---

### US-010 — Análise de assinatura digital

**Como** analista antifraude,
**quero** que o sistema verifique a autenticidade criptográfica e comportamental das assinaturas,
**para** detectar assinaturas falsificadas ou realizadas por terceiros não autorizados.

**Critérios de aceitação:**

- [ ] Verificação criptográfica de assinatura digital ICP-Brasil (cadeia de confiança completa)
- [ ] Comparação do CPF no certificado com o CPF declarado no contrato
- [ ] Comparação comportamental do traço com assinaturas históricas do mesmo titular
- [ ] Percentual de similaridade exibido com barra visual e limiar configurável (padrão 70%)
- [ ] Status da assinatura: `VÁLIDA` / `INVÁLIDA` / `SUSPEITA` / `NÃO ENCONTRADA`
- [ ] Contribuição da análise em pontos ao score de fraude exibida com justificativa

**Story Points:** 13 | **Sprint:** 4 | **Rastreabilidade:** RF-04

---

### US-011 — Score de risco com explicabilidade

**Como** analista antifraude,
**quero** receber um score de risco 0–100 com a lista detalhada dos fatores que o compõem,
**para** tomar e justificar decisões de aprovação ou reprovação com embasamento objetivo.

**Critérios de aceitação:**

- [ ] Score 0–100 exibido com gauge visual e classificação: `BAIXO` / `MÉDIO` / `ALTO` / `CRÍTICO`
- [ ] Lista de fatores com peso percentual de cada um sobre o score final
- [ ] Score de compliance e score de fraude exibidos separadamente e como score composto (40%/60%)
- [ ] Histórico de variação do score ao longo das análises do mesmo processo
- [ ] Recomendações automáticas de ação baseadas nos achados e na classificação

**Story Points:** 8 | **Sprint:** 4 | **Rastreabilidade:** RF-04

---

## 🔍 Épico 5 — Auditoria interna

---

### US-012 — Trilha de auditoria imutável com hash encadeado

**Como** auditor interno,
**quero** acessar uma trilha completa e imutável de todas as ações realizadas no sistema,
**para** garantir rastreabilidade total e provar conformidade em processos de investigação.

**Critérios de aceitação:**

- [ ] Cada evento registrado com: tipo, usuário, role, timestamp UTC, IP, resultado e hash SHA-256 do estado anterior
- [ ] Hash encadeado entre eventos consecutivos (integridade verificável)
- [ ] Indicador visual de integridade da cadeia (íntegra / violação detectada)
- [ ] Registros não podem ser editados ou excluídos por nenhum role, incluindo ADMIN
- [ ] Filtros por: usuário, período, tipo de evento, score de risco, processo, role
- [ ] Exportação da trilha em JSON e CSV

**Story Points:** 13 | **Sprint:** 4 | **Rastreabilidade:** RF-05, RNF-01

---

### US-013 — Assistente IA para auditoria

**Como** auditor interno,
**quero** fazer perguntas em linguagem natural sobre os achados de um processo,
**para** obter insights rapidamente sem precisar navegar manualmente por todos os relatórios.

**Critérios de aceitação:**

- [ ] Interface de chat contextualizada ao processo ou período filtrado
- [ ] Respostas estruturadas que citam o número do evento na trilha como referência
- [ ] Perguntas como "Quais inconsistências foram encontradas no processo 1042?" geram respostas precisas
- [ ] Histórico da conversa de auditoria salvo na própria trilha de auditoria
- [ ] Assistente não acessa processos de outros auditores (isolamento por sessão e role)

**Story Points:** 13 | **Sprint:** 5 | **Rastreabilidade:** RF-05

---

### US-014 — Geração de relatório de auditoria em PDF assinado

**Como** auditor interno,
**quero** exportar o relatório de auditoria em PDF assinado digitalmente pelo sistema,
**para** apresentar evidências formais e auditáveis em processos de compliance e investigações jurídicas.

**Critérios de aceitação:**

- [ ] Relatório inclui: capa, sumário executivo, score composto, achados por etapa, evidências, trilha resumida
- [ ] PDF assinado digitalmente pelo sistema com carimbo de tempo
- [ ] Hash SHA-256 do PDF registrado na trilha de auditoria no momento da geração
- [ ] Exportação também disponível em JSON estruturado com schema documentado
- [ ] Download disponível apenas para roles `AUDITOR`, `GESTOR_COMPLIANCE` e `ADMIN`

**Story Points:** 8 | **Sprint:** 5 | **Rastreabilidade:** RF-05

---

### US-015 — Alertas automáticos para scores críticos

**Como** gestor de compliance,
**quero** receber alertas automáticos por e-mail e notificação in-app quando um processo atinge score ALTO ou CRÍTICO,
**para** agir imediatamente e mitigar riscos antes que causem prejuízo financeiro ou legal.

**Critérios de aceitação:**

- [ ] Alertas enviados por e-mail e notificação in-app para scores `ALTO` (≥ 70) e `CRÍTICO` (≥ 85)
- [ ] Alerta inclui: ID do processo, score, classificação, tipo de ocorrência e link direto no AIAD
- [ ] SLA de envio: ≤ 2 minutos após conclusão da análise
- [ ] Destinatários, limiar de score e canais configuráveis por role e pelo ADMIN
- [ ] Registro do envio do alerta (com timestamp e destinatários) na trilha de auditoria

**Story Points:** 5 | **Sprint:** 5 | **Rastreabilidade:** RF-05

---

## 📊 Sumário do backlog

| ID | História | Épico | Pontos | Sprint |
|---|---|---|---|---|
| US-001 | Login com token JWT | Autenticação | 5 | 1 |
| US-002 | Controle de acesso por role | Autenticação | 8 | 1 |
| US-003 | Upload com validação e hash | Ingestão | 5 | 1 |
| US-004 | OCR e extração de metadados | Ingestão | 8 | 2 |
| US-005 | Checklist LGPD por artigo | Compliance | 13 | 2 |
| US-006 | Dados pessoais e mascaramento | Compliance | 8 | 2 |
| US-007 | Formalização e semântica | Compliance | 13 | 3 |
| US-008 | Inconsistências cadastrais | Antifraude | 13 | 3 |
| US-009 | Detecção de autofraude | Antifraude | 13 | 3 |
| US-010 | Análise de assinatura digital | Antifraude | 13 | 4 |
| US-011 | Score com explicabilidade | Antifraude | 8 | 4 |
| US-012 | Trilha de auditoria imutável | Auditoria | 13 | 4 |
| US-013 | Assistente IA para auditoria | Auditoria | 13 | 5 |
| US-014 | Relatório PDF assinado | Auditoria | 8 | 5 |
| US-015 | Alertas automáticos de score | Auditoria | 5 | 5 |
| **Total** | | | **147 pts** | **5 sprints** |

> **Duração total do MVP:** ~10 semanas (5 sprints × 2 semanas)
> **Velocidade estimada:** 30 story points por sprint
>
> # 📋 Requisitos Funcionais — AIAD

> Cinco requisitos funcionais com critérios de aceitação verificáveis e rastreabilidade para as histórias de usuário.

---

## RF-01 — Autenticação e controle de acesso por token

**Descrição:** O sistema deve autenticar todos os usuários via JWT (RS256) antes de permitir acesso a qualquer tela ou funcionalidade. O token deve ser validado em cada requisição, incluindo navegação entre telas.

**Critérios de aceitação:**

- [ ] Login com e-mail e senha gera JWT com claims: `sub`, `role`, `exp`, `iat`
- [ ] Token ausente ou inválido retorna HTTP 401 e redireciona ao login
- [ ] Expiração padrão de 8 horas, configurável por role pelo ADMIN
- [ ] Após 3 falhas consecutivas de login, conta é bloqueada e ADMIN é notificado por e-mail
- [ ] Logout invalida o token no servidor via blocklist Redis com TTL
- [ ] Roles suportadas: `ANALISTA_FRAUDE`, `AUDITOR`, `GESTOR_COMPLIANCE`, `ADMIN`
- [ ] Cada role acessa somente as telas e recursos autorizados (RBAC)
- [ ] Tentativas de acesso não autorizado são registradas na trilha de auditoria

**Rastreabilidade:** US-001, US-002

---

## RF-02 — Ingestão e pré-processamento de documentos

**Descrição:** O sistema deve aceitar documentos nos formatos PDF, DOCX, PNG, JPG e TIFF, realizar OCR quando necessário, extrair metadados e registrar o hash SHA-256 do arquivo original antes de qualquer análise.

**Critérios de aceitação:**

- [ ] Upload via interface web ou API REST (multipart/form-data)
- [ ] Limite de tamanho configurável, padrão 50 MB por arquivo
- [ ] OCR executado automaticamente para imagens e PDFs escaneados
- [ ] Hash SHA-256 calculado e gravado no banco antes de qualquer processamento
- [ ] Detecção de adulteração de metadados EXIF e datas de criação inconsistentes
- [ ] Extração de texto estruturado com localização de campos via NER
- [ ] Suporte a múltiplos documentos por processo (lote)
- [ ] Barra de progresso de upload visível ao usuário em tempo real

**Rastreabilidade:** US-003, US-004

---

## RF-03 — Análise de compliance e formalização de contratos (etapa compliance)

**Descrição:** O sistema deve executar, em etapa e pipeline separados da análise de fraude, a verificação de conformidade com a LGPD e a formalização de contratos, identificando cláusulas abusivas, dados pessoais sensíveis expostos e bases legais ausentes.

**Critérios de aceitação:**

- [ ] Checklist LGPD automático por artigo: base legal, consentimento, finalidade, retenção, transferência
- [ ] Cada item com status: `CONFORME` / `PENDENTE` / `NÃO CONFORME` e artigo referenciado
- [ ] Detecção de cláusulas abusivas via NLP com similaridade semântica (SBERT)
- [ ] Identificação e mascaramento opcional de dados pessoais sensíveis (CPF, RG, dados de saúde)
- [ ] Extração de partes contratantes, vigência, valores e obrigações
- [ ] Score de compliance 0–100 gerado ao final com nível geral
- [ ] Sugestões automáticas de adequação para itens não conformes
- [ ] A etapa de compliance **não executa simultaneamente** com a etapa de fraude — pipelines independentes com resultados distintos
- [ ] Tela de compliance com identidade visual diferenciada (paleta azul/verde)

**Rastreabilidade:** US-005, US-006, US-007

---

## RF-04 — Detecção de fraude, inconsistências e autofraude (etapa antifraude)

**Descrição:** O sistema deve executar, em etapa e pipeline separados da análise de compliance, a detecção de inconsistências cadastrais, tentativas de fraude documental e autofraude, com geração de evidências e score de risco.

**Critérios de aceitação:**

- [ ] Cruzamento automático de dados cadastrais entre todos os documentos do processo
- [ ] Alerta quando CPF/CNPJ aparece com nomes ou razões sociais diferentes entre documentos
- [ ] Detecção de **autofraude**: beneficiário é o mesmo CPF do solicitante ou partes societariamente relacionadas
- [ ] Análise de assinatura digital: verificação criptográfica ICP-Brasil e comparação comportamental de traço
- [ ] Detecção de documentos adulterados (manipulação de imagem, alteração de texto, metadados inconsistentes)
- [ ] Comparação com base histórica de padrões de fraude
- [ ] Score de fraude 0–100 com classificação `BAIXO` / `MÉDIO` / `ALTO` / `CRÍTICO`
- [ ] Explicabilidade do score: lista dos fatores e peso percentual de cada um
- [ ] A etapa de fraude **não executa simultaneamente** com a etapa de compliance — pipelines independentes
- [ ] Tela de antifraude com identidade visual diferenciada (paleta laranja/vermelho)

**Rastreabilidade:** US-008, US-009, US-010, US-011

---

## RF-05 — Auditoria interna e geração de relatórios

**Descrição:** O sistema deve manter uma trilha de auditoria imutável de todas as ações, permitir que auditores consultem evidências, exportem relatórios estruturados e utilizem assistente IA para perguntas sobre os achados.

**Critérios de aceitação:**

- [ ] Toda ação registrada na trilha com: timestamp UTC, usuário, role, tipo de evento, resultado e hash encadeado do evento anterior
- [ ] Trilha imutável: registros não podem ser editados ou excluídos por nenhum role, incluindo ADMIN
- [ ] Hash SHA-256 encadeado entre eventos (garantia de integridade da cadeia)
- [ ] Exportação de relatório em PDF (assinado digitalmente pelo sistema) e JSON
- [ ] Hash do PDF registrado na trilha no momento da geração
- [ ] Assistente IA responde perguntas em linguagem natural sobre os achados do processo, citando o número do evento na trilha
- [ ] Filtros de auditoria por: usuário, período, tipo de evento, score de risco, processo
- [ ] Dashboard de auditoria exclusivo para roles `AUDITOR` e `ADMIN`
- [ ] Alertas automáticos para scores `ALTO` e `CRÍTICO` enviados por e-mail em até 2 minutos após conclusão da análise

**Rastreabilidade:** US-012, US-013, US-014, US-015
# 📐 Diagramas UML — AIAD

> Todos os diagramas utilizam sintaxe Mermaid e renderizam automaticamente no GitHub.

---

## 1. Diagrama de Casos de Uso

```
Atores:
  - Analista Antifraude
  - Auditor Interno
  - Gestor de Compliance
  - Admin

Casos de uso por módulo:
  [Autenticação]
    UC-01: Autenticar com token JWT

  [Compliance — pipeline exclusivo]
    UC-02: Analisar compliance LGPD
    UC-03: Formalizar contrato
    UC-04: Detectar dados pessoais expostos

  [Antifraude — pipeline exclusivo]
    UC-05: Detectar inconsistências cadastrais
    UC-06: Detectar tentativa de fraude
    UC-07: Analisar autofraude
    UC-08: Analisar assinatura digital

  [Documentos]
    UC-09: Processar documento (OCR + hash)
    UC-10: Comparar documentos

  [Auditoria]
    UC-11: Conduzir auditoria interna
    UC-12: Usar assistente IA de auditoria
    UC-13: Gerar relatório de risco
    UC-14: Exportar relatório de auditoria

  [Gestão]
    UC-15: Gerenciar usuários e roles

Associações:
  Analista Antifraude → UC-01, UC-05, UC-06, UC-07, UC-08, UC-09, UC-10, UC-13
  Gestor Compliance   → UC-01, UC-02, UC-03, UC-04, UC-09, UC-13, UC-14
  Auditor Interno     → UC-01, UC-11, UC-12, UC-13, UC-14
  Admin               → todos os UCs + UC-15

Relações de extensão e inclusão:
  UC-02 <<include>> UC-09
  UC-05 <<include>> UC-09
  UC-11 <<extend>>  UC-13
  UC-10 <<extend>>  UC-05
```

---

## 2. Diagrama de Estados

```mermaid
stateDiagram-v2
    [*] --> AguardandoAutenticacao

    AguardandoAutenticacao --> TokenInvalido : credenciais incorretas
    TokenInvalido --> AguardandoAutenticacao : nova tentativa
    TokenInvalido --> ContaBloqueada : 3 falhas consecutivas
    ContaBloqueada --> [*] : ADMIN notificado

    AguardandoAutenticacao --> SessaoAutenticada : token JWT válido emitido

    SessaoAutenticada --> ProcessandoDocumento : upload realizado + hash SHA-256 registrado

    ProcessandoDocumento --> AnaliseCompliance : tipo = compliance (pipeline 1)
    ProcessandoDocumento --> AnaliseAntifraude : tipo = fraude (pipeline 2)
    ProcessandoDocumento --> AnaliseCompliance : tipo = ambos → pipeline 1 paralelo
    ProcessandoDocumento --> AnaliseAntifraude : tipo = ambos → pipeline 2 paralelo

    AnaliseCompliance --> AnaliseCompliance : OCR → NER → Checklist LGPD → Semântica
    AnaliseCompliance --> ScoreCalculado : score compliance gerado

    AnaliseAntifraude --> AnaliseAssinatura : documento contém assinatura
    AnaliseAssinatura --> AnaliseAntifraude : resultado assinatura incorporado
    AnaliseAntifraude --> ScoreCalculado : score fraude gerado

    ScoreCalculado --> AuditoriaEmAndamento : trilha iniciada + alertas disparados

    AuditoriaEmAndamento --> DecisaoDoAnalista : analista revisa evidências
    DecisaoDoAnalista --> ProcessoReprovado : fraude confirmada
    DecisaoDoAnalista --> ProcessoAprovado : sem indício de fraude
    DecisaoDoAnalista --> ProcessoPendente : necessita revisão adicional

    ProcessoReprovado --> RelatorioGerado : PDF assinado + hash na trilha
    ProcessoAprovado --> RelatorioGerado : PDF assinado + hash na trilha
    ProcessoPendente --> AuditoriaEmAndamento : retorna para revisão

    RelatorioGerado --> ProcessoEncerrado : encerramento confirmado
    ProcessoEncerrado --> [*]
```

---

## 3. Diagrama de Sequência

```mermaid
sequenceDiagram
    actor Analista
    participant GW as API Gateway
    participant Auth as Auth Service
    participant Orch as Orquestrador
    participant Comp as Módulo Compliance
    participant Fraud as Módulo Antifraude
    participant Sig as Módulo Assinatura
    participant Score as Score Engine
    participant DB as PostgreSQL + Redis
    participant Audit as Trilha de Auditoria

    Analista->>GW: POST /auth/login {email, senha}
    GW->>Auth: validar credenciais
    Auth-->>GW: JWT RS256 {sub, role, exp}
    GW-->>Analista: {token, expiresIn: 8h}
    Note over Auth,DB: Token registrado; blocklist Redis inicializada

    Analista->>GW: POST /processos {doc, tipo=ambos} [Bearer token]
    GW->>Auth: validar token JWT
    Auth-->>GW: claims válidos {role: ANALISTA_FRAUDE}
    GW->>Orch: enfileirar tarefa
    Orch->>DB: gravar hash SHA-256 do documento
    Orch->>Audit: registrar evento "upload" na trilha

    rect rgb(230, 241, 251)
        Note over Orch,Comp: Etapa Compliance — pipeline independente
        Orch->>Comp: iniciar análise LGPD + NLP
        Comp-->>Orch: {score_compliance: 59, items: [...], flags: [...]}
        Orch->>Audit: registrar evento "compliance_concluido"
    end

    rect rgb(250, 236, 231)
        Note over Orch,Sig: Etapa Antifraude — pipeline independente (paralelo)
        Orch->>Fraud: detectar inconsistências + autofraude
        Fraud-->>Orch: {flags: [AUTOFRAUDE, CPF_DIVERGENTE], evidencias: [...]}
        Orch->>Sig: validar assinatura ICP-Brasil + biometria
        Sig-->>Orch: {status: SUSPEITA, similaridade: 41%}
        Orch->>Audit: registrar evento "autofraude_detectada" [CRÍTICO]
    end

    Orch->>Score: calcular score composto
    Score-->>Orch: {score: 86, nivel: CRITICO, fatores: [...]}
    Orch->>DB: persistir resultado completo
    Orch->>Audit: registrar evento "score_calculado"
    Orch-->>GW: notificar analista {score: 86, alertas: [...]}
    GW-->>Analista: dashboard atualizado + alerta CRÍTICO

    Note over GW,Analista: SLA: alerta enviado por e-mail em < 2 minutos

    Analista->>GW: POST /processos/1042/decisao {acao: CONFIRMAR_FRAUDE} [Bearer token]
    GW->>Auth: validar token
    GW->>Orch: registrar decisão do analista
    Orch->>Audit: registrar evento "decisao_analista" {acao: CONFIRMAR_FRAUDE}
    Orch->>DB: atualizar status processo → REPROVADO

    Analista->>GW: GET /auditoria/1042 [Bearer token, role: AUDITOR]
    GW->>Auth: validar token + verificar role AUDITOR
    GW->>DB: buscar trilha + evidências
    DB-->>GW: {eventos: [...], integridade: OK, hash_chain: verificado}
    GW-->>Analista: trilha completa + link relatório PDF

    Analista->>GW: POST /relatorios/1042/exportar [Bearer token]
    GW->>Orch: gerar PDF assinado digitalmente
    Orch-->>GW: {pdf_url, hash_pdf: e4a7c2f1...}
    GW->>Audit: registrar evento "relatorio_gerado" {hash: e4a7c2f1...}
    GW-->>Analista: download disponível
```

---

## Notas sobre os diagramas

- **Pipelines independentes:** os módulos de Compliance e Antifraude nunca executam no mesmo pipeline — são processos paralelos com resultados e telas distintas, garantindo rastreabilidade e separação de responsabilidades.
- **Trilha imutável:** cada evento na sequência é registrado com hash encadeado ao evento anterior, impossibilitando adulteração retroativa.
- **Token em toda requisição:** o JWT é validado pelo Auth Service em cada chamada à API, não apenas no login inicial.

- # 🖥️ Telas do Sistema AIAD — Descrição Completa

> **28 telas** organizadas em 6 blocos funcionais.
> Todas as telas (exceto Login e Onboarding) exigem token JWT válido no header `Authorization: Bearer <token>`.
> Compliance = paleta azul/verde · Antifraude = paleta laranja/vermelho · Admin = paleta roxa.

---

## Bloco 1 — Autenticação e segurança de acesso

### Tela 01 — Login
**Rota:** `/login` | **Token:** não requerido | **Acesso:** público

- Formulário com e-mail corporativo e senha (máscara + botão de visibilidade)
- Painel lateral azul-escuro com informações de segurança (JWT, AES-256, LGPD)
- Aviso de token JWT gerado após autenticação
- Contador de tentativas restantes após primeira falha
- Bloqueio visual e mensagem após 3 falhas consecutivas
- reCAPTCHA v3 ativado após primeira falha

### Tela 16 — Redefinição de senha
**Rota:** `/auth/redefinir` | **Token:** não requerido (fluxo pré-autenticação)

- Fluxo em 3 etapas com progresso visual no painel lateral: solicitar → verificar token → nova senha
- Entrada de código por 6 campos individuais com contador de expiração (ex.: 07:42)
- Barra de força de senha em tempo real com checklist de requisitos
- Aviso de encerramento de todas as sessões ativas após redefinição
- Novo JWT emitido automaticamente após conclusão

### Tela 17 — Primeiro acesso (Onboarding)
**Rota:** `/auth/primeiro-acesso` | **Token:** token temporário de ativação

- Barra de progresso com 4 etapas: acesso verificado → definir senha → segurança & 2FA → preferências
- Formulário de dados pessoais com campos bloqueados (e-mail e role, definidos pelo ADMIN)
- Painel lateral mostrando o que a role pode e não pode acessar
- Checklist de validação de senha em tempo real
- Aceite dos termos de uso e política LGPD obrigatório para avançar

### Tela 18 — Acesso negado
**Rota:** `/erro/403` | **Token:** válido, mas role insuficiente

- Ícone de cadeado com mensagem clara identificando qual rota foi bloqueada e por qual role
- Bloco de log com dados do evento (usuário, rota, role, IP, timestamp UTC)
- Aviso de que a tentativa foi registrada automaticamente na trilha de auditoria
- Botões para voltar ao dashboard ou solicitar acesso ao ADMIN
- Indicação das roles autorizadas para a rota tentada

### Tela 19 — Sessão expirada / token inválido
**Rota:** `/erro/401`

- Explicação clara de por que a sessão foi encerrada (token expirado após 8h)
- Checklist do que foi preservado: processos salvos, trilha de auditoria, documentos
- Resumo da última sessão (usuário, duração, IP, processos analisados)
- Botão de reautenticação com nota sobre prazo configurável pelo ADMIN

---

## Bloco 2 — Análise documental

### Tela 03 — Upload e ingestão
**Rota:** `/processos/novo` | **Roles:** `ANALISTA_FRAUDE`, `GESTOR_COMPLIANCE`, `ADMIN`

- Identificação do processo com campo de nome/ID obrigatório
- Seleção obrigatória do tipo de análise antes do upload: compliance / fraude / ambos
- Área de drag-and-drop com validação de formato e tamanho
- Lista de arquivos com barra de progresso individual e status (validando / aceito / erro)
- Hash SHA-256 exibido para cada arquivo após upload, antes do processamento
- Aviso de proteção com criptografia AES-256

### Tela 04 — Análise de compliance (etapa LGPD)
**Rota:** `/processos/:id/compliance` | **Roles:** `GESTOR_COMPLIANCE`, `ANALISTA_FRAUDE`, `ADMIN`
**Identidade visual:** paleta azul/verde

- Banner explícito: "Esta etapa é executada em pipeline independente da análise antifraude"
- Barra de progresso com sub-etapas nomeadas: OCR → NER → Checklist LGPD → Análise Semântica
- Checklist LGPD item a item com status, artigo referenciado e sugestão de adequação
- Painel de dados pessoais detectados com localização (página/linha) e botão de mascaramento
- Gauge de score de compliance 0–100 com classificação
- Botão para avançar ao pipeline de fraude (se análise combinada)

### Tela 05 — Análise antifraude (etapa fraude)
**Rota:** `/processos/:id/fraude` | **Roles:** `ANALISTA_FRAUDE`, `ADMIN`
**Identidade visual:** paleta laranja/vermelho

- Banner explícito: "Esta etapa é executada em pipeline independente da análise de compliance"
- Barra de progresso: OCR → Cruzamento cadastral → Autofraude → Assinatura → Score
- Painel de inconsistências cadastrais com severidade (Leve / Moderada / Grave) e evidência do trecho
- Card de autofraude com vínculo identificado e botões de confirmação/descarte com registro obrigatório na trilha
- Painel de análise de assinatura com status e nível de similaridade
- Gauge de score de fraude 0–100 com fatores e pesos

### Tela 06 — Análise de assinatura digital
**Rota:** `/processos/:id/assinatura` | **Roles:** `ANALISTA_FRAUDE`, `ADMIN`
**Identidade visual:** paleta laranja escuro (sub-módulo antifraude)

- Verificação criptográfica ICP-Brasil com status, emissor, número de série e validade do certificado
- Comparação de CPF do certificado vs. CPF declarado no contrato
- Visualização de cadeia de confiança (raiz ICP-Brasil → AC intermediária → certificado do signatário)
- Comparação comportamental de traço (assinatura atual vs. histórico) com percentual de similaridade
- Barra visual de similaridade com limiar marcado (padrão 70%)
- Métricas de pressão, velocidade e ângulo do traço
- Contribuição em pontos ao score final de fraude

### Tela 07 — Score de risco e dashboard analítico
**Rota:** `/processos/:id/score` | **Roles:** todos os roles autenticados

- Três gauges lado a lado: score compliance / score fraude / score composto (0–100 cada)
- Classificação final: `APROVADO` / `APROVADO COM RESSALVAS` / `REPROVADO` / `ENCAMINHAR PARA AUDITORIA`
- Lista de fatores de risco com barra de peso percentual por fator
- Histórico de variação do score ao longo das análises do processo
- Recomendações automáticas baseadas nos achados
- Botões para exportar score e encaminhar para auditoria

### Tela 21 — Comparação de documentos
**Rota:** `/processos/:id/comparar` | **Roles:** `ANALISTA_FRAUDE`, `ADMIN`
**Identidade visual:** paleta laranja (módulo antifraude)

- Seletor de dois documentos com indicação de base vs. comparado
- Diff linha a linha com marcadores `+` (verde) e `−` (vermelho) por divergência
- Localização exata de cada divergência (página e linha em cada documento)
- Painel de resumo: campos comparados, idênticos, divergências críticas e moderadas
- Percentual de similaridade geral com classificação
- Botões para gerar evidência formal e adicionar à trilha de auditoria

---

## Bloco 3 — Gestão e operação

### Tela 02 — Dashboard principal
**Rota:** `/dashboard` | **Roles:** todos os roles autenticados

- Barra superior com nome, role e contador de sessão restante
- Cards de métricas: total de processos, em análise, aguardando revisão, score crítico
- Tabela de processos recentes com badges coloridos por score e status
- Painel de alertas pendentes (scores ALTO e CRÍTICO não revisados)
- Botão de novo processo

### Tela 10 — Detalhes do processo
**Rota:** `/processos/:id` | **Roles:** todos os roles autenticados

- Painel de identificação com hash SHA-256, tipo de análise, responsável e data
- Linha do tempo completa com todas as etapas, duração de cada uma e resultado
- Três gauges de score (compliance / fraude / composto) lado a lado
- Painel de decisão do analista com registro na trilha
- Lista de documentos anexados com tamanhos

### Tela 14 — Histórico de processos
**Rota:** `/processos` | **Roles:** todos os roles autenticados

- Filtros avançados: busca livre, tipo de análise, faixa de score, período, analista responsável
- Chips de filtragem rápida: Aprovados / Reprovados / Fraude confirmada / Autofraude / Assinatura suspeita
- Cards de métricas do período com comparação ao mês anterior
- Tabela paginada com ordenação configurável e link direto para o processo
- Exportação da lista em CSV

### Tela 20 — Busca global
**Rota:** `/busca` | **Roles:** todos os roles autenticados

- Campo de busca na topbar com resultados agrupados por categoria (Processos / Documentos / Trilha)
- Termos pesquisados destacados nos trechos dos resultados
- Painel lateral com detalhes do resultado selecionado e ações diretas
- Histórico de buscas recentes por usuário

---

## Bloco 4 — Auditoria e relatórios

### Tela 08 — Auditoria interna
**Rota:** `/auditoria` | **Roles:** `AUDITOR`, `ADMIN`

- Indicador de integridade da cadeia de hash (íntegra / violação detectada)
- Tabela de trilha com: sequência, timestamp UTC, usuário, role, tipo de evento, resultado, hash
- Linha expansível com detalhes completos e hash do evento anterior (encadeamento visível)
- Chat do assistente IA contextualizado ao processo filtrado, com citação de eventos
- Exportação da trilha em JSON e CSV
- Botão de geração de relatório PDF assinado digitalmente

### Tela 07B — Relatórios
**Rota:** `/relatorios` | **Roles:** `AUDITOR`, `GESTOR_COMPLIANCE`, `ADMIN`

- Relatório em destaque do processo mais recente com três seções colapsadas (compliance / fraude / assinatura)
- Tabela de relatórios com busca, filtro por tipo e badges de score
- Botões de download PDF e JSON por relatório
- Hash do PDF exibido para conferência

### Tela 22 — Exportação e integrações
**Rota:** `/exportacao` | **Roles:** `ADMIN`

- Seletor visual de formato: PDF assinado / JSON estruturado / CSV tabular
- Checkboxes de conteúdo a incluir: score, checklist LGPD, flags de fraude, evidências, trilha, diff
- Aviso de segurança: hash do PDF registrado na trilha no momento da geração
- Painel de integrações com status em tempo real: Receita Federal, ICP-Brasil, BACEN SCR e slots para configuração de CRM Jurídico e Webhook

---

## Bloco 5 — Administração do sistema

### Tela 09 — Gestão de usuários
**Rota:** `/admin/usuarios` | **Roles:** `ADMIN` exclusivo

- Cards de métricas: total de usuários ativos, contas bloqueadas, roles distintas, sessões ativas agora
- Lista de usuários com avatar, nome, e-mail, role, status (ativo / bloqueado) e último acesso
- Caso real de conta bloqueada com botão de desbloqueio manual
- Aviso: toda alteração é registrada na trilha de auditoria com o token do ADMIN

### Tela 12 — Configurações de segurança
**Rota:** `/admin/segurança` | **Roles:** `ADMIN` exclusivo
**Identidade visual:** paleta roxa

- Quatro painéis configuráveis: JWT e autenticação / limiares de score e alertas / criptografia e retenção / auditoria e trilha
- Toggles interativos com estado persistido
- Campos marcados como "Obrigatório" (AES-256, hash SHA-256, trilha imutável) não podem ser desativados
- Aviso: tentativa de desativar campos obrigatórios é registrada como evento de segurança crítico

### Tela 15 — Status do sistema
**Rota:** `/admin/status` | **Roles:** `ADMIN` exclusivo

- Cards de saúde global: uptime 30 dias, latência API p95, sessões ativas, tarefas na fila
- Tabela de todos os módulos com barra de uso de CPU/memória e badge de status
- Alertas de infraestrutura com destaque para módulos em atenção
- Checklist de conformidade de segurança (TLS, AES-256, JWT blocklist, rate limiting, trilha)
- Informações do último backup com hash verificado

### Tela 24 — Alerta por e-mail (preview e templates)
**Rota:** `/admin/email-templates` | **Roles:** `ADMIN` exclusivo

- Preview completo do e-mail de alerta com header institucional, grid de campos e botão de CTA
- Footer com conformidade LGPD e link de cancelamento de notificações
- Configuração do template: assunto com variáveis, destinatários, gatilho de score
- Estatísticas de uso: taxa de abertura, cliques no link, tempo médio de leitura

### Tela 25 — Logs do sistema
**Rota:** `/admin/logs` | **Roles:** `ADMIN` exclusivo

- Filtros por texto livre, serviço, nível (ERROR / WARN / INFO / OK) e período
- Tabela em fonte monospace com timestamp, nível colorido, serviço e mensagem completa
- Destaque automático do termo pesquisado nas mensagens
- Contador de erros, avisos e infos no cabeçalho
- Paginação e exportação em arquivo de texto

---

## Bloco 6 — Suporte e conformidade

### Tela 13 — Perfil do usuário
**Rota:** `/perfil` | **Roles:** todos os roles autenticados

- Avatar com iniciais, dados pessoais editáveis em grid, badge de role e status da conta
- Painel de segurança com toggles para 2FA, notificação de novo login e sessões ativas
- Cards de atividade recente (processos hoje, fraudes confirmadas, histórico total)
- Histórico dos últimos acessos com IP e horário
- Preferências de notificação por tipo de alerta

### Tela 23 — Painel executivo
**Rota:** `/executivo` | **Roles:** `GESTOR_COMPLIANCE`, `ADMIN`
**Identidade visual:** paleta roxa

- Cinco KPIs com variação percentual vs. mês anterior
- Gráfico de barras com distribuição de scores por classificação
- Ranking de tipos de fraude detectada e top 4 de não conformidades LGPD
- Tabela de performance por analista (processos, fraudes, tempo médio)
- Gráfico de tendência semanal de volume e gauge de SLA de alertas

### Tela 26 — Ajuda e documentação
**Rota:** `/ajuda` | **Roles:** todos os roles autenticados

- Campo de busca com tags de termos populares
- FAQ interativo com respostas expandíveis
- Guias rápidos com estimativa de tempo por tarefa
- Versão do sistema e status de atualização

### Tela 27 — Política de privacidade e LGPD
**Rota:** `/privacidade` | **Roles:** todos os roles autenticados

- Card de conformidade geral com data da última auditoria
- Checklist de 8 artigos da LGPD com descrição de como cada um é atendido
- Painel dos 5 direitos dos titulares com descrição de como exercê-los
- Contato do DPO com prazo de resposta garantido (15 dias)

### Tela 28 — Encerramento de processo
**Rota:** `/processos/:id/encerrar` | **Roles:** `ANALISTA_FRAUDE`, `GESTOR_COMPLIANCE`, `ADMIN`

- Card de resultado final com gauge de score, classificação e timestamp
- Checklist de 6 etapas concluídas (compliance → antifraude → decisão → auditoria → relatório → alertas)
- Próximos passos recomendados em ordem de prioridade (jurídico, bloqueio de CPF, adequação LGPD, ANPD)
- Ações finais: baixar relatório, enviar ao jurídico, encerrar processo
- Aviso de que o processo não pode ser reaberto sem autorização do ADMIN

- # Infraestrutura AIAD — Docker Compose

> Execute `docker compose up -d` para subir todos os serviços localmente.

```yaml
version: '3.9'

services:

  # ── API Gateway ──────────────────────────────────────────
  api-gateway:
    build: ./backend/api-gateway
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - JWT_PUBLIC_KEY_PATH=/run/secrets/jwt_public
      - REDIS_URL=redis://redis:6379
    depends_on:
      - auth-service
      - redis
    secrets:
      - jwt_public

  # ── Auth Service ─────────────────────────────────────────
  auth-service:
    build: ./backend/auth-service
    environment:
      - JWT_PRIVATE_KEY_PATH=/run/secrets/jwt_private
      - JWT_PUBLIC_KEY_PATH=/run/secrets/jwt_public
      - JWT_EXPIRY=8h
      - DB_URL=postgresql://aiad:aiad@postgres:5432/aiad
      - REDIS_URL=redis://redis:6379
      - MAX_LOGIN_ATTEMPTS=3
    depends_on:
      - postgres
      - redis
    secrets:
      - jwt_private
      - jwt_public

  # ── Orquestrador ─────────────────────────────────────────
  orchestrator:
    build: ./backend/orchestrator
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/1
      - DB_URL=postgresql://aiad:aiad@postgres:5432/aiad
    depends_on:
      - redis
      - postgres

  # ── Módulo Compliance ────────────────────────────────────
  module-compliance:
    build: ./backend/modules/compliance
    environment:
      - DB_URL=postgresql://aiad:aiad@postgres:5432/aiad
      - SBERT_MODEL=paraphrase-multilingual-MiniLM-L12-v2

  # ── Módulo Antifraude ────────────────────────────────────
  module-fraud:
    build: ./backend/modules/fraud
    environment:
      - DB_URL=postgresql://aiad:aiad@postgres:5432/aiad
      - RECEITA_FEDERAL_API_URL=${RECEITA_FEDERAL_API_URL}

  # ── Módulo Assinatura ────────────────────────────────────
  module-signature:
    build: ./backend/modules/signature
    environment:
      - ICP_BRASIL_ENDPOINT=${ICP_BRASIL_ENDPOINT}
      - SIMILARITY_THRESHOLD=70

  # ── Score Engine ─────────────────────────────────────────
  score-engine:
    build: ./backend/score-engine
    environment:
      - DB_URL=postgresql://aiad:aiad@postgres:5432/aiad
      - COMPLIANCE_WEIGHT=0.4
      - FRAUD_WEIGHT=0.6
      - SCORE_HIGH_THRESHOLD=70
      - SCORE_CRITICAL_THRESHOLD=85

  # ── Frontend ─────────────────────────────────────────────
  frontend:
    build: ./frontend
    ports:
      - "8080:80"
    depends_on:
      - api-gateway

  # ── PostgreSQL ───────────────────────────────────────────
  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=aiad
      - POSTGRES_USER=aiad
      - POSTGRES_PASSWORD=aiad
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./infra/sql/init.sql:/docker-entrypoint-initdb.d/init.sql

  # ── Redis ────────────────────────────────────────────────
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:

secrets:
  jwt_private:
    file: ./secrets/jwt_private.pem
  jwt_public:
    file: ./secrets/jwt_public.pem
```

## Variáveis de ambiente necessárias

Crie um arquivo `.env` na raiz com:

```env
# Integrações externas
RECEITA_FEDERAL_API_URL=https://api.receita.fazenda.gov.br
ICP_BRASIL_ENDPOINT=https://validar.iti.gov.br/ac-raiz

# E-mail (alertas)
SMTP_HOST=smtp.empresa.com.br
SMTP_PORT=587
SMTP_USER=alertas@empresa.com.br
SMTP_PASSWORD=sua_senha

# LGPD
DPO_EMAIL=dpo@empresa.com.br
DATA_RETENTION_DAYS=365
AUDIT_RETENTION_YEARS=5
```

## Gerar chaves JWT (RS256)

```bash
# Gerar chave privada
openssl genrsa -out secrets/jwt_private.pem 2048

# Extrair chave pública
openssl rsa -in secrets/jwt_private.pem -pubout -out secrets/jwt_public.pem
```
