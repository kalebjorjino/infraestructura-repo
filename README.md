# Infraestructura GitOps con Argo CD y Helm

Repositorio declarativo para la gestión y despliegue automatizado de la infraestructura base y servicios de plataforma de Kubernetes mediante **Argo CD** (patrón **App of Apps**) y **Helm Charts**.

## 🚀 Arquitectura GitOps

```
infraestructura-repo/
├── argocd/
│   ├── project.yaml              # AppProject "infrastructure" (RBAC y destinos)
│   ├── root/
│   │   └── application.yaml      # Aplicación Raíz (App of Apps)
│   └── applications/             # Aplicaciones hijas declarativas
│       ├── monitoring.yaml       # Argo CD App -> monitoring/k8s-monitoring (Helm)
│       ├── action-runner.yaml    # Argo CD App -> cicd/action-runner (Helm)
│       ├── wazuh.yaml            # Argo CD App -> security/wazuh (Helm)
│       └── platform.yaml         # Argo CD App -> platform/ (Manifiestos K8s)
├── cicd/
│   └── action-runner/            # Helm Chart: Runners autohospedados
├── monitoring/
│   └── k8s-monitoring/           # Helm Chart: Prometheus + Grafana + Exporters + Dashboards
├── security/
│   └── wazuh/                    # Helm Chart: Wazuh Manager & Dashboard
└── platform/
    ├── ingress/                  # Ingress Controller e IngressClass
    └── storage/                  # StorageClass base
```

## 📦 Flujo de Despliegue Nativo

1. **Sin ejecución manual de `helm template`**: Argo CD se encarga directamente de interpretar el `Chart.yaml`, fusionar los `values.yaml` y desplegar los recursos en el clúster.
2. **Bootstrapping**:
   Aplicar el proyecto y la aplicación raíz una única vez:
   ```bash
   kubectl apply -f argocd/project.yaml
   kubectl apply -f argocd/root/application.yaml
   ```
3. Argo CD sincronizará automáticamente todas las aplicaciones definidas en `argocd/applications/`.
