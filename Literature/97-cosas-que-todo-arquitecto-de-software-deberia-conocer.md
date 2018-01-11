# 97 cosas que todo arquitecto de software debería conocer (Spanish)

Un tiempo atrás estuve leyendo el libro [97 things every software architect should know][01] que, como su propio título indica, presenta una serie de conceptos sobre arquitectura de software que un conjunto de expertos en la materia consideran básicos para todo aquel que se dedique de uno u otro modo a esta área. Los 97 capítulos de los que consta el libro tienen licencia libre, hecho que permite que puedan leerse online en [esta página][02].

Al mismo tiempo que lo leía fui publicando en [mi cuenta de Twitter][03] brevísimos resúmenes de cada capítulo con el evidente límite en el número de caracteres (por aquel entonces 140). Presento aquí ordenadamente esos 97 tweets.

[01]: http://shop.oreilly.com/product/9780596522704.do
[02]: http://97things.oreilly.com/wiki/index.php/97_Things_Every_Software_Architect_Should_Know_-_The_Book
[03]: http://twitter.com/almata

1. No utilices un proyecto de un cliente para aprender una tecnología nueva que te interesa si no es lo que más le conviene a él.
2. Evita utilizar herramientas que aporten mayor complejidad al proyecto. Mantén el nivel de complejidad tan bajo como puedas.
3. Los proyectos triunfan o fracasan por las personas, no por la tecnología. Aprende a tratar y a conversar con tu equipo.
4. La comunicación es clave: procura ser claro, conciso y contar con el respeto bien ganado de tu equipo de desarrolladores.
5. El diseño y arquitectura de una aplicación inciden mucho más en su rendimiento que utilizar una u otra tecnología o hardware.
6. Descubre la necesidad real del cliente detrás de la característica que solicita: puede haber una solución mejor y más sencilla.
7. Para conseguir comunicarte mejor con tu equipo, tanto verbalmente como no verbal, levántate, hazlo de pie.
8. Los sistemas fallan. Es inevitable. Acéptalo y planifica modos seguros para cuando lo hagan o quedarás en manos del azar.
9. Estás siempre en plena negociación, así que no cedas en aquellos requisitos que sean fundamentales para el proyecto.
10. Rápido, escalable, muchos... eso no son cuantificadores válidos. Tienes que cuantificar de modo objetivo y comprobable.
11. Los diseños no tienen valor por sí mismos, sólo son medios para lograr el objetivo, así que escucha a los programadores.
12. No existen soluciones de diseño universales. Debes tener la experiencia para saber cuáles aplicar en cada caso (y cuáles no).
13. No dejes el análisis del rendimiento para el final. Hazlo desde el principio y verás rápidamente cuándo se vuelve un problema.
14. Además de las cuestiones técnicas, debes considerar los intereses de negocio de todos aquellos relacionados con el proyecto.
15. Crea y mantén un sistema de tests fácil y rápido de ejecutar para que los programadores se sientan cómodos utilizándolo.
16. No te aferres a la pureza técnica de un diseño ideal si otro menos rígido puede servir mejor a los intereses del cliente.
17. No pierdas de vista que el desarrollo de software está al servicio del negocio y sus intereses... y no al revés.
18. Hacerlo general y reutilizable está bien, pero asegúrate que primero cumple el objetivo de ser concreto y funcional.
19. Debes liderar a tu equipo de desarrolladores desde dentro. Que tu liderazgo comience en el terreno de la tecnología.
20. Practica la integración continua en tus proyectos: compilar a menudo y de modo automático, pasar tests, reportar resultados...
21. Evita que te adelanten las fechas de entrega. Si no es posible, negocia que ciertas partes queden para futuras versiones.
22. No existe un diseño con niveles altos de rendimiento, disponibilidad, seguridad y abstracción. Busca un equilibrio adecuado.
23. Presta gran atención al modelo de datos y hazlo robusto. La GUI cambia, la lógica de negocio también, los datos permanecen.
24. Si tienes que escoger entre dos opciones, replantéate aspectos del diseño que hagan que esa decisión sea menos trascendente.
25. Presta atención a los problemas de desarrollo tan pronto como alguien los identifique y trata los riesgos como si fueran bugs.
26. Para que tu equipo reutilice un elemento es necesario que conozca de su existencia, sepa cómo se utiliza y crea que es bueno.
27. No dejes que tu ego te haga ignorar las ideas de tus programadores ni creer que conoces los requisitos mejor que el cliente.
28. Para medir la calidad no basta con los diagramas generales y el código fuente. Crea unas métricas a medio camino para ello.
29. No bases las decisiones de diseño sólo en información teórica. Lleva a cabo pruebas prácticas de las distintas opciones.
30. Además del plano técnico, debes dominar al máximo el negocio en el que opera el cliente para entender del todo sus objetivos.
31. Programar implica más diseño creativo que construcción rutinaria. Lidera, pues, con técnicas que fomenten esta creatividad.
32. Como arquitecto, céntrate en asegurar que todas las piezas encajan y da libertad a tus desarrolladores para hacer su trabajo.
33. Piensa en el largo plazo y escoge la mejor opción, aunque sea más laboriosa, buscando al mismo tiempo máxima simplicidad.
34. Sé humilde. La profesión de arquitecto de software es todavía muy reciente para codearse con otras como médicos o arquitectos.
35. Intenta mantener el alcance de la aplicación tan pequeño como sea posible y da prioridad a los puntos clave.
36. Actúa buscando el mejor interés para el cliente y no para tu ego de arquitecto. Son sus recursos los que estás gestionando.
37. Toma las decisiones de diseño pensando siempre en lo que haga la vida más sencilla a los usuarios a largo plazo.
38. Intenta que el software crezca equilibrado y siempre haya una versión funcional, en lugar de desarrollar partes por separado.
39. Escoge el mejor lenguaje o paradigma para cada parte del proyecto y únelos. La tecnología actual lo permite sin sobrecoste.
40. Presta gran atención al rendimiento y la usabilidad del sistema. Pueden convertir un software a priori bueno en inutilizable.
41. Pon en tus diagramas suficiente información para entender cómo se relacionan los distintos sistemas y qué se espera de ellos.
42. Conoce los patrones de arquitectura y diseño para poderte comunicar de modo eficiente con otros arquitectos y programadores.
43. No partas de ideas preconcebidas sobre qué tecnología escoger. Deja que el contexto concreto de la aplicación te guíe.
44. Debes procurar que todos los miembros del equipo tengan un papel importante en el proyecto. Así, además, el equipo crecerá.
45. Como arquitecto de software puedes aprender cosas de los arquitectos. Por ejemplo, que nunca diseño alguno será perfecto.
46. Debes intentar que en el desarrollo se repitan el mínimo de código y patrones, por ejemplo creando mejores abstracciones.
47. En el nuevo entorno de sistemas cada vez más distribuídos, los errores e inconsistencias son comunes. Busca cómo minimizarlos.
48. No intentes controlarlo todo. Pero observa, valida y genera modelos que te ayuden a comprobar los grandes rasgos del diseño.
49. Debes tener mente abierta a nuevas ideas y herramientas para satisfacer los requisitos actuales sin dejar de mirar al futuro.
50. Una de tus principales tareas como arquitecto es encontrar los límites de cada contexto de negocio y sus conexiones.
51. Cuida a tus programadores: dales buenas herramientas y libérales de trabajo repetitivo. Que puedan enfocarse en los problemas.
52. Documenta las razones por las que tomas las decisiones y qué alternativas descartas y por qué. Te ayudará a analizar mejor.
53. Asegúrate que las asunciones sobre las que basas tus decisiones de arquitectura son sólidas, relevantes y están actualizadas.
54. Comparte tus conocimientos con los demás. Sólo con la confrontación reafirmas tu experiencia y corriges tus errores.
55. Los patrones de diseño son una herramienta excelente, pero no intentes hacerlos encajar donde no lo hacen.
56. Utiliza metáforas con cosas tangibles para explicar el diseño, pero cuidado con abusar de ellas o terminarán siendo un lastre.
57. El soporte y mantenimiento del software pueden suponer el 80% de su ciclo de vida. Préstales gran atención desde el principio.
58. Cuestiónate las características a priori innegociables del diseño. Aceptar alguna restricción puede dar mejores resultados.
59. Toma decisiones de diseño y documéntalas en base a principios y axiomas sólidos y no en base a tus opiniones y preferencias.
60. Comienza con un esqueleto funcional de tu arquitectura y evoluciónalo poco a poco. Te ayudará a ver si el diseño es correcto.
61. Cambia tu visión orientada a instrucciones por una visión orientada a datos. Son los datos el punto clave de todo sistema.
62. Aplica soluciones complejas a problemas complejos y soluciones sencillas a problemas sencillos. No lo hagas complejo tú.
63. Como arquitecto debes diseñar. Pero, aunque no sea tu trabajo, también deberías ser capaz de implementar lo que diseñas.
64. Al escoger entre varias opciones, ten muy presente la tasa de retorno de la inversión (ROI) y escoge la más beneficiosa.
65. El software que ahora diseñas será legacy algún día. Procura que sea fácil de entender para quienes tendrán que tratar con él.
66. Si ante un problema sólo se te ocurre una solución, raramente será la mejor. Busca una segunda opinión de alguien experto.
67. Tu labor como arquitecto no es tanto manejar el cambio como asegurar que es manejable. Estudia los posibles tipos de cambio.
68. Como arquitecto enlazas el negocio con el software, pero también debes dominar el hardware para enlazarlo con el software.
69. A largo plazo, mantener el software supone más coste que su desarrollo. No tomes atajos en el diseño o se pagarán muy caros.
70. No pretendas una arquitectura perfecta. Busca una que simplemente funcione muy bien sin más complejidad de la necesaria.
71. Huye de las "buenas ideas" que aparecen a mitad del proyecto para añadir algo no requerido. Aumentarán la complejidad.
72. Presta especial atención a la calidad de los contenidos, puesto que al final el éxito de un proyecto depende de los datos.
73. No caigas en el error de creer que ya conoces el negocio de tu cliente mejor que él, ni hables más de lo que escuches.
74. Comprueba cómo responde el software a medida que la carga y volumen de datos crecen. Busca debilidades y corrige el diseño.
75. Utiliza en tus diseños herramientas que hayas probado personalmente antes. Sólo así tus previsiones podrán ser realistas.
76. Si no tienes claro qué nombre poner a un elemento es que no tienes clara su función y por tanto no lo diseñarás adecuadamente.
77. Intenta que el problema a solucionar sea lo más estable posible y podrás conseguir diseños también estables y de calidad.
78. Tu éxito se basa en la diligencia. Eso incluye cumplir las restricciones de tiempo y dinero y aceptar las responsabilidades.
79. Responsabilízate de tus decisiones: comunícalas, revísalas, comprueba su ejecución y busca ayuda externa cuando se precise.
80. No bases tus diseños en la astucia y algunos trucos. Plantéate cada problema desde una visión tan ingenua como sea posible.
81. No tengas prisa por adoptar nuevas tecnologías que sustituyan a las que te han funcionado bien antes. Piensa en el negocio.
82. Tu verdadero cliente no es tu cliente, sino los clientes de tu cliente. Piensa en todo momento en lo que es mejor para ellos.
83. Acepta que el producto final nunca será exactamente como tu diseño ideal inicial. Sé flexible y adáptate a los cambios.
84. Utiliza frameworks que, más allá de su potencia individual, encajen bien con los demás que forman el proyecto y no se solapen.
85. Busca el mejor modo de vender tu arquitectura y diseño. Ayúdate de métricas y cifras económicas y busca el momento adecuado.
86. Haz que la parte de base de datos se integre en el proceso automático de compilación continua para poder testearla igualmente.
87. Si ante un problema debes escoger la solución "rápida y sucia" recuerda que después tienes que pagar la deuda. No lo demores.
88. No confundas los problemas de arquitectura con los de desarrollo: en los primeros debes cuestionar siempre las premisas dadas.
89. Diseña un sistema tal que el usuario lo use para conseguir un fin sin tener que prestar especial atención al propio sistema.
90. Crea un equipo de programadores apasionados y flexibles y cuando lo tengas mantenlo cohesionado a base de refuerzos positivos.
91. No compares la ingeniería de software con otras ingenierías. El software no es tangible y tiene sus propias restricciones.
92. Como buen arquitecto de software debes hablar bien tres idiomas: el de los negocios, el de los programadores y el del testing.
93. Decidas lo que decidas, la solución de hoy será el problema del futuro. De modo que decide en base a lo que es mejor ahora.
94. Los usuarios finales suelen rechazar los nuevos sistemas. Gestiona esto desde el inicio ayudándote con usuarios influyentes.
95. Sé muy preciso en las palabras usadas en los requisitos, huye de toda ambigüedad posible y refínalos hasta que sean claros.
96. Cuida mucho la interfaz gráfica desde la fase de diseño inicial. Para los usuarios finales, esa interfaz es el sistema entero.
97. No diseñes en base a requisitos del futuro, pero sí diseña un sistema que pueda evolucionar. Créalo pequeño y hazlo crecer.
