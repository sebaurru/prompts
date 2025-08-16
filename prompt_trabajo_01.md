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
    
    ```

     ```python
     
```

- **Modelos**
   ```python

class InformeDiarioActividad(models.Model):
    informe_diario = models.ForeignKey('InformeDiario', on_delete=models.CASCADE)
    actividad = models.ForeignKey('Actividad', on_delete=models.CASCADE)
    descripcion = models.TextField(blank=True)  # Nuevo campo
    orden = models.PositiveIntegerField(default=0)  # Nuevo campo

    def __str__(self):
        return f"Informe {self.informe_diario.id} - Actividad {self.actividad.id}"
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

class Actividad(models.Model):
    proyecto = models.ForeignKey(
        'Proyecto',
        related_name='activdades_creadas',
        on_delete=models.CASCADE
    )
    actividad = models.CharField(max_length=255)
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
    fotos = models.ManyToManyField(
        'Foto',
        through='ActividadFoto',
        related_name='actividades',
        blank=True,
        verbose_name='Fotos relacionadas'
    )

    def __str__(self):
        return f"{self.actividad} ({self.proyecto})"
    ```
- **Formularios**

    ```python

class ActividadForm(forms.ModelForm):
    class Meta:
        model = Actividad
        fields = [
            "proyecto",
            "actividad",
            "descripcion",
            "orden",
            "usuario",

        ]
        widgets = {
            "descripcion": forms.Textarea(attrs={"rows": 3}),
        }

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        for name, field in self.fields.items():
            if field.widget.__class__.__name__ in ["Select", "SelectMultiple"]:
                field.widget.attrs["class"] = "form-select"
            else:
                field.widget.attrs["class"] = "form-control"
    ```

- **Templates 1** 😄

    ```html
 {% extends "header_inicio.html" %}
{% block content %}
<div class="container mt-4">
    <div class="card mb-4">
        <div class="card-header bg-success text-white fw-bold">
            <i class="bi bi-pencil"></i> Editar Informe Diario
        </div>
        <div class="card-body">
            {% if form.errors %}
                <div class="alert alert-danger">
                    {{ form.errors }}
                </div>
            {% endif %}
            <form method="post" action="{% url 'gestion:informe_diario_update_2' informe.pk %}">
                {% csrf_token %}
                <div class="mb-3">
                    <label class="form-label fw-bold">Proyecto</label>
                    <!-- Muestra el nombre del proyecto como texto readonly y envía el ID en un campo oculto -->
                    <input type="text" class="form-control" value="{{ proyecto_nombre }}" readonly>
                    <input type="hidden" name="proyecto" value="{{ form.proyecto.value }}">
                </div>
                <div class="mb-3">
                    <label class="form-label fw-bold">Fecha del informe</label>
                    {{ form.fecha_inf }}
                </div>
                <h5 class="mt-4 fw-bold">Clima</h5>
                    <div class="mb-3">
                        <label class="form-label fw-bold">Clima</label>
                        <div style="width:100%">
                            {{ form.clima }}
                        </div>
                        <style>
                          select[name="clima"] { width: 100%; }
                        </style>
                    </div>

                <div class="mb-3">
                    <label class="form-label">Horas sin trabajo por clima</label>
                    {{ form.hora_sin_trabajo }}
                </div>
                <div class="mb-3">
                    <label class="form-label">Observaciones sobre el clima</label>
                    {{ form.obs_clima }}
                </div>
                <h5 class="mt-4 fw-bold">Asistencia de los Trabajadores</h5>
                <div class="mb-3">
                    <label class="form-label">Empleados presentes</label>
                    {{ form.empl_presentes }}
                </div>
                <div class="mb-3">
                    <label class="form-label">Empleados ausentes</label>
                    {{ form.empl_ausentes }}
                </div>
                <div class="mb-3">
                    <label class="form-label">Observaciones sobre empleados</label>
                    {{ form.obs_Empleados }}
                </div>
                <h5 class="mt-4 fw-bold">Herramientas</h5>
                <div class="mb-3">
                    <label class="form-label">Herramienta que llegan o salen de obra</label>
                    {{ form.herramienta }}
                </div>
                <button type="submit" class="btn btn-success">Guardar cambios</button>
                <a href="{% url 'gestion:supervision_fachada' %}" class="btn btn-secondary">Cancelar</a>
            </form>

            <!-- ACTIVIDADES: Contenedor más interno justo debajo del formulario -->
            <div class="card mt-5 border-info shadow-sm">
                <div class="card-header bg-info text-white fw-bold">
                    <i class="bi bi-list-task"></i> Actividades
                </div>
                <div class="card-body">
                    {% for actrel in actividades_relacionadas %}
                        <div class="border-top border-2 border-secondary my-3"></div>
                        <!-- Primera fila: datos de la actividad -->
                        <div class="row align-items-center mb-0">
                            <div class="col-md-4">
                                <strong>Actividad:</strong>
                                {{ actrel.actividad.actividad }}
                            </div>
                            <div class="col-md-5">
                                <strong>Descripción:</strong>
                                {{ actrel.descripcion }}
                            </div>
                            <div class="col-md-3">
                                <strong>Orden:</strong>
                                {{ actrel.orden }}
                            </div>
                        </div>
                        <!-- Segunda fila: botones de acción -->
                        <div class="row mb-3" style="margin-top: 6px;">
                            <div class="col-12 text-end" style="padding-right: 16px;">
                                <div style="display: inline-flex; gap: 7px;">
                                    <form method="post" action="{% url 'gestion:informe_diario_actividad_update_V2' actrel.pk %}" style="margin-bottom:0;">
                                        {% csrf_token %}
                                        <button type="submit"
                                            class="btn fw-bold"
                                            style="background-color: #198754; color: white; border: none; box-shadow: 0 2px 4px rgba(0,0,0,0.04);">
                                            <i class="bi bi-pencil"></i> Editar
                                        </button>
                                    </form>
                                    <button type="button"
                                        class="btn fw-bold"
                                        style="background-color: #ffc107; color: #212529; border: none; box-shadow: 0 2px 4px rgba(0,0,0,0.04);">
                                        <i class="bi bi-image"></i> Agregar una foto a esta actividad
                                    </button>
                                </div>
                            </div>
                        </div>
                    {% empty %}
                        <p class="text-muted mt-3">No hay actividades registradas para este informe.</p>
                    {% endfor %}

                    <!-- Card interna para el botón de agregar actividad -->
                    <div class="card border-light mt-4 mb-2 p-3" style="background-color: #f6f9fc;">
                        <div class="d-grid gap-2">
                            <button type="button" class="btn fw-bold fs-5 w-100"
                                style="background-color: #0dcaf0; color: white; border: none; box-shadow: 0 2px 4px rgba(0,0,0,0.04);">
                                <i class="bi bi-plus-circle"></i> Agregar actividad
                            </button>
                        </div>
                    </div>
                </div>
            </div>
            <!-- Fin actividades -->
        </div>
    </div>
</div>
{% endblock %}
    ```
- **Formulario** 😄

    ```html
class InformeDiarioForm(forms.ModelForm):
    class Meta:
        model = InformeDiario
        fields = [
            'fecha_inf', 'proyecto', 'clima', 'hora_sin_trabajo', 'obs_clima',
            'empl_presentes', 'empl_ausentes', 'obs_Empleados', 'material', 'herramienta'
        ]
        widgets = {
            'fecha_inf': forms.DateInput(attrs={'type': 'date'}),
        }

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        for name, field in self.fields.items():
            if field.widget.__class__.__name__ == "Select":
                field.widget.attrs["class"] = "form-select"
            else:
                field.widget.attrs["class"] = "form-control"
  ```

- **Referencias a URLs siempre usarán el formato `gestion:nombre_de_la_vista`**
    - Ejemplo de url:
    ```python
    
    ```

## Pregunta

[Escribe aquí tu pregunta] ❓

Aca te actualice el contexto
Puedes hacer que se habra la pagina para crear la actividad y la tabla relacio




---
