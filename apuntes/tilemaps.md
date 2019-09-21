# Tilemaps

Los tilemaps son imágenes o mapas generados a partir de la repetición de patrones (o mosaicos) más pequeños. La técnica surgió durante los primeros años del desarrollo de videojuegos para poder lograr un mejor aprovechamiento de los recursos y se volvió ampliamente adoptada en géneros específicos como juegos de plataformas o RPGs.

En la actualidad, la técnica es todavía muy utilizada, generalmente para representar niveles o mapas. Además de economizar el uso de memoria y acelerar la velocidad del dibujado, los tilemaps simplifican muchísimo la detección de colisiones y el diseño de mapas. Por otro lado, también otorgan una estética clásica y nostálgica de la era de las consolas.

Lo primero que se necesita para construir un tilemap es un conjunto de patrones, también conocido como *tileset*, que es una imagen como la siguiente que contiene todos los patrones que pueden utilizarse dentro del mapa.

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/tileTest2.png)

Todos los patrones deben tener el mismo tamaño. Si se asigna un número entero a cada patrón del tileset, se puede representar un mapa o *tilemap* como una matriz de enteros en donde 0 representa un espacio vacío y un valor distinto de 0 indica que en dicha posición se debe utilizar el patrón del tileset correspondiente a dicho número.

Así, por ejemplo, la siguiente matriz, utilizando el conjunto de patrones mostrado anteriormente, representaría el mapa que se observa debajo.

```
 2,12, 1, 1, 1, 1, 1, 1,12, 2,
12, 0, 0, 0, 0, 0, 0, 0, 0,12,
 1, 0, 0, 0, 0, 0, 0, 0, 0, 1,
 1, 0, 2,11,11,11,11, 2, 0, 1,
 1, 0, 0, 0, 0, 0, 0, 0, 0, 1,
12, 0, 0, 0, 0, 0, 0, 0, 0,12,
 2,12, 1, 1, 1, 1, 1, 1,12, 2
```

![](https://raw.githubusercontent.com/pabab/pvj2_fichunl_new/master/apuntes/assets/img/simple_tilemap.png)


## Tilemaps en Haxeflixel

En Haxeflixel se puede utilizar la clase [**FlxTilemap**](http://api.haxeflixel.com/flixel/tile/FlxBaseTilemap.html) para representar un tilemap.
Una vez creado el objeto, se puede cargar el mapa utilizando el método *loadMapFromCSV()* (CSV significa *Comma Separated Values* o valores separados por coma). El método recibe los datos del mapa (una matriz de valores separados por comas y saltos de línea que declaramos como string), la imagen con el conjunto de patrones y el ancho y alto de cada patrón. Los argumentos restantes deben tener el valor que se muestra en el ejemplo de abajo, para saber qué significan dichos parámetros se puede consultar la documentación.

El objeto calculará automáticamente el tamaño del mapa a partir del tamaño especificado para los patrones en la llamada a *loadMapFromCSV()* y la cantidad de filas/columnas de la matriz.

```
class PlayState extends FlxState{
    var map: FlxTilemap;
    var mapData: String = 
        "2,12, 1, 1, 1, 1, 1, 1,12, 2,
        12, 0, 0, 0, 0, 0, 0, 0, 0,12,
         1, 0, 0, 0, 0, 0, 0, 0, 0, 1,
         1, 0, 2,11,11,11,11, 2, 0, 1,
         1, 0, 0, 0, 0, 0, 0, 0, 0, 1,
        12, 0, 0, 0, 0, 0, 0, 0, 0,12,
         2,12, 1, 1, 1, 1, 1, 1,12, 2
        ";

    override public function create():Void
    {
        super.create();
        map = new FlxTilemap();
        map.loadMapFromCSV(mapData, "assets/images/tileTest2.png", 32, 32, FlxTilemapAutoTiling.OFF, 1);
        add(map);
    }
}
```

Además de *loadMapFromCSV()* existen otras opciones para especificar los números de patrones del mapa a partir de distintas fuentes y con distintos formatos (por ejemplo: [*loadMapFromArray()*](http://api.haxeflixel.com/flixel/tile/FlxBaseTilemap.html#loadMapFromArray) o [*loadMapFrom2DArray()*](http://api.haxeflixel.com/flixel/tile/FlxBaseTilemap.html#loadMapFrom2DArray)).

No resulta práctico codificar el mapa dentro del código fuente como en el ejemplo, por lo que más adelante veremos cómo utilizar un editor de niveles.

Para manejar las colisiones entre los personajes y un mapa implementado con tilemaps, se puede emplear *FlxG.collide()* como para el resto de los objetos.

```
override public function update(elapsed: Float):Void
{
    super.update(elapsed);
    FlxG.collide(map, hero);
}
```