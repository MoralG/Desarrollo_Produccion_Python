## Tarea 1 (3 puntos): Entorno de desarrollo

#### Formas parte del equipo de desarrollo de la aplicación “Gestión IESGN”, aplicación web desarrollada con python, con el framework django. Vamos a configurar tu equipo como entorno de desarrollo para trabajar con la aplicación, para ello:

* Realiza un fork del repositorio de GitHub: https://github.com/jd-iesgn/iaw_gestionGN.
* Clona el repositorio en tu equipo.

~~~
git clone git@github.com:MoralG/iaw_gestionGN.git
~~~

---------------------------------------------------------------------------------------

* Crea un entorno virtual python3 e instala las dependencias necesarias para que funcione el proyecto (fichero requierements.txt).

~~~
python3 -m venv django2
source django2/bin/activate
pip install -r requirements.txt
~~~

###### Puede que tengamos que instalar las dependencias:
~~~
sudo apt install python3-dev
sudo apt-get install libjpeg-dev zlib1g-dev
~~~

---------------------------------------------------------------------------------------

* Comprueba que vamos a trabajar con una base de datos sqlite (gestion\settings.py). ¿Cómo se llama la base de datos que vamos a crear?

###### Nuestra base de datos se va a llamar *djangodb*
~~~
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'djangodb'),
    }
}
~~~


---------------------------------------------------------------------------------------

* Crea la base de datos: python3 manage.py migrate. A partir del modelo de datos se crean las tablas de la base de datos.
~~~
python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, centro, contenttypes, convivencia, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying centro.0001_initial... OK
  Applying centro.0002_cursos_equipoeducativo... OK
  Applying centro.0003_auto_20161102_1656... OK
  Applying centro.0004_auto_20161102_1721... OK
  Applying centro.0005_auto_20161105_1217... OK
  Applying centro.0006_auto_20161106_1741... OK
  Applying convivencia.0001_initial... OK
  Applying sessions.0001_initial... OK
~~~

---------------------------------------------------------------------------------------

* Añade los datos de prueba a la base de datos. Para más información: https://coderwall.com/p/mvsoyg/django-dumpdata-and-loaddata. Utiliza el fichero datos.json.
~~~
./manage.py loaddata datos.json 
   Installed 89 object(s) from 1 fixture(s)
~~~

---------------------------------------------------------------------------------------

* Ejecuta el servidor web de desarrollo y comprueba en el navegador que la aplicación está funcionando.
~~~
python manage.py runserver
~~~
![Tarea1.1](Tarea1.1_Python2.png)

---------------------------------------------------------------------------------------

* Entra en la zona de administración para comprobar que los datos se han añadido correctamente. Usuario: admin contraseña: asdasd1234.

![Tarea1.2](Tarea1.2_Python2.png)

---------------------------------------------------------------------------------------

* Accede con el usuario usuario (contraseña: asdasd1234).

![Tarea1.3](Tarea1.3_Python2.png)










## Tarea 2 (1 punto): Desarrollando nuestra aplicación

#### Vamos a realizar un cambio en la aplicación y comprobar que los cambios se realizan correctamente.

* Modifica la página inicial de la aplicación para que aparezca tu nombre.
Sube los cambios al repositorio
~~~
 <div class="masthead">
        <h3 class="text-muted">Gestiona - IES Gonzalo Nazareno - Alejandro Morales</h3>

      </div>
~~~

---------------------------------------------------------------------------------------

* Muestra una captura de pantalla donde sea la modificación realizada. (1 punto)

![Tarea1.4](Tarea1.4_Python2.png)





## Tarea 3 (4 puntos): Entorno de producción

#### Vamos a realizar el despliegue de nuestra aplicación en un entorno de producción, para ello vamos a utilizar una instancia del cloud, para ello:

* Instala en el servidor los servicios necesarios (apache2, mysql, …). Instala el módulo de apache2 para ejecutar código python.
~~~
sudo apt install apache2
sudo apt install libapache2-mod-wsgi-py3
sudo apt install mysql-common 
~~~

---------------------------------------------------------------------------------------

* Clona tu repositorio en el DocumentRoot de tu virtualhost.
~~~
sudo apt install git
cd /var/www/html/
sudo git clone https://github.com/MoralG/iaw_gestionGN.git
~~~

---------------------------------------------------------------------------------------

* Crea un entorno virtual e instala las dependencias de tu aplicación.
~~~
sudo apt install python3-venv
python3 -m venv django2
source django2/bin/activate
pip install -r requirements.txt
~~~

---------------------------------------------------------------------------------------

