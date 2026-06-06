# DUOC Logística ERP
### Documento de Arquitetura, Módulos e Estimativa de Custos
**Versão 1.0 — Junho/2026**

> Este documento é um ponto de partida. Qualquer ajuste, melhoria ou item que precise ser revisto é só sinalizar que atualizo.

---

## 1. Visão Geral

Este documento descreve a arquitetura técnica, os módulos funcionais e a estimativa de custos para o desenvolvimento de um sistema web de gestão de transportadora, conforme alinhado entre as partes.

---

## 2. Tecnologias Utilizadas

### 2.1 Frontend
- Framework: React + Next.js
- Acesso via navegador (desktop, tablet e smartphone)
- Suporte a PWA (instalação como aplicativo)
- Interface responsiva e adaptada para diferentes tamanhos de tela
- Relatórios e dashboards construídos diretamente em React (gratuito e com maior autonomia)

### 2.2 Backend
- Plataforma: Node.js + NestJS
- API REST
- Autenticação via JWT e OAuth2
- Controle de acesso por perfis de usuário (Operadores, Financeiro, Gestão)

### 2.3 Banco de Dados
- Banco principal: PostgreSQL (gerenciado na nuvem via RDS)
- Cache e sessões: Redis (via ElastiCache)
- Armazenamento de documentos/arquivos: AWS S3

### 2.4 Deploy e Infraestrutura
- Cloud: AWS — Região **sa-east-1 (São Paulo)**
- Modelo: 100% nuvem (elimina necessidade de servidor local e internet dedicada)
- Serviços: EC2 ou ECS, RDS, ElastiCache, S3, Route 53

---

## 3. Módulos do Sistema

| Módulo | Funcionalidades |
|---|---|
| Clientes | Cadastro, consulta e gestão de clientes |
| Fornecedores | Cadastro e gestão de fornecedores |
| Pedidos | Rotas e SLAs de entrega |
| Financeiro | Emissão de NF-e, contas a pagar e contas a receber |
| Contratos | Contratos com clientes e SLAs acordados |
| Frota | Gestão de veículos, motoristas e seguros |
| Relatórios | BI e dashboards construídos em React |

---

## 4. Perfis de Usuários

- **Operadores** — execução das operações do dia a dia
- **Financeiro** — acesso ao módulo financeiro e NF-e
- **Gestão** — acesso completo e dashboards

---

## 5. Integrações Externas

- Receita Federal — emissão de Nota Fiscal Eletrônica (NF-e)

> ⚠️ A integração com NF-e requer atenção especial. Será necessário revisar bibliotecas atuais (ex: NFePHP, SEFAZ Webservices ou serviços SaaS como eNotas / NFe.io).

---

## 6. Estimativa de Custos

> Preços coletados das páginas oficiais da AWS para a região **sa-east-1 (São Paulo)**, modalidade **On-Demand, sistema Windows**. Cotação de referência: **USD 1,00 = R$ 5,06** (Investing.com, 03/06/2026). São Paulo é a região AWS mais cara da América, com preços ~20–30% acima das regiões US.
>
> ⚠️ **Atenção:** instâncias Windows custam aproximadamente **50% a mais** que instâncias Linux equivalentes na AWS. Caso o sistema possa rodar em Linux, há economia significativa.

### 6.1 Infraestrutura AWS — Preços Reais (sa-east-1, On-Demand, Windows)

#### Como funciona a cobrança na AWS

Com as instâncias **On-Demand**, você paga pela capacidade computacional **por hora ou por segundo**, dependendo da instância — sem compromissos de longo prazo nem pagamentos adiantados. Na prática, isso significa que **você paga somente pelo tempo que a máquina estiver ligada**.

> 💡 **Exemplo:** se o servidor ficar ligado 12h/dia em vez de 24h, o custo do EC2 cai pela metade.

Existem 4 modelos de cobrança principais:

| Modelo | Como funciona | Economia vs On-Demand |
|---|---|---|
| **On-Demand** | Paga por hora/segundo, sem compromisso | — (referência) |
| **Reserved (1 ou 3 anos)** | Compromisso de longo prazo com pagamento adiantado | até 75% |
| **Savings Plans** | Compromisso de valor/hora por 1 ou 3 anos, mais flexível | até 72% |
| **Spot** | Usa capacidade ociosa da AWS — pode ser interrompida | até 90% |

> ⚠️ **Atenção sobre Windows:** a cobrança por segundo se aplica a instâncias Linux. Instâncias Windows continuam no modelo de cobrança por hora cheia. Ou seja, mesmo que o servidor Windows fique ligado por 1 minuto, você paga 1 hora completa.

#### Comparativo de preço: Windows vs Linux (sa-east-1)

| Instância | Linux (USD/h) | Linux (USD/mês) | Windows (USD/h) | Windows (USD/mês) | Diferença |
|---|---|---|---|---|---|
| t3.small | $0,0260 | ~$18,98 | $0,0393 | ~$28,69 | +51% |
| t3.medium | $0,0502 | ~$36,75 | $0,0602 | ~$43,95 | +20% |

#### Preços dos demais serviços (independente de SO)

