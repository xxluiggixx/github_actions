
# github_actions
## Push
Push se entiende cuando mandamos al repositorio remoto las actualizaciones del repositorio local.

Este script acaparará el trigger push mientras suceda en los branches main o releases/** (cualquier rama con nombre release o que nazca a partir de este nombre) y solo se tendrá en cuenta si se han modificado archivos de formato .js.

```
on: #Acaparará los triggers de este workflow
	push: #El tipo de trigger: push
		branches: #Las ramas en las que se activará el workflow
			- 'main' #Se activará si se hace push en la rama main
			- 'releases/**' #O en la rama releases y derivados
		paths: #Serán las rutas que se tendrán en cuenta para activar el workflow
			- '**.js' #Todos los archivos .js en el repositorio
```

## Pull Request
El Pull Request será la petición de mergear una rama aislada con otra rama (generalmente principal).

Este script se activará por un PR, específicamente en el momento de abrirlo y etiquetarlo (opened y labeled), seguirá el resto de reglas que el push.


```
on: #Acaparará los triggers de este workflow
    pull_request: #El tipo de trigger: pr
        types: #Los estados que activarán el trigger
            - [opened, labeled] #Tomará los estados del PR
        branches: #Las ramas en las que se activará el workflow
            - 'releases/**' #En la rama releases y derivados
        paths: #Serán las rutas que se tendrán en cuenta para activar el workflow
            - '**.js' #Todos los archivos .js en el repositorio
```

## Issues
Los issues son foros donde se anuncian difuncionalidades del código por parte de la comunidad o los propios developers.

Tiene la misma lógica de los PR, sin embargo, los types cambiarán.

```
on: #Acaparará los triggers de este workflow
	issues: #El tipo de trigger: issues
		types: #Los estados que activarán el trigger
			- [opened, edited, closed]
```

## Issue Comment
Cuando se generen comentarios en un Issue o un PR se activará este trigger.

Tendremos 2 casos, el primero donde se ejecutará un issue comment, y el segundo donde se ejecutará en un PR comment.

En este caso cada que se cree o elimine un comentario de un Issue se activará y correrá el workflow.

```
on:
	issue_comment: 
		types:  [created, deleted]
```
En este segundo ejemplo también trabajaremos por issue_comment, sin embargo, añadiremos un job que verificará que estamos sobre un pull request con el condicional dado, si es verdad, podrá continuar.

```
on: issue_comment
jobs:
	pr_commented:
		name : PR comment
		if : ${{ github.event.issue.pull_request }}
```
## Workflow dispatch
Los Workflow dispatch son Workflows de activación manual, estos poseen la característica de poder setear inputs, los configuraremos dentro del archivo YAML.

Este Workflow contendrá 3 variables a ingresar las cuales serán alerta, tags y enviroment, serán contenidas por la etiqueta inputs y en cada una especificaremos sus características. La primera tendrá una descripción, será obligatoria, tendrá medio como valor por defecto y será de tipo choice (las opciones serán alto, medio o bajo). Los otros 2 casos son parecidos, donde tags será un boolean opcional y enviroments un string obligatorio.

```
on: 
	workflow_dispatch:
		inputs:
			alerta:
				description : 'Nivel'
				required : true
				default : medio
				type : choice
				option :
				- bajo
				- medio
				- alto

			tags:
				description : 'Opcional'
				required : false
				****type : boolean

			enviroment:
				description : 'Objetivo'
				required : true
				type : string
```
## Schedule
Los schedules son un tipo de trigger que se activará automáticamente por una regla de tiempo.

```
on:
	schedule:
		- cron : '30 5,17 * * *'
```
Puedes notar que hay 2 valores puestos (30 - 5,17) y 3 asteriscos, esta es una notación de cronograma que indicará valores a nivel de tiempo.


[Minuto, Hora, Dia del mes, Mes, Día de la semana]

Minuto (0 - 59)
Hora (0 - 23) 
Día del mes (1 - 31) 
Mes (1 - 12 o JAN - DEC)
Día de la semana ( 0 - 6 o SUN - SAT)
Si el valor tiene un asterisco significa que será cíclico, si tiene coma significa que agrupará 2 valores

Por lo que nuestro '30 5,17 * * *' significa a las 5:30AM y 5:30PM todos los días todos los meses del año.