###### Puede que tengamos que instalar las dependencias:
~~~
sudo apt-get install python3 python-dev python3-dev build-essential libssl-dev libffi-dev libxml2-dev libxslt1-dev zlib1g-dev python-pip libjpeg-dev
~~~

---------------------------------------------------------------------------------------

* Instala el módulo que permite que python trabaje con mysql:
~~~
sudo apt install python3-mysqldb
~~~

###### Y en el entorno virtual:
~~~
pip install mysql-connector-python
~~~

---------------------------------------------------------------------------------------

* Configura un virtualhost en apache2 con la configuración adecuada para que funcione la aplicación. El punto de entrada de nuestro servidor será iaw_gestionGN/gestion/wsgi.py.
###### Creamos un fichero _.conf_ y metemos los siguiente:
~~~
<VirtualHost *:80>

        ServerName www.appython.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/django_tutorial

        WSGIScriptAlias / /var/www/django_tutorial/django_tutorial/wsgi.py

        WSGIDaemonProcess django user=www-data group=www-data processes=5 python-path=/var/www/django_tutorial

        <Directory /var/www/django_tutorial>
                WSGIProcessGroup django
                WSGIApplicationGroup %{GLOBAL}
                Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
~~~

###### Tenemos que poner nuestra NameServer en el fichero */var/www/django_tutorial/django_tutorial/settings.py
~~~
ALLOWED_HOSTS = ['www.appython.com']
~~~

###### Reiniciamos el servicio:
~~~
sudo systemctl restart apache2.service
~~~

###### Puede que tengamos que activar el módulo wsgi
~~~
sudo a2enmod wsgi
~~~

---------------------------------------------------------------------------------------

* Crea una base de datos y un usuario en mysql.
~~~

~~~

---------------------------------------------------------------------------------------

* Configura la aplicación para trabajar con mysql, para ello modifica la configuración de la base de datos en el archivo settings.py:
~~~
  DATABASES = {
      'default': {
          'ENGINE': 'mysql.connector.django',
          'NAME': 'myproject',
          'USER': 'myprojectuser',
          'PASSWORD': 'password',
          'HOST': 'localhost',
          'PORT': '',
      }
  }
~~~

---------------------------------------------------------------------------------------

* Crea las tablas de la base de datos y carga los datos de pruebas. Accede a mysql y comprueba que se han creado de forma adecuada.

---------------------------------------------------------------------------------------

* Desactiva en la configuración (fichero settings.py) el modo debug a False. Para que los errores de ejecución no den información sensible de la aplicación.

---------------------------------------------------------------------------------------

* Muestra la página funcionando.









## Tarea 4 (4 puntos): Modificación de la aplicación en el entorno de producción

#### Vamos a realizar cambios en el entorno de desarrollo y posteriormente vamos a subirlas a producción.

* Modifica la página inicial para que muestre otra imagen. Despliega los cambios en el servidor de producción.

---------------------------------------------------------------------------------------

* Vamos a crear una nueva tabla en la base de datos, para ello sigue los siguientes pasos:

1. Añade un n uevo modelo al fichero centro/models.py:
~~~
 class Modulos(models.Model):	
     Abr = models.CharField(max_length=4)
     Nombre = models.CharField(max_length=50)
     Unidad = models.ForeignKey(Cursos,blank=True,null=True,on_delete=models.SET_NULL)
		
     def __unicode__(self):
         return self.Abr+" - "+self.Nombre 		

     class Meta:
         verbose_name="Modulo"
         verbose_name_plural="Modulos"
~~~
2. Crea una nueva migración: python3 manage.py makemigrations.

3. Y realiza la migración: python3 manage.py migrate
4. Añade el nuevo modelo al sitio de administración de django:

Para ello cambia la siguiente línea en el fichero centro/admin.py:
~~~
from centro.models import Cursos,Alumnos,Departamentos,Profesores,Areas
~~~

Por esta otra:
~~~
from centro.models import Cursos,Alumnos,Departamentos,Profesores,Areas,Modulos
~~~

Y añade al final la siguiente línea:
~~~
admin.site.register(Modulos)
~~~

* Despliega el cambio producido al crear la nueva tabla en el entorno de producción.

---------------------------------------------------------------------------------------






## Tarea 5 (3 puntos): Despliegue de nuestra aplicación en un hosting python: pythonanywhere

#### Siguiendo la documentación despliega nuestra aplicación django en pythonanwhere. Utiliza git para desplegar los ficheros y crea una base de datos en tu proyecto. Si con la documentación no es suficiente puede seguir mi documento: Despliegue de aplicación flask en hosting pythonanywhere.

---------------------------------------------------------------------------------------