| Serviço | Instância | USD/hora | USD/mês | BRL/mês |
|---|---|---|---|---|
| RDS PostgreSQL | db.t3.medium Single-AZ | $0,0870/h | ~$63,51 | ~R$ 321 |
| ElastiCache Redis | cache.t3.micro | $0,0220/h | ~$16,06 | ~R$ 81 |
| S3 Standard | primeiros 50 GB | $0,0405/GB | ~$2,03 | ~R$ 10 |
| Route 53 | 1 zona + queries | — | ~$0,90 | ~R$ 5 |

#### Total estimado (On-Demand, 24h/dia)

| Configuração | EC2 | + Outros serviços | Total USD/mês | Total BRL/mês |
|---|---|---|---|---|
| t3.small — Linux | ~$18,98 | ~$82,50 | **~$101** | **~R$ 511** |
| t3.small — Windows | ~$28,69 | ~$82,50 | **~$111** | **~R$ 562** |
| t3.medium — Linux | ~$36,75 | ~$82,50 | **~$119** | **~R$ 602** |
| t3.medium — Windows | ~$43,95 | ~$82,50 | **~$127** | **~R$ 643** |

> 💡 Com **Reserved Instances (1 ano)** é possível reduzir o custo em até 75%. Use o [AWS Pricing Calculator](https://calculator.aws) para simular cenários exatos.

### 6.2 Ferramentas e Licenças

| Ferramenta | Custo | Observação |
|---|---|---|
| React + Next.js | Gratuito | Open source (MIT) |
| NestJS + Node.js | Gratuito | Open source (MIT) |
| PostgreSQL | Gratuito | Gerenciado via RDS |
| Redis | Gratuito | Gerenciado via ElastiCache |
| Relatórios (React) | Gratuito | Substituiu BI pago |
| Serviço NF-e (ex: eNotas / NFe.io) | ~$30–$80/mês | A definir — verificar planos e APIs |

---

## 7. Links de Referência

### Frontend
- [React – Documentação oficial](https://react.dev)
- [Next.js – Documentação oficial](https://nextjs.org/docs)
- [Progressive Web Apps (PWA) – Web.dev Google](https://web.dev/progressive-web-apps/)

### Backend
- [Node.js – Site oficial](https://nodejs.org)
- [NestJS – Documentação oficial](https://docs.nestjs.com)
- [JWT (JSON Web Tokens) – jwt.io](https://jwt.io)
- [OAuth2 – RFC 6749](https://oauth.net/2/)

### Banco de Dados
- [PostgreSQL – Site oficial](https://www.postgresql.org)
- [Redis – Site oficial](https://redis.io)
- [Amazon RDS – Preços (PostgreSQL)](https://aws.amazon.com/rds/postgresql/pricing/)
- [Amazon ElastiCache – Preços](https://aws.amazon.com/elasticache/pricing/)
- [Amazon S3 – Preços](https://aws.amazon.com/s3/pricing/)

### Infraestrutura AWS
- [AWS EC2 – Preços On-Demand](https://aws.amazon.com/ec2/pricing/on-demand/)
- [AWS ECS – Preços](https://aws.amazon.com/ecs/pricing/)
- [Amazon Route 53 – Preços](https://aws.amazon.com/route53/pricing/)
- [AWS Pricing Calculator](https://calculator.aws)

### NF-e e Integração Fiscal
- [SEFAZ – Portal da Nota Fiscal Eletrônica](https://www.nfe.fazenda.gov.br)
- [NFePHP – Biblioteca PHP para NF-e (GitHub)](https://github.com/nfephp-org/sped-nfe)
- [eNotas – Serviço SaaS NF-e](https://enotas.com.br)
- [NFe.io – Serviço SaaS NF-e](https://nfe.io)

### Cotação Cambial
- [Investing.com – USD/BRL em tempo real](https://br.investing.com/currencies/usd-brl)

---

## 8. Próximos Passos

- [ ] Revisão conjunta deste documento via Google Meet
- [ ] Validar stack tecnológica (React, NestJS, PostgreSQL, Redis)
- [ ] Validar lista de módulos e funcionalidades de cada um
- [ ] Validar perfis de usuário e permissões necessárias
- [ ] Definir serviço de integração NF-e (eNotas, NFe.io ou biblioteca própria)
- [ ] Validar instância EC2 escolhida (t3.small ou t3.medium) e avaliar Linux vs Windows
- [ ] Simular custo exato no [AWS Pricing Calculator](https://calculator.aws)
- [ ] Criar repositório no GitHub com estrutura inicial do projeto

---

## 9. Observações e Decisões Registradas

- **Deploy 100% em AWS sa-east-1 (São Paulo)** — descartado modelo híbrido devido ao custo de servidor local e internet dedicada.
- **RBAC descartado por complexidade** — controle de acesso por perfis simples de usuário.
- **Banco gerenciado via RDS** — facilita operação e backups automáticos.
- **Relatórios em React** — solução gratuita com maior autonomia de customização.
- **Módulo de Fornecedores incluído** — identificado como ausente na estrutura inicial.
- **Windows ~50% mais caro que Linux no EC2** — avaliar se há necessidade real de Windows Server ou se Linux atende.
- **São Paulo (~20–30% acima das regiões US)** — avaliar se a latência para usuários brasileiros justifica o custo vs. usar us-east-1.
