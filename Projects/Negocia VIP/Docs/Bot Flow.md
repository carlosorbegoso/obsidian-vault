# Asistente Virtual para WhatsApp

---

## 1. Asi se ve un pedido por WhatsApp

> El cliente pide por WhatsApp, el asistente arma el pedido, el vendedor aprueba y la central despacha.

```mermaid
sequenceDiagram
    participant C as Cliente
    participant B as Asistente Virtual
    participant V as Vendedor
    participant F as Central

    rect rgba(37, 211, 102, 0.15)
    Note over C,B: El cliente hace su pedido
    C->>B: Hola, quiero hacer un pedido
    B->>C: Hola Don Pedro! Que productos necesita?
    C->>B: 10,000 papas clasicas y 5,000 chifles
    B->>C: Papas $5,000 + Chifles $2,250 = $7,250. Confirma?
    C->>B: Si
    B->>C: Pedido #0128 recibido!
    end

    rect rgba(124, 58, 237, 0.15)
    Note over V: El vendedor aprueba
    V->>V: Ve pedido #0128 en su lista
    V->>F: Aprueba con un click
    end

    rect rgba(59, 130, 246, 0.15)
    Note over F: La central despacha
    F->>F: Recibe, prepara y envia
    end
```

---

## 2. El asistente recuerda a cada cliente

> Cuando un cliente vuelve a pedir, no necesita repetir nada. El asistente recuerda su historial.

```mermaid
sequenceDiagram
    participant C as Cliente
    participant B as Asistente Virtual

    rect rgba(37, 211, 102, 0.15)
    C->>B: Mandame lo mismo que la vez pasada
    B->>C: Su ultimo pedido fue 10,000 Papas + 5,000 Chifles = $7,250. Repetimos?
    C->>B: Si, pero agrega 2,000 papas picantes
    B->>C: Nuevo total $8,350. Confirma?
    C->>B: Dale
    B->>C: Pedido recibido!
    end

    Note over B: El cliente no tuvo que volver a dictar todo.
```

---

## 3. Flujo de un Pedido

> El pedido pasa por 3 etapas: el cliente pide, el vendedor aprueba, la central despacha.

```mermaid
flowchart LR
    subgraph uno ["CLIENTE"]
        A(Pide por\nWhatsApp) --> B(El asistente\narma el pedido) --> C(Confirma)
    end

    subgraph dos ["VENDEDOR"]
        D{Aprueba?}
    end

    subgraph tres ["CENTRAL"]
        F(Despacha) --> H(COMPLETADO)
    end

    C --> D
    D -- Si --> F
    D -- No --> I(Notifica al cliente)

    style uno fill:#f0fdf4,stroke:#25D366,color:#166534
    style dos fill:#f5f3ff,stroke:#7c3aed,color:#5b21b6
    style tres fill:#eff6ff,stroke:#3b82f6,color:#1e40af
    style H fill:#10b981,color:#fff,stroke:#059669
    style I fill:#ef4444,color:#fff,stroke:#dc2626
```

---

## 4. Cuando llega un cliente nuevo

> Si un numero desconocido escribe, el asistente lo atiende, recopila sus datos y lo asigna a un vendedor.

```mermaid
flowchart LR
    subgraph uno ["CLIENTE NUEVO"]
        A(Escribe al\nWhatsApp) --> B(El asistente\nle hace preguntas) --> C(Muestra catalogo)
        C --> D{Quiere pedir?}
    end

    subgraph dos ["VENDEDOR"]
        G(Recibe los datos) --> H(Lo suma a\nsu cartera)
    end

    D -- Si --> G
    D -- Todavia no --> G

    style uno fill:#f0fdf4,stroke:#25D366,color:#166534
    style dos fill:#f5f3ff,stroke:#7c3aed,color:#5b21b6
    style H fill:#10b981,color:#fff,stroke:#059669
```

---

## 5. Vision General

> Todo el sistema conectado: del cliente al despacho, en un solo flujo.

```mermaid
flowchart LR
    subgraph uno ["ENTRADA"]
        CLIENT(Clientes\nWhatsApp 24/7) <--> BOT(Asistente Virtual)
    end

    subgraph dos ["GESTION"]
        SELLER(Vendedores) --> VPANEL(Panel del Vendedor)
    end

    subgraph tres ["DESPACHO"]
        FPANEL(Central) --> ENVIO(Entrega)
    end

    BOT --> VPANEL
    VPANEL --> FPANEL
    ENVIO --> CLIENT

    style uno fill:#f0fdf4,stroke:#25D366,color:#166534
    style dos fill:#f5f3ff,stroke:#7c3aed,color:#5b21b6
    style tres fill:#eff6ff,stroke:#3b82f6,color:#1e40af
```

---

## Beneficios

> Tu negocio funciona 24/7 sin perder un solo pedido.

| Sin el asistente | Con el asistente |
|---|---|
| Pedidos se pierden o confunden | Cada pedido queda registrado |
| El vendedor anota mal cantidades | El sistema calcula con el catalogo |
| La central recibe info incompleta | Recibe pedidos completos y aprobados |
| No hay historial por cliente | Historial completo por cliente |
| Solo se puede pedir en horario laboral | Los clientes piden 24/7 |
| El vendedor depende de su memoria | Todo queda en el sistema |
| No se sabe que se vendio en tiempo real | La central ve todo al instante |
| Clientes nuevos esperan al vendedor | El asistente los captura automaticamente |
