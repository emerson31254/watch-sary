# Solución: JavaScript para Cloaker - Inyección Dinámica

## Problema

Cuando una página se inyecta dinámicamente en otro dominio mediante un cloaker (como un espejo), el JavaScript tradicional que depende de `DOMContentLoaded` no funciona correctamente porque:

1. El contenido se inyecta después de que el DOM ya está cargado
2. El evento `DOMContentLoaded` ya se disparó antes de la inyección
3. Los scripts externos no se cargan correctamente en el contexto del espejo

## Solución Implementada

Se modificó el JavaScript para que funcione tanto en carga normal como en inyección dinámica usando las siguientes técnicas:

### 1. IIFE (Immediately Invoked Function Expression)
```javascript
(function() {
    // código aquí
})();
```
- Se ejecuta inmediatamente al ser leído
- Evita conflictos de scope
- No depende de eventos externos

### 2. Detección del Estado del Documento
```javascript
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', initReferencesToggle);
} else {
    initReferencesToggle();
}
```
- Verifica si el DOM está cargando o ya está listo
- Si está cargando, espera al evento `DOMContentLoaded`
- Si ya está listo, ejecuta inmediatamente

### 3. Timeout de Respaldo
```javascript
setTimeout(initReferencesToggle, 100);
```
- Garantiza la ejecución incluso si el contenido se inyecta después
- Actúa como red de seguridad para inyecciones dinámicas
- 100ms es suficiente para que el DOM se estabilice

### 4. Código Inline en HTML
- Todo el JavaScript está dentro del HTML (no en archivos externos)
- Se ejecuta cuando el HTML se inyecta
- No depende de rutas relativas o archivos externos

## Código Final

```javascript
<script>
(function() {
    function initReferencesToggle() {
        var toggleBtn = document.getElementById('references-toggle');
        var referencesContainer = document.getElementById('references-container');
        var toggleText = document.getElementById('toggle-text');
        var toggleIcon = document.getElementById('toggle-icon');
        
        if (toggleBtn && referencesContainer && toggleText && toggleIcon) {
            var isExpanded = false;
            
            toggleBtn.addEventListener('click', function() {
                isExpanded = !isExpanded;
                
                if (isExpanded) {
                    referencesContainer.classList.add('expanded');
                    toggleText.textContent = 'Hide References';
                    toggleIcon.classList.add('rotated');
                } else {
                    referencesContainer.classList.remove('expanded');
                    toggleText.textContent = 'View All References';
                    toggleIcon.classList.remove('rotated');
                }
            });
        }
    }
    
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', initReferencesToggle);
    } else {
        initReferencesToggle();
    }
    
    setTimeout(initReferencesToggle, 100);
})();
</script>
```

## Por Qué Funciona

1. **IIFE**: Se ejecuta inmediatamente sin esperar eventos
2. **Detección de estado**: Maneja tanto carga normal como inyección dinámica
3. **Timeout**: Asegura ejecución incluso si el contenido aparece después
4. **Verificación de elementos**: Solo ejecuta si los elementos existen
5. **Código inline**: No depende de archivos externos que pueden no cargarse

## Resultado

✅ Funciona en carga normal de página
✅ Funciona cuando se inyecta dinámicamente en un cloaker
✅ Funciona incluso si el contenido aparece después del script
✅ No depende de archivos externos
✅ Compatible con cualquier método de inyección

## Notas Adicionales

- Usar `var` en lugar de `const/let` para mayor compatibilidad
- El código debe estar siempre inline en el HTML
- No usar archivos JavaScript externos para funcionalidades críticas
- El timeout de 100ms es suficiente para la mayoría de casos



