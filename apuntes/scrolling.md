# Scrolling y operaciones con la cámara

Scrolling significa que el movimiento de la cámara sigue al personaje. En Haxeflixel el scrolling se puede hacer de la siguiente forma.

```haxe
FlxG.camera.follow(sprite);
```

La clase **FlxG** tiene el atributo *camera* que representa la camara principal del juego. El objeto *camera* tiene un método [*follow()*](http://api.haxeflixel.com/flixel/FlxCamera.html#follow) que recibe como argumento el objeto al cual la cámara debe seguir.
Además, como segundo argumento opcional, el método *follow()* puede recibir un estilo de seguimiento. Por ejemplo:

```haxe
FlxG.camera.follow(sprite, TOPDOWN);
```

Existen varios [tipos predefinidos de scrolling](http://api.haxeflixel.com/flixel/FlxCameraFollowStyle.html):

* **LOCKON**: El estilo por defecto. La cámara no posee zona muerta (dead zone), simplemente sigue al objeto de manera directa.
* **PLATFORMER**: La cámara tiene una zona muerta rectangular angosta y alta. Útil en videojuegos de plataformas para evitar el movimiento excesivo de la cámara al saltar.
* **TOPDOWN**: La cámara tiene una zona muerta cuadrada, de tamaño medio, enfocada en el objeto.
* **TOPDOWN_TIGHT**: La cámara tiene una zona muerta cuadrada, de tamaño pequeño, enfocada en el objeto.
* **SCREEN_BY_SCREEN**: La cámara se moverá dando saltos de una pantalla completa.

La zona muerta es una región cuadrada o rectangular dentro de la cual el objetivo de la cámara podrá moverse sin producir un desplazamiento de esta última. Cuando el objetivo trate de moverse fuera de la zona muerta, la cámara se desplazará para evitar que quede fuera como se observa en la siguiente imagen. El objetivo de utilizar una zona muerta es evitar el movimiento excesivo de la cámara, ya que éste puede producir mareos o resultar confuso.

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/dead_zone.gif)


## Scrolling y los bordes de la escena

Cuando la cámara sigue al objetivo, es importante que se detenga donde finaliza la zona jugable. De lo contrario la camara puede mostrar zonas que que se encuentran fuera del territorio de juego como se puede ver en la primera de las siguientes imágenes. Esto produce un efecto desagradable y desaprovecha espacio en la pantalla.

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/scroll_bounds.png)

