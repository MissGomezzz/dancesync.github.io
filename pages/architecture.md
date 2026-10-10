---

title: Arquitectura
nav_order: 4
---

# Arquitectura

DanceSync separa la interfaz, la comunicación en tiempo real y la gestión de usuarios e historial en componentes con responsabilidades distintas.

## Arquitectura general

![Arquitectura general de DanceSync](/img/arquitecture-diagram-mermaid.png)

### Componentes principales

* **Frontend:** React, TypeScript y Vite. Gestiona la interfaz, las salas, las cámaras, las votaciones y la clasificación.
* **API Gateway:** punto de entrada para las peticiones HTTP y el tráfico Socket.IO.
* **Battle Service:** gestiona salas, participantes, batallas, chat, votaciones, puntuación y retos en tiempo real.
* **Users Service:** administra perfiles e historial de partidas.
* **PostgreSQL:** almacena los datos persistentes gestionados por `users-service`, incluidos los resultados de las partidas.

## Organización interna

Los servicios del backend separan el dominio y las reglas de negocio de los casos de uso y de los adaptadores de infraestructura. Esto facilita probar la lógica sin depender directamente de los mecanismos de comunicación o persistencia.

## Modelos de clases de los microservicios

DanceSync está organizado en tres microservicios, cada uno con responsabilidades específicas. Los siguientes diagramas muestran la estructura de sus clases y los componentes principales.

### 1. API Gateway

![Diagrama del API Gateway](/img/api-gateway-cd.png)

El API Gateway actúa como punto de entrada del sistema. Centraliza el enrutamiento de las peticiones HTTP y las conexiones WebSocket hacia los servicios correspondientes, además de gestionar la configuración de comunicación y CORS.

### 2. Users Service

![Diagrama de clases de Users Service](/img/users-service-cd.png)

El servicio de usuarios gestiona los perfiles y el historial de partidas. Sus clases permiten organizar la lógica de negocio, el acceso a los datos y la persistencia de la información en PostgreSQL.

### 3. Battle Service

![Diagrama de clases de Battle Service](/img/battle-service-cd.png)

El servicio de batallas gestiona las salas, los participantes y el estado de las partidas en tiempo real. También coordina el chat, las votaciones, la puntuación y los retos de Word Race. El estado activo de las batallas se mantiene en memoria, mientras que los resultados finalizados se envían al servicio de usuarios para su persistencia.

### Relación entre los microservicios

Cada microservicio tiene una responsabilidad independiente: el API Gateway dirige las comunicaciones, el Battle Service coordina la experiencia multijugador y el Users Service administra los perfiles y los resultados persistentes.


## Decisiones de diseño

* La lógica autoritativa de la batalla reside en `battle-service`; el frontend representa el estado recibido del servidor.
* Socket.IO distribuye eventos de juego y señalización para las conexiones de vídeo.
* El vídeo utiliza conexiones WebRTC entre navegadores; el servidor retransmite la señalización, no el contenido multimedia.
* Los resultados finalizados se persisten para que puedan consultarse después de terminar la batalla.
