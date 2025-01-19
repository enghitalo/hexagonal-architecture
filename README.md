# hexagonal-architecture

## Tree

```shell
src/
├── core/                  # Lógica de negócio  (lógica de negócios)
│   ├── ports/             # Portos de entrada e saída (interfaces)
│   │   ├── input/         # Portos de entrada (Interfaces dos adaptadores de entrada)
│   │   │   └── user/      # Interfaces para entradas (ex.: IUserService, IUserRepository, IUserValidator)
│   │   └── output/        # Portos de saída (Interfaces dos adaptadores de saída)
│   │       ├── user/      # Interfaces para saídas (ex.: repositório de usuários)
│   │       └── payment/
│   ├── use_cases/         # Implementação dos casos de uso (regras de negócio) (serviços)
│   │   └── user/          # Casos de uso relacionados a usuários (CRUD)
│   │   └── payment/
│   └── dtos/              # Estruturas de transferência de dados
│       └── user/
│       └── payment/
├── adapters/              # Adaptadores (Interfaces com o mundo externo)
│   ├── input/             # Adaptadores de entrada
│   │   ├── rest/          # API HTTP/REST
│   │   │   └── user_controller.v
│   │   ├── cli/           # Interface de linha de comando (se necessário)
│   │   ├── grpc/          # (Se necessário para APIs gRPC)
│   │   └── messaging/     # Consumidores de mensagens
│   │       └── kafka/     # Kafka como sistema de mensageria
│   └── output/            # Adaptadores de saída
│       ├── database/      # Implementações de repositórios
│       ├── external/      # Integrações com APIs externas
│       │   └── payment/   # Implementação dos adaptadores para Stripe, Mercado Pago e Pagar.me
│       │       ├── stripe/
│       │       ├── mercadopago/
│       │       └── pagarme/
│       └── messaging/     # Produtores de mensagens
│           └── kafka/     # Kafka como sistema de mensageria
├── domain/                # Entidades e regras específicas do domínio
│   ├── models/            # Estruturas de dados do domínio (inclui todas as entidades e valores-objetos.)
│   │   └── user.v
│   │   └── payment.v
│   └── exceptions/        # Erros específicos do domínio (relacionadas às regras de negócio)
│       └── user.v
│       └── payment.v
├── config/                # Configurações do sistema
│   ├── app/               # Configurações gerais (ex.: variáveis de ambiente)
│   ├── database/          # Configurações do banco de dados
│   ├── security/          # Configurações de segurança (autenticação/autorização)
|   ├── logging/           # Configurações de logs
│   └── messaging/         # Configurações de mensageria
|       └── kafka/         # Configurações do Kafka
├── tests/                 # Testes
│   ├── unit/              # Testes unitários
|       ├── core/          # Testes de lógica de negócio
│       ├── adapters/      # Testes de adaptadores
│       └── domain/        # Testes de domínio
│   ├── integration/       # Testes de integração
│   └── e2e/               # Testes end-to-end
└── main.v                 # Ponto de entrada da aplicação
```
