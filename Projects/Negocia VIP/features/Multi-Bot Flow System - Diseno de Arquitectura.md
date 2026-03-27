
## Contexto

  

Hoy 1 tenant = 1 bot. La config del bot (nombre, prompt, tono, tools) vive en la entidad `Tenant`. El usuario quiere que cada negocio pueda tener **multiples agentes IA** conectados en un flujo (grafo), donde cada agente se especializa en algo (ventas, soporte, citas, etc.) y puedan transferir conversaciones entre si.

  

**Modelo hibrido**: Templates predefinidos como punto de partida, pero el usuario puede editar el grafo libremente (agregar/quitar nodos, cambiar conexiones). Contexto entre bots configurable por conexion.

  

**Alcance**: Solo diseno, no implementacion por ahora.

  

---

  

## 0. Diagramas de Arquitectura

  

### Diagrama ER - Modelo de Datos

  

```mermaid

erDiagram

TENANT ||--o{ BOT_AGENT : "tiene"

TENANT ||--o{ FLOW : "tiene"

TENANT }o--o| FLOW : "active_flow_id"

  

BOT_AGENT ||--o{ BOT_AGENT_CAPABILITIES : "tiene"

BOT_AGENT ||--o{ BOT_AGENT_TOOLS : "tiene"

  

FLOW ||--o{ FLOW_NODE : "contiene"

FLOW ||--o{ FLOW_EDGE : "contiene"

FLOW }o--|| BOT_AGENT : "entry_agent_id"

  

FLOW_NODE }o--|| BOT_AGENT : "agent_id"

FLOW_EDGE }o--|| FLOW_NODE : "source_node_id"

FLOW_EDGE }o--|| FLOW_NODE : "target_node_id"

  

TENANT {

UUID id PK

string name

UUID active_flow_id FK "nullable - null=legacy"

string botName "legacy fields"

text botPrompt "legacy fields"

}

  

BOT_AGENT {

UUID id PK

UUID tenant_id FK

string name

string slug "unique per tenant"

text prompt

string tone

string language

text greeting

text farewell

int max_messages

boolean is_entry_point

boolean active

}

  

FLOW {

UUID id PK

UUID tenant_id FK

UUID entry_agent_id FK

string name

boolean is_active "1 activo por tenant"

int max_transfers "default 10"

int session_ttl_hours "default 24"

}

  

FLOW_NODE {

UUID id PK

UUID flow_id FK

UUID agent_id FK

int position_x

int position_y

boolean is_fallback

}

  

FLOW_EDGE {

UUID id PK

UUID flow_id FK

UUID source_node_id FK

UUID target_node_id FK

int priority

string condition_type "TOOL_CALL|KEYWORD|INTENT|LEAD_SCORE|ALWAYS|FALLBACK"

jsonb condition_config

string context_transfer "FULL|SUMMARY|RELEVANT|CLEAN"

jsonb custom_context_fields

text transfer_message

}

  

FLOW_TEMPLATE {

UUID id PK

string name

string category

string business_type

jsonb template_data

boolean is_featured

}

```

  

### Diagrama de Flujo - Pipeline de Procesamiento de Mensajes

  

```mermaid

flowchart TD

A[Mensaje WhatsApp] --> B[messaging-service]

B --> C[Redis Stream: negocia:messages]

C --> D[brain-service: MessageConsumer]

  

D --> E{tenant.activeFlowId?}

  

E -->|null| F[MODO LEGACY]

F --> F1[Cargar config tenant]

F1 --> F2[buildSystemPrompt con tenant.botPrompt]

F2 --> F3[Llamar Claude con todos los tools]

F3 --> F4[Responder]

  

E -->|tiene flow| G[MODO FLOW]

G --> G1[Cargar FlowSession de Redis]

G1 --> G2{Session existe?}

  

G2 -->|no| G3[Crear FlowSession con entry_agent]

G2 -->|si| G4[Obtener activeAgentId]

  

G3 --> G5[Cargar AgentContext]

G4 --> G5

  

G5 --> H[buildAgentPrompt con agent.prompt]

H --> I[Llamar Claude con tools filtrados + transfer_to_agent]

I --> J[Parsear respuesta]

  

J --> K{Evaluar Transiciones}

  

K -->|TOOL_CALL transfer| L[executeTransfer]

K -->|LEAD_SCORE match| L

K -->|KEYWORD match| L

K -->|INTENT match| L

K -->|ALWAYS| L

K -->|Sin match| M[Responder con agente actual]

  

L --> L1[Construir contexto segun modo]

L1 --> L2[Enviar farewell agente actual]

L2 --> L3[Actualizar FlowSession]

L3 --> L4[Enviar greeting nuevo agente]

L4 --> M

  

M --> N[Redis Stream: negocia:responses]

N --> O[messaging-service: enviar WhatsApp]

```

  

