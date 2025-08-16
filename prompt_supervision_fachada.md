# Prompt Base Para Proyecto Django (django_pinehille / gestion)

## Contexto del Proyecto

- **Framework:** Django
- **Hosting:** PythonAnywhere
- **Proyecto:** django_pinehille
- **Aplicación interna:** gestion
- **Ruta de la aplicación:** `django_pinehille/gestion` (cargada en la URL principal con `include`)
- **Vistas:** Basadas en clases (Class-Based Views)
- **Namespace de URLs:** Todas las referencias a URLs o vistas deben tener el formato `"gestion:nombre_de_la_vista"`

## Referencias para respuestas

Ten en cuenta que las respuestas pueden requerir información sobre:
- **Vistas**
  :smile:
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

  
- **Modelos**
- **Formularios**
- **Templates**
-   :simile:
        {% extends "header_inicio.html" %}
        {% block content %}
        <div class="container mt-4">
        
            <!-- Supervisión en contenedor diferenciado -->
            <div class="card bg-light border-primary mb-4">
                <div class="card-body text-center py-4">
                    <h1 class="fw-bold text-primary" style="font-size:2rem;">
                        <i class="bi bi-clipboard-check"></i>
                        Supervisión de Trabajo del Proyecto de Reparación y Impermeabilización de Fachada
                    </h1>
                </div>
            </div>
        
            <!-- Generar informe diario en tarjeta igual a la de informes -->
            <div class="card mb-4">
                <div class="card-header bg-success text-white fw-bold">
                    <i class="bi bi-calendar-plus"></i> Generar informe diario
                </div>
                <div class="card-body">
                    <form method="get" action="{% url 'gestion:informe_diario_create' %}">
                        <!-- Label en su propio renglón -->
                        <div class="mb-2">
                            <label for="proyecto_hijo" class="form-label fw-bold">
                                <i class="bi bi-diagram-3"></i> Selecciona un proyecto hijo:
                            </label>
                        </div>
                        <!-- Select y botón ocupan todo el ancho -->
                        <div class="row g-2">
                            <div class="col-12 col-md-9">
                                <select id="proyecto_hijo" name="proyecto_hijo" class="form-select fs-5 w-100">
                                    <option value="">-- Selecciona --</option>
                                    {% for proyecto in proyectos_hijos %}
                                        <option value="{{ proyecto.id }}"{% if proyecto.id|stringformat:"s" == request.GET.proyecto_hijo %} selected{% endif %}>{{ proyecto.nombre }}</option>
                                    {% endfor %}
                                </select>
                            </div>
                            <div class="col-12 col-md-3">
                                <button type="submit" class="btn btn-primary fw-bold fs-5 w-100">
                                    <i class="bi bi-file-earmark-plus"></i> Generar informe diario
                                </button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        
            <!-- Contenedor de informes diarios -->
            {% if informes_diarios and informes_diarios|length > 0 %}
            <div class="card mb-4">
                <div class="card-header bg-info text-white fw-bold">
                    <i class="bi bi-journal-text"></i>
                    {% if proyecto_hijo_seleccionado %}
                        Informes diarios de: {{ proyecto_hijo_seleccionado.nombre }}
                    {% else %}
                        Informes Diarios del Proyecto
                    {% endif %}
                </div>
                <div class="card-body">
                    <ul class="list-group">
                        {% for informe in informes_diarios %}
                        <li class="list-group-item">
                            <div class="d-flex justify-content-between align-items-center">
                                <div>
                                    {% if not proyecto_hijo_seleccionado %}
                                        <strong>Proyecto:</strong> {{ informe.proyecto.nombre }}<br>
                                    {% endif %}
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
                {% if proyecto_hijo_seleccionado %}
                <div class="card mb-4">
                    <div class="card-header bg-info text-white fw-bold">
                        <i class="bi bi-journal-text"></i> Informes diarios de: {{ proyecto_hijo_seleccionado.nombre }}
                    </div>
                    <div class="card-body">
                        <div class="alert alert-warning">
                            No hay informes diarios para este proyecto hijo.
                        </div>
                    </div>
                </div>
                {% endif %}
            {% endif %}
        </div>
        {% endblock %}
    ```
- 
- **Referencias a URLs siempre usarán el formato `gestion:nombre_de_la_vista`**
      - Voy a trabajar en este url
      path('supervision-fachada/', views.SupervisionFachadaView.as_view(), name='supervision_fachada'),

## Pregunta





[Escribe aquí tu pregunta]

---

_Este archivo sirve como base para generar prompts sobre el proyecto. Agrega tu pregunta en el espacio correspondiente y especifica si necesitas ejemplos de vistas, modelos, formularios o templates._
