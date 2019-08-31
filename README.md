# Comando para iniciar

	docker-compose up

## Comando para ver las instancias corriendo

	docker ps

## Comando para correr un comando en una instancia específica, los que aparecen con el comando anterior

	docker exec -it mongocluster_node1_1 mongo


# Pasos para configurar el conjunto de replicación en tres nodos

## Paso 1
### Del nodo 1

	db = (new Mongo('localhost:27017')).getDB('test')

## Paso 2
### Configuración de los 3 nodos juntos (ejecutar desde uno de los 3 nodos)

	config = {
		"_id": "comments",
		"members":[
			{
				"_id": 0,
				"host": "node1:27017"
			},
			{
				"_id": 1,
				"host": "node2:27017"
			},
			{
				"_id": 2,
				"host": "node3:27017"
			},
		] 
	}

## Paso 3
### Después de escribir el archivo de configuración, necesitamos iniciar la replicación de datos, del conjunto de replicación de datos "comments"

	rs.initiate(config)


Aplicar comandos anteriores al primer nodo, luego volver a iniciar en el primer nodo, y ejecutar los demas

Ejecutar el siguiente comando en los nodos secundarios, para que se conecten a la base de datos.

db = (new Mongo('localhost:27017')).getDB('test')


En el nodo primario 

	comments:PRIMARY> db.mycollection.insert({comment: "Hola, ¿cómo estas?"})
	WriteResult({ "nInserted" : 1 })


	comments:PRIMARY> db.mycollection.find()
	{ "_id" : ObjectId("5d6afa1ade794fd84f96d72a"), "comment" : "Hola, ¿cómo estas?" }


## Configurar los nodos secundarios como esclavos

Para que no se pueda crear datos desde ellos y solamente desde el maestro

	comments:SECONDARY> db.mycollection.find()
	{ "_id" : ObjectId("5d6afa1ade794fd84f96d72a"), "comment" : "Hola, ¿cómo estas?" }


# Referencias

https://www.youtube.com/watch?v=vVTExFrUxE4

