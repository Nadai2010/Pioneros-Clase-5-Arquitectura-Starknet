<div align="center">
  <img src="https://github.com/Nadai2010/Pioneros-Maths-STARKs-101/blob/master/Im%C3%A1genes/Pioneros.png" style="width: 200px">
  <h1 style="font-size: larger;">
    <img src="https://github.com/Nadai2010/Nadai-SHARP-Starknet/blob/master/im%C3%A1genes/Starknet.png" width="40">
    <strong>Starknet Pioneros - Arquitectura de Starknet</strong> 
    <img src="https://github.com/Nadai2010/Nadai-SHARP-Starknet/blob/master/im%C3%A1genes/Starknet.png" width="40">
  </h1>
</div>

<div align="center">
     
Puede encontrar las Diapositvas de la Presentación [aquí]()
</div>
<p align="center">
    <a href="https://starkware.co/">
        <img src="https://img.shields.io/badge/powered_by-StarkWare-navy">
    </a>
</p>

# Arquitectura de Starknet

En este documento, exploraremos la arquitectura de Starknet y sus componentes fundamentales para ayudarte en la construcción de aplicaciones web3 escalables. Starknet ha experimentado un crecimiento significativo como el primer rollup basado en ZK. Nuestro objetivo es proporcionarte un conocimiento profundo de los elementos clave de Starknet, permitiéndote desarrollar e implementar dApps de manera eficiente.

A lo largo de este documento, abordaremos los siguientes temas:

* **Nodos de L2:** Una descripción general de los diferentes nodos de Starknet (Secuenciador, Verificador, Nodos completos e Indexador) y sus roles dentro de la red.
* **Ciclo de vida de las transacciones:** Una explicación detallada de los diferentes estados por los que pasa una transacción de capa 2, discutiendo los beneficios y riesgos de considerar un estado en particular como "finalidad".
* **Starknet OS:** Una exploración de cómo el Secuenciador valida y ejecuta transacciones, y su conexión con la Abstracción de Cuentas (AA, por sus siglas en inglés).
* **SHARP:** Un examen del flujo de trabajo del Verificador para generar pruebas para Starknet y Starkex utilizando la recursividad, centrándose en el flujo de trabajo en lugar de las matemáticas detrás de STARKs.
* **DA o Disponibilidad de datos:** Una discusión sobre la Disponibilidad de Datos en Starknet en modo Rollup (excluyendo Volition por ahora).
* **Componentes de L1:** Un análisis exhaustivo del Verificador en la cadena y el Registro de Hechos, y una discusión sobre cómo funcionaría una salida de emergencia de capa 1.
* **Puentes:** Una explicación de cómo funciona la comunicación entre capa 1 y capa 2, y cómo se pueden crear puentes entre redes.

## Temas

