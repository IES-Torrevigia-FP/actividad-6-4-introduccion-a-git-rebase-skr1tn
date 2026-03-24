
# 🪜 Actividad 6.4 – Introducción a `git rebase`

> En la Actividad 6.2 aprendiste a trabajar con ramas y en la 6.3 a hacer `merge` y resolver conflictos.
> Ahora vas a aprender a **usar `git rebase` para “mover” una rama sobre otra y obtener un historial más lineal y limpio**.

---

## 🎯 Objetivos de la actividad

Al finalizar esta actividad serás capaz de:

- Explicar qué significa “cambiar la base” de una rama con `git rebase`.
- Actualizar una rama de trabajo con los cambios de `main` usando `git rebase`.
- Resolver un conflicto sencillo durante un rebase.
- Usar `git rebase --continue` y `git rebase --abort` en caso de problemas.

---

## 🧠 Conceptos clave: ¿qué hace `git rebase`?

- `git merge` combina ramas creando un **commit de merge**, manteniendo la “ramificación” visible en el historial.
- `git rebase` **“reaplica”** los commits de tu rama sobre otra base más reciente, reescribiendo la historia para que parezca lineal.

En resumen:

- Git:
  1. “Quita” temporalmente tus commits.
  2. Aplica los commits de la rama destino.
  3. Vuelve a aplicar tus commits encima de esa nueva base, creando commits nuevos (C′, D′…).

> Regla de oro: **usa rebase solo en ramas que no hayas compartido con otras personas** (ramas locales de trabajo).

---

## 🧩 Parte 1 – Preparar el escenario

### Reto 1.1 – Crear repositorio con `main` y una rama de feature

1. Crea una carpeta para esta actividad:

   ```bash
   mkdir actividad-6-4-rebase
   cd actividad-6-4-rebase
   ```

2. Inicializa el repositorio:

   ```bash
   git init
   ```

3. Crea un archivo `changelog.md` con:

   ```markdown
   # Changelog

   - v1.0: Versión inicial
   ```

4. Añade y haz commit en `main`:

   ```bash
   git add changelog.md
   git commit -m "Crea changelog con versión inicial"
   ```

5. Crea una rama de trabajo para una nueva funcionalidad:

   ```bash
   git checkout -b feature-notas
   ```

6. En `feature-notas`, crea `notas.md` con:

   ```markdown
   # Notas de la funcionalidad

   - Nota 1: pendiente de completar.
   ```

7. Añade y haz commit:

   ```bash
   git add notas.md
   git commit -m "Añade notas iniciales de la funcionalidad"
   ```

### Checkpoint 1 – Evidencia

- Crea `cp1-estado-inicial-rebase.txt` con:
  - Salida de `git branch`.
  - Salida de `git log --oneline --all` (para ver `main` y `feature-notas`).

---

## 🧩 Parte 2 – Avanza `main` mientras trabajas en la rama

### Reto 2.1 – Nuevos cambios en `main`

1. Vuelve a `main`:

   ```bash
   git checkout main
   ```

2. Edita `changelog.md` para añadir una nueva versión:

   ```markdown
   # Changelog

   - v1.1: Mejora la documentación
   - v1.0: Versión inicial
   ```

3. Añade y haz commit:

   ```bash
   git add changelog.md
   git commit -m "Actualiza changelog a v1.1"
   ```

En este punto:

- `main` tiene commits adicionales que `feature-notas` aún no ha visto.
- `feature-notas` tiene sus propios commits (el archivo `notas.md`).

### Checkpoint 2 – Evidencia

- Crea `cp2-divergencia.txt` con la salida de:

  ```bash
  git log --oneline --all
  ```

- Añade una breve descripción de la situación:
  - ¿Qué commits exclusivos tiene `main`?
  - ¿Qué commits exclusivos tiene `feature-notas`?

---

## 🧩 Parte 3 – Rebasing: poner `feature-notas` “encima” de `main`

### Reto 3.1 – Ejecutar `git rebase main`

1. Cámbiate a la rama `feature-notas`:

   ```bash
   git checkout feature-notas
   ```

2. Ejecuta el rebase sobre `main`:

   ```bash
   git rebase main
   ```

Git va a:

- Tomar los commits de `feature-notas`.
- Aplicarlos de nuevo encima del último commit de `main`.

3. Tras el rebase, revisa el historial:

   ```bash
   git log --oneline --all
   ```

Deberías ver un historial **lineal**, donde los commits de `main` van primero y luego los de `feature-notas`.

### Checkpoint 3 – Evidencia

- Crea `cp3-despues-rebase.txt` con:
  - Salida de `git log --oneline --all`.
  - Una explicación (3–4 líneas) de qué ha cambiado en el historial tras el rebase (no el contenido de los archivos, sino la “historia”).

---

## 🧩 Parte 4 – Rebase con conflicto sencillo

Ahora vas a provocar un conflicto durante un rebase.

### Reto 4.1 – Preparar un cambio conflictivo

1. Asegúrate de estar en `main`:

   ```bash
   git checkout main
   ```

2. Modifica `notas.md` en `main` (primero créalo en `main` si no existe):

   - Si `notas.md` no existe en `main`, crea uno sencillo:

     ```markdown
     # Notas de la funcionalidad

     - Nota 1: versión desde main.
     ```

   - Añade y haz commit:

     ```bash
     git add notas.md
     git commit -m "Crea notas en main"
     ```

