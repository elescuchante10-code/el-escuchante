# El Escuchante - IBFI-C Pro

## 📋 Descripción del Proyecto

**El Escuchante** es una aplicación web profesional para la evaluación del bienestar filosófico integral. Implementa el **Instrumento de Bienestar Filosófico Integral (IBFI-C Pro)**, una herramienta cuantitativa basada en cuatro dimensiones filosóficas fundamentales.

### Objetivo
Permitir a profesionales de la salud mental/philosophical counselors evaluar el bienestar integral de sus consultantes mediante un instrumento estructurado, con almacenamiento en tiempo real de resultados y dashboard analítico exclusivo para el profesional.

---

## 🏗️ Arquitectura Técnica

### Stack Tecnológico

| Componente | Tecnología |
|------------|------------|
| **Frontend** | HTML5, Tailwind CSS, JavaScript (Vanilla) |
| **Base de Datos** | Firebase Firestore (NoSQL) |
| **Gráficos** | Chart.js |
| **Exportación** | jsPDF |
| **Hosting** | GitHub Pages |

### Estructura de Archivos

```
/
├── index.html          # Aplicación completa (monolito)
├── PROJECT.md          # Este archivo - Documentación del proyecto
└── AGENTS.md           # Guía para desarrolladores/agentes
```

> Nota: El proyecto está contenido en un único archivo HTML por simplicidad de despliegue.

---

## 📊 Modelo de Datos

### Estructura de Evaluación

```javascript
{
  id: string,                    // ID único de Firebase
  confirmCode: string,           // Código de confirmación para el usuario (ej: A3F9K2)
  name: string,                  // Nombre completo del consultante
  age: number,                   // Edad
  prof: string,                  // Profesión/Ocupación
  date: timestamp,               // Fecha de evaluación
  retroalimentado: boolean,      // Si ya se hizo la sesión de retroalimentación
  
  // Dimensiones Filosóficas (S1-S4)
  scores: {
    s1: number,                  // Serenidad y Fortaleza Interior (Ataraxia) [1-4]
    s2: number,                  // Autenticidad y Sentido Vital (Existencialismo) [1-4]
    s3: number,                  // Integración y Resistencia (Crítica) [1-4]
    s4: number                   // Relacionalidad y Cuidado (Ética del Vínculo) [1-4]
  },
  
  // Valores Existenciales
  valuesData: [
    { name: string, imp: number, pres: number, gap: number },  // Libertad
    { name: string, imp: number, pres: number, gap: number },  // Belleza
    { name: string, imp: number, pres: number, gap: number },  // Dignidad
    { name: string, imp: number, pres: number, gap: number },  // Justicia
    { name: string, imp: number, pres: number, gap: number },  // Verdad
    { name: string, imp: number, pres: number, gap: number }   // Amor
  ],
  
  // Métricas Calculadas
  metrics: {
    bienestarGlobal: number,     // Promedio de S1-S4 [1-4]
    tensionExistencial: number,  // Suma de gaps [0-54]
    prioridadIntervencion: string // Valor con mayor gap
  },
  
  // Reflexión Final
  hermeneutic: string            // Respuesta narrativa del usuario
}
```

---

## 📐 Formulación Matemática

### Variables y Rangos

| Variable | Símbolo | Fórmula | Rango |
|----------|---------|---------|-------|
| Puntaje Sección | Sₙ | Sₙ = Σ(rᵢ) / 10, rᵢ ∈ {1,2,3,4} | [1, 4] |
| Bienestar Global | BG | BG = (S₁ + S₂ + S₃ + S₄) / 4 | [1, 4] |
| Gap de Valor | Gᵥ | Gᵥ = \|Iᵥ - Pᵥ\|, Iᵥ,Pᵥ ∈ [1,10] | [0, 9] |
| Tensión Existencial | Tₑ | Tₑ = Σ(Gᵥ) para v ∈ {1..6} | [0, 54] |
| Prioridad | P | P = argmax(Gᵥ) | string |

