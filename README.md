# Proyecto 5: API que realiza la Secuencia de Fibonacci en Julia

Este proyecto implementa una API en Julia que calcula los primeros 10 números de la secuencia de Fibonacci utilizando programación dinámica. La API responde a las solicitudes HTTP y devuelve los resultados en formato JSON.

## Miembros del Proyecto

- Andrés Gutiérrez: 30.662.790
- José Ramírez: 30.532.553
- Mauricio Romero: 29.704.772

## Descripción

La secuencia de Fibonacci es una secuencia de números en la que cada número es la suma de los dos números anteriores, excepto los primeros dos números, que son 0 y 1.

### Funcionalidad

1. **Función `fibonacci_dinamica(n)`**:
   - Calcula el n-ésimo número de la secuencia de Fibonacci utilizando programación dinámica.
   - Almacena los resultados intermedios en una tabla para evitar cálculos repetitivos.

2. **API**:
   - Devuelve los primeros 10 números de la secuencia de Fibonacci en formato JSON.

## Instalación

1. Instala Julia desde [el sitio oficial](https://julialang.org/downloads/).
2. Instala el paquete `HTTP.jl` y `JSON.jl`:
    ```julia
    using Pkg
    Pkg.add("HTTP")
    Pkg.add("JSON")
    ```

## Uso

1. Crea un archivo llamado `fibonacci_api.jl` con el siguiente contenido:

    ```julia
    using HTTP
    using JSON
    
    # Aqui nuestra función que hace uso de la programacion dinamica :)
    function fibonacci_dinamica(n::Int)
        if n < 0
            error("El número debe ser mayor o igual a 0")
        elseif n == 0
            return 0
        elseif n == 1
            return 1
        else
            tabla_fib = zeros(Int, n+1) # Definimos nuestra tabla llena de ceros para evitar hacer calculos de más y se vayan guardando los resultados intermedios
            tabla_fib[1] = 0
            tabla_fib[2] = 1
    
            # Se calculan los números de la sucesión y se almacenan en la tabla
            for i in 3:n+1
                tabla_fib[i] = tabla_fib[i-1] + tabla_fib[i-2] # Cada número es la suma de los dos anteriores
            end
            
            return tabla_fib[n+1] # Devolver el n-ésimo número de la sucesión de Fibonacci
        end
    end
    
    # Función que maneja las solicitudes a la API
    function manejar_solicitud(req::HTTP.Request)
        try
            # Aqui calculamos los 10 primeros numeros de la sucesión
            numeros_fibonacci = [fibonacci_dinamica(i) for i in 0:9]
            # Damos la respuesta en formato JSON
            respuesta = JSON.json(numeros_fibonacci)
            return HTTP.Response(200, respuesta) # Si el servidor colabora debe retornar el array con la sucesión
        catch e
            return HTTP.Response(500, "Error del servidor: $e") # En dado caso que el servidor no colabore D:
        end
    end
    
    # Inicia el servidor en el puerto 8080
    HTTP.serve(manejar_solicitud, "0.0.0.0", 8080)
    ```

2. Para ejecutar la API, abre una terminal en el directorio donde guardaste el archivo `fibonacci_api.jl` y ejecuta el siguiente comando:
    ```sh
    julia fibonacci_api.jl
    ```

3. Puedes probar la API abriendo un navegador web o utilizando una herramienta como `curl` o `Postman`. Por ejemplo, desde la terminal, puedes usar `curl` de la siguiente manera:
    ```sh
    curl http://localhost:8080/
    ```

    Esto debería devolver una respuesta similar a la siguiente:
    ```sh
    StatusCode        : 200
    StatusDescription : OK
    Content           : {91, 48, 44, 49...}
    RawContent        : HTTP/1.1 200 OK
                        Transfer-Encoding: chunked
    
                        [0,1,1,2,3,5,8,13,21,34]
    Headers           : {[Transfer-Encoding, chunked]}
    RawContentLength  : 24
    ```
