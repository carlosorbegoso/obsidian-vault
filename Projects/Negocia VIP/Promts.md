---
project: Untitled
status: active
created: 2026-03-28
tags:
  - project
---
ai/
│
├── engine/                         ← 🧠 Orquestación principal
│   ├── PromptEngineService.java
│   ├── PromptAssembler.java
│   ├── PromptContext.java
│   ├── EngineRequest.java
│   └── EngineResponse.java
│
├── sections/                       ← 🔌 Plugins de prompt
│   ├── PromptSection.java
│   ├── IdentitySection.java
│   ├── BaseSection.java
│   ├── CatalogSection.java
│   ├── RagSection.java
│   ├── RulesSection.java
│   ├── CommunicationSection.java
│   ├── MemorySection.java
│   └── AgendaSection.java
│
├── tools/                          ← 🛠️ Tools (acciones del bot)
│   ├── ToolExecutor.java
│   ├── ToolRegistry.java
│   ├── impl/
│   │     ├── ShowCatalogTool.java
│   │     ├── CreateOrderTool.java
│   │     └── ScheduleAppointmentTool.java
│
├── guardrails/                     ← 🛡️ Seguridad del LLM
│   ├── ResponseValidator.java
│   ├── Rule.java
│   ├── rules/
│   │     ├── NoPersonalDataRule.java
│   │     ├── NoPriceCalculationRule.java
│   │     └── RequireConfirmationRule.java
│
├── scoring/                        ← 📊 Inteligencia de negocio
│   ├── LeadScoringService.java
│   ├── LeadScore.java
│   └── ScoringRule.java
│
├── memory/                         ← 🧠 Memoria conversacional
│   ├── ConversationMemory.java
│   ├── MemoryService.java
│   └── MemorySummarizer.java
│
├── rag/                            ← 🔎 Retrieval (RAG real)
│   ├── RagService.java
│   ├── EmbeddingService.java
│   ├── VectorStore.java
│   └── impl/
│         ├── InMemoryVectorStore.java
│         └── PineconeVectorStore.java
│
├── config/                         ← ⚙️ Configuración dinámica
│   ├── PromptConfigService.java
│   ├── TenantFeatureFlags.java
│   └── PromptVersion.java
│
└── util/
    └── PromptUtils.java