### Diagrama de Flujo - Evaluacion de Transiciones

  

```mermaid

flowchart TD

START[Respuesta de Claude recibida] --> A{Claude llamo transfer_to_agent?}

  

A -->|Si| TRANSFER[Transferir al agente destino]

  

A -->|No| B[Obtener edges salientes del nodo actual]

B --> C[Ordenar por priority ASC]

C --> D{Hay mas edges?}

  

D -->|No| STAY[Quedarse con agente actual]

  

D -->|Si| E[Tomar siguiente edge]

E --> F{condition_type?}

  

F -->|LEAD_SCORE| G{score >= config.value?}

G -->|Si| TRANSFER

G -->|No| D

  

F -->|INTENT| H{mensaje contiene intent?}

H -->|Si| TRANSFER

H -->|No| D

  

F -->|KEYWORD| I{mensaje contiene keyword?}

I -->|Si| TRANSFER

I -->|No| D

  

F -->|ALWAYS| TRANSFER

  

F -->|FALLBACK| J{Agente senalo CANT_HANDLE?}

J -->|Si| TRANSFER

J -->|No| D

  

TRANSFER --> K{transferCount >= maxTransfers?}

K -->|Si| LOOP[Loop detectado - quedarse con agente actual]

K -->|No| EXEC[Ejecutar transferencia]

  

EXEC --> CTX{context_transfer?}

CTX -->|FULL| CTX1[Pasar historial completo]

CTX -->|SUMMARY| CTX2[Generar resumen via Claude]

CTX -->|RELEVANT| CTX3[Extraer campos + ultimos N msgs]

CTX -->|CLEAN| CTX4[Sin contexto]

  

CTX1 --> DONE[Transferencia completada]

CTX2 --> DONE

CTX3 --> DONE

CTX4 --> DONE

```

  

### Diagrama de Secuencia - Transferencia entre Agentes

  

```mermaid

sequenceDiagram

participant C as Cliente WhatsApp

participant MS as messaging-service

participant BS as brain-service

participant R as Redis

participant AI as Claude API

  

C->>MS: "Tengo un problema con mi pedido"

MS->>R: Publish negocia:messages

  

BS->>R: Poll message

BS->>R: Get FlowSession (activeAgent = "sales")

BS->>AI: Chat con Sales Agent prompt + tools

  

AI-->>BS: "Entiendo, voy a transferirte con soporte"<br/>tool_use: transfer_to_agent(target: "support")

  

Note over BS: Evaluar transicion: TOOL_CALL match

  

BS->>R: Get FlowSession

Note over BS: transferCount < maxTransfers? Si

  

alt context_transfer = SUMMARY

BS->>AI: "Resume esta conversacion en 2-3 oraciones"

AI-->>BS: "Cliente reporta problema con pedido #45..."

end

  

BS->>R: Update FlowSession:<br/>activeAgentId = support-agent<br/>history = [summary]<br/>transferCount++

  

BS->>R: Publish farewell: "Te conecto con soporte..."

MS->>C: "Te conecto con soporte..."

  

BS->>R: Publish greeting: "Hola, soy el asistente de soporte..."

MS->>C: "Hola, soy el asistente de soporte..."

  

C->>MS: "Mi pedido #45 no llego"

MS->>R: Publish negocia:messages

  

BS->>R: Poll message

BS->>R: Get FlowSession (activeAgent = "support")

BS->>AI: Chat con Support Agent prompt + contexto del summary

AI-->>BS: Respuesta de soporte

BS->>R: Publish response

MS->>C: Respuesta de soporte

```

  

