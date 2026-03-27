# 🌟 Sistema de Efectos Visuales - Portfolio

## 🎯 Descripción

Sistema avanzado de efectos visuales 3D implementado con Three.js que cambia dinámicamente según el tema seleccionado, creando una experiencia inmersiva y única para cada usuario.

## ✨ Características

### 🌞 Tema Claro - Naturaleza
- **Partículas de Naturaleza**: 500 partículas verdes flotando suavemente
- **Mariposas**: 15 mariposas coloridas volando con movimientos realistas
- **Hojas**: 100 hojas cayendo con rotación natural
- **Sol**: Esfera dorada brillante con efecto de resplandor
- **Fondo**: Gradiente azul cielo con transiciones suaves

### 🌙 Tema Oscuro - Noche Estrellada
- **Estrellas**: 2000 estrellas blancas parpadeando
- **Constelaciones**: Osa Mayor, Osa Menor y Casiopea con líneas conectivas
- **Nebulosa**: Esfera púrpura girando lentamente
- **Fondo**: Espacio profundo con atmósfera espacial

## 🛠️ Tecnologías

- **Three.js**: Motor 3D para efectos visuales
- **ES6+**: JavaScript moderno con módulos
- **CSS3**: Transiciones y animaciones suaves
- **WebGL**: Aceleración por hardware

## 📁 Estructura de Archivos

```
src/scripts/engines/
└── visual-effects-engine.js    # Motor principal de efectos

src/styles/themes/
├── _light.css                  # Tema claro - Naturaleza
└── _dark.css                   # Tema oscuro - Noche estrellada

demo-effects.html               # Página de demostración
```

## 🚀 Instalación y Uso

### 1. Dependencias
```bash
# Three.js ya está incluido via CDN
# No se requieren instalaciones adicionales
```

### 2. Inicialización
```javascript
import { VisualEffectsEngine } from './src/scripts/engines/visual-effects-engine.js';

const visualEngine = new VisualEffectsEngine();
await visualEngine.init(container);
```

### 3. Cambio de Tema
```javascript
// Cambiar a tema oscuro
visualEngine.changeTheme('dark');

// Cambiar a tema claro
visualEngine.changeTheme('light');
```

## 🎮 Controles

### Botones de Control
- **🎬 Toggle Efectos**: Activar/desactivar efectos visuales
- **🎨 Cambiar Tema**: Cambiar entre tema claro y oscuro
- **⚡ Test Performance**: Verificar rendimiento del sistema

### Navegación por Teclado
- **T**: Cambiar tema (con Ctrl)
- **ESC**: Cerrar menús móviles

## ⚙️ Configuración

### Efectos de Noche
```javascript
night: {
    stars: {
        count: 2000,           // Número de estrellas
        size: { min: 0.1, max: 2.0 },
        color: 0xFFFFFF,       // Color blanco
        twinkleSpeed: 0.02     // Velocidad de parpadeo
    },
    constellations: {
        enabled: true,         // Habilitar constelaciones
        lines: true,           // Mostrar líneas conectivas
        glow: true             // Efecto de brillo
    },
    nebula: {
        enabled: true,         // Habilitar nebulosa
        color: 0x4A148C,       // Color púrpura
        opacity: 0.3           // Opacidad
    }
}
```

### Efectos de Día
```javascript
day: {
    nature: {
        particles: {
            count: 500,        // Partículas de naturaleza
            colors: [0x4CAF50, 0x8BC34A, 0xCDDC39, 0xFFEB3B]
        },
        butterflies: {
            count: 15,         // Número de mariposas
            colors: [0xFF9800, 0xE91E63, 0x9C27B0, 0x2196F3]
        },
        leaves: {
            count: 100,        // Número de hojas
            colors: [0x4CAF50, 0x8BC34A, 0xCDDC39]
        }
    },
    sun: {
        enabled: true,         // Habilitar sol
        size: 50,              // Tamaño del sol
        color: 0xFFD700,       // Color dorado
        glow: true             // Efecto de resplandor
    }
}
```

## 🎨 Personalización