### Interpretación de Métricas

**Bienestar Global (BG):**
- [3.5 - 4.0]: Óptimo 🟢
- [2.5 - 3.5): Bueno 🟡
- [1.5 - 2.5): Regular 🟠
- [1.0 - 1.5): Bajo 🔴

**Tensión Existencial (Tₑ):**
- [0 - 15]: Baja 🟢
- (15 - 30]: Moderada 🟡
- (30 - 45]: Alta 🟠
- (45 - 54]: Crítica 🔴

---

## 🔄 Flujo de Trabajo

### Para Usuarios (Consultantes)

```
1. Acceden al link de GitHub Pages
2. Completan registro (nombre, edad, profesión)
3. Aceptan declaratoria de confidencialidad
4. Responden 4 secciones dimensionales (40 ítems Likert 1-4)
5. Evalúan 6 valores (Importancia 1-10, Presencia 1-10)
6. Escriben cierre hermenéutico (reflexión narrativa)
7. Reciben código de confirmación
8. NUNCA ven resultados numéricos
```

### Para Administrador (Profesional)

```
1. Accede al panel mediante clic en "D" + contraseña
2. Ve lista de expedientes en tiempo real
3. Identifica nuevos registros (borde verde, últimas 24h)
4. Selecciona consultante para ver dashboard completo
5. Analiza:
   - Gráfico radar de dimensiones
   - Gráfico de barras con brechas de valores
   - Tabla de análisis dimensional
   - Tabla de brechas de valores
6. Descarga PDF profesional
7. Realiza sesión de retroalimentación
8. Marca como "retroalimentado"
```

---

## 🔧 Configuración y Despliegue

### Firebase Configuration

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyCX1AZICs5iNBv-zsm_GAaxfepeCcZdzQM",
  authDomain: "el-escuchante.firebaseapp.com",
  projectId: "el-escuchante",
  storageBucket: "el-escuchante.firebasestorage.app",
  messagingSenderId: "794056663018",
  appId: "1:794056663018:web:b1e97875b8b922751d5c93",
  measurementId: "G-2NWHDMN60T"
};
```

### GitHub Pages URL

```
https://elescuchante10-code.github.io/el-escuchante/
```

### Acceso Administrativo

- **Ubicación:** Footer de la página (letra "D")
- **Contraseñas actuales:** 
  - `IBFI-Pro-2026!`
  - `ElEscuchante#99`

---

## 📦 Dependencias Externas

Las siguientes librerías se cargan vía CDN:

```html
<!-- Tailwind CSS -->
<script src="https://cdn.tailwindcss.com"></script>

<!-- Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<!-- jsPDF -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<!-- Firebase (ES Modules) -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
  import { getFirestore, ... } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";
</script>
```

---

## 🔒 Seguridad

### Consideraciones Actuales

1. **Contraseña de Admin:** Hardcodeada en JavaScript (obfuscada pero no cifrada)
2. **Firebase:** En modo de prueba (lectura/escritura abierta)
3. **Datos:** Almacenados en Firestore sin cifrado adicional

### Recomendaciones para Producción

- Implementar Firebase Authentication para el panel admin
- Cambiar reglas de seguridad de Firestore
- Habilitar reglas de validación de datos
- Considerar cifrado de datos sensibles

---

## 📄 Licencia y Uso

© 2026 El Escuchante. Todos los derechos reservados.

Instrumento desarrollado para uso profesional en acompañamiento filosófico y bienestar integral.

---

## 👥 Créditos

- **Desarrollo:** Sistema implementado con asistencia de IA
- **Base Teórica:** Filosofía práctica, existencialismo, ética del cuidado
- **Modelo:** IBFI-C Pro (Instrumento de Bienestar Filosófico Integral)

---

## 📞 Soporte y Contacto

Para modificaciones, soporte técnico o consultas sobre el instrumento, referirse al archivo `AGENTS.md` para guías de desarrollo.
