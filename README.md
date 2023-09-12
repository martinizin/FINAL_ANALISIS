# FINAL_ANALISIS
# INTEGRANTES:
- Jimenez Martin
- Moreira Alejandro
- Novillo Ismael
- Valenzuela Jared
- Yepez Joseph

- Enlace video explicativo datawarehouse: https://www.youtube.com/watch?v=IoUcdnGCpOY

- Enlace video dashboards explicativos: https://www.youtube.com/watch?v=dDJnu9vqap4

ESQUEMA DE DATAWARE HOUSE

![image](https://github.com/martinizin/FINAL_ANALISIS/assets/117743846/665d5a7f-45bb-413c-b098-d29175828816)

# CODIGO PARA EL PASO DE DATOS CSV A JSON O VICEVERSA


import pandas as pd

df = pd.read_csv('tematica.csv')

def csv_to_json(csv_file, json_file):

df = pd.read_csv(csv_file)

    # Conversion
    
df.to_json(json_file, orient='records', lines=True)

csv_filename = 'aquí irá el nombre del archivo dependiendo la temática'

json_filename = 'el nombre que le queramos poner a nuestro json'

csv_to_json(csv_filename, json_filename)

# CÓDIGO PARA LA LIMPIEZA/FILTRACIÓN DE DATOS

 - Función para limpiar los caracteres no deseados de una cadena y convertirlo de csv a json
def limpiar_cadena(cadena):

    ### Eliminamos las comas, puntos y comas, y espacios vacíos
    
    cadena_limpia = ''.join(caracter for caracter in cadena if caracter not in [',', ';', ' '])
    
    return cadena_limpia

  - Función para convertir un archivo CSV a JSON con la limpieza realizada previamente
def csv_to_json(csvFilePath, jsonFilePath):

    jsonArray = []
      
    with open(csvFilePath, encoding='utf-8') as csvf: 
    
        csvReader = csv.DictReader(csvf) 

        for row in csvReader:
        
            # Realizamos la limpieza de cadenas a cada valor en el diccionario
            
            cleaned_row = {key: limpiar_cadena(value) for key, value in row.items()}
            
            jsonArray.append(cleaned_row)
  
    with open(jsonFilePath, 'w', encoding='utf-8') as jsonf: 
    
        jsonString = json.dumps(jsonArray, indent=4)
        
        jsonf.write(jsonString)

    
csvFilePath = r'accidentes.csv'

jsonFilePath = r'accidentes.json'

csv_to_json(csvFilePath, jsonFilePath)

# Envio de datos de mysql a mongodb compass

### Temática: Eventos Deportivos

import csv
import pandas as pd
import json


# Función para convertir un archivo JSON a CSV con limpieza de cadenas

def json_to_csv(jsonFilePath, csvFilePath):

    jsonData = []

    # Cargar el archivo JSON
    
    with open(jsonFilePath, 'r', encoding='utf-8') as jsonf:
    
        jsonData = json.load(jsonf)

    # Crear una lista de encabezados
    
    if jsonData:
    
        headers = list(jsonData[0].keys())

        # Abrir el archivo CSV y escribir los encabezados
        
        with open(csvFilePath, 'w', encoding='utf-8', newline='') as csvf:
        
            csvWriter = csv.DictWriter(csvf, fieldnames=headers)
            
            csvWriter.writeheader()

            # Escribir los datos limpios en el archivo CSV
            
            for data in jsonData:
            
                cleaned_data = {key: limpiar_cadena(value) for key, value in data.items()}
                
                csvWriter.writerow(cleaned_data)

jsonFilePath = 'eventslimpio.json'

csvFilePath = 'events3.csv'

json_to_csv(jsonFilePath, csvFilePath)

Este código carga datos desde un archivo JSON llamado eventslimpio, limpia las cadenas de caracteres de caracteres
 
no deseados y los convierte en un archivo CSV con encabezados. La función limpiar_cadena es utilizada para eliminar 

comas, puntos y espacios en blanco de las cadenas de caracteres antes de escribir los datos en el archivo CSV.


- eventos

import pandas as pd

from pymongo import MongoClient

def load_csv_to_mongodb(database_name, collection_name, csv_file):

    client = MongoClient()

    db = client[database_name]

    collection = db[collection_name]
    
    df = pd.read_csv(csv_file)

    records = df.to_dict(orient='records')
    
    collection.insert_many(records)

    print(f"Archivo {csv_file} cargado correctamente en MongoDB.")
    
    client.close()

database_name = "Eventos"  

collection_name = "Eventos"

csv_file = r"C:\\Users\\Alejandro\\Documents\\Analisis\\events2.csv"

load_csv_to_mongodb(database_name, collection_name, csv_file)

Este código carga datos desde un archivo CSV llamado events2 en una base de datos de MongoDB utilizando
 
las bibliotecas de Pandas y PyMongo, y puede ser útil cuando se desea transferir datos tabulares desde 

archivos CSV a una base de datos NoSQL como MongoDB.

![Eventos](https://github.com/martinizin/FINAL_ANALISIS/assets/117743484/e8d997a5-f464-44ff-93c7-0e1470bfe03e)

# Envío de json a RavenDB

### Temática: Accidentes de tránsito

Carga de Datos CSV a RavenDB

pip install -U pyravendb

pip install pandas

#ACCIDENTES

#Cargar archivos a RAVENDB

from pyravendb.store import document_store

import pandas as pd

class CustomObject:

    def __init__(self, data_dict):
    
        self.__dict__ = data_dict
        
    def load_csv_to_ravendb(store_url, database_name, csv_file):

    # Creamos una instancia del DocumentStore
    
    store = document_store.DocumentStore(store_url, database_name)

    # Inicializar el DocumentStore
    store.initialize()

    # Cargar el archivo CSV en un DataFrame de pandas
    df = pd.read_csv(csv_file)

    # Convertir el DataFrame a una lista de diccionarios (formato JSON)
    records = df.to_dict(orient='records')

    # Abrir una sesión en la base de datos RavenDB
    with store.open_session() as session:
        # Insertar los registros en la base de datos RavenDB
        for record in records:
            custom_object = CustomObject(record)
            session.store(custom_object)
        session.save_changes()

    print(f"Archivo {csv_file} cargado correctamente en RavenDB.")

    if __name__ == "__main__":
    
    store_url = "http://localhost:8080"  
    
    database_name = "accidentes"  
    
    csv_file = "accidentes.csv"  

    load_csv_to_ravendb(store_url, database_name, csv_file)


- Explicacion:

Este código se encarga de cargar datos desde un archivo CSV en una base de datos RavenDB. Aquí está la explicación detallada:

Importamos las bibliotecas necesarias:

pyravendb.store.document_store: Para interactuar con la base de datos RavenDB.

pandas as pd: Para trabajar con DataFrames de pandas.

Define una clase CustomObject:

Esta clase permite convertir un diccionario en un objeto personalizado que puede ser almacenado en RavenDB.

Define una función llamada load_csv_to_ravendb:

Esta función toma tres argumentos: store_url (URL de la base de datos RavenDB), database_name (nombre de la base de datos en RavenDB),

 y csv_file (ruta del archivo CSV de entrada).

Crea una instancia del DocumentStore de RavenDB y lo inicializa.

Carga el archivo CSV en un DataFrame de pandas.

Convierte el DataFrame a una lista de diccionarios (formato JSON).

Abre una sesión en la base de datos RavenDB y almacena los registros en ella como objetos personalizados.

Guarda los cambios en la base de datos RavenDB.

En el bloque if __name__ == "__main__":, se especifica la URL de la base de datos RavenDB (store_url), el nombre de la base de datos

 (database_name), y la ruta del archivo CSV de entrada (csv_file).

Se llama a la función load_csv_to_ravendb con los argumentos mencionados, lo que ejecuta el proceso de carga de datos desde el archivo 

CSV a la base de datos RavenDB. En resumen, el primer código convierte un archivo CSV en un archivo JSON después de limpiar los datos,

mientras que el segundo código carga datos desde un archivo CSV a una base de datos RavenDB. Cada código tiene su propia funcionalidad y objetivo.

# Paso de datos Json a Couchdb y de Couchdb a SQL Managment Studio

### Temática: Eventos y noticias mundiales

import pandas as pd

- Especificamos la ruta del archivo CSV que deseamos leer

archivo_csv = 'abcnews-date-text.csv'

- Leemos el archivo CSV en un DataFrame de pandas

data = pd.read_csv(archivo_csv)

- Mostramos las primeras filas del DataFrame para verificar la lectura

data.head()

- Leemos  el archivo CSV 

archivo_csv = 'abcnews-date-text.csv'  

data = pd.read_csv(archivo_csv)

- Transformamos el DataFrame a JSON y guardamos

archivo_json = 'abc-news.json' 

data.to_json(archivo_json, orient='records', lines=True)

print(f'Archivo JSON guardado en {archivo_json}')

- Conexión a la base de datos CouchDB

server = couchdb.Server('http://admin:admin@localhost:5984/') 

db_name = 'noticias2'


- Cargamos y procesamos el archivo JSON línea por línea

ruta_json = 'abc-news.json'  

with open(ruta_json, 'r', encoding='utf-8') as file:
    
    for line in file:
        
        try:
            
            data = json.loads(line)
            
            - Guardamos los datos en la base de datos CouchDB
            
            db = server[db_name]
            
            db.save(data)
            
        except json.JSONDecodeError as e:
            
            print(f"Error al decodificar JSON en línea: {line}")

- Conexión a SQL Server

server = 'DESKTOP-A3ENU4G'

database = 'NOTICIAS'

conn_str = f'DRIVER={{SQL Server}};SERVER={server};DATABASE={database};Trusted_Connection=yes'

connection = pyodbc.connect(conn_str)

- Inserción de los datos de couchdb en SQL Server

cursor = connection.cursor()

for doc in db.view('_all_docs', include_docs=True):

    - Definimos una consulta SQL de inserción y ejecutamos
    
    insert_query = "INSERT INTO Noticias2 (publish_date, headline_text) VALUES (?, ?)"
    
    cursor.execute(insert_query, (doc['publish_date'], doc['headline_text']))

- Confirmamos la transacción y cerramos la conexión

connection.commit()

connection.close()


