# Resumen 

# Índice

- [Resumen](#resumen)
- [Índice](#índice)
  - [Tipos de búsqueda de datos](#tipos-de-búsqueda-de-datos)
  - [¿Qué es una aplicación “performant” React?](#qué-es-una-aplicación-performant-react)
  - [Limitaciones del navegador y obtención de datos](#limitaciones-del-navegador-y-obtención-de-datos)
  - [Requests waterfalls](#requests-waterfalls)
  - [Cómo resolver las solicitudes cascada](#cómo-resolver-las-solicitudes-cascada)
  - [¿Qué pasa si uso bibliotecas para la obtención de datos?](#qué-pasa-si-uso-bibliotecas-para-la-obtención-de-datos)
  - [Referencia](#referencia)

## Tipos de búsqueda de datos

En general, podemos dividir la "búsqueda de datos" en dos categorías: búsqueda de datos inicial y búsqueda de datos a pedido en el mundo moderno de frontend.

Los datos bajo demanda se obtienen después de que un usuario interactúa con una página para mejorar su experiencia. Esa categoría incluye todas las diversas formas autocompletadas, formas dinámicas y experiencias de búsqueda. La obtención de estos datos suele ocurrir en las devoluciones de llamada de React.


Los datos iniciales son los datos que espera ver cuando abre una página. Antes de que un componente termine en la pantalla, necesitamos obtener los datos.  Es algo que necesitamos para poder mostrar a los usuarios una experiencia significativa lo antes posible. En React, la obtención de datos como este generalmente ocurre en useEffect (o en componentDidMount para componentes de clase).

## ¿Qué es una aplicación “performant” React?

¿Cómo puedes determinar si una aplicación es "eficiente"? ¡Solo mide el tiempo de renderización y listo! Cuanto más pequeño sea el número, más "eficiente" (es decir, más rápido) es el componente.

Con las operaciones async, que generalmente implican la obtención de datos, no es tan evidente en el contexto de las grandes aplicaciones y la experiencia del usuario.

Imagine que estábamos implementando una vista de problemas para un rastreador de problemas. Tendría navegación en la barra lateral a la izquierda con un montón de enlaces; la información principal del problema en el centro, cosas como título, descripción o cesionario; y una sección con comentarios debajo de eso.

imagen

Y digamos que la aplicación se implementa de tres maneras diferentes:

* Muestra un estado de carga hasta que se cargan todos los datos y, a continuación, renderiza todo de una sola vez. Toma ~3 segundos.
* Muestra un estado de carga hasta que los datos de la barra lateral se cargan primero, renderiza la barra lateral y mantiene el estado de carga hasta que los datos se terminan en la parte principal. La barra lateral que aparece toma ~1 segundos, el resto de la aplicación aparece en ~3 segundos. En general, toma ~ 4 segundos.
* Muestra un estado de carga hasta que se cargan los datos del problema principal, los renderiza, mantiene el estado de carga para la barra lateral y los comentarios. Cuando se carga la barra lateral, la renderiza, los comentarios aún están en estado de carga. La parte principal aparece en ~2 segundos, barra lateral en ~1 segundo después de eso, toma otro ~2 segundo para que aparezcan los comentarios. En general, se necesitan ~5s para aparecer.

## Limitaciones del navegador y obtención de datos

¿Podemos disparar todas las solicitudes lo antes posible, empujar esos datos en alguna tienda global y luego usarlos cuando estén disponibles? ¿Por qué molestarse con el ciclo de vida y la orquestación de algo?

Se puede hacer, si la aplicación es simple y solo tiene algunas solicitudes que hacer. Pero en las aplicaciones grandes,  puede tener docenas de solicitudes de obtención de datos, esa estrategia puede ser contraproducente.

¿Sabías que los navegadores tienen un límite en la cantidad de solicitudes en paralelo al mismo host que pueden manejar? Suponiendo que el servidor es HTTP1 (que sigue siendo el 70% de Internet), el número no es tan grande. En Chrome itys solo 6. 6 solicitudes en paralelo! Si disparas más al mismo tiempo, todos los demás tendrán que hacer cola y esperar a la primera “slot” disponible.

Y 6 solicitudes de obtención de datos iniciales en una aplicación grande no son irrazonables. Nuestro muy simple “issue tracker” ya tiene 3, y aún no hemos implementado nada de valor. Imagine todas las miradas enojadas que obtendrá, si solo agrega una solicitud de análisis algo lenta que literalmente no hace nada al comienzo de la aplicación, y termina ralentizando toda la experiencia.

## Requests waterfalls

Las "Requests waterfalls" (cascadas de solicitudes) se refieren a la representación gráfica secuencial de las solicitudes HTTP que un navegador realiza para cargar una página web. Cada barra horizontal en la cascada representa una solicitud individual, y la longitud de la barra indica la duración de la solicitud. Las cascadas de solicitudes son herramientas comunes utilizadas en el análisis del rendimiento web para identificar cuellos de botella, tiempos de carga lentos y oportunidades de optimización.

## Cómo resolver las solicitudes cascada

Promise.all es un método en JavaScript que toma un iterable de promesas como entrada y devuelve una sola promesa que se resuelve cuando todas las promesas en el iterable han sido resueltas o al menos una ha sido rechazada. En otras palabras, Promise.all espera hasta que todas las promesas dentro del iterable se completen, ya sea resueltas o rechazadas, y luego devuelve una promesa que se resuelve con un arreglo de los resultados de cada una de las promesas originales.

Esto es útil cuando se necesita realizar múltiples operaciones asíncronas al mismo tiempo y se desea esperar hasta que todas se completen antes de continuar con alguna acción posterior. Por ejemplo, al hacer varias solicitudes de red para cargar datos en una página web, se puede utilizar Promise.all para esperar a que todas las solicitudes se completen antes de renderizar la página completa o ejecutar algún código adicional. Esto puede ayudar a mejorar el rendimiento al evitar bloqueos en serie y permitir que las operaciones se realicen de manera concurrente.

## ¿Qué pasa si uso bibliotecas para la obtención de datos?

Si se decide utilizar bibliotecas para la obtención de datos en React, como axios o swr, puedes obtener ventajas y desventajas.

Ventajas:

Abstracción de complejidades: Las bibliotecas pueden abstraer muchas de las complejidades asociadas con la obtención de datos, como el manejo de errores, la gestión de caché, la cancelación de solicitudes, etc. Esto puede simplificar considerablemente tu código y reducir la cantidad de trabajo necesario para implementar estas funcionalidades.
Funcionalidades específicas de React: Algunas bibliotecas están diseñadas específicamente para integrarse bien con React y pueden proporcionar funcionalidades específicas para optimizar el rendimiento y la experiencia del usuario en aplicaciones React.
Comunidad y soporte: Utilizar bibliotecas populares significa que estás aprovechando la experiencia y el soporte de una comunidad más amplia. Puedes encontrar soluciones a problemas comunes, ejemplos de código y documentación detallada que te ayudarán a desarrollar más rápido y de manera más efectiva.
Desventajas:

Dependencia externa: Al depender de una biblioteca externa, estás agregando una dependencia adicional a tu proyecto. Esto puede aumentar la complejidad de tu código y potencialmente introducir vulnerabilidades de seguridad o problemas de compatibilidad en el futuro.
Sobrecarga de funcionalidades: Algunas bibliotecas pueden ofrecer más funcionalidades de las que realmente necesitas, lo que puede aumentar el tamaño de tu aplicación y afectar el rendimiento. Es importante evaluar cuidadosamente las necesidades de tu proyecto y elegir la biblioteca que mejor se adapte a ellas.

## Referencia

Makarevich, N. (s/f). How to fetch data in React with performance in mind. Recuperado el 7 de mayo de 2024, de Developerway.com website: https://www.developerway.com/posts/how-to-fetch-data-in-react#part5