### Colores de Tema
```css
/* Tema Claro - Naturaleza */
[data-theme="light"] {
    --bg-color: #f0f8ff;
    --accent-color: #4CAF50;
    --accent-light: #8BC34A;
    --accent-warm: #FFD700;
}

/* Tema Oscuro - Noche Estrellada */
[data-theme="dark"] {
    --bg-color: #0A0A1A;
    --accent-color: #4FC3F7;
    --accent-light: #81D4FA;
    --accent-purple: #4A148C;
}
```

### Transiciones
```css
* {
    transition: background-color 0.6s ease, 
                color 0.6s ease, 
                border-color 0.6s ease, 
                box-shadow 0.6s ease;
}
```

## 📱 Responsive Design

### Breakpoints
- **Mobile**: < 768px
- **Tablet**: 768px - 1024px
- **Desktop**: > 1024px

### Optimizaciones
- **Mobile**: Reducción de partículas para mejor rendimiento
- **Tablet**: Configuración balanceada
- **Desktop**: Máxima calidad visual

## ⚡ Performance

### Optimizaciones Implementadas
- **Lazy Loading**: Carga diferida de efectos
- **Throttling**: Limitación de eventos de resize
- **Efficient Rendering**: Uso optimizado de WebGL
- **Memory Management**: Limpieza automática de recursos

### Métricas de Rendimiento
- **FPS Target**: 60 FPS
- **Memory Usage**: < 100MB
- **Load Time**: < 2 segundos

## 🧪 Testing

### Demo Interactiva
```bash
# Acceder a la demo
http://localhost:3000/demo-effects.html
```

### Funcionalidades de Test
- ✅ Inicialización del motor
- ✅ Cambio de temas
- ✅ Rendimiento visual
- ✅ Responsive design
- ✅ Transiciones suaves

## 🐛 Troubleshooting

### Problemas Comunes

#### 1. Efectos no se muestran
```javascript
// Verificar inicialización
if (visualEngine && visualEngine.isInitialized) {
    console.log('Motor visual activo');
}
```

#### 2. Bajo rendimiento
```javascript
// Reducir número de partículas
config.night.stars.count = 1000; // En lugar de 2000
```

#### 3. Errores de WebGL
```javascript
// Verificar soporte de WebGL
if (!window.WebGLRenderingContext) {
    console.error('WebGL no soportado');
}
```

### Logs de Debug
```javascript
// Habilitar logs detallados
CONFIG.DEBUG = true;
```

## 🔮 Futuras Mejoras

### Próximas Funcionalidades
- [ ] Efectos de partículas interactivas
- [ ] Animaciones de scroll personalizadas
- [ ] Efectos de cursor 3D
- [ ] Más constelaciones y objetos espaciales
- [ ] Efectos de sonido ambientales
- [ ] Modo VR/AR

### Optimizaciones Planificadas
- [ ] Web Workers para cálculos pesados
- [ ] Instancing para mejor rendimiento
- [ ] LOD (Level of Detail) dinámico
- [ ] Compresión de texturas

## 📚 Recursos

### Documentación
- [Three.js Documentation](https://threejs.org/docs/)
- [WebGL Fundamentals](https://webglfundamentals.org/)
- [CSS Transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/transition)

### Herramientas de Desarrollo
- [Three.js Editor](https://threejs.org/editor/)
- [WebGL Inspector](https://github.com/benvanik/WebGL-Inspector)
- [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)

## 🤝 Contribuir

### Reportar Bugs
1. Verificar que el problema no esté documentado
2. Crear issue con detalles del problema
3. Incluir información del sistema y navegador

### Sugerir Mejoras
1. Describir la funcionalidad deseada
2. Proponer implementación
3. Considerar impacto en performance

### Pull Requests
1. Fork del repositorio
2. Crear rama para la feature
3. Implementar cambios
4. Probar en diferentes dispositivos
5. Enviar PR con descripción detallada

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver [LICENSE](LICENSE) para detalles.

## 🙏 Agradecimientos

- **Three.js Team** - Motor 3D increíble
- **WebGL Community** - Estándares web 3D
- **CSS Working Group** - Transiciones y animaciones
- **Open Source Community** - Inspiración y colaboración

---

**Desarrollado con ❤️ y 🌟 por Carlos Orbegoso**

*"La belleza está en los detalles, y los efectos visuales son el toque mágico que hace que la experiencia sea inolvidable."*
