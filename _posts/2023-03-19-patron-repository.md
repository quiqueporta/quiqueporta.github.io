---
layout: post
title:  Patrón repository
date: 2023-03-19 10:00:00 +0200
author: Quique Porta
comments: true
share: true
categories:
    - Programacion
tags:
    - programacion
---

En este artículo, te explicaré qué es el patrón Repository y cómo se puede implementar en Python.

## ¿Qué es el patrón Repository?

El patrón Repository es un patrón de diseño de software que se utiliza para separar la lógica de negocio de la lógica de almacenamiento. En lugar de acceder directamente a los datos en una base de datos o en cualquier otro medio de almacenamiento, el patrón Repository se utiliza para encapsular el acceso a los datos y proporcionar una interfaz uniforme para trabajar con ellos.

En otras palabras, el patrón Repository se utiliza para proporcionar una abstracción entre la lógica de negocio y la lógica de almacenamiento, lo que permite que la lógica de negocio sea independiente del medio de almacenamiento subyacente.

## Implementación del patrón Repository en Python

Veamos cómo se puede implementar el patrón Repository en Python. Supongamos que tenemos una aplicación que maneja una lista de tareas pendientes. Para simplificar el ejemplo, supongamos que solo necesitamos realizar operaciones básicas en la lista de tareas, como agregar una tarea, eliminar una tarea y obtener la lista completa de tareas.

Para implementar el patrón Repository, primero necesitamos crear una clase abstracta que defina la interfaz para acceder a la lista de tareas. Esta clase abstracta actuará como nuestra abstracción entre la lógica de negocio y la lógica de almacenamiento. Aquí está el código para la clase abstracta:

{% highlight python linenos%}
{% raw %}

from abc import ABC, abstractmethod

class TaskRepository(ABC):

    @abstractmethod
    def add_task(self, task: str):
        pass

    @abstractmethod
    def remove_task(self, task_id: int):
        pass

    @abstractmethod
    def get_all_tasks(self):
        pass

{% endraw %}
{% endhighlight %}

En este código, estamos definiendo una clase abstracta TaskRepository que tiene tres métodos abstractos: add_task, remove_task y get_all_tasks. Estos métodos son la interfaz que se utilizará para acceder a la lista de tareas.

Ahora, necesitamos crear una clase concreta que implemente la interfaz TaskRepository. Esta clase concreta será responsable de acceder a los datos reales y realizar las operaciones de almacenamiento en la lista de tareas.

Aquí está el código para la clase concreta:

{% highlight python linenos%}
{% raw %}

class InMemoryTaskRepository(TaskRepository):
    def __init__(self):
        self.tasks = []
        self.current_id = 1

    def add_task(self, task: str):
        task_id = self.current_id
        self.tasks.append({'id': task_id, 'task': task})
        self.current_id += 1
        return task_id

    def remove_task(self, task_id: int):
        self.tasks = [task for task in self.tasks if task['id'] != task_id]

    def get_all_tasks(self):
        return self.tasks

{% endraw %}
{% endhighlight %}

En este código, estamos definiendo una clase InMemoryTaskRepository que implementa la interfaz TaskRepository.

Los repositorios en memoria se suelen utilizar sobretodo para cuando estas testeando tu logica de dominio de manera unitaria.

Podriamos hacer otra implementación, por ejemplo, utilizando el ORM de Django.

{% highlight python linenos%}
{% raw %}

from abc import ABC, abstractmethod
from myapp.models import Task


class DjangoTaskRepository(TaskRepository):

    def add_task(self, task: str):
        new_task = Task(task_name=task)
        new_task.save()

    def remove_task(self, task_id: int):
        try:
            task = Task.objects.get(id=task_id)
            task.delete()
        except Task.DoesNotExist:
            pass

    def get_all_tasks(self):
        return Task.objects.all()

{% endraw %}
{% endhighlight %}

En este ejemplo, estamos definiendo la clase DjangoTaskRepository que hereda de la clase TaskRepository. En la implementación de DjangoTaskRepository, estamos utilizando el modelo Task de Django para crear, eliminar y recuperar tareas de la base de datos.
