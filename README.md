# Cifrater-dockenus
uso de ejemplos de Docker,un cifrado que convierte carácteres ASCII a una representación de 512 bits cruda, para practicar el uso de contenedores
primero lo que hicimos fue:
idrar un código básico 

python:
```
import sys

def cifrado_not_512(valor_original):
    # Definimos el límite de 512 bits
    BITS = 512
    MAX_VAL = (1 << BITS) - 1
    
    # Operación NOT lógica en el espacio de 512 bits
    resultado = MAX_VAL ^ valor_original
    return resultado

if __name__ == "__main__":
    try:
        # Recibimos el número por argumento
        entrada = int(sys.argv[1])
        salida = cifrado_not_512(entrada)
        
        print(f"--- Resultado 512-bit NOT ---")
        print(f"Entrada: {entrada}")
        print(f"Salida:  {salida}")
    except:
        print("Uso: python cifrador.py <numero_entero>")
```
éste sólo tomaba un número a la vez, luego idee un boceto 
python 
```
def cifrado_not(str(texto)):

BITS = 512

MAX_VAL = (1 << BITS) - 1

i = 0

while caracter != \0:

caracter = texto[i:1]

i += 1

caracter = ord(caracter)

resultado = MAX_VAL ^ caracter

return resultado

if __name__ == "__main__":

try:

entrada = sys.argv[1]

salida = cifrado_not(entrada)

print("----- Resultado 512-bit NOT -----")

print(f"Entrada: {entrada}")

print(f"Salida: {salida}")

except:

print("Uso: python3 cifrador.py <texto>")
```
que luego mejoramos a

python
```
import sys

def cifrado_not(texto):
    # Definimos el límite de 512 bits
    BITS = 512
    MAX_VAL = (1 << BITS) - 1
    
    resultado_final = ""
    
    # En Python, 'for caracter in texto' es como recorrer un array 
    # hasta encontrar el fin de la cadena.
    for caracter in texto:
        # Obtenemos el valor ASCII/Unicode
        valor_numerico = ord(caracter)
        
        # Aplicamos tu lógica de inversión de 512 bits
        valor_cifrado = MAX_VAL ^ valor_numerico
        
        # El truco de la concatenación: usamos el operador +
        # Convertimos el número a string para poder pegarlo
        resultado_final += str(valor_cifrado) + " "
    
    return resultado_final.strip()

if __name__ == "__main__":
    if len(sys.argv) > 1:
        # Unimos todos los argumentos por si el texto tiene espacios
        entrada = " ".join(sys.argv[1:])
        salida = cifrado_not(entrada)
        
        print("----- Resultado 512-bit NOT -----")
        print(f"Entrada: {entrada}")
        print(f"Salida: {salida}")
    else:
        print("Uso: python3 cifrador.py <texto>")
```

le agreamos una imagen de modo Alpine (muy importante darle sudo)
Dockerfile
```
# Usamos una imagen ultra ligera
FROM python:3.11-alpine

# Copiamos el script al contenedor
COPY cifrador.py /app/cifrador.py

# Directorio de trabajo
WORKDIR /app

# Comando por defecto
ENTRYPOINT ["python", "cifrador.py"]
```
y usamos 
bash
```
docker build -t cifrador-txt .
docker run --rm cifrador-txt "Hola"
```
aquí ne dió error por no usar sudo, por eso lo arreglamos 

bash
```
sudo docker build -t cifrador-txt .
sudo docker run --rm cifrador-txt "Hola"
```
si quieren pueden automatizarlo (para no tener que usar sudo siempre)
bash
```
# aquí creamos el grupo por si no existe
sudo groupadd docker
#pones tu usuario
sudo usermod -aG docker $USER
#y el importantísimo apply changes
newgrp docker
```

a mí me funcionó así (y lo mejor es que puede ser reversible 

Usage
```
uziel_gamma@uziel-PCINUX:~/docker$ sudo docker run --rm cifrador-txt "Hola"
----- Resultado 512-bit NOT -----
Entrada: Hola
Salida: 13407807929942597099574024998205846127479365820592393377723561443721764030073546976801874298166903427690031858186486050853753882811946569946433649006084023 13407807929942597099574024998205846127479365820592393377723561443721764030073546976801874298166903427690031858186486050853753882811946569946433649006083984 13407807929942597099574024998205846127479365820592393377723561443721764030073546976801874298166903427690031858186486050853753882811946569946433649006083987 13407807929942597099574024998205846127479365820592393377723561443721764030073546976801874298166903427690031858186486050853753882811946569946433649006083998
uziel_gamma@uziel-PCINUX:~/docker$

#me encanta
```
