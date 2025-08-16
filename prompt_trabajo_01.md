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
    ```
- **Formularios**

    ```python
    
    ```

- **Templates 1** 😄

    ```html
   {% extends "header_inicio.html" %}
{% block content %}
<div class="container mt-4">
    <div class="card mb-4">
        <div class="card-header bg-primary text-white fw-bold">
            <i class="bi bi-pencil"></i> Editar Actividad del Informe Diario
        </div>
        <div class="card-body">
            {% if form.errors %}
                <div class="alert alert-danger">
                    {{ form.errors }}
                </div>
            {% endif %}
            <form method="post">
                {% csrf_token %}
                <!-- Mostrar nombre del informe y enviar id oculto -->
                <div class="mb-3">
                    <label class="form-label fw-bold">Informe Diario</label>
                    <input type="text" class="form-control" value="{{ informe_diario_nombre }}" readonly>
                    <input type="hidden" name="informe_diario" value="{{ actrel.informe_diario.pk }}">
                </div>
                <!-- Mostrar nombre de la actividad y enviar id oculto -->
                <div class="mb-3">
                    <label class="form-label fw-bold">Actividad</label>
                    <input type="text" class="form-control" value="{{ actividad_nombre }}" readonly>
                    <input type="hidden" name="actividad" value="{{ actrel.actividad.pk }}">
                </div>
                <!-- Campos editables -->
                <div class="mb-3">
                    <label class="form-label">Descripción</label>
                    {{ form.descripcion }}
                </div>
                <div class="mb-3">
                    <label class="form-label">Orden</label>
                    {{ form.orden }}
                </div>
                <button type="submit" class="btn btn-success">Guardar cambios</button>
                <a href="{% url 'gestion:informe_diario_detail' actrel.informe_diario.pk %}" class="btn btn-secondary">Cancelar</a>
            </form>
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

quiero que el campo clima ocupe el ancho del contenedor
pero no quiero agregar tweaks



---