### Diagrama - Templates de Flujo

  

```mermaid

graph LR

subgraph "Template: Embudo de Ventas"

BV[Bienvenida<br/>entry point] -->|intent: compra, precio| VT[Ventas<br/>tools: catalog, order]

BV -->|keyword: soporte, problema| SP[Soporte<br/>tools: get_last_order]

VT -->|tool_call| SP

SP -->|tool_call| VT

end

```

  

```mermaid

graph LR

subgraph "Template: Negocio de Citas"

RC[Recepcionista<br/>entry point<br/>tools: catalog, appointment] -->|keyword: detalle, diferencia| ES[Especialista<br/>tools: catalog]

ES -->|always| RC

end

```

  

```mermaid

graph LR

subgraph "Template: E-commerce"

AC[Asesor Compras<br/>entry point<br/>tools: catalog, order] -->|keyword: mi pedido| PV[Posventa<br/>tools: get_last_order]

AC -->|lead_score >= 85| PG[Pagos]

PV -->|tool_call| AC

PG -->|tool_call| AC

end

```

  

### Diagrama - Estado de Session en Redis

  

```mermaid

stateDiagram-v2

[*] --> NoSession: Cliente escribe por primera vez

  

NoSession --> LegacyMode: tenant.activeFlowId == null

NoSession --> FlowMode: tenant.activeFlowId != null

  

LegacyMode --> LegacyActive: Crear List de ConversationMessage

LegacyActive --> [*]: TTL 24h expira

  

FlowMode --> EntryAgent: Crear FlowSession con entry_agent

  

EntryAgent --> AgentActive: Agente procesa mensajes

AgentActive --> AgentActive: Cliente sigue hablando

  

AgentActive --> Transferring: Condicion de transferencia match

Transferring --> ContextBuild: Construir contexto (FULL/SUMMARY/RELEVANT/CLEAN)

ContextBuild --> NewAgent: Cambiar activeAgentId + reset history

  

NewAgent --> AgentActive: Nuevo agente procesa

  

AgentActive --> LoopDetected: transferCount >= maxTransfers

LoopDetected --> AgentActive: Quedarse con agente actual

  

AgentActive --> [*]: TTL expira

EntryAgent --> [*]: TTL expira

```

  

### Diagrama de Componentes - Arquitectura de Microservicios

  

```mermaid

graph TB

subgraph "vip-panel (Angular)"

FB[Flow Builder Page]

AG[Agent CRUD]

TG[Templates Gallery]

end

  

subgraph "vip-core-service (Quarkus)"

BAR[BotAgentResource]

FR[FlowResource]

FTR[FlowTemplateResource]

IR[InternalResource]

BAS[BotAgentService]

FS[FlowService]

DB[(PostgreSQL<br/>negocio schema)]

  

BAR --> BAS --> DB

FR --> FS --> DB

FTR --> DB

IR --> FS

end

  

subgraph "vip-brain-service (Quarkus)"

MC[MessageConsumer]

FES[FlowExecutionService]

TE[TransitionEvaluator]

PB[PromptBuilderService]

CL[ClaudeAIService]

TAH[TransferToAgentHandler]

SR[SessionRepository]

RD[(Redis<br/>sessions + cache)]

  

MC --> FES

FES --> TE

FES --> SR --> RD

MC --> PB

MC --> CL

CL --> TAH

end

  

subgraph "vip-messaging-service (Quarkus)"

WH[WebhookResource]

RC[ResponseConsumer]

WA[WhatsApp API]

end

  

FB --> BAR

FB --> FR

TG --> FTR

AG --> BAR

  

WH -->|negocia:messages| MC

MC -->|negocia:responses| RC

RC --> WA

  

FES -->|GET /internal/flow| IR

```

  

---

  

## 1. Modelo de Datos

  

### BotAgent (negocio.bot_agents)

| Campo | Tipo | Descripcion |

