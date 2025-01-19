# hexagonal-architecture

## Tree

```shell
src/
├── core/                  # Lógica de negócio  (lógica de negócios)
│   ├── ports/             # Portos de entrada e saída (interfaces)
│   │   ├── input/         # Portos de entrada
│   │   └── output/        # Portos de saída
│   ├── use_cases/         # Implementação dos casos de uso (regras de negócio) (serviços)
│   └── dtos/              # Estruturas de transferência de dados
├── adapters/              # Adaptadores (Interfaces com o mundo externo)
│   ├── input/             # Adaptadores de entrada
│   │   ├── rest/          # API HTTP/REST
│   │   ├── cli/           # Interface de linha de comando (se necessário)
│   │   ├── grpc/          # (Se necessário para APIs gRPC)
│   │   └── messaging/     # Consumidores de mensagens
│   │       └── kafka/     # Kafka como sistema de mensageria
│   └── output/            # Adaptadores de saída
│       ├── database/      # Implementações de repositórios
│       ├── external/      # Integrações com APIs externas
│       └── messaging/     # Produtores de mensagens
│           └── kafka/     # Kafka como sistema de mensageria
├── domain/                # Entidades e regras específicas do domínio
│   ├── models/            # Estruturas de dados do domínio (inclui todas as entidades e valores-objetos.)
│   └── exceptions/        # Erros específicos do domínio (relacionadas às regras de negócio)
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
