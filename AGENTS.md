# AGENTS.md - Guía para Desarrolladores

> **Este archivo contiene instrucciones específicas para agentes de IA o desarrolladores** que trabajen en el mantenimiento o mejora de "El Escuchante - IBFI-C Pro".

---

## 🎯 Contexto del Sistema

### Qué es este proyecto

Aplicación web monolítica de una sola página (SPA) que implementa un instrumento psicométrico/filosófico para evaluar bienestar integral. El sistema:

- **NO tiene backend propio** - Usa Firebase Firestore
- **Es un archivo único** - Todo en `index.html` (HTML + CSS + JS)
- **No usa frameworks** - Vanilla JS con Tailwind vía CDN
- **Es stateless** - Todo el estado está en Firebase

### Arquitectura Crítica a Entender

```
┌─────────────────────────────────────────────────────────────┐
│                     GitHub Pages                            │
│                    (index.html estático)                    │
└───────────────────────────┬─────────────────────────────────┘
                            │ HTTPS
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   Firebase Firestore                        │
│              (Base de datos NoSQL en tiempo real)           │
└─────────────────────────────────────────────────────────────┘
```

**Punto clave:** El HTML es estático, pero la app es dinámica gracias a Firebase.

---

## 🔧 Modificaciones Comunes

### 1. Cambiar Contraseña del Panel Admin

**Ubicación:** Línea ~1440 en `index.html`

```javascript
function abrirAdmin() {
    const pwd = prompt("Contraseña de acceso:");
    if (pwd === "NUEVA_CONTRASEÑA_1" || pwd === "NUEVA_CONTRASEÑA_2") {
        // ...
    }
}
```

**Nota:** No usar caracteres especiales que puedan romper el string HTML (como `"` sin escapar).

---

### 2. Modificar Preguntas del Instrumento

**Ubicación:** Objeto `questionBanks` (línea ~300)

```javascript
const questionBanks = {
    s1: [/* 10 preguntas de Serenidad */],
    s2: [/* 10 preguntas de Autenticidad */],
    s3: [/* 10 preguntas de Resistencia */],
    s4: [/* 10 preguntas de Relacionalidad */]
};
```

**IMPORTANTE:** Mantener exactamente 10 preguntas por sección o ajustar la fórmula de cálculo:

```javascript
// Si cambias el número de preguntas, actualiza:
const secAvg = secSum / NUMERO_DE_PREGUNTAS;
```

---

### 3. Modificar Valores Existenciales

**Ubicación:** Objeto `valueBank` (línea ~350)

```javascript
const valueBank = [
    { id: "libertad", name: "Libertad", desc: "..." },
    // Agregar/modificar valores aquí
];
```

**Consecuencias:**
- Cambiar la cantidad de valores afecta el rango máximo de Tensión Existencial
- Actualizar la documentación si cambias de 6 valores

---

### 4. Cambiar Escalas de Medición

**Actual:** Likert 1-4 para dimensiones, 1-10 para valores

Si necesitas cambiar las escalas:

**Dimensiones (1-4 → 1-5):**
1. Actualizar `min` y `max` en Chart.js (Radar)
2. Actualizar interpretaciones de status
3. Ajustar fórmulas de porcentaje

**Valores (1-10 → 1-7):**
1. Actualizar `max` en inputs range
2. Actualizar `max` en gráfico de barras
3. Recalcular umbrales de "Tensión Existencial"

---

## 🐛 Debugging y Solución de Problemas

### Firebase no conecta

**Síntomas:** Alert "Firebase no configurado", panel admin vacío

**Verificar:**
1. Credenciales en `firebaseConfig` (líneas ~17-25)
2. Consola del navegador (F12) → errores de red
3. Reglas de Firestore (Firebase Console → Firestore Database → Reglas)

