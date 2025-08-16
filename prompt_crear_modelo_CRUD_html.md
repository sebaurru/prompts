# Prompt para Proyecto Django (django_pinehille / gestion)

## Contexto del Proyecto

- **Framework:** Django
- **Hosting:** PythonAnywhere
- **Proyecto:** django_pinehille
- **Aplicación interna:** gestion
- **Ruta de la aplicación:** `django_pinehille/gestion` (cargada en la URL principal con `include`)
- **Vistas:** Basadas en clases (Class-Based Views)
- **Namespace de URLs:** Todas las referencias a URLs o vistas deben tener el formato `"gestion:nombre_de_la_vista"`
- - **URLs:** en lsa url del proyecto cargo los vistas así: from . import views

## Referencias para respuestas

Ten en cuenta que las respuestas pueden requerir información sobre:

- **Vistas** 😄

    ```python
    class ProyectoDetailView(LoginRequiredMixin, DetailView):
    model = Proyecto
    template_name = 'gestion/proyecto_detail.html'
    context_object_name = 'proyecto'

class ProyectoCreateView(LoginRequiredMixin, CreateView):
    model = Proyecto
    form_class = ProyectoForm
    template_name = 'gestion/proyecto_form.html'

    def get_success_url(self):
        return reverse_lazy('gestion:proyecto_detail', kwargs={'pk': self.object.pk})

class ProyectoUpdateView(LoginRequiredMixin, UpdateView):
    model = Proyecto
    form_class = ProyectoForm
    template_name = 'gestion/proyecto_form.html'

    def get_success_url(self):
        return reverse_lazy('gestion:proyecto_detail', kwargs={'pk': self.object.pk})

class ProyectoDeleteView(LoginRequiredMixin, DeleteView):
    model = Proyecto
    template_name = 'gestion/proyecto_confirm_delete.html'
    success_url = reverse_lazy('gestion:proyecto_list')
    ```

     ```python
     
```

- **Modelos**
    ```python
class ActividadDiaria(models.Model):
    proyecto = models.ForeignKey(
        'Proyecto',
        related_name='activdades_creadas',
        on_delete=models.CASCADE
    )
    actividad_diaria = models.CharField(max_length=255)
    descripcion = models.TextField(blank=True)
    orden = models.PositiveIntegerField(default=0)

    usuario = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
        related_name='actividades_creadas',
        verbose_name='Usuario que realizó la actividad'
    )
    fecha_creado = models.DateTimeField(auto_now_add=True)
    fecha_update = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f"{self.actividad} ({self.proyecto})"

class InformeDiario(models.Model):
    CLIMA_CHOICES = [
        ('soleado', 'Soleado'),
        ('nublado', 'Nublado'),
        ('lluvioso', 'Lluvioso'),
        ('variado', 'Variado'),
    ]

    fecha_inf = models.DateField(verbose_name='Fecha del informe')
    proyecto = models.ForeignKey(
        'Proyecto',
        related_name='informes_diarios',
        on_delete=models.CASCADE
    )
    clima = models.CharField(
        max_length=10,
        choices=CLIMA_CHOICES,
        verbose_name='Clima',
        default='variado'
    )
    hora_sin_trabajo = models.IntegerField(
        default=0,
        verbose_name='Horas sin trabajo por clima'
    )
    obs_clima = models.TextField(
        blank=True,
        verbose_name='Observaciones sobre el clima'
    )
    empl_presentes = models.IntegerField(
        default=0,
        verbose_name='Empleados presentes'
    )
    empl_ausentes = models.IntegerField(
        default=0,
        verbose_name='Empleados ausentes'
    )
    obs_Empleados = models.TextField(
        blank=True,
        verbose_name='Observaciones sobre empleados'
    )
    material = models.TextField(
        blank=True,
        verbose_name='Materiales que llegan a obra'
    )
    herramienta = models.TextField(
        blank=True,
        verbose_name='Herramienta que llegan o salen de obra'
    )

    observaciones = models.ManyToManyField(
        'Observacion',
        through='InformeDiarioObservacion',
        related_name='informes_diarios'
    )
    actividades = models.ManyToManyField(
        'Actividad',
        through='InformeDiarioActividad',
        related_name='informes_diarios'
    )

    def __str__(self):
        return f"Informe Diario {self.fecha_inf} - {self.proyecto}"

class Proyecto(models.Model):
    nombre = models.CharField(max_length=255)
    nombre_corto = models.CharField(max_length=100, blank=True)
    descripcion = models.TextField(blank=True)
    fecha_creado = models.DateTimeField(auto_now_add=True)
    fecha_update = models.DateTimeField(auto_now=True)
    proyecto_padre = models.ForeignKey(
        'self',
        null=True, blank=True,
        related_name='subproyectos',
        on_delete=models.CASCADE
    )
    orden = models.PositiveIntegerField(
        default=0,
        help_text="Orden para mostrar este proyecto entre sus hermanos"
    )

    class Meta:
        ordering = ['proyecto_padre__id', 'orden', 'nombre']

    def __str__(self):
        return self.nombre

from django.conf import settings  # Importa settings para obtener el modelo User

    ```
- **Formularios**
```python
    class ProyectoForm(forms.ModelForm):
    class Meta:
        model = Proyecto
        fields = '__all__'

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        for field in self.fields.values():
            field.widget.attrs["class"] = "form-control"
    ```

- **Templates 1** 😄

    ```html
{% extends "header_inicio.html" %}
{% block content %}
<div class="container mt-5">
    <div class="row justify-content-center">
        <div class="col-md-8 col-lg-6">
            <div class="card shadow">
                <div class="card-header bg-primary text-white">
                    <h3 class="mb-0">
                        {% if form.instance.pk %}
                            <i class="bi bi-pencil-square"></i> Editar Proyecto
                        {% else %}
                            <i class="bi bi-plus-circle"></i> Crear Proyecto
                        {% endif %}
                    </h3>
                </div>
                <div class="card-body">
                    <form method="post" novalidate>
                        {% csrf_token %}
                        {% for field in form %}
                            <div class="mb-3">
                                <label class="form-label" for="{{ field.id_for_label }}">{{ field.label }}</label>
                                {{ field }}
                                {% if field.help_text %}
                                    <small class="form-text text-muted">{{ field.help_text }}</small>
                                {% endif %}
                                {% for error in field.errors %}
                                    <div class="invalid-feedback d-block">
                                        {{ error }}
                                    </div>
                                {% endfor %}
                            </div>
                        {% endfor %}
                        <div class="d-flex justify-content-end gap-2">
                            <button type="submit" class="btn btn-success">
                                <i class="bi bi-save"></i> Guardar
                            </button>
                            <a href="{% url 'gestion:proyecto_list' %}" class="btn btn-secondary">
                                <i class="bi bi-x-circle"></i> Cancelar
                            </a>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}   
    ```



- **Referencias a URLs siempre usarán el formato `gestion:nombre_de_la_vista`**
    - Ejemplo de url:
    ```python
    
    ```

## Pregunta

[Escribe aquí tu pregunta] ❓
quiero crear un modelo que se llame
ActividadDiaria
basado en el que estoy poniendo en el contexto

y que tengo una relacion de foreingkey con
class InformeDiario(models.Model):

quiero que me armes el crud y los html
te paso un ejemplo de crud y un html y un formulario para el modelo Proyecto




---
