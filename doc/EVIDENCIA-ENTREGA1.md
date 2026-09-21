# Constancia — Entrega 1, Tarea Integradora SGMMS

**Fecha y hora de esta constancia:** domingo 20 de septiembre de 2026, 17:02 
**Equipo:** Yandriani Castañeda, Samuel Sepúlveda, Joshua García

## Situación

La Entrega 1 de la Tarea Integradora debía subirse hoy al repositorio de GitHub
Classroom, dentro de la carpeta `doc/`. Al intentar aceptar la invitación de la
tarea en GitHub Classroom para crear el repositorio del equipo, la invitación
**no estuvo disponible** (no se pudo aceptar el enlace del equipo). No
había ningún profesor o monitor del curso disponible en este horario para
habilitar la asignación.

## Evidencia de que el trabajo se completó a tiempo

1. Los tres entregables de la Entrega 1 (especificación de requerimientos,
   diseño del diagrama de clases y diseño de casos de prueba funcionales)
   fueron elaborados y finalizados **antes** de la hora límite de entrega del
   día 20 de septiembre de 2026.
2. Este repositorio se inicializó localmente con Git en la misma fecha, con un
   commit cuya fecha de autor (`author date`) queda registrada en el historial
   de Git de forma verificable (`git log`), como evidencia local del momento
   de finalización.
3. Adicionalmente, como equipo y forma de evidencia lo que hicimos fue lo siguiente:
   - Enviar un correo al profesor, **hoy mismo**, adjuntando
     estos archivos y explicando la imposibilidad de aceptar la invitación de
     Classroom.
   - Subir este mismo repositorio (con su historial de commits) a un
     repositorio **personal** en GitHub (privado, agregando al
     profesor como colaborador si es privado). GitHub sella cada
     commit con una fecha del servidor al recibir el `push`, lo cual sirve
     como evidencia externa e independiente de la fecha de entrega.
   - Guardar una captura de pantalla del mensaje de error o de la página de
     Classroom mostrando que la invitación no estaba disponible.

## Acción pendiente

En cuanto un profesor o monitor habilite la asignación en GitHub Classroom:

1. Nos encargaremos de aceptar la invitación y crear el
   repositorio oficial del equipo.
2. Se agrega ese repositorio como remoto a este repositorio local:
   ```bash
   git remote add classroom <URL-del-repo-del-classroom-oficial-que-nos-daran>
   git push classroom main
   ```
   Esto demuestra las fechas de autor de los commits ya realizados, dejando
   constancia dentro del propio repositorio oficial de cuándo se hizo el
   trabajo realmente.
3. Se notifica al profesor que la entrega ya está disponible en el
   repositorio oficial, referenciando este archivo y el correo enviado
   previamente como respaldo.
