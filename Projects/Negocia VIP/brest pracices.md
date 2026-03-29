---
project: Untitled
status: active
created: 2026-03-29
tags:
  - project
---

 Voyage AI es un servicio de embeddings externo. Veamos los pros/contras vs las alternativas:                                                                           
                                                                                                                                                                         
  Tu setup actual:                                                                                                                                                       
  User query → core-service → Voyage AI (API) → embedding vector → pgvector search → results                                                                             
                                                                                                                                                                         
  Opciones:                                                                                                                                                              
                                                                                                                                                                         
  ┌─────────────────────────┬─────────────────────┬──────────────┬────────────────────────┬──────────────────────────────┐                                               
  │         Opción          │      Latencia       │    Costo     │        Calidad         │     Dependencia externa      │                                               
  ├─────────────────────────┼─────────────────────┼──────────────┼────────────────────────┼──────────────────────────────┤
  │ Voyage AI (actual)      │ ~200-500ms por call │ $$ por token │ Excelente para español │ Sí - si cae, RAG no funciona │                                               
  ├─────────────────────────┼─────────────────────┼──────────────┼────────────────────────┼──────────────────────────────┤
  │ OpenAI Embeddings       │ ~200-500ms          │ $$ por token │ Muy buena              │ Sí                           │                                               
  ├─────────────────────────┼─────────────────────┼──────────────┼────────────────────────┼──────────────────────────────┤
  │ Anthropic               │ No tiene embeddings │ -            │ -                      │ -                            │
  ├─────────────────────────┼─────────────────────┼──────────────┼────────────────────────┼──────────────────────────────┤                                               
  │ Local (e5/multilingual) │ ~50ms               │ Gratis       │ Buena                  │ No - corre en tu VPS         │                                               
  └─────────────────────────┴─────────────────────┴──────────────┴────────────────────────┴──────────────────────────────┘                                               
                                                                                                                                                                         
  El problema que viste en los logs:                        
  Embedding search unavailable (Voyage AI down), falling back                                                                                                            
                                                             
  Cuando Voyage AI cae, tu bot pierde todo el contexto RAG. Eso es un riesgo de disponibilidad.                                                                          
                                                            
  Mi recomendación: Voyage AI está bien para calidad, pero el punto de falla es que depende de un tercero para cada búsqueda. Tu CachedVectorStore mitiga esto (cache hit
   = no llama a Voyage), pero para queries nuevas sí depende.
                                                                                                                                                                         
  Opciones para mejorar:                                                                                                                                                 
  1. Mantener Voyage AI pero con mejor fallback (usar catálogo directo cuando falla) - cambio solo en brain-service                                                      
  2. Migrar a embeddings locales en core-service (modelo como multilingual-e5-small corre en CPU) - cambio en core-service                                               
  3. Usar el embedding de LangChain4j/Anthropic que ya tienes en el classpath - cambio en core-service                    
                                                                                                      
  La opción 1 es la que puedes hacer ahora sin tocar core-service. La 2 es la ideal a largo plazo (cero dependencias externas, latencia baja, gratis).                   
                                              
  ¿Quieres que implemente el fallback mejorado en brain-service para cuando Voyage AI falla?                                                                             
                                                                                                                                                                         
● How is Claude doing this session? (optional)                                                                                                                           
  1: Bad    2: Fine   3: Good   0: Dismiss                                                                                                                               
                                                                                                                                                                         
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
❯                                                           
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  ⏵⏵ accept edits on (shift+tab to cycle)
                                                                                                                                                                         
                                                                                                                                                                         

