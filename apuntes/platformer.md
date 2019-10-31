# Movimiento de un personaje de plataformas

En un juego de plataformas el movimiento del personaje principal se divide en movimiento horizontal y vertical, que son generalmente independientes entre sí.

El movimiento horizontal (hacia los costados), se puede implementar como un movimiento uniforme (sólo velocidad) o un movimiento uniformemente acelerado (aceleración y velocidad). Por otro lado, el movimiento vertical (el salto y la caída) requiere algunos pasos extra ya que se necesitan algunas comprobaciones de colisiones adicionales. Ambos movimientos simultáneos forman un movimiento parabólico como el que se ve en la imagen. 

![](https://i.ytimg.com/vi/c4b9lCfSDQM/maxresdefault.jpg)

# Gravedad

En el movimiento horizontal de un salto intervienen la velocidad (que hace que el personaje salte) y la aceleración (la gravedad que lo atrae nuevamente hacia el suelo).

Para el motor de colisiones de Haxeflixel no existe una gravedad global sino que se debe agregar aceleración a cada objeto, lo cual se puede hacer modificando la componente *y* el atributo *acceleration* como se observa debajo.

```haxe
acceleration.y = 300;
```

Este valor implica que la velocidad del objeto se incrementará en 300px hacia abajo cada segundo, lo cual implica que, cuanto más tiempo pase, mayor será su velocidad de caída.


# Física de un salto

Para que el personaje salte es necesario ajustar su velocidad vertical dándole signo opuesto al de la aceleración (es decir negativo o hacia arriba). El personaje comenzará entonces a moverse hacia arribe, pero, a medida que esto ocurre, la velocidad será gradualmente contrarrestada por la aceleración hasta anularse por completo cuando el personaje alcance el punto máximo del salto. En el punto máximo de un salto la velocidad es 0, ya que el personaje no se mueve hacia arriba ni hacia abajo por un breve instante. Luego, la velocidad comenzará a crecer nuevamente en el mismo sentido de la aceleración (hacia abajo) hasta que el personaje toque el suelo.

# Detección del contacto con el suelo

Es importante notar que, para que el personaje pueda experimentar un correcto movimiento acelerado de elevación y caída, la velocidad debe ser ajustada por única vez cuando el jugador presione le tecla de salto. Luego, una vez que el personaje se despega del suelo, la velocidad debe quedar sometida a la aceleración aplicada por el motor de física (la cual permanecerá constante con el valor que se le asignó inicialmente).

Para que el personaje no pueda volver a saltar estando en el aire es necesario que la velocidad de salto sea aplicada únicamente si se encuentra tocando el suelo.

El hecho de que el motor de física de Haxeflixel utilice AABBs como figuras de colisión simplifica enormente esta tarea, ya que un objeto sólo puede colisionar con otro en cuatro direcciones.

Las llamadas a *separate()* (función invocada por *collide()*), además de separar los objetos entre sí, modifican un atributo interno de cada objeto que indica en qué direcciones colisionó durante la última actualización. Se trata del entero [wasTouching](http://api.haxeflixel.com/flixel/FlxObject.html#wasTouching).

Para almacenar varios valores en un número entero (ya que un objeto puede colisionar simultáneamente en varias direcciones) se utiliza un bit para cada dirección. Para saber si el personaje estaba en contacto con otro objeto en determinada dirección se puede consultar si el bit correspondiente estaba encendido. Para esto se puede utilizar el operador & (AND a nivel de bits). Por ejemplo, para saber si el personaje estaba en contacto con el suelo se puede hacer la siguiente pregunta.

```haxe
if(wasTouching & FlxObject.FLOOR != 0){
    ...
}
```
Lo mismo puede hacerse para otras direcciones reemplazando ```FlxObject.FLOOR``` por ```FlxObject.CEILING```, ```FlxObject.RIGHT``` o ```FlxObject.LEFT```.

Un ejemplo más completo puede verse a continuación.

```haxe
public override function update(elapsed: Float)
{
    super.update(elapsed);
 
    ...
 
    if(FlxG.keys.justPressed.W && (wasTouching & FlxObject.FLOOR != 0))
    {
        y--;
        velocity.y = -150;
    }
 
    if(wasTouching & FlxObject.FLOOR == 0)
    {
        if(velocity.y > 0)
        {
            animation.play("fall");
        }
        else
        {
            animation.play("jump");
        }
    }
}
```

El personaje sólo saltará si se presionó la tecla W y se encuentra tocando el suelo. Al saltar, se le imprime una velocidad de 150px/s hacia arriba. 

Un detalle que merece la pena mencionar es que al saltar se debe separar al personaje del suelo (en el código: ```y--;```). Ésto ocurre por lo siguiente: la llamada a *super.update()* (que actualiza la velocidad del personaje en función de la aceleración) se ejecuta al principio de la actualización del personaje por lo cual, al asignarle la velocidad de salto, ésta no tendrá efecto hasta la próxima actualización. Sin embargo, dado que la aceleración es constante, ya ha afectado a la posición del personaje en la llamada a super.update(), por lo cual el mismo se ha desplazado levemente hacia abajo y está solapándose con el suelo (aunque luego será separado al invocar a *collide()*). Antes de la próxima actualización del personaje, *collide()* detectará ésta situación y no sólo separará al personaje del suelo sino que anulará su velocidad vertical, sobreescribiendo el valor que nosotros le habíamos asignado.

En otras palabras, al tener aceleración vertical hacia abajo, el personaje se encuentra en permanente caída, solapándose con el suelo (aunque muy poco) y siendo separado del mismo en cada actualización, lo cual implica que su velocidad vertical sea anulada en éste proceso. Para evitar que *collide()* anule la velocidad del personaje, lo desplazamos levemente hacia arriba antes de saltar para que no entre en contacto con el suelo.

# Otras mecánicas


## Double jump

En muchos juegos el personaje tiene la habilidad de saltar nuevamente cuando se encuentra en el aire. Existen diferentes maneras de implementar esto dependiendo en qué momento se permita realizar el segundo salto y la intensidad del mismo.

Una alternativa es permitir al personaje realizar el segundo salto cuando se encuentra en la parte más alta del primero. Sabemos que en el punto más alto del salto la velocidad es cero durante un breve instante. Para detectar dicho momento se puede utilizar la siguiente condición:

```haxe
if(Math.abs(velocity.y)<0.1 && nJumps == 0){
    jump();
    nJumps++;
}
```

La condición ```Math.abs(velocity.y)<0.1``` se asegura que la velocidad se encuentre en el rango -0.1 - 0.1 (es decir, justo antes o justo después del cénit). El valor 0.1 se puede modificar para permitir mayor o menor tolerancia.

Además, para impedir que el personaje realice un número infinito de saltos dobles, es necesario agregar un contador de saltos que debe reiniciarse al tocar el suelo.


## Coyote time

Los jugadores de juegos de plataformas tienden a maximizar los saltos, esperando hasta el último momento (el mismísimo borde) para saltar. Si se hace correctamente, el personaje realizará un salto que cubrirá una gran distancia. Sin embargo, si falla, el personaje caerpa sin saltar (provocando generalmente la muerte o daño).

Para arreglar esto, se puede implementar algo llamado *coyote time* que es simplemente un tiempo de gracia para ejecutar el salto cuando el personaje ya no se encuentra tocando la plataforma.

Para implementar esta característica se necesita una variable para medir el tiempo transcurrido desde que se abandonó por última vez una plataforma: *coyoteTimer*. La variable se pone en 0 si el personaje se encuentra tocando el suelo o se incrementa de acuerdo al tiempo transcurrido de lo contrario.

Para saltar, es necesario que el personaje se encuentre tocando el suelo o que se encuentre en caída y el tiempo transcurrido se este dentro de un cierto valor.

```haxe
public override function update(elapsed: Float){
    super.update(elapsed);
 
    var isTouchingFloor = wasTouching & FlxObject.FLOOR != 0; 
    if(isTouchingFloor){
        jumping = false;
        coyoteTimer = 0;
    }else{
	if(!jumping){
            coyoteTimer += elapsed;
        }
    }

    if(FlxG.keys.justPressed.W && (isTouchingFloor || (!jumping && coyoteTimer <= 0.1)))
    {
        jump();
    }
 
}

function jump(){
    y--;
    velocity.y = -150;
    jumping = true;
}
```

## One way platforms

Se trata de plataformas o paredes contra las que el personaje puede colisionar desde un lado pero no desde otros. Un ejemplo típico son plataformas con las que el personaje no puede chocar desde abajo, pero una vez que se encuentra sobre ellas no puede caer.

![](https://chipmunk-physics.net/unityDocs/Tutorials/images/onewayPlatforms.png)

En Haxeflixel, esto es muy sencillo de lograr utilizando el atributo *allowCollisions* de cada objeto.

Para el caso de los tilemaps, se puede utilizar el método [*setTileProperties()*](http://api.haxeflixel.com/flixel/tile/FlxBaseTilemap.html#setTileProperties). Por ejemplo, con el siguiente código se puede hacer que todos los tiles con el número de patrón 13 de un tilemap sean sólo colisionables desde arriba.

```haxe
tilemap.setTileProperties(13, FlxObject.CEILING);
```
