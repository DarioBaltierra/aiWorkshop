# Tutorial: Uso de Oracle Cloud Infrastructure (OCI) con servicios de IA GENERATIVA Y SELECTAI



Este tutorial te guiará a través de los pasos necesarios para trabajar con perfiles y modelos en una base de datos autónoma utilizando el SQL Web de OCI. Asegúrate de seguir cada paso cuidadosamente para completar con éxito las tareas.

## Prerrequisitos

Antes de comenzar, asegúrate de tener acceso a:
- Una **Base de Datos Autónoma** en OCI.
- Acceso al usuario `admin` <- **_IMPORTANTE_**
- El **SQL Web** habilitado logueado en usuario `admin` para ejecutar consultas.
- Regiones compatibles: `US Midwest (Chicago)`, `UK South (London)`, `Brazil East (Sao Paulo)`, `Germany Central (Frankfurt)`

---

## Paso 1: Creacion de Credenciales

Para comenzar, primero necesitamos crear las credenciales dentro de SQL en nuestra base de datos.

```sql
-- Create Credential with OCI API key
--
BEGIN                                                                         
  DBMS_CLOUD.CREATE_CREDENTIAL(                                               
    credential_name => 'GENAI_CRED',                                          
    user_ocid       => 'ocid1.user....',
    tenancy_ocid    => 'ocid1.tenancy....',
    private_key     => 'MIIEvgIBA....',
    fingerprint     => 'b2:22....'      
  );                                                                          
END;                                                                         
/

```

Es importante recordar que para poder obtener los datos debemos tener la API key y el fingerprint.

<img width="464" alt="image" src="https://github.com/user-attachments/assets/28b35f2c-d49b-4d3c-9c0b-8b0cd557c5d5" />


---

# Paso 2: Crear un perfil de IA utilizando **_`Llama 3`_**

A continuación, crearemos un perfil de IA llamado `ociai_llama`. Este perfil utilizará el modelo **meta.llama-3-70b-instruct** para ejecutar tareas de inteligencia artificial sobre los datos de la base de datos.

Primero, eliminamos el perfil si ya existe:

```sql
BEGIN
    -- Elimina el perfil si ya existe
    DBMS_CLOUD_AI.drop_profile(
        profile_name => 'ociai_llama',
        force => true
    );     
END;
/
```

Luego, creamos el perfil con el modelo `meta.llama-3-70b-instruct` llamando las tablas requeridas para el lab:

En owner pondremos el nombre del usuario al que pertenecen esas tablas.

En name pondremos el nombre de las tablas que vamos a ocupar

```sql
BEGIN
    DBMS_CLOUD_AI.create_profile (                                              
        profile_name => 'ociai_llama',
        attributes   => 
        '{"provider": "oci",
            "credential_name": "GENAI_CRED",
            "object_list": [
                {"owner": "SH", "name": "sales"},
                {"owner": "SH", "name": "products"},
                {"owner": "SH", "name": "countries"}
            ],
            "model": "meta.llama-3.1-70b-instruct"
            }');
END;
/
```

Después de crear el perfil, necesitamos configurarlo para usarlo en nuestras consultas:

```sql
BEGIN
    DBMS_CLOUD_AI.SET_PROFILE(
        profile_name => 'ociai_llama'
    );
END;
/
```
---

# Paso 3: Generar consultas con el perfil de IA en OCI Autonomous

En este numeral, aprenderemos a utilizar el paquete `DBMS_CLOUD_AI` para generar respuestas narrativas y consultas SQL automáticas utilizando la funcionalidad de SELECT AI en Oracle Cloud Infrastructure (OCI) Autonomous Database.

## 3.1 Consulta: (respuesta narrativa)
Para obtener una respuesta narrativa:

```sql
SELECT DBMS_CLOUD_AI.GENERATE(
    prompt => '¿Cuales son los productos mas vendidos?',
    profile_name => 'ociai_llama',
    action => 'narrate'
) AS chat
FROM dual;
```



## 3.2 Consulta: (consulta SQL generada)

```sql
SELECT DBMS_CLOUD_AI.GENERATE(
    prompt => '¿Cuales son los productos mas vendidos?',
    profile_name => 'ociai_llama',
    action => 'showsql'
) AS query
FROM dual;
```


# FIN

---

## ¡Gracias por seguir este tutorial!

Espero que este tutorial te haya sido útil y te permita sacar el máximo provecho de las capacidades de Oracle Cloud Infrastructure en tus proyectos. Si tienes alguna duda o sugerencia, no dudes en contactarme. Estoy siempre dispuesto a apoyar en el proceso de transformación digital y adopción de tecnologías innovadoras.

**[Jesús Darío Baltierra Pérez](www.linkedin.com/in/jesusdariobaltierraperez)**  
**Ingeniero en Tecnologías de la Información y Sistemas Inteligentes**  
**Maestría en Ciencia de Datos**  

¡Éxito en tu camino hacia la innovación tecnológica!

---