|-------|------|-------------|

| id | UUID PK | |

| tenant_id | UUID FK -> tenants | |

| name | VARCHAR(100) | "Asistente de Ventas" |

| slug | VARCHAR(50) | "sales-agent" (unique per tenant) |

| prompt | TEXT | System prompt del agente |

| tone | VARCHAR(50) | Default 'profesional' |

| language | VARCHAR(5) | Default 'es' |

| greeting | TEXT | Mensaje al tomar la conversacion |

| farewell | TEXT | Mensaje al transferir |

| max_messages | INT | Default 50 |

| is_entry_point | BOOLEAN | Puede ser el primer agente |

| active | BOOLEAN | Soft delete |

| created_at / updated_at | INSTANT | |

  

**Tablas join**: `bot_agent_capabilities` (agente + capability enum), `bot_agent_tools` (agente + tool_name string)

  

### Flow (negocio.flows)

| Campo | Tipo | Descripcion |

|-------|------|-------------|

| id | UUID PK | |

| tenant_id | UUID FK | |

| name | VARCHAR(200) | "Flujo Principal" |

| description | TEXT | |

| entry_agent_id | UUID FK -> bot_agents | Agente inicial |

| is_active | BOOLEAN | Solo 1 activo por tenant |

| max_transfers | INT | Default 10 (anti-loop) |

| session_ttl_hours | INT | Default 24 |

| created_at / updated_at | INSTANT | |

  

### FlowNode (negocio.flow_nodes)

| Campo | Tipo | Descripcion |

|-------|------|-------------|

| id | UUID PK | |

| flow_id | UUID FK -> flows ON DELETE CASCADE | |

| agent_id | UUID FK -> bot_agents | |

| position_x / position_y | INT | Para editor visual futuro |

| is_fallback | BOOLEAN | Nodo fallback global |

  

### FlowEdge (negocio.flow_edges)

| Campo | Tipo | Descripcion |

|-------|------|-------------|

| id | UUID PK | |

| flow_id | UUID FK -> flows ON DELETE CASCADE | |

| source_node_id | UUID FK -> flow_nodes | |

| target_node_id | UUID FK -> flow_nodes | |

| priority | INT | Menor = evalua primero |

| condition_type | VARCHAR(30) | TOOL_CALL, KEYWORD, INTENT, LEAD_SCORE, ALWAYS, FALLBACK |

| condition_config | JSONB | Config especifica del tipo |

| context_transfer | VARCHAR(20) | FULL, SUMMARY, RELEVANT, CLEAN |

| custom_context_fields | JSONB | Para modo RELEVANT |

| transfer_message | TEXT | Mensaje durante transferencia |

  

### FlowTemplate (negocio.flow_templates)

| Campo | Tipo | Descripcion |

|-------|------|-------------|

| id | UUID PK | |

| name | VARCHAR(200) | |

| description | TEXT | |

| category | VARCHAR(50) | SALES, SUPPORT, APPOINTMENTS, ECOMMERCE |

| business_type | VARCHAR(20) | SERVICES, PRODUCTS, MIXED o NULL |

| template_data | JSONB | Definicion completa (agents[], edges[]) |

| is_featured | BOOLEAN | |

  

### Cambio en Tenant

Agregar campo nullable `active_flow_id UUID` -> cuando es NULL, funciona como hoy (single-bot legacy).

  

---

  

## 2. Session State

  

El key Redis `session:{tenantId}:{phone}` cambia de `List<ConversationMessage>` a:

  

```

FlowSession {

activeAgentId: UUID | null // null = legacy mode

activeFlowId: UUID | null

history: ConversationMessage[]

agentHistory: AgentVisit[] // breadcrumb de transferencias

transferCount: int // proteccion anti-loop

startedAt: Instant

}

  

AgentVisit {

agentId, agentName, enteredAt, exitReason

}

```

  

**Backward compat**: SessionRepository detecta formato (array = legacy, object = nuevo). Sessions existentes siguen funcionando sin migracion.

  

---

  

## 3. Motor de Ejecucion (brain-service)

  

### Pipeline modificado

