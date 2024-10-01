# **Guia creada por Daniel Zavala**



# **Instalacion**

## **scrapy**
```pip install scrapy```
## **scrapy-playwright**
```pip install scrapy-playwright```
## **playwright_dependencies** 
```python -m playwright install```


# **Flujo de estudio**

* **Aprender scrapy:** https://www.youtube.com/watch?v=mBoX_JCKZTE (Hasta la parte 6)
* **Aprender scrapy-playwright:** https://github.com/scrapy-plugins/scrapy-playwright
* **Dudas:** https://chatgpt.com/



# **Funcionamiento del codigo**


## **Requests**
El codigo hace requests concurrentes. Es decir de forma paralela.  Esto lo hace mediante la sentencia 
yield scrapy.Request()
En nuestro caso, en el parametro meta se activa playwright y se crea un contexto nuevo por cada pagina que se abra. Tambien se configura el contexto.Hay que considerar que el meta es un diccionario que tiene claves preestablecidas para activar o configurar algo, sin embargo, puedes crear una nueva clave como por ejemplo "site" en donde puse como valor el link del site del momento en el for. Otro parametro del Request es el callback que es la funcion a la que se va a llamar para que maneje la respuesta del pedido, haciendo el scraping.
La logica se encuentra en el metodo start_requests(self) que es un metodo propio de scrapy por lo que no es necesario llamarlo para que se ejecute, este lo hara cada vez que ejecutemos el codigo. 


## **Scraping**
Aqui se obtiene la pagina y el site desde el response. Debemos de tener la pagina de playwright para poder realizar interacciones como por ejemplo darle a buscar temas. Se configura el timeout a 1 minuto debido a que la pagina muchas veces se demora hasta 50 minutos en buscar. Luego de que haya realizado la busqueda de los temas, el contenido html se convierte en scrapy html. Es como cuando obteniamos el html al utilizar la libreria requests y luego lo transformabamos a un soup para realizar busquedas por selectores css. Luego, se crean funciones para extraer lo necesario utilizando las funciones que scrapy tiene. Despues se cierra la pagina y todos los diccionarios se mandan a escribir el prueba.csv.


## **Settings.py**
Aqui se encuentran las sentencias que permiten activar playwright para el proyecto y tambien ciertas configuraciones de como queremos que playwright realice acciones como abrir el navegador. Estas ultimas configuraciones de "personalizacion" en playwright puedes igualmente configurarlas dentro del meta de cada solicitud. Hay que entender que el setting.py describe configuraciones globales asi que se aplicaran a cada spider. Vas a estar cambiando tres especialmente:

#### **1. Browser Type**

```PLAYWRIGHT_BROWSER_TYPE = "chromium"  # O "firefox" o "webkit" o "chromium"```

**Descripcion:** Aqui se elige el navegador a usarse.

#### **2. Launch Options**
```
PLAYWRIGHT_LAUNCH_OPTIONS = {
    "headless": False,
}
```

**Descripcion:** False para que aparezca el navegador visualmente y True para que no.

#### **3. Wait enabled**

``` 
AUTOTHROTTLE_ENABLED = True
AUTOTHROTTLE_START_DELAY = 10
```

**Descripcion:** Se configura los segundos que se desea esperar entre cada request. 


## **Posibles mejoras**
1. Mejores nombres de variables.
2. Cambiar: 
```
        yield {
            "WordPress_site":site,
            "self.number_of_themes": self.number_of_themes,
            "theme_name":self.theme_name_dic,
            ...
        }
```
    Para que en vez de eso se utilice scrapy items.

3. No se si este correcto poner el site en el meta del pedido para luego poder extraerlo en la response ya estando en la funcion parse_search_site(self,response). Preguntarle a Erick.



# **Errores comunes**

1. Cambiar manualmente el nombre de la carpeta padre del proyecto. 
**Descripcion:** Esto no permitira que el spider se ejecute ya que el nombre original es utilizado en otros archivos del proyecto. Asi que el nombre debe ser el que se puso originalmente.
**Solucion:** Te tocara crear un nuevo proyecto scrapy con el nuevo nombre y pasar los own-settings del otro proyecto al nuevo archivo de settings. Tambien crea un spider en el nuevo proyecto y copia el contenido del wp_detector_spider.py a ese. 

2. Tratar de configurar proxys a nivel de scrapy
**Descripcion:** scrapy.Request(url=url, callback=self.parse, meta={'proxy': 'http://proxy:port'})
**Solucion:** En nuestro caso, tiene que ser a nivel del contexto de playwright. Es decir agregar el proxy en cada contexto del navegador.



# **Comando principal (Usar para wp_detector_spider)**

``` scrapy crawl wp_detector_spider -O prueba.csv ```



# **Otros comandos esenciales**

#### **Comando para crear un proyecto scrapy**
```scrapy startproject nombre_del_proyecto```

#### **Comando para crear un spider**

```scrapy genspider nombre_del_spider dominio.com```

**Nota:** Se considera que estas dentro de la carpeta nombre_del_proyecto

#### **Comando para ejecutar un spider**
```scrapy crawl nombre_del_spider```

**Nota:** El nombre del spider no es el nombre del archivo.py. Es el que se encuentra dentro de la clase con la variable name="nombre"

#### **Comando para ejecutar un spider y guardarlo en un csv**
```scrapy crawl nombre_del_spider -o nombre_archivo.csv```

**Nota:** Si el archivo no existe, se crea. Si existe, cada vez ejecutado este comando, su contenido será reemplazado con la nueva salida del spider.