Existen funciones que permiten especificar los límites del escenario para evitar que la camara salga fuera al seguir al objetivo. Una de esllas es [*setScrollBoundsRect()*](http://api.haxeflixel.com/flixel/FlxCamera.html#setScrollBoundsRect), que se puede invocar de la siguiente manera.

```haxe
FlxG.camera.setScrollBoundsRect(0, 0, 1300, 1200, true);
```

Los dos primeros argumentos de la llamada corresponden a los valores mínimos para *x* e *y*, mientras que los dos siguientes corresponden al ancho y alto de la zona de juego (en este caso una zona de juego de 1300x1200 px).

El último argumento es muy importante. El valor *true* especifica que se deben actualizar las dimensiones del quad-tree global para coincidir con el tamaño especificado de la zona de juego. El quad-tree global es la estructura utilizada por el motor para la detección de colisiones entre objetos. El tamaño del mismo, inicialmente, coincide con el tamaño de la pantalla. Cuando creamos escenarios mas grandes que la zona visible, es necesario actualizar el tamaño del quad-tree para que la detección de colisiones funcione de manera correcta fuera de la zona inicial de la cámara.   

## Otras opciones de FlxCamera

Existen varias opciones para modificar el comportamiento de la cámara. Algunas de ellas son:

* [*followLerp*](http://api.haxeflixel.com/flixel/FlxCamera.html#followLerp): suaviza el movimiento de la cámara introduciendo una breve demora en el seguimiento. El valor del atributo varía entre 0 y 1, indicando el primer valor que la cámara no se mueve y el segundo que sigue al objeto sin suavizado. Un valor de 0.5 indica un suavizado medio. Ejemplo de uso:
```haxe
FlxG.camera.followLerp = 0.5;
```

* [*followLead*](http://api.haxeflixel.com/flixel/FlxCamera.html#followLead): se utiliza para forzar el enfoque de la cámara de manera que se adelante al movimiento del objetivo. Esto puede ser útil cuando el objetivo se mueve rápidamente y se desea enfocar la porción del nivel hacia donde se dirige. Se utiliza la velocidad del objetivo para el cálculo y la intensidad del efecto depende de la misma, además del valore del atributo. Ejemplo de uso 

```haxe
FlxG.camera.followLead.set(1, 1);
```

## Demo de FlxCamera

Para observar y experimentar con distintos valores para las funcionalidades mencionadas anteriormente, se recomienda dar un vistazo al [demo de **FlxCamera**](https://haxeflixel.com/demos/FlxCamera/)

## Parallax Scrolling

Parallax Scrolling es una técnica que consiste en superponer varias capas de imágenes y moverlas a velocidades diferentes para lograr un efecto de profundidad. La técnica fue ampliamente utilizadas en juegos de la era de las consolas como el que se muestran en la siguiente imagen.

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/vice.gif)

Una forma sencilla de lograr este efecto en Haxeflixel es utilizar el atributo *scrollFactor*, que define cuanto es afectado un sprite por el movimiento de la cámara. 

Se debe crear un sprite por cada una de las capas y ajustar el *scrollFactor* para cada uno de ellos. Los sprites del fondo deberían tener un valores menores para tener un desplazamiento pequeño o nulo. Las capas intermedias deberían tener valores entre 0 y 1. Para la capa correspondiente al plano principal (el que se moverá en relación 1:1 con la cámara) se dejará el valor por defecto de 1.

Por ejemplo:

```haxe
background_layer.scrollFactor.set(0, 0);
mid_layer.scrollFactor.set(0.2, 0);
```

Notar que el atributo *scrollFactor* es de tipo **FlxPoint** y posee componentes para las direcciones *x* e *y*. 

[Descargar el ejemplo](https://minhaskamal.github.io/DownGit/#/home?url=https://github.com/pabab/aprende-haxeflixel/tree/master/src/ejemplos/parallax_scrolling)

## Infinite scrolling background 

Cuando necesitamos hacer scrolling infinito, es necesario encontrar una manera simular un paisaje infinito a partir de imágenes que no lo son.

Una forma de lograr esto (aunque no es la única) es mover los sprites correspondientes las capas y reposicionarlos cuando salen de la pantalla de manera que no se note el salto de posición.

Para que esto sea posible, y no se vean espacios vacios cuando el sprite de la capa sale de la pantalla, será necesario generar imágenes más grandes a partir de la repetición de las originales como se muestra debajo.

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/foregound-merged.png)

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/foregound-merged-repeat.png)

Es necesario que las imágenes puedan concatenarse sin que se note el punto de unión entre ellas. Así, si el ancho original de la imagen era de 512px, se generó una nueva imagen 1536px que repite el mismo patron a ambos lados.

Las capas deben posicionarse de manera que inicialmente se vea en pantalla el patrón central (de manera que el movimiento sea posible hacia ambos lados). Luego las capas deben moverse a distintas velocidades. Es importante notar que lo que se mueve en este caso son los sprites y no la cámara (aunque la cámara también podría moverse), por ello es necesario ajustar a 0 el atributo *scrollFactor* de las capas.

Cuando el patrón central abandona la pantalla, ya sea por cualquier dirección, se lo reposiciona dando un salto en píxeles igual al tamaño original del patrón para que la imagen vuelva a estar casi centrada. Este salto no es notorio por la repetición del patrón.

```haxe
    override public function create():Void
    {
		super.create();
		background = new FlxSprite(0, 0, "assets/images/far.png");
		mid_layer = new FlxSprite(0, 0, "assets/images/sand-repeat.png");
		front_layer = new FlxSprite(0, 0, "assets/images/foregound-merged-repeat.png");

		add(background);
		add(mid_layer);
		add(front_layer);

		background.scrollFactor.set(0, 0);
		mid_layer.scrollFactor.set(0, 0);
		front_layer.scrollFactor.set(0, 0);

		mid_layer.x = -512;
		front_layer.x = -512;

		mid_layer.velocity.x = -30;
		front_layer.velocity.x = -100;
	}

	override public function update(elapsed:Float):Void
	{
		super.update(elapsed);
	
		if(mid_layer.x < -512){
			mid_layer.x += 512;
		}else if(mid_layer.x > 512){
			mid_layer.x -= 512;
		}
		if(front_layer.x < -512){
			front_layer.x += 512;
		}else if(front_layer.x > 512){
			front_layer.x -= 512;
		}
	}
```

[Descargar el ejemplo](https://minhaskamal.github.io/DownGit/#/home?url=https://github.com/pabab/aprende-haxeflixel/tree/master/src/ejemplos/infinite_scrolling)

## HUD

El HUD (Head-Up Display) es información sobre la partida que se muestra en todo momento. Suele incluir cosas como la cantidad de vidas, puntos, nivel de salud, minimapa, etc.

En el caso de que se utilice scrolling, los elementos del HUD no deberán ser afectados por el movimiento de la cámara sino que por el contrario deberán estar "anclados" al viewport. Este efecto puede lograrse igualando a 0 las componentes del atributo *scrollFactor* para cada uno de los elementos del HUD.

Una posible forma de modelar el HUD es creando un grupo de sprites o una clase que herede de **FlxGroup**. De ésta manera se facilita el manejo conjunto de dichos sprites. Por ejemplo:

```haxe
class HUD extends FlxSprite{
    var lives: FlxSprite;
    var level: FlxSprite;

    public function new(){
        lives = new FlxSprite(...);
        level = new FlxSprite(...);

        // agrega los sprites al grupo
        add(lives);
        add(level);

        // ajusta scrollfactor para cada miembro del grupo
        forEach(function(e: FlxObject){
            var sp = cast(e, FlxSprite);
            e.scrollFactor.set(0, 0);
        })
    }

    public function setLives(l: Int){
        ...
    }

    public function setLevel(l: Int){
        ...
    }
}
```

Las funciones miembro permiten, desde la escena, indicar la cantidad de vidas/nivel/etc. que el HUD debe mostrar.


## Otros algoritmos de scrolling

El movimiento de la cámara es algo muy importante para dirigir la mirada del jugador a los lugares que importan, para poder alcanzar la estética buscada y como elemento para mejorar la jugabilidad.

Existen otros algoritmos e ideas mucho más complicadas que las disponibles entre las opciones de Haxeflixel. Los invitamos a leer [el siguiente artículo](https://www.gamasutra.com/blogs/ItayKeren/20150511/243083/Scroll_Back_The_Theory_and_Practice_of_Cameras_in_SideScrollers.php) discute y categoriza algunas de estas posibilidades.