```

Mensaje entrante

-> resolveAgent(tenantId, phone)

-> Si tenant.activeFlowId == null: modo legacy (sin cambios)

-> Si tiene flow: cargar FlowSession, obtener agente activo

-> buildAgentPrompt(agentContext) // usa prompt/tone/tools del agente

-> getHistory() // historial del agente actual

-> callAI(tools filtrados por agente + transfer_to_agent)

-> evaluateTransitions() // evaluar si hay que transferir

-> Si hay transferencia: executeTransfer()

```

  

### Evaluacion de transiciones (por prioridad)

1. **TOOL_CALL**: Claude llamo `transfer_to_agent(targetSlug, reason)` -- maxima prioridad

2. **LEAD_SCORE**: `{ "operator": ">=", "value": 80 }` -- evaluado contra tag `[LEAD:true:SCORE]`

3. **INTENT**: `{ "intents": ["support", "complaint"] }` -- match por frases/keywords del mensaje

4. **KEYWORD**: `{ "keywords": ["soporte", "ayuda"] }` -- contains case-insensitive

5. **ALWAYS**: Sin condicion, siempre transfiere (para agentes one-shot)

6. **FALLBACK**: Solo cuando el agente no puede manejar el request

  

### Tool transfer_to_agent

```json

{

"name": "transfer_to_agent",

"input_schema": {

"properties": {

"target_agent": { "type": "string", "enum": ["sales", "support"] },

"reason": { "type": "string" }

}

}

}

```

El `enum` se genera dinamicamente con los slugs de los agentes destino (edges salientes del nodo actual). Asi Claude solo puede transferir a destinos validos.

  

---

  

## 4. Transferencia de Contexto

  

Configurable **por conexion** (campo `context_transfer` en FlowEdge):

  

| Modo | Comportamiento | Costo |

|------|---------------|-------|

| **FULL** | Pasa todo el historial | Alto (mas tokens) |

| **SUMMARY** | Genera resumen via Claude (2-3 oraciones) y lo pasa | Medio (+1 llamada Claude corta) |

| **RELEVANT** | Extrae campos especificos + ultimos N mensajes | Medio |

| **CLEAN** | Sin contexto, empieza de cero | Bajo |

  

### Flujo de transferencia

1. Evaluar transicion -> TransitionResult

2. Si `shouldTransfer`:

- Construir contexto segun modo configurado

- Enviar farewell del agente actual (si tiene)

- Actualizar FlowSession (cambiar activeAgentId, agregar AgentVisit, reemplazar history)

- Enviar greeting del nuevo agente (si tiene)

  

---

  

## 5. Edge Cases

  

| Caso | Solucion |

|------|----------|

| Agente destino eliminado | Fallback al entry agent del flow, log warning |

| Loop circular de transfers | `transferCount` vs `maxTransfers` (default 10), se queda en agente actual |

| Agente eliminado en sesion activa | Reset a entry agent, preservar historial |

| Cliente vuelve despues de 24h | Session TTL expira, empieza de cero desde entry agent |

| Mensajes concurrentes durante transfer | Lock Redis `processing:{tenantId}:{phone}` con EX 30s |

| Flow desactivado mid-conversacion | Sesiones activas siguen con ultimo agente, nuevas usan legacy |

  

---

  

## 6. API Endpoints

  

### BotAgent CRUD: `/api/core/agents`

- `POST /` - Crear agente

- `GET /` - Listar agentes del tenant

- `GET /{id}` - Obtener agente

- `PUT /{id}` - Actualizar

- `DELETE /{id}` - Soft delete

  

### Flow CRUD: `/api/core/flows`

- `POST /` - Crear flow

- `GET /` - Listar flows del tenant

- `GET /{id}` - Obtener con nodos y edges

- `PUT /{id}` - Actualizar metadata

- `DELETE /{id}` - Eliminar

- `POST /{id}/activate` - Activar flow

- `POST /{id}/deactivate` - Desactivar (volver a single-bot)

- `POST /{id}/nodes` - Agregar nodo

- `DELETE /{id}/nodes/{nodeId}` - Quitar nodo