3. Ahora, en `feature-notas`, cambia el mismo archivo de otra forma:

   ```bash
   git checkout feature-notas
   ```

   Edita `notas.md` a algo como:

   ```markdown
   # Notas de la funcionalidad

   - Nota 1: versión desde feature-notas.
   - Nota 2: añadida en la rama de feature.
   ```

4. Añade y haz commit en `feature-notas`:

   ```bash
   git add notas.md
   git commit -m "Modifica notas en feature-notas"
   ```

En este punto, `main` y `feature-notas` tienen modificaciones distintas en la misma línea de `notas.md`.

### Reto 4.2 – Ejecutar el rebase con conflicto

1. Asegúrate de estar en `feature-notas`:

   ```bash
   git checkout feature-notas
   ```

2. Ejecuta:

   ```bash
   git rebase main
   ```

Git debería detenerse con un **conflicto** en `notas.md`.

3. Ejecuta:

   ```bash
   git status
   ```

Comprobarás que el rebase está en curso y hay un archivo en conflicto.

4. Abre `notas.md` y observa los marcadores de conflicto:

   ```text
   <<<<<<< HEAD
   (versión de main)
   =======
   (versión de feature-notas)
   >>>>>>> nombre-del-commit
   ```

### Checkpoint 4 – Evidencia

- Crea `cp4-conflicto-rebase.txt` con:
  - La salida de `git status` durante el conflicto.
  - El fragmento de `notas.md` donde se ven los marcadores de conflicto.

---

## 🧩 Parte 5 – Resolver el conflicto en un rebase

### Reto 5.1 – Resolver, continuar o abortar

1. Edita `notas.md` para elegir una versión o combinar ambas, por ejemplo:

   ```markdown
   # Notas de la funcionalidad

   - Nota 1: combinamos las versiones de main y feature-notas.
   - Nota 2: añadida en la rama de feature.
   ```

   Importante: **elimina los marcadores** `<<<<<<<`, `=======`, `>>>>>>>`.

2. Marca el conflicto como resuelto:

   ```bash
   git add notas.md
   ```

3. Continúa el rebase:

   ```bash
   git rebase --continue
   ```

   Si hubiera más conflictos, Git los presentaría uno a uno.

4. Comprueba el estado e historial:

   ```bash
   git status
   git log --oneline --all
   ```

Si en algún momento te equivocas y quieres deshacer el rebase completo:

```bash
git rebase --abort
```

Esto devuelve la rama al estado anterior al `git rebase`.

### Checkpoint 5 – Evidencia

- Crea `cp5-resolucion-rebase.txt` con:
  - Salida de `git log --oneline --all` después de completar el rebase.
  - Una explicación de:
    - Qué has hecho para resolver el conflicto.
    - Cuándo usarías `git rebase --abort`.

---

## 🧩 Parte 6 – Reflexión final sobre rebase vs merge

Crea `reflexion-6-4.md` y responde:

1. Explica con tus palabras la diferencia entre `git merge` y `git rebase` en cuanto al **historial** que generan.
2. Indica una situación en la que preferirías usar `rebase` y otra en la que preferirías usar `merge`.
3. ¿Por qué se recomienda **no usar rebase** sobre ramas que ya han sido compartidas con otras personas (push al remoto)?

---

## 📦 Entrega esperada en el repositorio

Tu repositorio de la Actividad 6.4 debe incluir, al menos:

- `changelog.md`
- `notas.md` (versión final tras el rebase)
- `cp1-estado-inicial-rebase.txt`
- `cp2-divergencia.txt`
- `cp3-despues-rebase.txt`
- `cp4-conflicto-rebase.txt`
- `cp5-resolucion-rebase.txt`
- `reflexion-6-4.md`

---

## 🏁 Evaluación sugerida

- 4 pts – Uso correcto de `git rebase` (sin conflicto y con conflicto).
- 3 pts – Evidencias claras en los checkpoints (estado, conflicto, resolución, historial).
- 3 pts – Calidad de la reflexión en `reflexion-6-4.md` (comprensión de cuándo y por qué usar rebase, no solo los comandos).

**Puntuación máxima: 10 puntos.**

---

## 🤖 Evaluación automática y redacción de informe

Esta actividad incluye una evaluación automática similar a la de la actividad anterior, adaptada a rebase y resolución de conflictos:

- Workflow: `.github/workflows/evaluacion.yml`
- Script de evaluación: `tools/evaluar_repo.py`
- Script de redacción IA: `tools/ai_redactor.py`

### ¿Qué genera?

Al ejecutarse en GitHub Actions, se generan los siguientes archivos en `reportes/`:

- `informe.md` (resumen técnico de evaluación)
- `informe.json` (métricas en formato estructurado)
- `metricas.csv` (puntuaciones por criterio)
- `arbol.txt` (resumen del árbol del repositorio)

Si hay credenciales de IA configuradas en Secrets/Variables, también se generan:

- `retro.md` (retroalimentación redactada por IA)
- `retro.docx` (versión formal en Word)

### Parámetros del workflow (`workflow_dispatch`)

- `min_commits` (por defecto: `5`)
- `required_files` (por defecto: `changelog.md,notas.md,cp1-estado-inicial-rebase.txt,cp2-divergencia.txt,cp3-despues-rebase.txt,cp4-conflicto-rebase.txt,cp5-resolucion-rebase.txt,reflexion-6-4.md`)

---