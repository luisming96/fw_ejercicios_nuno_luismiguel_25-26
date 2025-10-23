# Práctica 1 – Introducción a la instalación de Angular y creación del primer proyecto

## 1. Investigación sobre Standalone Components vs NgModules

### 1.1 Definición

**NgModules**
Desde las primeras versiones de Angular, los módulos (`NgModule`) han sido la base de la organización de las aplicaciones.
Un módulo agrupa componentes, directivas, pipes y servicios relacionados bajo un mismo contexto lógico.
Cada aplicación Angular tiene al menos un módulo raíz (`AppModule`), que indica a Angular qué componentes deben cargarse y cómo se estructuran las dependencias.

**Standalone Components**
A partir de Angular 14, se introdujeron los *Standalone Components* como una alternativa más ligera.
Estos componentes pueden funcionar sin estar declarados dentro de un módulo, ya que gestionan sus propias dependencias a través de la propiedad `imports` del decorador `@Component`.
El objetivo es simplificar la estructura y reducir el número de archivos necesarios.

---

### 1.2 Motivación de Angular para incorporar Standalone Components

Angular incorporó los *Standalone Components* para simplificar el desarrollo y reducir la complejidad de los proyectos.
Durante años, el uso obligatorio de `NgModules` generaba cierta confusión, sobre todo en proyectos pequeños, donde los módulos apenas aportaban valor real.

**Principales motivaciones:**

- Simplificar la estructura del proyecto, eliminando la necesidad de módulos innecesarios.
- Hacer las dependencias más explícitas, indicando directamente qué necesita cada componente.
- Reducir el tiempo de aprendizaje para nuevos desarrolladores.
- Facilitar las pruebas y la carga perezosa (*lazy loading*) de componentes o rutas.
- Permitir migraciones graduales, ya que ambos enfoques pueden coexistir en una misma aplicación.

**Por su parte, los NgModules siguen existiendo porque:**

- Son útiles en aplicaciones grandes para agrupar funcionalidades en módulos independientes.
- Permiten compartir componentes y servicios fácilmente entre distintas partes del proyecto.
- Facilitan estructuras bien organizadas y escalables en entornos empresariales.

---

### 1.3 Ventajas e inconvenientes

| **Enfoque** | **Ventajas** | **Inconvenientes** |
|--------------|--------------|--------------------|
| **Standalone Components** | - Menor complejidad inicial. - Menos archivos y estructura más simple. - Dependencias más claras y directas. - Ideal para proyectos nuevos o pequeños. | - Puede resultar menos claro en proyectos muy grandes. - No todas las librerías externas están totalmente adaptadas. |
| **NgModules** | - Organización modular y escalable. - Mejor separación por áreas funcionales. - Amplia compatibilidad con librerías existentes. | - Mayor número de archivos. - Estructura más compleja y curva de aprendizaje inicial más alta. |

---

### 1.4 Cuándo sigue teniendo sentido usar NgModules

Aunque Angular recomienda el uso de *standalone* para nuevos proyectos, los `NgModules` siguen teniendo sentido en los siguientes casos:

- En aplicaciones grandes donde se requiere dividir el código en módulos funcionales (por ejemplo, `UserModule`, `AdminModule`, `SharedModule`, etc.).
- Cuando se reutiliza código de librerías que aún dependen de módulos.
- En equipos o empresas que mantienen proyectos antiguos, donde la migración completa no es viable.
- Si se busca mantener patrones conocidos de arquitectura (lazy loading tradicional o módulos compartidos).

---

### 1.5 Tabla comparativa: Standalone Components vs NgModules