- `POST /{id}/edges` - Agregar conexion

- `PUT /{id}/edges/{edgeId}` - Editar conexion

- `DELETE /{id}/edges/{edgeId}` - Quitar conexion

- `POST /{id}/validate` - Validar flow (entry point, orphans, etc.)

  

### Templates: `/api/core/flow-templates`

- `GET /` - Listar templates

- `GET /{id}` - Detalle

- `POST /{id}/clone` - Clonar al tenant

  

### Internal: `/internal/flow/{tenantId}/active`

- `GET` - Flow activo con agentes y edges (para brain-service)

  

---

  

## 7. Frontend (vip-panel)

  

### Nuevas paginas

- **Flow Builder** (`features/flow-builder/flow-builder.ts`): 3 tabs - Agentes (cards), Conexiones (tabla), Preview (diagrama simple)

- **Agent CRUD**: Slide-over con campos: nombre, slug, prompt, tono, greeting, farewell, tools (checkboxes), capabilities (checkboxes)

- **Templates Gallery**: Grid de cards por categoria, boton "Usar template"

- **Sidebar**: Nuevo item "Flow Builder" con icono `fa-diagram-project` (solo tenant_admin)

  

---

  

## 8. Templates Predefinidos

  

### Embudo de Ventas (SALES)

```

[Bienvenida] --intent:compra--> [Ventas] --keyword:problema--> [Soporte]

\--keyword:soporte--> [Soporte] \--tool_call--> [Soporte]

```

  

### Negocio de Citas (APPOINTMENTS)

```

[Recepcionista] --keyword:detalle--> [Especialista] --always--> [Recepcionista]

```

  

### E-commerce (ECOMMERCE)

```

[Asesor Compras] --keyword:mi_pedido--> [Posventa]

\--lead_score>=85--> [Pagos]

```

  

---

  

## 9. Backward Compatibility

  

- `Tenant.activeFlowId == null` -> sistema legacy, sin cambios

- `SessionRepository` detecta formato automaticamente (array vs object)

- Cero migracion de datos existentes

- El usuario activa flows cuando quiera, puede desactivar para volver a single-bot

  

---

  

## 10. Archivos Criticos a Modificar

  

### vip-core-service

- `domain/entity/Tenant.java` - Agregar `activeFlowId`

- Nuevas entidades: `BotAgent.java`, `Flow.java`, `FlowNode.java`, `FlowEdge.java`, `FlowTemplate.java`

- Nuevos repositories y services

- Nuevos resources: `BotAgentResource`, `FlowResource`, `FlowTemplateResource`

- Nuevo endpoint interno en `InternalResource` o separado

  

### vip-brain-service

- `stream/MessageConsumer.java` - Agregar resolucion de agente y evaluacion de transiciones

- `repository/SessionRepository.java` - FlowSession con backward compat

- `ai/PromptBuilderService.java` - Nuevo metodo `buildAgentPrompt()`

- `ai/ClaudeAIService.java` - Tools filtrados + tool `transfer_to_agent` dinamico

- Nuevos: `FlowExecutionService`, `TransitionEvaluator`, `TransferToAgentHandler`

  

### vip-panel

- Nuevas paginas en `features/flow-builder/`

- Nuevos metodos en `api.service.ts`

- Nuevas rutas en `app.routes.ts`

- Nuevo item en sidebar

  

---

  

## 11. Verificacion

  

Cuando se implemente, probar:

1. Tenant sin flow -> debe funcionar exactamente como hoy

2. Crear flow desde template -> clonar agentes y conexiones

3. Activar flow -> nuevas conversaciones empiezan con entry agent

4. Transfer por tool_call -> Claude llama transfer_to_agent, cambia agente

5. Transfer por keyword -> mensaje con "soporte" activa transferencia

6. Context SUMMARY -> verificar que el resumen llega al nuevo agente

7. Context CLEAN -> nuevo agente no tiene historial previo

8. Loop protection -> verificar que despues de maxTransfers se detiene

9. Agente eliminado -> fallback a entry agent sin crash

10. Desactivar flow -> nuevas conversaciones usan single-bot legacy