**Reglas mínimas necesarias:**
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;  // Modo desarrollo
    }
  }
}
```

---

### Gráficos no se renderizan

**Causa común:** Chart.js no cargó

**Verificar:**
```javascript
// En consola:
console.log(typeof Chart); // Debe ser "function"
```

**Solución:** Verificar conectividad a `cdn.jsdelivr.net`

---

### Usuario ve resultados (Bug crítico)

El diseño intencional es que **los usuarios NUNCA vean resultados numéricos**.

Si accidentalmente expones resultados:

1. **Revisar** `step-thanks` section - NO debe tener:
   - Scores numéricos
   - Gráficos
   - Tablas de análisis
   
2. **Solo debe mostrar:**
   - Mensaje de confirmación
   - Código de confirmación
   - Instrucciones de siguiente paso

---

## 🎨 Guía de Estilos (Tailwind)

### Paleta de Colores Principal

| Uso | Clase Tailwind | Hex |
|-----|---------------|-----|
| Primario | `indigo-600` | #4f46e5 |
| Primario oscuro | `indigo-900` | #312e81 |
| Éxito | `emerald-400` | #34d399 |
| Advertencia | `amber-400` | #fbbf24 |
| Error/Alerta | `rose-400` | #fb7185 |
| Fondo claro | `gray-50` | #f9fafb |
| Fondo oscuro (admin) | `slate-900` | #0f172a |

### Componentes Reutilizables

**Card estándar:**
```html
<div class="bg-white shadow-xl shadow-indigo-100/50 rounded-3xl p-8 border border-gray-100">
```

**Card admin:**
```html
<div class="bg-slate-900 rounded-2xl border border-slate-700 shadow-inner">
```

**Botón primario:**
```html
<button class="bg-indigo-900 text-white font-bold py-4 rounded-xl hover:bg-indigo-800 transition">
```

---

## 📊 Firebase - Operaciones Comunes

### Estructura de Colección

```
evaluaciones/
  ├── {auto-id}/
  │   ├── confirmCode: string
  │   ├── name: string
  │   ├── age: number
  │   ├── prof: string
  │   ├── date: timestamp
  │   ├── retroalimentado: boolean
  │   ├── scores: { s1, s2, s3, s4 }
  │   ├── valuesData: array
  │   ├── metrics: { bienestarGlobal, tensionExistencial, prioridadIntervencion }
  │   └── hermeneutic: string
```

### Queries Importantes

**Obtener todos ordenados por fecha:**
```javascript
const q = query(collection(db, "evaluaciones"), orderBy("date", "desc"));
```

**Buscar por código de confirmación:**
```javascript
const q = query(collection(db, "evaluaciones"), where("confirmCode", "==", "A3F9K2"));
```

---

## 🚀 Deployment

### Actualizar GitHub Pages

1. Modificar `index.html` localmente
2. Commit a rama `main`
3. Esperar 1-2 minutos a que se propague
4. Verificar en `https://[usuario].github.io/[repo]/`

### Migrar a otro Hosting

El único requisito es servir el HTML estáticamente. Opciones:
- Netlify Drop
- Vercel
- Firebase Hosting
- Cualquier servidor web (Apache, Nginx)

---

## 📋 Checklist de Calidad

Antes de hacer push, verificar:

- [ ] Firebase `firebaseConfig` tiene credenciales válidas
- [ ] Contraseña de admin es segura (no "admin" ni "1234")
- [ ] Usuario NO ve resultados numéricos (solo confirmación)
- [ ] Gráficos se renderizan correctamente
- [ ] PDF se genera sin errores
- [ ] Responsive en móviles (testear con DevTools)
- [ ] No hay errores en consola del navegador

---

## 🔒 Consideraciones de Seguridad (IMPORTANTE)

### Vulnerabilidades Conocidas

1. **Contraseña hardcodeada:** Visible en código fuente
   - **Mitigación:** Implementar Firebase Auth para autenticación real

2. **Firebase en modo prueba:** Cualquiera puede leer/escribir
   - **Mitigación:** 
     ```javascript
     rules_version = '2';
     service cloud.firestore {
       match /databases/{database}/documents {
         match /evaluaciones/{doc} {
           allow create: if true;  // Cualquiera puede crear evaluación
           allow read: if request.auth != null;  // Solo auth puede leer
         }
       }
     }
     ```

3. **Datos sensibles:** Nombres y reflexiones personales sin cifrado
   - **Mitigación:** Considerar cifrado de campos sensibles

---

## 📝 Convenciones de Código

### Nombres de Variables

- **CamelCase** para JavaScript
- **PascalCase** para clases/componentes (si se agregan)
- **IDs de elementos:** kebab-case (ej: `step-registration`, `dash-bg`)

### Comentarios

Agregar comentarios para:
- Fórmulas matemáticas
- Lógica compleja de cálculo
- Workarounds o hacks temporales
- Configuraciones críticas

### Ejemplo:
```javascript
// Cálculo de Bienestar Global según fórmula:
// BG = (S₁ + S₂ + S₃ + S₄) / 4
currentData.metrics.bienestarGlobal = globalSum / 4;
```

---

## 📞 Escalamiento

Si encuentras un problema que no puedes resolver:

1. **Verificar Firebase Console** - Estado de servicio
2. **GitHub Issues** - Si hay repo colaborativo
3. **Documentación oficial:**
   - Firebase: https://firebase.google.com/docs
   - Chart.js: https://www.chartjs.org/docs/
   - Tailwind: https://tailwindcss.com/docs

---

## 🔄 Historial de Cambios Recientes

| Fecha | Cambio | Autor |
|-------|--------|-------|
| 2026-03-11 | Configuración inicial Firebase | Setup |
| 2026-03-11 | Cambio de contraseña admin | Update |
| 2026-03-11 | Implementación dashboard analítico | Feature |

---

**Última actualización:** Marzo 2026
**Versión del documento:** 1.0