| **Aspecto** | **Enfoque Standalone** | **Enfoque con NgModules** |
|--------------|-------------------------|-----------------------------|
| **Archivos característicos** | Utiliza: - `main.ts` con `bootstrapApplication(AppComponent)`. - `app.config.ts` para la configuración global y proveedores. - `app.routes.ts` para la definición de rutas. Los componentes se marcan con `standalone: true`. | Utiliza: - `main.ts` con `platformBrowserDynamic().bootstrapModule(AppModule)`. - `app.module.ts` como módulo raíz donde se declaran los componentes principales. - `app-routing.module.ts` para la configuración de rutas. |
| **Imports** | Cada componente *standalone* declara sus propias dependencias en la propiedad `imports: [...]` dentro del decorador `@Component`. Ejemplo: `imports: [CommonModule, RouterModule]`. | Los módulos agrupan las dependencias en la sección `imports` del decorador `@NgModule`. Ejemplo: `imports: [BrowserModule, AppRoutingModule]`. Los componentes se declaran en `declarations`. |
| **Impacto en el bootstrap, aprendizaje y mantenimiento** | El arranque se realiza mediante `bootstrapApplication()`, lo que simplifica el inicio del proyecto al eliminar la necesidad de un módulo raíz. Aprendizaje más rápido y estructura más ligera, ideal para proyectos nuevos o de tamaño medio. | El arranque se realiza con `bootstrapModule(AppModule)`, donde el módulo raíz gestiona las dependencias iniciales. Mayor complejidad inicial, pero más adecuado para proyectos grandes o con una arquitectura modular. |

---

### 1.C. Análisis de la Estructura (`fw_proyecto1_standalone`)

| **Fichero** | **Descripción Breve** |
| :--- | :--- |
| **`main.ts`** | Punto de entrada y arranque de la app con `bootstrapApplication(...)`. |
| **`app.config.ts`** | Contiene la configuración global y define los proveedores (servicios) a nivel de la raíz. |
| **`app.routes.ts`** | Archivo donde se define el *array* de rutas de la aplicación. |
| **`app.component.ts`** | Componente raíz. Está marcado con `standalone: true` y gestiona sus dependencias en `imports: [...]`. |

---

### 1.D. Análisis de la Estructura (`fw_proyecto2_no_standalone`)

| **Fichero** | **Descripción Breve** |
| :--- | :--- |
| **`main.ts`** | Punto de entrada. Arranca la aplicación llamando a **`platformBrowserDynamic().bootstrapModule(AppModule)`**. |
| **`app.module.ts`** | Es el **Módulo Raíz** de la app. Contenedor que agrupa componentes en `declarations` y otros módulos en `imports`. |
| **`app-routing.module.ts`** | Módulo dedicado exclusivamente a la definición y gestión de las rutas de la aplicación. |
| **`app.component.ts`** | Componente raíz. **No tiene** `standalone: true`. Depende de que sea declarado en el `app.module.ts`. |

---

## 1.E. Comparativa Técnica: Standalone Components vs NgModules

| **Aspecto** | **Enfoque Standalone (Moderno)** | **Enfoque con NgModules (Clásico)** |
| :--- | :--- | :--- |
| **Bootstrap** | Se inicia con **`bootstrapApplication(AppComponent)`** en `main.ts`, usando directamente el componente raíz. | Se inicia con **`platformBrowserDynamic().bootstrapModule(AppModule)`** en `main.ts`, que arranca el módulo raíz. |
| **Archivos y Organización** | **Menos archivos.** Utiliza `app.config.ts` (para configuración global) y `app.routes.ts` (para rutas) en lugar de módulos dedicados. | **Más archivos.** Requiere la presencia de `app.module.ts` (módulo raíz) y, si procede, `app-routing.module.ts` (módulo de rutas). |
| **Imports y Declaración** | El componente se autocontiene (es **`standalone: true`**) y declara sus dependencias (otros componentes, módulos, pipes) en su propia propiedad **`imports: [...]`**. | Los componentes deben listarse obligatoriamente en la sección **`declarations`** del `NgModule` al que pertenecen. Los módulos se listan en el `imports` del módulo. |
| **Curva de Aprendizaje y Mantenimiento** | **Curva de aprendizaje más rápida.** Estructura más sencilla y directa, ideal para proyectos pequeños o medianos. Mantenimiento más sencillo al tener dependencias explícitas en cada componente. | **Curva de aprendizaje más alta.** Estructura más compleja debido a la necesidad de crear y gestionar múltiples archivos de módulos. |
| **Consideraciones de Migración** | Se recomienda migrar proyectos a Standalone para simplificar la estructura y reducir el *boilerplate*. Es ideal para **proyectos nuevos**. | Sigue siendo útil en **aplicaciones grandes y escalables** donde se requiere agrupar funcionalidades complejas en módulos independientes (`AdminModule`, `SharedModule`). La migración puede no ser viable en proyectos heredados o muy grandes. |
