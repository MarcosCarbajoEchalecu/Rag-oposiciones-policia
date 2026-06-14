# 🚔 RAG para Preparación de Oposiciones — Policía Nacional

> Sistema de recuperación aumentada por generación (RAG) end-to-end construido en n8n, que lleva la precisión de respuestas del **70% al 94%** eliminando alucinaciones en la preparación de oposiciones a la Policía Nacional.

---

## 📌 El problema

Un opositor llevaba meses consultando dudas a ChatGPT con resultados poco fiables:
- ❌ Respuestas incorrectas sobre temario oficial
- ❌ Validación errónea de tests de opción múltiple
- ❌ Alucinaciones que generaban más confusión que claridad

**Se necesitaba un sistema que respondiera exclusivamente desde el temario oficial, sin inventarse nada.**

---

## ✅ La solución

Sistema RAG completo orquestado en **n8n**, con recuperación semántica desde **Supabase**, accesible en tiempo real vía **Telegram** desde cualquier dispositivo.

```
Usuario (texto / audio / imagen)
        │
        ▼
   [Telegram Bot]
        │
        ▼
   [n8n Workflow]
     ├── Procesamiento multimodal (texto, audio, imagen)
     ├── Búsqueda semántica en Supabase (pgvector)
     └── Generación de respuesta con contexto recuperado
        │
        ▼
   Respuesta precisa ✅
```

---

## 📈 Resultados

| Métrica | Antes (ChatGPT directo) | Después (RAG) |
|---|---|---|
| Precisión de respuestas | 70% | **94%** |
| Alucinaciones | Frecuentes | Eliminadas |
| Validación de tests | Errónea | Correcta |
| Acceso | Web | Telegram (cualquier dispositivo) |

---

## 🛠️ Stack tecnológico

| Tecnología | Rol |
|---|---|
| **n8n** | Orquestación del workflow completo |
| **Supabase + pgvector** | Base de datos vectorial y recuperación semántica |
| **JavaScript** | Indexación personalizada y lógica de chunking |
| **Telegram API** | Interfaz de usuario multimodal |
| **Dockploy** | Despliegue y orquestación en producción |

---

## 🏗️ Arquitectura

### Flujo de ingesta de documentos
```
PDF/Documentos oficiales
        │
        ▼
  Extracción de texto
        │
        ▼
  Chunking inteligente (JS)
        │
        ▼
  Generación de embeddings
        │
        ▼
  Indexación en Supabase (pgvector)
```

### Flujo de consulta
```
Mensaje Telegram (texto / audio / imagen)
        │
        ├─ Audio → Transcripción (Whisper)
        ├─ Imagen → Extracción de texto (Vision)
        └─ Texto → directo
        │
        ▼
  Embedding de la consulta
        │
        ▼
  Búsqueda semántica en Supabase
        │
        ▼
  Contexto recuperado + Prompt estructurado
        │
        ▼
  LLM → Respuesta fundamentada
        │
        ▼
  Respuesta al usuario por Telegram
```

---

## 🤖 Demo real — OpoBot CNP en acción

Ejemplos reales de conversaciones con el bot en Telegram:

---

**Pregunta de test sobre legislación:**
> *92. En virtud de lo dispuesto en la Ley 12/2009 de 30 de octubre, ¿quién podrá no admitir a trámite una solicitud de protección internacional presentada en un puesto fronterizo?*
> *a) El Ministro del Interior. b) El Jefe del Puesto Fronterizo. c) El Director General de la Policía.*

**OpoBot CNP:**
> **Respuesta: B**
> ➡️ Según el artículo 9 de la Ley 12/2009, el Jefe del Puesto Fronterizo es quien puede no admitir a trámite una solicitud de protección internacional presentada en un puesto fronterizo de aquella persona extranjera que no reúna los requisitos necesarios para entrar en territorio español.

---

**Pregunta sobre estructura orgánica:**
> *¿A quién corresponde la función de actuar como Unidad Nacional de la Agencia de la Unión Europea para la Formación Policial (CEPOL)?*

**OpoBot CNP:**
> **Respuesta: A — La División de Formación y Perfeccionamiento**
> ➡️ Según el Real Decreto 207/2024 del Ministerio del Interior, la función de actuar como Unidad Nacional de CEPOL corresponde a la División de Formación y Perfeccionamiento de la Dirección General de la Policía.

---

**Pregunta sobre Derecho Penal:**
> *Un individuo encuentra unas llaves con la dirección en el llavero, entra en la vivienda y sustrae un móvil de 400€. ¿Qué delito comete?*
> *a) Robo con fuerza. b) Hurto. c) Delito leve de hurto.*

**OpoBot CNP:**
> **Respuesta: A — Delito de robo con fuerza en las cosas**
> ➡️ Según los artículos 237 y 238 del Código Penal, se considera robo con fuerza el acceso a una vivienda mediante uso de llaves halladas sin consentimiento del titular para acceder al domicilio.

---

**Cuando el sistema no tiene la información:**
> *Pregunta fuera del temario indexado*

**OpoBot CNP:**
> ❌ *No hay información disponible en el temario oficial de oposiciones a Policía Nacional sobre esta consulta.*

> 💡 El sistema **nunca inventa respuestas** — si no está en el temario indexado, lo dice. Esto es lo que elimina las alucinaciones.

---

## 📁 Estructura del repositorio

```
rag-oposiciones-policia/
├── workflow/
│   └── rag_oposiciones.json      # Workflow de n8n (exportar e importar)
├── sql/
│   └── schema.sql                # Schema de Supabase con pgvector
├── docs/
│   └── arquitectura.md           # Documentación técnica detallada
├── assets/
│   └── diagrama.png              # Diagrama de arquitectura
└── README.md
```

---

## 🚀 Cómo replicar el sistema

### 1. Supabase

1. Crear un proyecto en [supabase.com](https://supabase.com)
2. Habilitar la extensión `pgvector`
3. Ejecutar el schema en `sql/schema.sql`

### 2. n8n

1. Importar `workflow/rag_oposiciones.json`
2. Configurar las credenciales:
   - Supabase (URL + anon key)
   - OpenAI API Key
   - Telegram Bot Token
3. Activar el workflow

### 3. Telegram Bot

1. Crear un bot con [@BotFather](https://t.me/BotFather)
2. Copiar el token al workflow de n8n

### 4. Despliegue (Dockploy)

El servidor está configurado en Dockploy para garantizar disponibilidad y escalabilidad en producción.

---

## ⚙️ Variables de entorno necesarias

```env
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_ANON_KEY=eyJ...
OPENAI_API_KEY=sk-...
TELEGRAM_BOT_TOKEN=123456:ABC...
```

> ⚠️ **Nunca subas estas claves al repositorio.** Usa variables de entorno o el gestor de secretos de n8n.

---

## 📄 Licencia

MIT — libre para usar, adaptar y mejorar.

---

*Desarrollado como solución real para un opositor a la Policía Nacional 🇪🇸*
