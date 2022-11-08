# ¿Cómo armar un template para SAM con serverless Framework

## Paso 1

Instalar cloudformation template parser (https://github.com/awslabs/aws-cfn-template-flip)

```pip install cfn-flip```

## Paso 2

Instalar NodeJS, versión LTS (https://nodejs.org/en/download/)

Esto nos brindara también el gestor de paquetes, NPM. (https://www.npmjs.com/)

## Paso 3

Instalar en el global de tu PC serverless framework

````npm i -g serverless````

## Paso 4

Dentro del proyecto, debería haber un file que se llame package.json. Este file nos permite visualizar una estructura de proyecto en NodeJS, pero para este caso solamente lo utilizaremos como gestor de dependencias con el fin de utilizar serverless framework.
Una vez valido el package.json, ejecutar el comando:

````npm i ````

## Paso 5

Relevar el serverless.yml, archivo padre que contiene todas las importaciones y definiciones de tu infraestructura cloud.

## Paso 6

Armar el package del proyecto serverless. Para ello vamos a ejecutar:

 ```` serverless package````

 Este comando nos creara una carpeta que se llama `.serverless`. Ahí dentro estará armado un json que compone toda tu infra cloud. El nombre de ese json sera: `cloudformation-template-update-stack.json`.

## Paso 7

Deberemos ejecutar en el root de nuestro proyecto el siguiente comando:

` cfn-flip ./.serverless/cloudformation-template-update-stack.json template.yaml`

Este comando lo que genera es un archivo YAML con todos los recursos que vamos a necesitar desplegar a traves de cloudformation utilizando AWS SAM.

No podremos ejecutar directamente. Debemos realizar algunas modificaciones.

## Paso 8
Abrir el template.yml. Con el file abierto empezaremos a realizar modificaciones.

 - Agregar la linea
`Transform: 'AWS::Serverless-2016-10-31'` luego de `AWSTemplateFormatVersion: '2010-09-09'`

 - Reemplazar `AWS::Lambda::Function` por `AWS::Serverless::Function` en todas las lambdas que correspondan.

 - Modificar dentro de las properties:
    * Eliminar `Code`
    * Agregar `CodeUri` apuntando al path donde contiene el codigo fuente.
    * Eliminar los recursos de versiones de lambda. Ejemplo `HelloWorldLambdaVersionMYyxQwpQJTQujobHzr5UgcNCApe6Kwy6DAkS5JZPE`. Ya que para SAM es irrelevante.
    * Eliminar los Outputs de las versiones de Lambda.

## Paso 9

Configurar las credenciales de AWS que provienen de la consola de AWS. Eso lo pueden encontrar en la sección `Command line or programmatic access` cuando acceden a AWS.

` export AWS_ACCESS_KEY_ID=dummykey`

` export AWS_ACCESS_SECRET_KEY=dummysecretkey`

También pueden dejarlas configuradas en el archivo `$HOME/.aws/credentials` y ahí dejar armados distintos profiles.
Una vez armado el profile, puede ejecutar:

` export AWS_PROFILE=dummyprofile`


## Paso 10
Una vez finalizada la adaptación del template.yml, iremos al buildeo de SAM. Para ello, ejecutaremos:
 `sam build --region $AWS_REGION`. En el caso que `$AWS_REGION` no este definida, puede ser usar directamente us-east-1 (Si corresponde).

Una vez finalizado el build, validar que haya buildeado correctamente revisando la carpeta .aws-sam

## Paso 11

Todo esto nos debería dejar listo para que el pipeline pueda desplegarlo automaticamente. Si quisieramos validarlo manualmente, hay que realizar lo siguiente:

----
### YAPA

En el caso que el archivo `samconfig.toml`, deberíamos ejecutar `sam deploy --guided`. Este comando te va a permitir armar un deploy con las configuraciones necesarias para AWS.

---


### Paso 11.1

Debemos ejecutar el comando `sam deploy --region us-east-1 --profile $AWS_PROFILE  --template-file "template.yaml" --stack-name $stack_name --capabilities CAPABILITY_NAMED_IAM`

## Paso 12

No te la mandes y hace el commit.


