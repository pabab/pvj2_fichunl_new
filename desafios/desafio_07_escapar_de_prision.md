# Desafío #7: Escape de la prisión

## Resumen

Para este desafío deben utilizar programar un videojuego de vista superior acerca de un prisionero que debe escapar. Para ello deberá sortear **trampas** y **encontrar tres llaves** que abren las puertas hacia su libertad.


## Elementos del juego

* El personaje principal
* Nivel (modelado con tilemaps)
* Llaves (llave roja, llave azul y llave amarilla)
* Puertas (rojas, azules y amarillas)
* Puerta de salida del nivel
* Trampas

## Mecánicas

* El **personaje principal** puede moverse en cuatro direcciones (arriba, abajo, izquierda y derecha) mediante las flechas del teclado u otra combinación de teclas (como por ejemplo WASD). El movimiento del personaje debe tener una leve inercia, es decir, un movimiento uniformemente acelerado.
* El movimiento del personaje estara limitado por el **nivel o mapa**. El mismo debe ser modelado mediante un tilemap que será leído desde una capa de patrones de un archivo de Tiled.
* Además de la capa de las paredes, se pueden utilizar otras capas de patrones para modelar el piso u otros elementos visuales.
* El nivel debe ser amplio y tener un tamaño mayor que la porción visible de la pantalla, por lo cual **será necesario utilizar scrolling**. El scrolling deberá tener una zona muerta de tamaño mediano o pequeño para evitar el movimiento excesivo de la cámara.
* Existirán 3 llaves distribuidas a lo largo del mapa que el jugador deberá adquirir: 1 **llave roja**, 1 **llave azul** y 1 **llave amarilla**. Existe una sola llave de cada color. Cada
llave es capaz de abrir todas las puertas de su correspondiente color.
* Las puertas son objetos que impiden el paso del personaje, existen 3 tipos de puertas: las **puertas rojas**, las **puertas azules** y las **puertas amarillas**. Puede haber más de una puerta de
cada color. El personaje sólo podrá pasar a través de una puerta si posee la llave del color correspondiente. Deberá haber un mínimo de 4 puertas de colores en el escenario.
* Las **trampas** son obstáculos que el personaje debe esquivar. Son fijas, es decir, no se mueven. Sin embargo, **se activan y desactivan en determinados intervalos de tiempo** (por ejemplo permanecen 2 segundos desactivadas y luego se activan durante un segundo). Pueden imaginar las trampas como espinas que salen del piso del calabozo o algo similar. Los intervalos de activación y desactivación de cada trampa (es decir, cuánto tiempo están desactivadas y cuando se activan) pueden elegirlos ustedes. Incluso pueden (aunque no es obligatorio) crear trampas
que se activen a intervalos distintos y combinarlas para que el juego resulte más desafiante. **Si el personaje toca un trampa cuando ésta está activada entonces muere. Al morir, el personaje vuelve al principio y pierde las llaves que había juntado**. Pista: una forma sencilla de hacer ésto es utilizar *FlxG.resetState()* para reiniciar la escena actual.
* La **puerta de salida** es una puerta especial que permite al personaje escapar de la prisión, pero **sólo puede cruzarla si ha adquirido las 3 llaves**.
* Cuando personaje principal finalmente abandona la prisión se debe mostrar un mensaje de victoria que debe ser implementado en un nuevo estado, similar al del desafío anterior.

## Otros aspectos

* No es necesario utilizar animaciones.
* No pueden utilizar los assets de los ejemplos, deben elegir y buscar assets propios. Recuerden que [opengameart.org](opengameart.org) e [itch.io](https://itch.io/game-assets) son muy buenas fuentes de recursos gratuitos.
* Deberían utilizar grupos para mejorar el manejo de colisiones.
* Pueden agregar otros elementos a la mecánica (powerups, trampas de distintos tipos, etc), siempre y cuando no omitan ninguno de los elementos solicitados.
