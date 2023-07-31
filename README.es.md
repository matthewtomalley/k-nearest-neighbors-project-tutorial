<!-- hide -->
# K-Vecinos más cercanos
<!-- endhide -->

- Comprender un dataset nuevo.
- Modelar los datos utilizando un KNN.
- Analizar los resultados y optimizar el modelo.

## 🌱  Cómo iniciar este proyecto

Esta vez no se hará Fork, tómate un tiempo para leer estas instrucciones:

1. Crear un nuevo repositorio basado en el [proyecto de Machine Learing](https://github.com/4GeeksAcademy/machine-learning-python-template/generate) [haciendo clic aquí](https://github.com/4GeeksAcademy/machine-learning-python-template).
2. Abre el repositorio creado recientemente en Codespace usando la [extensión del botón de Codespace](https://docs.github.com/en/codespaces/developing-in-codespaces/creating-a-codespace-for-a-repository#creating-a-codespace-for-a-repository).
3. Una vez que el VSCode del Codespace haya terminado de abrirse, comienza tu proyecto siguiendo las instrucciones a continuación.

## 🚛 Cómo entregar este proyecto

Una vez que hayas terminado de resolver los ejercicios, asegúrate de confirmar tus cambios, hazle "push" al fork de tu repositorio y ve a 4Geeks.com para subir el enlace del repositorio.

## 📝 Instrucciones

### Sistema de recomendación de películas

¿Seríamos capaces de predecir qué películas podrían ser o no un éxito comercial? Este conjunto de datos recopila parte del conocimiento de la API [TMDB](https://www.themoviedb.org/?language=es), que contiene solo 5000 películas del número total. Se disponibilizan los siguientes recursos:

- **tmdb_5000_movies**: `https://raw.githubusercontent.com/4GeeksAcademy/k-nearest-neighbors-project-tutorial/main/tmdb_5000_movies.csv`

- **tmdb_5000_credits**: `https://raw.githubusercontent.com/4GeeksAcademy/k-nearest-neighbors-project-tutorial/main/tmdb_5000_credits.csv`

#### Paso 1: Carga del conjunto de datos

Debemos cargar los dos ficheros y almacenarlos en dos estructuras de datos (DataFrames de Pandas) separadas. Por un lado tendremos almacenada la información de las películas y sus créditos.

#### Paso 2: Creación de una base de datos

Crea una base de datos para almacenar los dos DataFrames en tablas distintas. A continuación, une las dos tablas con SQL (e intégralo con Python) para generar una tercera tabla que contenga información de ambas unificada. La clave a través de la cual se puede hacer la unión es el título de la película (`titulo`).

Ahora, limpia la tabla generada y deja solo las siguientes columnas:

- `movie_id`
- `title`
- `overview`
- `genres`
- `keywords`
- `cast`
- `crew`

#### Paso 3: Transforma los datos

Como puedes ver, hay algunas columnas con formato JSON. Selecciona, de cada uno de los JSONs, selecciona el atributo `name` y reemplaza las columnas `genres` y `keywords`. Para la columna `cast`, selecciona los tres primeros nombres.

Las únicas columnas que quedan por modificar son `crew` (equipo) y `overview` (resumen). Para la primera columna, transfórmala para que contenga el nombre del director. Para la segunda, conviértela en una lista.

Una vez hayamos terminado de procesar las columnas y que el modelo de recomendación no se confunda, por ejemplo, entre *Jennifer Aniston* y *Jennifer Conelly*, quitaremos los espacios entre las palabras. Aplica esta función a las columnas `genres`, `cast`, `crew` y `keywords`.

Por último, reduciremos nuestro conjunto de datos combinando todas nuestras columnas convertidas anteriores en una sola columna llamada `tags` (que crearemos). Esta columna ahora tendrá todos los elementos separados por comas y luego las reemplazaremos por espacios en blanco. Debería quedar algo así:

```py
new_df['tags'][0]

>>>>'In the 22nd century, a paraplegic Marine is dispatched to the moon Pandora on a unique mission, but becomes torn between following orders and protecting an alien civilization. Action Adventure Fantasy ScienceFiction cultureclash future spacewar spacecolony society spacetravel futuristic romance space alien tribe alienplanet cgi marine soldier battle loveaffair antiwar powerrelations mindandsoul 3d SamWorthington ZoeSaldana SigourneyWeaver JamesCameron'
```

#### Paso 4: Construye un KNN

Para resolver este problema crearemos nosotros nuestro propio KNN. Lo primero de todo es vectorizar el texto siguiendo los mismos pasos que aprendiste en la lección anterior.

Una vez lo hayas hecho, tendríamos que elegir una distancia para comparar texto. En este módulo hemos visto algunas, y la única compatible con lo que queremos hacer es la `distancia coseno`:

```py
from sklearn.metrics.pairwise import cosine_similarity

similarity = cosine_similarity(vectors)
```

Con este código podremos ver la similaridad existente entre nuestros vectores (representaciones vectoriales de la columna `tags`).

Finalmente, podemos diseñar nuestra función de similaridad basada en la distancia del coseno. Nuestra propuesta es la siguiente:

```py
def recommend(movie):
    movie_index = new_df[new_df["title"] == movie].index[0]
    distances = similarity[movie_index]
    movie_list = sorted(list(enumerate(distances)), reverse = True , key = lambda x: x[1])[1:6]
    
    for i in movie_list:
        print(new_df.iloc[i[0]].title)
```

De tal forma que devolveríamos las 5 películas más similares a la que introduzcamos en el título. Podríamos utilizarla como sigue:

```py
recommend("introduce una película")
```