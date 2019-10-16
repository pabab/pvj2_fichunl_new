# Desafío #8: Running Slime

## Resumen

Este desafío consiste en completar ciertas partes del código de un videojuego de plataformas sobre una babosa que corre frenéticamente por el escenario y debe encontrar la puerta de salida de cada nivel sorteando diversos peligros.

Pueden probar cómo debería quedar el ejemplo terminado [aquí](https://pabab.itch.io/running-slime?secret=l7Ov4vllre5DeBrOW9T7tOmw7hU).

El ejemplo incompleto que deberán tomar como base puede ser obtenido desde [aquí](https://minhaskamal.github.io/DownGit/#/home?url=https://github.com/pabab/aprende-haxeflixel/tree/master/src/desafios/running_slime).


## Mecánica

Las reglas de juego que deberán implementar son las siguientes:

* El personaje debe tener velocidad horizontal permanente, empezando hacia la derecha.
* El personaje debe tener gravedad.
* El personaje debe invertir su velocidad horizontal al chocar con una pared.
* El personaje puede saltar utilizando la tecla W, pero sólo si se encuentra tocando el suelo.
* El personaje puede efectuar un salto doble si se encuentra en la parte más alta de un salto.
* Se debe configurar determinados tiles para que actúen como one-sided platforms.
* El personaje muere si toca las espinas.
* El personaje puede juntar los items (el item desaparece).
* El personaje debe colisionar contra el escenario (el tilemap), las puertas (Doors, no la puerta de salida) y los botones.
* Se debe pasar de nivel cuando el personaje toca la puerta de salida.
* Además, debe agregar un nivel adicional diseñado por usted mismo.

