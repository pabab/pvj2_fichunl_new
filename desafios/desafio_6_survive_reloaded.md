Para este desafío vamos a retomar el juego del Desafío #3: Survive. Pueden utilizar como punto de partida el código que crearon para dicho desafío.

En este caso utilizaremos Orientación a Objetos para organizar y crear una versión más compleja del juego. Deberán cumplir los siguientes puntos. 

## Hero

El héroe deberá tener su propia clase. Pueden mover (y arreglar) parte del código que ya tienen en la escena a la clase. La clase **Hero** deberá tener las siguiente funcionalidades:

* Un **constructor** (función **new**) que no reciba parámetros. El héroe aparece en el centro de la pantalla.
* Un método privado, de uso interno, llamado *update movement()* encargado de actualizar el movimiento interno.
* Un método privado, de uso interno, llamado *limi  t_movement()* encargado de impedir que el personaje salga de la pantalla.
* Un método *get_lives()* que devuelva la cantidad de vidas del personaje (puede agregar los atributos que considere necesarios). 
* Un método *take_hit()* que resta una vida al personaje y le concede un período de gracia (lo hace invulnerable) por 2 segundos. 


## Clase Ghost

El fantasta también deberá tener su propia clase. Encapsular el comportamiento de un fantasma en una clase permitirá manejar una gran cantidad de fantasmas de manera transparente, algo que no resultaría tan sencillo hacer desde el método de actualización de la escena.

* Un **constructor** (función **new**) que no reciba parámetros. El fantasma aparece en una posicion al azar dentro de la pantalla. Además, luego de aparecer tiene un período de gracia en el cual no se mueve ni puede colisionar con nada durante 2 segundos. Durante éste período el sprite se muestra con algun efecto visual (por ejemplo flicker o semitransparencia).
* Un método privado, de uso interno, llamado *update movement()* encargado de actualizar el movimiento interno.
* Un método privado, de uso interno, llamado *update movement()* encargado de actualizar el movimiento interno.
* Un método *set_initial_velocity()*, que será llamado cuando el fantasma empiece a moverse y deberá otorgarle una velocidad al azar.

    
## Clase Hud

Además, vamos a crear una clase **Hud** (Heads Up Display) para ayudarnos a representar las vidas del personaje. La clase es simplemente un grupo de sprites que representan la cantidad de vidas del personaje. La clase hereda de **FlxGroup** (es simplemente un grupo que contiene los sprites para los 3 corazones).
Por heredar de **FlxGroup**, el objeto se puede agregar a la escena.

* Debe tener un método *setLives()* que reciba un entero con la cantidad de vidas del personaje y mostrar/ocultar los sprites que correspondan para reflejar tal situación. Pueden utilizar, por ejemplo, [ésta imagen para los corazones](https://opengameart.org/content/animated-lives)


## PlayState

La escena se encarga de gestionar las interacciones entre los elementos el héroe, los fantasmas y el HUD. La clase debe tener los siguientes métodos:

* Un método *next_level()* que se invoca automáticamente cada 7 segundos, el método debe crear un nuevo fantasma y agregarlo a la escena (puede agregar los atributos que considere necesarios).
* Un método *show_game_over()* que debe mostrar el mensaje "GAME OVER" en pantalla (puede agregar los atributos que considere necesarios).

Además, la escena debe aplicar la siguiente lógica:

* Debe permitir que el héroe y los fantasmas se actualicen y apliquen su propia lógica de movimiento.
* Si el héroe colisiona con algún fantasma se le debe restar una vida al héroe, actualizar el HUD y reducir la velocidad del fantasma a la mitad.
* Si el héroe pierde todas las vidas, se debe mostrar el mensaje "GAME OVER" en pantalla.


## OPCIONAL

**IMPORTANTE**: El siguiente item **no es obligatorio**, simplemente es para quienes deseen un desafío extra.

* Periodicamente, cada una cantidad de tiempo que puede variar entre los 10 y 15 segundos, debe aparecer un powerup en una posición al azar del escenario. Si el héroe logra alcanzar el powerup, todos los fantasmas reduciran su velocidad a la mitad por un tiempo de 7 segundos. Luego de este período los fantasmas recobrarán su velocidad anterior. Además, durante el tiempo que dura el efecto del powerup, se deberá indicar dicha situación mediante algún efecto visual. Se recomienda crear nuevos métodos para implementar esta funcionalidad.

## Pista: Timers

Muchas de las funcionalidades necesarias para el desafío involucran realizar acciones que se disparen después de una determinada cantidad de tiempo.
Una manera de lograr esto es con una variable que acumule tiempo transcurrido. Otra manera más práctica es utilizar timers. 

En Haxeflixel, los timers se representan con objetos de tipo [**FlxTimer**](http://api.haxeflixel.com/flixel/util/FlxTimer.html). Una vez creado el objeto, se puede disparar el timer con el método *start()* que recibe 2 argumentos: la cantidad de tiempo que debe transcurrir antes de ejecutar la acción, y una función callback que será invocada al finalizar dicho tiempo.

Por ejemplo, el siguiente código dispara una función luego de transcurridos 2 segundos.

```haxe
var timer = new FlxTimer();
timer.start(2, function(t: FlxTimer){
    // do something...
})
```
No es necesario preocuparse por la variable *timer*, el recolector de basura se encargara de eliminar el objeto cuando ya no se necesita.
Si no se desea pasar una función inline como segundo argumento de la llamada a *start()* se puede utilizar un método de clase, el único detalle a tener en cuenta es que la función callback del timer debe recibir como argumento el objeto timer que la disparó.
Además, desde la función callback, se puede reutilzar el timer recibido invocando nuevamente a *start()* para que el timer vuelva a dispararse nuevamente en una determinada cantidad de tiempo.

En el siguiente ejemplo, la función *onTimer()* se disparará a los 2 segundos de creado el timer. La función reiniciará el timer para que se dispare nuevamente a los 3 segundos. Luego, cada nueva invocación a la función callback del timer lo disparará nuevamente cada 3 segundos.

```haxe
public function new(){
    var timer = new FlxTimer();
    timer.start(2, onTimer)
}

function onTimer(t: FlxTimer){
    // do something...

    // restart the timer
    t.start(3, onTimer);
})
```

Pueden ver un ejemplo de cómo podría quedar el juego terminado aquí: [https://pabab.itch.io/survive-reloaded?secret=lvvU43QI205RlD6QHI8BqaTog](https://pabab.itch.io/survive-reloaded?secret=lvvU43QI205RlD6QHI8BqaTog)