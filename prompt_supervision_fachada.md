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
    class SupervisionFachadaView(LoginRequiredMixin, TemplateView):
        template_name = "gestion/supervision_fachada.html"

        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)
            proyecto_padre = get_object_or_404(Proyecto, nombre="Proyecto de Reparación de Fachadas y Aplicación de Pintura 2025")
            proyectos_hijos = Proyecto.objects.filter(proyecto_padre=proyecto_padre)
            context['proyecto_padre'] = proyecto_padre
            context['proyectos_hijos'] = proyectos_hijos

            proyecto_hijo_id = self.request.GET.get('proyecto_hijo')
            if proyecto_hijo_id:
                try:
                    proyecto_hijo = Proyecto.objects.get(id=proyecto_hijo_id)
                    informes_diarios = InformeDiario.objects.filter(proyecto=proyecto_hijo).order_by('-fecha_inf')
                    context['proyecto_hijo_seleccionado'] = proyecto_hijo
                    context['informes_diarios'] = informes_diarios
                except Proyecto.DoesNotExist:
                    context['proyecto_hijo_seleccionado'] = None
                    context['informes_diarios'] = None
            else:
                informes_diarios = InformeDiario.objects.filter(proyecto__in=proyectos_hijos).order_by('-fecha_inf')
                context['proyecto_hijo_seleccionado'] = None
                context['informes_diarios'] = informes_diarios

            return context
    ```

     ```python
     class InformeDiarioCreateAutomatico(View):
    def get(self, request, *args, **kwargs):
        proyecto_padre = get_object_or_404(Proyecto, nombre="Proyecto de Reparación de Fachadas y Aplicación de Pintura 2025")
        fecha_hoy = timezone.now().date()
        informe, creado = InformeDiario.objects.get_or_create(
            proyecto=proyecto_padre,
            fecha_inf=fecha_hoy,
            defaults={
                # Los campos con default se completan automáticamente
                # Los demás quedarán vacíos (blank=True)
            }
        )
        if not creado:
            messages.info(request, "Ya existe un informe diario para hoy.")
        else:
            messages.success(request, "Informe diario generado exitosamente.")
        return redirect('gestion:supervision_fachada')
```

- **Modelos**
- **Formularios**
- **Templates 1** 😄

    ```html
    {% extends "header_inicio.html" %}
{% block content %}
<div class="container mt-4">

    <!-- Supervisión en contenedor diferenciado -->
    <div class="card bg-light border-primary mb-4">
        <div class="card-body text-center py-4">
            <h1 class="fw-bold text-primary" style="font-size:2rem;">
                <i class="bi bi-clipboard-check"></i>
                Supervisión de Trabajo del Proyecto de Reparación y Aplicación de Pintura 2025
            </h1>
        </div>
    </div>

    <!-- Generar informe diario (modificado, sin select, solo botón y nombre de proyecto) -->
    <div class="card mb-4">
        <div class="card-header bg-success text-white fw-bold">
            <i class="bi bi-calendar-plus"></i> Generar informe diario
        </div>
        <div class="card-body text-center">
            <div class="mb-2">
                <label class="form-label fw-bold">
                    <i class="bi bi-diagram-3"></i> Proyecto:
                </label>
                <div class="fs-5 fw-semibold text-primary">
                    {{ proyecto_padre.nombre }}
                </div>
            </div>
            <a href="{% url 'gestion:informe_diario_create_automatico' %}" class="btn btn-primary fw-bold fs-5 w-100 mt-3">
                <i class="bi bi-file-earmark-plus"></i> Generar informe diario
            </a>
        </div>
    </div>

    <!-- Contenedor de informes diarios -->
    {% if informes_diarios and informes_diarios|length > 0 %}
    <div class="card mb-4">
        <div class="card-header bg-info text-white fw-bold">
            <i class="bi bi-journal-text"></i>
            Informes diarios de: {{ proyecto_padre.nombre }}
        </div>
        <div class="card-body">
            <ul class="list-group">
                {% for informe in informes_diarios %}
                <li class="list-group-item">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <strong>Fecha:</strong> {{ informe.fecha_inf|date:"d/m/Y" }}<br>
                            <strong>Clima:</strong> {{ informe.clima }}<br>
                            <strong>Descripción:</strong> {{ informe.descripcion|default:"Sin descripción" }}
                        </div>
                        <!-- Botones separados con CSS inline -->
                        <div class="d-flex align-items-center" style="gap: 12px;">
                            <a href="{% url 'gestion:informe_diario_detail' informe.id %}" class="btn btn-outline-primary btn-md">
                                Ver detalle
                            </a>
                            <a href="{% url 'gestion:informe_diario_editar' informe.id %}" 
                               class="btn btn-md fw-bold"
                               style="border: 2px solid #b39ddb; color: #7c4dff; background: transparent;">
                                Editar
                            </a>
                        </div>
                    </div>
                </li>
                {% endfor %}
            </ul>
        </div>
    </div>
    {% else %}
    <div class="card mb-4">
        <div class="card-header bg-info text-white fw-bold">
            <i class="bi bi-journal-text"></i> Informes diarios de: {{ proyecto_padre.nombre }}
        </div>
        <div class="card-body">
            <div class="alert alert-warning">
                No hay informes diarios para este proyecto.
            </div>
        </div>
    </div>
    {% endif %}
</div>
{% endblock %}
    ```



- **Referencias a URLs siempre usarán el formato `gestion:nombre_de_la_vista`**
    - Ejemplo de url:
    ```python
    path('supervision-fachada/', views.SupervisionFachadaView.as_view(), name='supervision_fachada'),
    ```

## Pregunta

[Escribe aquí tu pregunta] ❓

cuando quiero generar un informe y el informe ya existe, la vista generar un mensaje. pero el mensaje no esta desplegando en el html puedes ajustarlo para que se vea.
y deja marcado en el html donde hiciste el cambio.


---

