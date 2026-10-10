---

title: Tiempo real y concurrencia
nav_order: 5
------------

# Tiempo real y concurrencia

DanceSync debe coordinar varios navegadores durante una misma batalla. Los usuarios pueden votar, escribir respuestas, desconectarse o volver a entrar mientras la partida sigue activa.

## ¿Cómo se sincroniza una batalla?

El backend gestiona el estado de la partida y publica los cambios mediante Socket.IO. Los clientes reciben eventos y actualizan la interfaz y la clasificación. Para los retos temporizados, el servidor determina los tiempos de las rondas y comunica el tiempo restante; el cliente utiliza esa referencia para mostrar la cuenta regresiva.

## Retos principales

### 1. Acciones simultáneas

Dos bailarines pueden enviar la respuesta correcta de una ronda casi al mismo tiempo. El servidor debe decidir un único ganador y evitar que una misma ronda se contabilice dos veces.

### 2. Estado consistente

Los votos y los retos ganados modifican la puntuación. El backend calcula el resultado y distribuye el nuevo estado para que los clientes no dependan de cálculos locales potencialmente diferentes.

### 3. Desconexiones y temporizadores

Un usuario puede perder la conexión, recargar la página o abandonar una sala durante la partida. La plataforma contempla períodos de gracia para gestionar las desconexiones y evitar liberar inmediatamente un puesto cuando se trata de una recarga.

## Flujo de una ronda de Word Race

1. El servidor programa y abre una ronda.
2. Los bailarines reciben la palabra y envían sus respuestas.
3. El servidor valida las respuestas y determina al ganador.
4. El resultado se comunica a los participantes y se actualiza la puntuación.

El principio central es mantener las decisiones de juego en el servidor y usar el frontend para presentar los cambios.

## Límite actual

El estado activo de las partidas se mantiene en memoria. Por ello, la coordinación entre clientes de una misma instancia funciona dentro del modelo actual, pero la recuperación del estado de una batalla después de reiniciar el servicio o el escalado a varias instancias requerirían mecanismos adicionales de persistencia y coordinación distribuida.
