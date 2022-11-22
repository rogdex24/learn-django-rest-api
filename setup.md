## Prepare Dev Environment

### Setup virtual env for packages etc.
```python
python -m venv .venv
```
### Activate the env (Run for every new Terminal)

```python
. .venv/Scripts/activate
```
## Installing Django

```python
pip install django
pip install djangorestframework
```
### Start the project
```python
django-admin startproject drinks .
```
### Run server
```python
python manage.py runserver
```
### Create default databases/tables
```python
python manage.py migrate
```
### Create user in django-admin
```python
python manage.py createsuperuser
```  
### To fix linting issues
```
Crtl+Shift+P -> Select Interpreter -> Python venv
```

## Create and Test Database

### Add the app drinks
&rarr; Open **drinks/settings.py** file  \
&rarr; Add `'drinks'` to `INSTALLED_APPS` array
### Applying it
```python
python manage.py migrate
```
### Create **models.py** file in <u>drinks</u> folder
&rarr; Create a Class `Drink` defining the data in 
the file
```python
# models.py
from django.db import models

class Drink(models.Model):
    name = models.CharField(max_length=200)
    description = models.CharField(max_length=500)
```
```python
python manage.py makemigrations drinks
```
### Create **admin.py** file in <u>drinks</u> folder
```python 
# admin.py
from django.contrib import admin
from .models import Drink

admin.site.register(Drink)
```
&rarr; Restart the server (rerun migrate commands if needed) and the table would be visible on the django admin page

## REST Framework
### Add REST to installed app list
&rarr; Open **drinks/settings.py** file  \
&rarr; Add `'rest_framework'` to `INSTALLED_APPS` array
### Create **serializers.py** file in <u>drinks</u> folder
&rarr; Create a Class `DrinkSerializer`
```python
# serializers.py
from rest_framework import serializers
from .models import Drink

class DrinkSerializer(serializers.ModelSerializer):
    class Meta:
        model = Drink
        fields = '__all__'
```
### Create **views.py** file in <u>drinks</u> folder
&rarr; The actual API stuff happens here
```python
# views.py
from django.http import JsonResponse
from .models import Drink
from .serializers import DrinkSerializer

@api_view(['GET', 'POST'])
def drink_list(request):
    if request.method == 'GET':
        drinks = Drink.objects.all()
        serializer = DrinkSerializer(drinks, many=True)
        return JsonResponse({"drinks":serializer.data})
```
### Open **urls.py** file in <u>drinks</u> folder
&rarr; Import the views and add the endpoint to `urlpatterns`
```python
# urls.py
from drinks import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('drinks/', views.drink_list),
]
```

&rarr; Go to "***http://localhost:8000/drinks*** " for GET API Request in django front-end

&rarr; Go to "***http://localhost:8000/drinks.json*** " for the request result in json format