- [Nodos de L2](#nodos-de-l2)
    - [Secuenciadores en Starknet](#secuenciadores-en-starknet)
    - [El papel de los Secuenciadores](#el-papel-de-los-secuenciadores)
    - [La evolución de Starknet y los Secuenciadores](#la-evolución-de-starknet-y-los-secuenciadores)
- [SHARP (Shared Prover) en Starknet](#sharp-shared-prover-en-starknet)
    - [¿Qué es SHARP?](#¿qué-es-sharp)
    - [Pruebas Recursivas en SHARP](#pruebas-recursivas-en-sharp)
    - [Arquitectura del Backend y Canalización de Datos de SHARP](#arquitectura-del-backend-y-canalización-de-datos-de-sharp)
    - [Desafíos y Optimización](#desafíos-y-optimización)
- [Storage Proof: Acceso Trustless Cross-Chain a la información en Starknet](#storage-proof-acceso-trustless-cross-chain-a-la-información-en-starknet)
    - [¿Qué son las Storage Proof?](#¿qué-son-las-storage-proof)
    - [Compromisos criptográficos y estructuras de datos](#compromisos-criptográficos-y-estructuras-de-datos)
    - [Generación y verificación de Storage Proof](#generación-y-verificación-de-storage-proof)
    - [Optimización de las Storage Proof con STARKs o SNARKs](#optimización-de-las-storage-proof-con-starks-o-snarks)
    - [Casos de uso de las Storage Proof](#casos-de-uso-de-las-storage-proof)
    - [Ejemplos de Storage Proof](#ejemplo-de-storage-proof)
    - [Comparación y contraste de Storage Proof y oráculos](#comparación-y-contraste-de-storage-proof-y-oráculos)
    - [El estado de los oráculos en Starknet](#el-estado-de-los-oráculos-en-starknet)
    - [¿Pueden los oráculos ser sustituidos o mejorados por Storage Proof?](#¿pueden-los-oráculos-ser-sustituidos-o-mejorados-por-storage-proof)
- [Bloques en Starknet](#bloques-en-starknet)
    - [Encabezado del bloque](#encabezado-del-bloque)
    - [Hash del bloque](#hash-del-bloque)
- [El ciclo de vida de las transacciones](#el-ciclo-de-vida-de-las-transacciones)
    - [Estado de la transacción](#estado-de-la-transacción)
    - [El bloque pendiente](#el-bloque-pendiente)
    - [Recibo de transacción](#recibo-de-transacción)
- [Verificadores en Solidity](#verificadores-en-solidity)
    - [Ejemplo de una transacción de verificación](#ejemplo-de-una-transacción-de-verificación)
    - [Rastreo de transacciones](#rastreo-de-transacciones)
    - [Contrato Verifier en la arquitectura de Starknet](#contrato-verifier-en-la-arquitectura-de-starknet)
- [Interactuando con SHARP](#interactuando-con-sharp)
    - [Pruebas con SHARP](#pruebas-con-sharp)
    - [RPC de Alchemy](#rpc-de-alchemy)

---

# Secuenciadores en Starknet

Los secuenciadores desempeñan un papel crucial en las soluciones blockchain y de Capa 2, ya que son responsables de gestionar el orden y la ejecución de las transacciones. En Starknet, los secuenciadores garantizan que las transacciones se ejecuten en un orden específico para prevenir el doble gasto y mantener la consistencia del estado de la red.

Los secuenciadores reciben transacciones, las procesan según reglas predefinidas y producen una serie de acciones u cambios de estado ordenados. Estas acciones se agrupan en bloques y se incluyen en la cadena de bloques.

![secuenciador](sequencer.png)

## El papel de los Secuenciadores

Los secuenciadores desempeñan un papel vital en el rendimiento y la seguridad de Starknet al ejecutar transacciones y aplicar cambios de estado según las reglas y requisitos del protocolo.

El secuenciador de Starknet es especialmente relevante debido al uso de pruebas de conocimiento cero (ZKPs, por sus siglas en inglés). El correcto funcionamiento del secuenciador garantiza que las transacciones se ejecuten de manera compatible con la generación de ZKPs, lo que contribuye a la escalabilidad y seguridad general de Starknet. Los usuarios interactúan con los secuenciadores al enviar transacciones a Starknet, siendo este el primer paso hacia el escalado de STARK. Una vez que un secuenciador ensambla un bloque, el protocolo de consenso lo aprueba y los probadores generan una prueba para la Capa 1 (L1).

En Ethereum, los mineros ordenan y ejecutan las transacciones, mientras que los secuenciadores cumplen un propósito similar en Starknet. Los secuenciadores procesan las transacciones, las agregan y producen bloques, lo que los convierte en una pieza fundamental de la infraestructura del ecosistema de Starknet.

A medida que Starknet evoluciona, es crucial mejorar el rendimiento de los secuenciadores. Aquí es donde se vuelve fundamental la transición a secuenciadores basados en Rust. Al reescribir los componentes principales del secuenciador en Rust, la red puede lograr un mejor rendimiento, mayor capacidad y mayor resistencia.

Algunos ejemplos de implementaciones de secuenciadores incluyen [Starknet Tendermint Sequencer](https://github.com/lambdaclass/starknet_tendermint_sequencer) de LambdaClass y [Madara](https://github.com/keep-starknet-strange/madara), un secuenciador de Starknet impulsado por Rust utilizando Substrate.

## La evolución de Starknet y los Secuenciadores

Inicialmente, los secuenciadores se escribieron en Python, lo cual resultó ineficiente para operaciones a gran escala. A medida que la red alcanzaba su capacidad máxima, los desarrolladores buscaron mejorar el rendimiento de los secuenciadores. El primer hito fue establecer una cadena de bloques completamente funcional, seguida de un enfoque en la optimización del rendimiento.

La mejora inicial de rendimiento implicó la implementación de concurrencia optimista para la ejecución paralela de transacciones. Sin embargo, el avance más significativo se produjo al reescribir los secuenciadores

---

# SHARP (Shared Prover) en Starknet

SHARP es como el transporte público para las pruebas en Starknet, que agrega múltiples programas Cairo para ahorrar costos y aumentar la eficiencia. Utiliza pruebas recursivas que permiten la paralelización y optimización, lo que lo hace más asequible para todos los usuarios. Los servicios críticos como la pasarela, el validador y el Prover trabajan juntos con un diseño sin estado para mayor flexibilidad. La adopción de SHARP por parte de StarkEx, Starknet y usuarios externos (a través de Cairo Playground) resalta su importancia y potencial para la optimización futura.

Veámos cómo SHARP, ha evolucionado para incorporar pruebas recursivas y su papel en la reducción de costos y mejora de la eficiencia dentro de la red Starknet.

## ¿Qué es SHARP?

SHARP, que significa `Shared Prover` (Proveedor Compartido), es un mecanismo utilizado en Starknet que agrupa múltiples programas Cairo de diferentes usuarios, cada uno con una lógica diferente. Estos programas Cairo se ejecutan juntos, generando una prueba única común a todos los programas. En lugar de enviar la prueba directamente al verificador de Solidity en Ethereum, se envía inicialmente a un programa Verificador STARK escrito en Cairo. El Verificador STARK genera una nueva prueba para confirmar que las pruebas iniciales fueron verificadas, que luego puede enviarse de vuelta a SHARP y al Verificador STARK usando el  proceso de prueba recursiva que se discutirá en más detalle más adelante en este capítulo. En última instancia, la última prueba de la serie se envía al Verificador de Solidity en Ethereum. En otras palabras, se generan muchas pruebas hasta llegar a Ethereum y al Verificador de Solidity.

El principal beneficio del sistema SHARP radica en su capacidad para reducir costos y mejorar la eficiencia dentro de la red de Starknet. Esto se logra al agrupar múltiples trabajos de Cairo, que son conjuntos individuales de cálculos. Esta agregación permite que el protocolo aproveche la amortización exponencial ofrecida por las pruebas STARK.

La amortización exponencial significa que a medida que aumenta la carga computacional de las pruebas, el costo de verificar esas pruebas aumenta a una tasa logarítmica más lenta que el aumento de la computación. En otras palabras, la computación en sí crece más lentamente que el costo de verificación. Como resultado, el costo de cada transacción dentro del conjunto agregado se reduce significativamente, lo que hace que el proceso general sea más rentable y accesible para los usuarios.

En el contexto de SHARP y Cairo, `trabajos` se refiere a los programas Cairo individuales o tareas presentados por diferentes usuarios. Estos trabajos contienen lógica o cálculos específicos que deben ejecutarse en la red de Starknet.

Además, SHARP permite que usuarios más pequeños con recursos limitados se beneficien al unirse a otros trabajos y compartir el costo de generar las pruebas. Este enfoque colaborativo es similar a usar el transporte público en lugar de un automóvil privado, donde el costo se distribuye entre todos los participantes, lo que lo hace más asequible para todos.

## Pruebas Recursivas en SHARP

Una de las características más poderosas de SHARP es su uso de pruebas recursivas. En lugar de enviar directamente las pruebas generadas al Verificador de Solidity, primero se envían a un programa Verificador STARK escrito en Cairo. Este Verificador, que también es un Programa Cairo, recibe la prueba y crea un nuevo trabajo de Cairo que se envía al Proveedor. El Proveedor luego genera una nueva prueba para confirmar que las pruebas iniciales fueron verificadas. Estas nuevas pruebas se pueden enviar de vuelta a SHARP y al Verificador STARK, reiniciando el proceso.

Este proceso continúa de forma recursiva, enviando cada nueva prueba al Verificador de Cairo hasta que se alcanza un disparador. En este punto, la última prueba de la serie se envía al Verificador de Solidity en Ethereum. Este enfoque permite una mayor paralelización de la computación y reduce el tiempo y los costos asociados con la generación y verificación de pruebas.

```bash
    Pruebas Generadas
            |
            V
Programa Verificador STARK (en Cairo)
            |
            V
     Trabajo de Cairo
            |
            V
        Proveedor
            |
            V
  Nueva Prueba Generada
            |
            V
    Repetir Proceso
            |
            V
Disparador Alcanzado (última prueba)
            |
            V
Verificador de Solidity
```

A primera vista, las pruebas recursivas pueden parecer más complejas y consumir más tiempo. Sin embargo, hay varios beneficios en este enfoque:

* Paralelización: Las pruebas recursivas permiten la paralelización del trabajo, reduciendo la latencia del usuario y mejorando la eficiencia de SHARP.
* Menores costos en la cadena: La paralelización permite que SHARP cree pruebas más grandes, que anteriormente se limitarían por la disponibilidad de máquinas en la nube grandes (que son escasas y limitadas). Como resultado, los costos en la cadena se reducen.
* Menores costos en la nube: Dado que cada trabajo es más corto, se reduce la memoria requerida para el procesamiento, lo que resulta en menores costos en la nube.
* Optimización: Las pruebas recursivas permiten que SHARP se optimice para varios factores, incluyendo la latencia, los costos en la cadena y el tiempo de prueba.
* Compatibilidad con Cairo: Las pruebas recursivas solo requieren soporte en Cairo, sin necesidad de agregar soporte en el Verificador de Solidity.

La latencia en Starknet abarca el tiempo que lleva procesar, confirmar e incluir transacciones en un bloque. Está afectada por factores como la congestión de la red, las tarifas de transacción y la eficiencia del sistema. Minimizar la latencia garantiza un procesamiento de transacciones más rápido y una retroalimentación del usuario más rápida.

El tiempo de prueba, por otro lado, se refiere específicamente a la duración requerida para generar y verificar pruebas criptográficas para transacciones u operaciones.

## Arquitectura del Backend y Canalización de Datos de SHARP

La arquitectura del backend de SHARP consta de varios servicios que trabajan en conjunto para procesar trabajos de Cairo y generar pruebas. Estos servicios incluyen:

* *Gateway*: Los trabajos de Cairo ingresan a SHARP a través del gateway.
* *Job Creator*: Evita la duplicación de trabajos y garantiza que el sistema funcione de manera consistente, independientemente de múltiples solicitudes idénticas.
* *Validator*: Este es el primer paso importante. El servicio de validación realiza verificaciones en cada trabajo, asegurándose de que cumplan con los requisitos y puedan adaptarse a las máquinas de prueba. Los trabajos inválidos se marcan como tales y no continúan hacia el Proveedor.
* *Scheduler*: El servicio de planificación crea `trains` que agrupan trabajos y los envían al Proveedor. Los trabajos recursivos se emparejan y se envían juntos al Proveedor.
* *Cairo Runner*: Este servicio ejecuta Cairo según las necesidades del Proveedor. El servicio de ejecución de Cairo ejecuta programas de Cairo, realiza los cálculos necesarios y genera el rastro de ejecución como un resultado intermedio. Luego, el Proveedor utiliza este rastro de ejecución.
* *Prover*: El Proveedor calcula las pruebas para cada "train" (que contiene algunos trabajos).
* *Dispatcher*: El Despachador cumple dos funciones en el sistema SHARP.
    * En el caso de una prueba recursiva, el Despachador ejecuta el programa Verificador de Cairo en la prueba que ha recibido del Proveedor, lo que resulta en un nuevo trabajo de Cairo que vuelve al Validador.
    * En el caso de una prueba que debe enviarse a la cadena (por ejemplo, a Ethereum), el Despachador crea "paquetes" a partir de la prueba, que luego se pueden enviar al Escritor de Blockchain.
* *Blockchain Writer*: Una vez que los paquetes han sido creados por el Despachador, se envían al Escritor de Blockchain. El Escritor de Blockchain es responsable de enviar los paquetes a la cadena de bloques correspondiente (por ejemplo, Ethereum) para su verificación. Este es un paso importante en el sistema SHARP, ya que garantiza que las pruebas se verifiquen correctamente y que las transacciones se registren de manera segura en la cadena de bloques.
* *Catcher*: El Catcher monitorea las transacciones en la cadena de bloques (por ejemplo, Ethereum) para garantizar que hayan sido aceptadas. Si bien el Catcher es relevante para fines de monitoreo interno, es importante tener en cuenta que si una transacción falla, el hecho no se registrará en el registro de hechos de la cadena de bloques. Como resultado, la integridad del sistema se mantiene incluso sin el Catcher.

SHARP está diseñado para ser stateless (cada trabajo de Cairo se ejecuta en su propio contexto y no depende de otros trabajos), lo que permite una mayor flexibilidad en el procesamiento de trabajos.

### Usuarios Actuales de SHARP

Actualmente, los principales usuarios de SHARP incluyen:

* StarkEx
* Starknet
* Usuarios externos que utilizan el Cairo Playground

## Desafíos y Optimización

Optimizar el Proveedor implica numerosos desafíos y proyectos potenciales en los que el equipo de Starkware y la comunidad están trabajando actualmente:

* Exploración de funciones hash más eficientes: SHARP está constantemente explorando funciones hash más eficientes para Cairo, el Proveedor y Solidity.
* Investigación de campos más pequeños: La investigación de campos más pequeños para los pasos de prueba recursiva podría conducir a cálculos más eficientes.
* Ajuste de varios parámetros: SHARP está ajustando constantemente varios parámetros del protocolo STARK, como los parámetros FRI y los factores de bloque.
* Optimización del código de Cairo: SHARP está optimizando el código de Cairo para hacerlo más rápido, lo que resulta en un Proveedor recursivo más rápido.
* Desarrollo de diseños dinámicos: Esto permitirá a los programas de Cairo adaptar los recursos según sus necesidades.
* Mejora del algoritmo de programación: Este es otro camino de optimización que se puede tomar. No está dentro del Proveedor en sí.

En particular, los Dynamic Layouts o diseños dinámicos, permitirán que los programas de Cairo adapten los recursos según sus necesidades. Esto puede llevar a una computación más eficiente y a una mejor utilización de los recursos. Los diseños dinámicos permiten a SHARP determinar los recursos necesarios para un trabajo específico y ajustar el diseño en consecuencia en lugar de depender de diseños predefinidos con recursos fijos. Este enfoque puede proporcionar soluciones personalizadas para cada trabajo, mejorando la eficiencia general.

En conclusión, SHARP es un componente crítico de la arquitectura de Starknet, proporcionando una solución más eficiente y rentable para procesar programas de Cairo y verificar sus pruebas. Al aprovechar el poder de la tecnología STARK e incorporar pruebas recursivas, SHARP desempeña un papel vital en la mejora del rendimiento general y la escalabilidad de la red de Starknet. La naturaleza stateless de SHARP y la dependencia de la solidez criptográfica del sistema de prueba STARK lo convierten en una adición innovadora y valiosa al ecosistema de blockchain.

---

# Storage Proof: Acceso Trustless Cross-Chain a la información en Starknet

Las pruebas de almacenamiento han sido una parte esencial del panorama criptográfico desde la invención de los árboles de Merkle en 1979. La innovación radica en combinar las pruebas de almacenamiento con la computación demostrable para crear pruebas sucintas que puedan verificarse en la cadena.

En el mundo de los sistemas descentralizados y la tecnología blockchain, garantizar la exactitud y autenticidad de los datos es de vital importancia. A medida que el ecosistema evoluciona, se hace cada vez más necesario compartir información a través de diferentes cadenas, lo que lleva al desarrollo de soluciones innovadoras para verificar la integridad de los datos sin sacrificar la seguridad o la eficiencia. Una de estas soluciones es el uso de Storage Proof o pruebas de almacenamiento.

Las Storage Proof ofrecen un método criptográfico para rastrear y compartir información de blockchain entre cadenas, similar a los oráculos. Sin embargo, la diferencia clave radica en el modelo de confianza; las pruebas de almacenamiento proporcionan intrínsecamente una prueba de autenticidad sin depender de la confianza de terceros. En algunas situaciones, pueden incluso sustituir o complementar a los oráculos, allanando el camino para nuevos casos de uso y aplicaciones en el ecosistema blockchain.

Más información sobre Storage Proof del [blog oficial Starkware](https://medium.com/@starkware/what-are-storage-proofs-and-how-can-they-improve-oracles-e0379108720a) 

imagen::almacenamiento5.png[almacenamiento5]

## ¿Qué son las Storage Proof?

Las pruebas de almacenamiento son una forma de demostrar que cierta información existe en una cadena de bloques y es verdadera, sin tener que confiar en terceros. Para entender mejor las pruebas de almacenamiento, vamos a desglosar el concepto utilizando una sencilla analogía:

Imagina que tienes un libro gigante lleno de información y quieres demostrar que un dato concreto está en ese libro. En lugar de pedir a alguien que revise todo el libro para encontrar la información, se utiliza un sistema inteligente (en nuestro caso, la criptografía) para crear una pequeña `prueba` que pueda demostrar fácilmente la presencia de la información en el libro. Esta `prueba` es lo que llamamos una Storage Proof.

En el contexto de las cadenas de bloques, estos libros gigantes son las bases de datos que almacenan todas las transacciones y datos de la red. Las Storage Proof permiten crear una prueba pequeña y verificable de que ciertos datos existen dentro del estado del blockchain en un momento determinado. Para ello se utilizan técnicas criptográficas incorporadas al propio almacenamiento.

Las cadenas de bloques utilizan varias estructuras de datos, como los árboles Merkle, los árboles Merkle Patricia y los árboles Verkle, para comprometer criptográficamente sus datos. Utilizando estas estructuras de datos, se pueden generar pruebas de almacenamiento para demostrar que una información específica forma parte de un estado determinado. Sin embargo, cuando se utilizan solas, estas pruebas pueden llegar a ser bastante grandes, lo que las hace poco prácticas para la verificación en cadena. Para superar este problema, las pruebas de almacenamiento suelen combinarse con técnicas criptográficas avanzadas, como STARK o SNARK, para crear pruebas más pequeñas y eficientes que puedan verificarse en cualquier dominio sin confiar en terceros. En su lugar, la seguridad y la confianza proceden de la propia blockchain subyacente.

## Compromisos criptográficos y estructuras de datos

Las pruebas de almacenamiento se basan en compromisos criptográficos y estructuras de datos específicas para verificar la autenticidad de los datos sin revelar todo el conjunto de datos. Estas estructuras de datos, como los árboles de Merkle, los árboles de Merkle Patricia o los árboles de Verkle, permiten almacenar y recuperar datos de forma eficiente, al tiempo que mantienen la seguridad criptográfica de todo el conjunto de datos.

Cuando se añade un dato a la cadena de bloques, se le aplica un hash y se almacena en una ubicación específica dentro del árbol. Estos datos, junto con otros del árbol, se combinan mediante un algoritmo hash para crear nodos raíz. Este proceso continúa hasta que se obtiene un único hash raíz, conocido como raíz Merkle o raíz Verkle. Este hash raíz representa todo el conjunto de datos y sirve como compromiso de los datos.

## Generación y verificación de Storage Proof

Para generar una Storage Proof, se debe proporcionar un dato específico y su ruta Merkle o Verkle asociada dentro del árbol. La ruta consiste en los hashes hermanos necesarios para reconstruir el hash raíz utilizando el mismo algoritmo de hashing. La prueba de almacenamiento es esencialmente la combinación de los datos y su ruta Merkle o Verkle.

Al verificar una prueba de almacenamiento, el destinatario puede utilizar los datos proporcionados y la ruta Merkle o Verkle para volver a calcular el hash raíz. Si el hash raíz recalculado coincide con el hash raíz conocido, el receptor puede estar seguro de que los datos son auténticos y forman parte del conjunto de datos comprometido.

## Optimización de las Storage Proof con STARKs o SNARKs

Aunque las pruebas de almacenamiento proporcionan una forma segura y eficiente de demostrar la existencia de datos en una cadena de bloques, su tamaño y requisitos computacionales pueden optimizarse aún más utilizando pruebas de conocimiento cero, como STARK (Scalable Transparent ARguments of Knowledge) o SNARK (Succinct Non-interactive ARguments of Knowledge).

Estas técnicas criptográficas avanzadas permiten crear pruebas más pequeñas y eficaces que pueden verificarse rápidamente, incluso en cadena. Combinando pruebas de almacenamiento con STARKs o SNARKs, podemos conseguir un intercambio de datos entre cadenas fiable y eficiente sin depender de terceros.

## Casos de uso de las Storage Proof

Las Storage Proof ofrecen un medio eficaz de comprimir datos de blockchain y transmitirlos a otras cadenas, lo que abre un amplio abanico de aplicaciones. Sus asequibles costes de verificación permiten a las cadenas de destino validar la prueba, reduciendo la necesidad de complejos sistemas de mensajería entre cadenas.

Algunos posibles casos de uso de las Storage Proof son:

* Acceso general a la información: Las pruebas de almacenamiento permiten a una cadena acceder a información sobre el estado y las transacciones de otra cadena, lo que facilita la comunicación entre cadenas.

* Sistemas simplificados de votación entre cadenas: Las pruebas de almacenamiento pueden agilizar los procesos de votación entre cadenas en los que los usuarios mantienen activos en una cadena segura pero más lenta (Cadena A), mientras que la votación tiene lugar en una cadena más rápida y rentable (Cadena B). En lugar de incurrir en elevadas tasas de transacción para transferir activos entre cadenas, las pruebas de almacenamiento permiten a los usuarios demostrar su saldo de tokens en la cadena A y emitir su voto sin problemas en la cadena B.

* Puentes entre cadenas sin confianza: Los actuales puentes entre cadenas suelen implicar a terceros intermediarios, como custodios u organizaciones autónomas descentralizadas (DAO), que requieren un cierto nivel de confianza. Las pruebas de almacenamiento pueden facilitar los puentes sin confianza, con contratos inteligentes en la cadena de destino que validan las transacciones y acuñan los activos del puente basándose en la prueba. En algunos casos, las pruebas de almacenamiento pueden incluso eliminar la necesidad de transferir activos entre cadenas, simplemente demostrando la propiedad en la cadena original.

image::almacenamiento1.png[almacenamiento1]

* Mejora de la experiencia del usuario en los casos de uso de abstracción de cuentas (AA): La AA, una innovación clave en la tecnología blockchain, se ha implantado en varias cadenas para mejorar la incorporación de usuarios. Las pruebas de almacenamiento pueden aumentar aún más la AA, permitiendo a los monederos restablecer el acceso en función de condiciones específicas, como la inactividad, o aplicar comprobaciones adicionales que requieran datos de otras cadenas.

* *Acceso a datos históricos sin ejecutar un nodo de archivo*: Las pruebas de almacenamiento no sólo se utilizan para casos de uso entre cadenas, también pueden utilizarse para obtener datos sobre el pasado en la misma blockchain sin necesidad de ejecutar un nodo de archivo. Esto permite a los usuarios acceder a datos históricos de forma más eficiente, reduciendo los requisitos de almacenamiento y disminuyendo la barrera de entrada para participar en la red.

Al aprovechar el poder de las pruebas de almacenamiento, estos casos de uso demuestran el potencial de interacciones más eficientes, seguras y fluidas dentro del ecosistema blockchain.

## Ejemplo de Storage Proof

Generar Storage Proof en cadenas compatibles con EVM es sencillo. Por ejemplo, la biblioteca Web3.js tiene la función `getProof` que puede generar pruebas del estado de un contrato en Ethereum (y otras cadenas compatibles con EVM como Polygon o BSC). Una dirección de contrato y la ranura de almacenamiento para el contrato deben ser pasados a la función.

En Ethereum, los contratos inteligentes utilizan un almacén de clave-valor para almacenar los datos en su almacén. Cada dato se almacena en una ubicación específica conocida como `ranura de almacenamiento`. Las ranuras de almacenamiento son ubicaciones de memoria dentro del almacenamiento del contrato y se identifican mediante un índice único. Veamos un ejemplo de contrato inteligente con el siguiente código desplegado en la mainnet de Ethereum en `0xcc...da8b`.

image::storage2.png[storage2]

La variable `owner` se almacenaría en la ranura 0. Ahora, para generar la prueba de que el `owner` de este contrato era una dirección A, podemos utilizar la función `getProof` de la siguiente manera:

imagen::almacenamiento3.png[almacenamiento3]

El resultado del código anterior es algo parecido a esto:

imagen::almacenamiento1.png[almacenamiento4]

El `storageProof` devuelto contiene la prueba de almacenamiento para la variable `owner`. Dado que Ethereum utiliza Merkle Patricia Trees para comprometer su estado, el estado de las cuentas y su almacenamiento, el almacenamiento generado puede ser utilizado para probar una ranura de almacenamiento (o estado de la cuenta). Sin embargo, como se ha indicado anteriormente, estas pruebas no son lo suficientemente escalables como para discutir las transferencias de mensajes entre cadenas. El uso de complejas matemáticas ZK sobre esto puede disminuir el cálculo necesario para verificar la prueba.

## Comparación y contraste de Storage Proof y oráculos

Las cadenas de bloques están diseñadas para no generar confianza, lo que significa que no pueden acceder intrínsecamente a datos fuera de la cadena. Esta limitación afecta a la capacidad de los contratos inteligentes para tomar decisiones basadas en eventos del mundo real o en información histórica de la cadena de bloques. Como solución, se introdujeron los oráculos para proporcionar a los contratos inteligentes datos fuera de la cadena o resultados de cálculos fuera de la cadena que consumen muchos recursos.

Los oráculos suelen requerir que un tercero, como una institución o una red descentralizada de operadores de nodos, envíe datos a la cadena. Aunque esto introduce un nivel de confianza, varios equipos, como Pragma, están trabajando para minimizar este requisito de confianza.

Chainlink es un conocido oráculo de blockchain que proporciona datos del mundo real, servicios de cálculo fuera de la cadena y servicios entre cadenas. Dado que los contratos inteligentes dependen actualmente de los oráculos para obtener datos del mundo real, los oráculos se han convertido en una parte crucial del ecosistema blockchain.

## El estado de los oráculos en Starknet

En la red de pruebas de Starknet, Chainlink proporciona datos de precios para siete pares de criptomonedas y se ha asociado con Starkware para acelerar el desarrollo y crecimiento de aplicaciones en el ecosistema de Starknet. Chainlink minimiza los supuestos de confianza a través de una red descentralizada de nodos, pero la agregación de datos se produce fuera de la cadena.

Pragma y Stork Network son dos importantes proveedores de oráculos en Starknet, que operan tanto en la mainnet como en la testnet. Ofrecen tickers de precios para múltiples pares de criptomonedas, y Pragma está trabajando en la implementación de una fuente de aleatoriedad verificable en la mainnet para una aleatoriedad segura en la cadena. Las fuentes de precios de Pragma se basan en envíos de grandes instituciones y creadores de mercado, aprovechando la eficiente tecnología ZK para la agregación de precios en la cadena.

En resumen, tanto las pruebas de almacenamiento como los oráculos son cruciales para facilitar la comunicación entre cadenas y el acceso a datos fuera de la cadena. Mientras que los oráculos son indispensables para los datos del mundo real, las pruebas de almacenamiento ofrecen una forma fiable y eficiente de transferir y verificar la información de la cadena de bloques entre cadenas. Juntos, ayudan a crear un ecosistema de cadenas de bloques más sólido e interconectado.

## ¿Pueden los oráculos ser sustituidos o mejorados por Storage Proof?

En algunos casos, las Storage Proof pueden sustituir a los oráculos. Algunos datos proporcionados por los oráculos ya están disponibles en la cadena, y una prueba de almacenamiento puede eliminar la necesidad de confiar en un tercero, permitiendo que los contratos inteligentes se basen por completo en la seguridad de los compromisos criptográficos. Sin embargo, en otros casos en los que las pruebas de almacenamiento no pueden sustituir completamente a los oráculos, pueden mejorarlos con una funcionalidad adicional:

* Las pruebas de almacenamiento permiten realizar cálculos sobre datos de diferentes fuentes y exportar los resultados a otras cadenas, haciendo posible que los oráculos transmitan información a través de múltiples cadenas.
* Las pruebas de almacenamiento pueden facilitar una validación rentable en las cadenas de destino, ya que la cadena de origen preferida suele tener un cálculo barato.
* Líderes en investigación, como Herodotus, permiten el acceso a datos entre dominios a través de cadenas Ethereum utilizando pruebas de almacenamiento y matemáticas ZK. Pragma planea asociarse con Herodotus para soportar oráculos entre cadenas en un futuro próximo.
* Las pruebas de almacenamiento pueden unificar el estado de múltiples rollups e incluso permitir lecturas síncronas entre capas Ethereum.
* La recuperación fiable de datos históricos de la cadena es otra mejora posible gracias a las pruebas de almacenamiento. Las cadenas de bloques con estado, como Ethereum y Starknet, preservan criptográficamente su estado a través de estructuras de datos especializadas, lo que permite probar la inclusión de datos. Esto permite a los contratos inteligentes acceder a información que se remonta al bloque de génesis.

Pragma está explorando el desarrollo de un oráculo L3 en Starknet, que podría permitir a otras cadenas `extraer` y verificar datos utilizando Storage Proof. Los beneficios de tener un oráculo L3 en una red computacionalmente barata como Starknet incluyen:

* Consenso más rápido en bloques debido a la cadena L3 altamente personalizable, reduciendo significativamente la latencia de datos para el oráculo.
* Transferencia asíncrona de datos de baja latencia a otras cadenas al alcanzar el consenso en la cadena de origen, en combinación con pruebas de almacenamiento.
* Mayor confianza en los datos mediante un sistema incorporado para penalizar a los proveedores de datos deshonestos. Los proveedores de datos en la L3 podrían poner en juego sus activos como garantía de la exactitud de los datos. Como toda la red L3 debe alcanzar un consenso antes de que otras cadenas puedan utilizar los datos, los datos del oráculo pueden considerarse garantizados por la apuesta de los validadores en L3.

La creciente adopción de soluciones L2 de Ethereum, como Starknet, Optimism y Arbitrum, ha permitido vislumbrar el futuro del sector. Sin embargo, un reto clave que impide un mayor crecimiento es la implementación de un sistema descentralizado de mensajería entre cadenas. Aunque todavía se encuentran en una fase incipiente, las pruebas de almacenamiento tienen un enorme potencial para resolver este problema.

En algunos casos, las pruebas de almacenamiento pueden sustituir o mejorar los oráculos, facilitando una comunicación entre cadenas más eficiente y el acceso a datos históricos. Al reducir la dependencia de la confianza en terceros, las pruebas de almacenamiento pueden reforzar significativamente la seguridad y la eficiencia de las aplicaciones de blockchain.

A medida que el panorama de las cadenas de bloques siga evolucionando, podemos anticipar nuevos desarrollos e innovaciones en pruebas de almacenamiento, oráculos y comunicación entre cadenas. Aprovechando estas tecnologías, el ecosistema blockchain puede mantener su crecimiento y ofrecer más valor tanto a los usuarios como a los desarrolladores.

---

# Bloques en Starknet

En Starknet, un bloque consta de una lista de transacciones y un encabezado de bloque. Los bloques representan lotes de transacciones vinculados entre sí por el hash del bloque anterior en la cadena. Esta conexión de bloques forma una cadena, ya que los hashes se generan criptográficamente a partir de los datos del bloque. Esta estructura garantiza la seguridad y previene el fraude, ya que cualquier alteración en un bloque histórico invalidaría todos los bloques posteriores debido a los cambios en sus hashes. En consecuencia, todos los participantes en la cadena de bloques detectarían rápidamente la discrepancia.

## Encabezado del bloque

El encabezado del bloque consta de los siguientes campos:

```bash
| Nombre | Tipo | Descripción | Implementado
| parent_block_hash | FieldElement | El hash del bloque padre | ✓
| block_number | Integer | El número (altura) de este bloque | ✓
| global_state_root | FieldElement | El compromiso de estado después de este bloque | ✓
| sequencer_address | FieldElement | La dirección de Starknet del secuenciador que creó este bloque | ✓
| block_timestamp | Timestamp | El momento en que el secuenciador creó este bloque antes de ejecutar las transacciones | ✓
| transaction_count | Integer | El número de transacciones en un bloque | ✓
| transaction_commitment | FieldElement | Un compromiso con las transacciones incluidas en el bloque | ✓
| event_count | Integer | El número de eventos | ✓
| event_commitment | FieldElement | Un compromiso con los eventos producidos en este bloque | ✓
| protocol_version | Integer | La versión del protocolo Starknet utilizada al crear este bloque |
| extra_data | FieldElement | Datos adicionales que pueden ser útiles para ejecutar transacciones |
```

**NOTA** Los campos de compromiso *event_commitment* y *transaction_commitment* son las raíces de un árbol Patricia Merkle binario de altura 64.

La hoja en el índice _i_ corresponde al hash del *_i-ésimo_* evento para *event_commitment* y *_h(transaction hash, signature)_* para *invoke transaction_commitment*.

Para otros tipos de transacciones, utilizamos *h(0,0)*.

## Hash del bloque

El hash del bloque se define como el hash de Pedersen sobre los elementos del encabezado.

Poseidon es una familia de funciones hash diseñadas para ser muy eficientes en circuitos algebraicos. Como tal, pueden ser muy útiles en sistemas de demostración de conocimiento cero como STARKs y otros.

```bash
h(B) = h(block_number, global_state_root, sequencer_address, block_timestamp, transaction_count, transaction_commitment, event_count, event_commitment, 0, 0, parent_block_hash)
```

Donde *_h_* es el hash de Pedersen.

**NOTA** Los ceros dentro del cálculo de hash de un objeto se utilizan como marcadores de posición, que se reemplazarán en el futuro por campos significativos.

---

# El ciclo de vida de las transacciones

En Starknet Alpha, existen dos tipos de transacciones: `DEPLOY` (implementación) e `INVOKE` (invocación). Pasan por el siguiente ciclo de vida a medida que se envían desde los clientes al secuenciador:

```bash
NOT_RECEIVED \-> RECEIVED \-> PENDING \-> REJECTED || ACCEPTED_ON_L2 \-> ACCEPTED_ON_L1
```

## Estado de la transacción

### NOT_RECEIVED

La transacción aún no es conocida por el secuenciador.

### RECEIVED

La transacción fue recibida por el secuenciador. Ahora, la transacción se ejecutará con éxito o será rechazada.

### PENDING

La transacción se ejecutó correctamente y se agregó al bloque pendiente.

### REJECTED

La transacción se ejecutó sin éxito y, por lo tanto, se omitió. O el bloque puede ser rechazado en L1, lo que resulta en un estado de *REJECTED*.

### ACCEPTED_ON_L2

La transacción pasó la validación y se agregó a un bloque creado real en Starknet.

### ACCEPTED_ON_L1

La transacción fue aceptada en la cadena principal (L1).

## El bloque pendiente

Durante la construcción del bloque, a medida que se acumulan nuevas transacciones, el estado del bloque es *PENDING* (pendiente). Mientras el estado sea *PENDING*, se agregan dinámicamente nuevas transacciones al bloque. Una vez que el secuenciador decide *cerrar* el bloque, pasa a ser *ACCEPTED_ON_L2* y se calcula su hash.

## Recibo de transacción

El recibo de transacción contiene detalles básicos de la transacción (identificadores de bloque y el índice dentro del bloque), un resumen de los recursos de ejecución utilizados por la transacción, los eventos emitidos, una lista de mensajes enviados a L1 y un mensaje de L1 consumido (en caso de que la transacción invoque un controlador de L1). A continuación, se muestra un ejemplo de un recibo:

```c
{
  "execution_resources": {
    "builtin_instance_counter": {
      "pedersen_builtin": 0,
      "range_check_builtin": 0,
      "bitwise_builtin": 0,
      "output_builtin": 0,
      "ecdsa_builtin": 0,
      "ec_op_builtin": 0
    },
    "n_steps": 178,
    "n_memory_holes": 0
  },
  "block_number": 6807,
  "transaction_index": 0,
  "transaction_hash": "0x3f187b7522320f1c87271772fedd6ad119f62595e2d9208824367463df94a5d",
  "status": "PENDING",
  "block_hash": "0x23173d4e2d5c0ecc1376b8dbe345c028aa424048c67f68812a9a83873a2d87f",
  "l2_to_l1_messages": [],
  "events": [
    {
      "data": ["0", "4321"],
      "from_address": "0x14acf3b7e92f97adee4d5359a7de3d673582f0ce03d33879cdbdbf03ec7fa5d",
      "keys": [
        "1744303484486821561902174603220722448499782664094942993128426674277214273437"
      ]
    }
  ]
}
```

# Verificadores en Solidity

Contrato inteligente en L1 que verifica pruebas STARK de SHARP.

- [Starkware Sharp Verifier](https://etherscan.io/address/0x47312450b3ac8b5b8e247a6bb6d523e7605bdb60): Contrato proxy
- [GpsStatementVerifier](https://etherscan.io/address/0x6cb3ee90c50a38a0e4662bb7e7e6e40b91361bf6): Contrato que verifica una prueba y registra los hechos correspondientes.
- [CpuFrilessVerifier](https://etherscan.io/address/0xe9664d230490d5a515ef7ef30033d8075a8d0e24)
- [MemoryPageFactRegistry](https://etherscan.io/address/0xfd14567eaf9ba941cb8c8a94eec14831ca7fd1b4)

## Ejemplo de una transacción de verificación

Veamos una transacción que se envió al contrato Verifier en la red principal de Ethereum. [0xde0bcb45698e8ef67fef8bff8615715dac0cf0882a131a21273e58607330d5f9](https://etherscan.io/tx/0xde0bcb45698e8ef67fef8bff8615715dac0cf0882a131a21273e58607330d5f9)

### Rastreo de la transacción

* El contrato Verifier es un contrato proxy, por lo que cuando se llama, en realidad se llama al contrato de implementación. El contrato de implementación es el contrato GpsStatementVerifier. La transacción que estableció la última implementación se puede encontrar [aquí](https://etherscan.io/tx/0xbaa8ffb1b7e5177dbf75de753b9b2ff2fc313b244ff910cfd6d7f1f6254b6e1a).

* En el contrato GpsStatementVerifier, se ejecutó la función `verifyProofAndRegister` con el siguiente rastreo, puedes verificar su código [aquí](https://github.com/starkware-libs/starkex-contracts/blob/aecf37f2278b2df233edd13b686d0aa9462ada02/evm-verifier/solidity/contracts/gps/GpsStatementVerifier.sol#L71):

    - Llamada a `MemoryPageFactRegistry.registerRegularMemoryPage()`, código disponible [aquí](https://github.com/starkware-libs/starkex-contracts/blob/aecf37f2278b2df233edd13b686d0aa9462ada02/evm-verifier/solidity/contracts/cpu/MemoryPageFactRegistry.sol#L31). Esta función:
        . Calcula y almacena el factHash en el mapeo verifiedFact.
        . Emite un evento LogMemoryPageFactRegular con factHash = `0x72C8D723081B60693B32D77A369782FF189754A5897002314FB1569B7E2EE1E2`

    - Llamada a `CpuFrilessVerifier.verifyProofExternal()`, código disponible [aquí](https://github.com/starkware-libs/starkex-contracts/blob/aecf37f2278b2df233edd13b686d0aa9462ada02/evm-verifier/solidity/contracts/cpu/CpuVerifier.sol#L54), que conduce al contrato [StarkVerifier](https://github.com/starkware-libs/starkex-contracts/blob/aecf37f2278b2df233edd13b686d0aa9462ada02/evm-verifier/solidity/contracts/StarkVerifier.sol#L500).
    
    - A continuación, se realiza una `staticcall` a MemoryPageFactRegistry.isValid() con el argumento que es el factHash mencionado anteriormente, y se obtiene la respuesta true.

    - Puedes verificar por ti mismo consultando la función `isValid` en el contrato MemoryPageFactRegistry con la entrada `0x72C8D723081B60693B32D77A369782FF189754A5897002314FB1569B7E2EE1E2` para obtener `true` como resultado.

## # Contrato Verifier en la arquitectura de Starknet

El ejemplo anterior muestra cómo se verifica y registra una prueba en Ethereum, y ofrece una idea de cómo se utiliza el contrato Verifier en la arquitectura de Starknet.

En general, SHARP toma la traza de ejecución del programa Cairo, demuestra que es válida y envía esta prueba al contrato Verifier en Ethereum. Después de verificar la prueba, el verificador en la cadena realiza un paso adicional importante: escribe un hecho que certifica la validez de la prueba en el Registro de Hechos (Fact Registry). Este hecho funciona como un sello de aprobación sin confianza, certificando que el programa Cairo se calculó correctamente. Ahora, todo lo que queda para el contrato inteligente de la dApp es verificar que este hecho exista para poder confiar en el cálculo que se realizó fuera de la cadena.

Aquí tienes el diagrama general de cómo interactúan entre sí cada uno de los componentes en la arquitectura de Starknet.

![Arquitectura de Starknet](starknet_architecture.png)


# Interactuando con SHARP

Cuando se envía un programa Cairo a SHARP, este utiliza un proceso denominado `"probabilidad mínima de desacuerdo"` para generar una prueba de la validez de su ejecución. Esto implica ejecutar el programa en un entorno de ejecución seguro y recopilar un conjunto de datos que demuestra que el programa se ha ejecutado de manera correcta y completa, siguiendo todas las reglas especificadas en el lenguaje Cairo. Una vez generada la prueba, SHARP la envía a la testnet de Ethereum Goerli, donde un contrato inteligente verificador la evalúa, si la prueba es válida, el contrato inteligente registra la ejecución del programa en la blockchain.

SHARP ofrece una interfaz de línea de comandos `(cairo-sharp)` y un API para facilitar el envío y la consulta de programas. Los usuarios pueden utilizar estas herramientas para enviar sus programas a SHARP y recibir actualizaciones sobre el progreso de la verificación. Una vez que un programa ha sido verificado y registrado en la blockchain, puede ser ejecutado de manera segura y confiable por cualquier persona que tenga acceso a la blockchain.

![Graph](/im%C3%A1genes/architecture.png)

SHARP es una plataforma valiosa para garantizar la seguridad y la verificabilidad de los contratos inteligentes escritos en Cairo. Su uso aumenta a medida que se desarrollen más aplicaciones basadas en el lenguaje. Sin embargo, es importante tener en cuenta que, en su forma actual, SHARP es un servicio centralizado que actúa como intermediario entre los programas Cairo y la blockchain. 

En Cairo 1.0 se está trabajando en la implementación de soluciones de gobernanza descentralizadas para mejorar la descentralización del proceso de verificación de programas.

Conocimientos o recursos necesarios, e información sobre SHARP:

* [Ajustes de Configuración de entorno](https://www.cairo-lang.org/docs/quickstart.html)
* También puede seguir David Barreto [Setting Starknet Dev-environment Python](https://medium.com/starknet-edu/setting-up-a-starknet-dev-environment-with-python-e4c61c1e8da6)
* [Documentación oficial sobre Cairo-SHARP](https://www.cairo-lang.org/docs/sharp.html)
* [Documentación oficial sobre Starknet-SHARP](https://starknet.io/docs/0.10.0/sharp.html)
* [Presentación Basecamp Edition January 2023](https://github.com/starknet-edu/basecamp/tree/main/camp_1)
* [PDF del Basecamp Edition January 2023 LINK](https://docs.google.com/presentation/d/1rjV7nFGuXyIWghbKpIJmOVox8EVbEX974xCHzL4XU5o/edit)

---

## Pruebas con Sharp

Haremos el compile de un contrato que tenemos preparado como [sum.cairo](/src/sum.cairo) el cual le haremos una prueba inicial para ve que nos imprima el resultado correcto, pero primero veamos unas fotos de como SHARP funciona y el trabajo que nos resuevle.

![Graph](/im%C3%A1genes/donttrust.png)
![Graph](/im%C3%A1genes/trust.png)

* Sequencer: Valida, agrupa y ejecuta transacciones
* Prover: Crea una prueba que certifica que el nuevo estado global es el resultado de ejecutar todas las transacciones en un bloque
* Verifier: Contrato inteligente en L1 que verifica la prueba de validez enviada por el Prover
* Fact Registry: contrato inteligente que almacena las pruebas que se han verificado con éxito

---

Ahora que entendemos mejor SHARP, deberemos ajustar nuestra configuración de entorno, usaremos desde nuestra terminal:

```bash
python3.9 -m venv ~/cairo_venv
source ~/cairo_venv/bin/activate
```

![Graph](/im%C3%A1genes/env.png)


Luego si hemos clonado esta repo no nos hara falta crear el contrato, sino, debera de crear una carpeta `src` y otra `build` en el que le indicaremos con el siguiente comando en la terminal la salida del compile en `.json`.

```bash
cairo-compile src/sum.cairo --output build/sum.json
```

Si todo ha ido bien se debería crea un archivo `sum.json` en nuestra carpeta de `build`, con el que haremos la prueba para ver si nos imprime el resultado correcto, en la versión [sum.cairo](/src/sum.cairo) podemos ver una suma de 3 cantidades, las cuales deberian dar `131`, así que procedemos a los comandos:

```bash
cairo-run --program build/sum.json --print_output --layout=small
```

![Graph](/im%C3%A1genes/run.png)


Luego haremos el submit sharp, que nos ejecutará una serie de pasos hasta subir el programa o código, y nos dará un `Job key` y un `Fact`. Podemos consultar su estado para verificar que ha sido aceptada, ya que mientras esté en proceso no podremos verificar la vericidad de la prueba, esto suele llevar algunos minutos por parte del SHARP.

```bash
cairo-sharp submit --source src/sum.cairo
```

![Graph](/im%C3%A1genes/submit.png)


Que podremos verficar el estado con el `Job-key` hasta que veamos que cambió su estado usando desde el terminal:

```bash
cairo-sharp status 1e1ec39a-4ebe-48a8-9273-0b2aa1542c9a
```

![Graph](/im%C3%A1genes/progres.png)
![Graph](/im%C3%A1genes/ok.png)

---

## RPC de Alchemy 

En este paso creamos nuestro propio rpc para no tener que depender de llamadas de nadie, aunque podria usar cualquier rpc, usaremos directamente de nuestro nodo así podemos verficar el estado de la prueba, para ello deberemos de crear una cuenta en [Alchemy](https://auth.alchemy.com/), después de hacer el registro podremos crear su propia App con ajustes de Goerli y copiar la dirección de rpc de su nodo que usaremos una vez haya sido aceptada la prueba, fíjese en la siguiente foto y recuerdo que todos estos datos sensibles al final sólo son en entornos de pruebas.

![Graph](/im%C3%A1genes/app.png)
![Graph](/im%C3%A1genes/api.png)
 
 Y una vez aceptada la prueba podremos verifcarla usando el siguiente comando.

```bash
cairo-sharp is_verified 0x908f30889da7846d2051b189322802dde73e55cb1d34eb4ae892fb4d04d71376 --node_url https://eth-goerli.g.alchemy.com/v2/zbIXJ9iA7vvwIJH9DagNh_4LSYhId9mW
```

![Graph](/im%C3%A1genes/verified.png)

Puede verificar el estado de su prueba ha sido correcta desde [aquí](https://goerli.etherscan.io/address/0xAB43bA48c9edF4C2C4bB01237348D1D7B28ef168#readProxyContract#F5), podra revisar que su prueba ha sido aceptada.

![Graph](/im%C3%A1genes/true.png)


## Comandos CLI

A continuación les adjunto usados para esta guía sin los valores.

* Compile
```bash
cairo-compile src/sum.cairo --output build/sum.json
```
* Run & print output
```bash
cairo-run --program build/sum.json --print_output --layout=small
```
* Debug memory
```bash
cairo-run --program build/sum.json --print_memory --relocate_prints
```
* Debug memory of unbound program
```bash
cairo-run --program build/sum.json --print_memory --relocate_prints --no_end --steps 16
```
* Compile, run and submit to sharp
```bash
cairo-sharp submit --source src/sum.cairo
```
* Sharp job status
```bash
cairo-sharp status <job-key>
```
* Proof validity status
```bash
cairo-sharp is_verified <fact> --node_url <eth-rpc-url>
```
