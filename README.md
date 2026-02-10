# Módulo Terraform - AWS CloudWatch Dashboard

Módulo de Terraform para la creación automatizada de dashboards de CloudWatch en AWS, utilizando expresiones SEARCH para descubrir y monitorear recursos dinámicamente basados en métricas de múltiples servicios AWS.

## Descripción

Este módulo permite crear dashboards de CloudWatch personalizados que automáticamente descubren y visualizan métricas de diversos servicios AWS sin necesidad de especificar recursos individuales. Utiliza la funcionalidad SEARCH de CloudWatch para encontrar métricas basadas en namespaces y nombres de métricas, lo que permite un monitoreo escalable y dinámico.

## Características Principales

- **Descubrimiento Automático**: Utiliza expresiones SEARCH para encontrar métricas automáticamente
- **Multi-Servicio**: Soporta 11 servicios AWS diferentes en un solo módulo
- **Dashboards Modulares**: Permite crear múltiples dashboards con diferentes combinaciones de servicios
- **Configuración Flexible**: Habilita/deshabilita servicios específicos por dashboard
- **Widgets Predefinidos**: Incluye widgets optimizados para cada servicio con las métricas más relevantes
- **Naming Convention**: Nomenclatura estandarizada para fácil identificación

## Servicios Soportados

El módulo soporta los siguientes servicios AWS:

| Servicio | Key | Métricas Principales |
|----------|-----|---------------------|
| Amazon EC2 | `ec2` | CPUUtilization, NetworkIn/Out, StatusCheckFailed |
| Amazon RDS | `rds` | CPUUtilization, DatabaseConnections, FreeStorageSpace, ReadLatency, WriteLatency |
| AWS Lambda | `lambda` | Invocations, Errors, Duration, Throttles, ConcurrentExecutions |
| Application Load Balancer | `alb` | RequestCount, HTTPCode_ELB_4XX/5XX, TargetResponseTime, HealthyHostCount |
| Network Load Balancer | `nlb` | ActiveFlowCount, ProcessedBytes, TCP_Client/Target_Reset_Count, HealthyHostCount |
| Amazon S3 | `s3` | BucketSizeBytes, NumberOfObjects |
| Amazon API Gateway | `apigateway` | 4XXError, 5XXError, Count, Latency |
| Amazon DynamoDB | `dynamodb` | ConsumedReadCapacityUnits, ConsumedWriteCapacityUnits, UserErrors |
| Amazon ECS | `ecs` | CPUUtilization, MemoryUtilization |
| Amazon SES | `ses` | Send, Delivery, Bounce, Complaint, Reject |
| Amazon SQS | `sqs` | NumberOfMessagesSent, NumberOfMessagesReceived, ApproximateNumberOfMessagesVisible |
| Amazon CloudFront | `cloudfront` | Requests, BytesDownloaded, BytesUploaded, 4xxErrorRate, 5xxErrorRate |

## Estructura del Módulo

```
cloudops-ref-repo-aws-cw-dashboard-terraform/
├── main.tf                    # Recurso principal del dashboard
├── variables.tf               # Variables de entrada
├── locals.tf                  # Variables locales y configuraciones
├── outputs.tf                 # Outputs del módulo
├── providers.tf               # Configuración de providers
├── data.tf                    # Data sources
├── README.md                  # Documentación principal
└── sample/                    # Ejemplo de uso
    ├── main.tf
    ├── variables.tf
    ├── providers.tf
    ├── terraform.auto.tfvars
    └── README.md
```

## Requisitos

| Nombre | Versión |
|--------|---------|
| terraform | >= 1.0 |
| aws | >= 4.0 |

## Providers

| Provider | Versión |
|----------|---------|
| aws | >= 4.0 |

## Variables de Entrada

### Variables Requeridas

| Nombre | Tipo | Descripción |
|--------|------|-------------|
| `client` | `string` | Nombre del cliente |
| `project` | `string` | Nombre del proyecto |
| `environment` | `string` | Ambiente de despliegue (dev, qa, prod) |
| `application` | `string` | Nombre de la aplicación |
| `dashboard_configs` | `list(object)` | Lista de configuraciones de dashboards |

### Estructura de dashboard_configs

```hcl
dashboard_configs = [
  {
    functionality = string  # Nombre de la funcionalidad (usado en el nombre del dashboard)
    services = {
      ec2        = bool  # Habilitar monitoreo de EC2
      rds        = bool  # Habilitar monitoreo de RDS
      lambda     = bool  # Habilitar monitoreo de Lambda
      alb        = bool  # Habilitar monitoreo de ALB
      nlb        = bool  # Habilitar monitoreo de NLB
      s3         = bool  # Habilitar monitoreo de S3
      apigateway = bool  # Habilitar monitoreo de API Gateway
      dynamodb   = bool  # Habilitar monitoreo de DynamoDB
      ecs        = bool  # Habilitar monitoreo de ECS
      ses        = bool  # Habilitar monitoreo de SES
      sqs        = bool  # Habilitar monitoreo de SQS
      cloudfront = bool  # Habilitar monitoreo de CloudFront
    }
  }
]
```

### Variables Opcionales

| Nombre | Tipo | Default | Descripción |
|--------|------|---------|-------------|
| `aws_region` | `string` | `"us-east-1"` | Región de AWS donde se despliegan los recursos |

## Outputs

| Nombre | Descripción |
|--------|-------------|
| `dashboard_arns` | ARNs de los dashboards creados |
| `dashboard_names` | Nombres de los dashboards creados |

## Uso Básico

```hcl
module "cloudwatch_dashboard" {
  source = "git::https://github.com/somospragma/cloudops-ref-repo-aws-cw-dashboard-terraform.git?ref=v1.0.0"

  client      = "pragma"
  project     = "myproject"
  environment = "dev"
  application = "myapp"

  dashboard_configs = [
    {
      functionality = "compute"
      services = {
        ec2    = true
        lambda = true
        ecs    = true
      }
    }
  ]
}
```

## Ejemplos de Uso

### Dashboard para Infraestructura de Compute y Base de Datos

```hcl
module "cloudwatch_dashboard_infra" {
  source = "git::https://github.com/somospragma/cloudops-ref-repo-aws-cw-dashboard-terraform.git?ref=v1.0.0"

  client      = "pragma"
  project     = "hefesto"
  environment = "dev"
  application = "infrastructure"

  dashboard_configs = [
    {
      functionality = "ec2rds"
      services = {
        ec2      = true
        rds      = true
        s3       = true
        sqs      = true
        dynamodb = true
        ses      = true
        ecs      = true
      }
    }
  ]
}
```

### Dashboard para APIs y Microservicios

```hcl
module "cloudwatch_dashboard_api" {
  source = "git::https://github.com/somospragma/cloudops-ref-repo-aws-cw-dashboard-terraform.git?ref=v1.0.0"

  client      = "pragma"
  project     = "hefesto"
  environment = "prod"
  application = "api"

  dashboard_configs = [
    {
      functionality = "api"
      services = {
        apigateway = true
        alb        = true
        nlb        = true
        lambda     = true
        cloudfront = true
        ecs        = true
      }
    }
  ]
}
```

### Múltiples Dashboards en una Sola Configuración

```hcl
module "cloudwatch_dashboards" {
  source = "git::https://github.com/somospragma/cloudops-ref-repo-aws-cw-dashboard-terraform.git?ref=v1.0.0"

  client      = "pragma"
  project     = "myproject"
  environment = "prod"
  application = "fullstack"

  dashboard_configs = [
    {
      functionality = "frontend"
      services = {
        cloudfront = true
        s3         = true
        alb        = true
      }
    },
    {
      functionality = "backend"
      services = {
        apigateway = true
        lambda     = true
        dynamodb   = true
        sqs        = true
      }
    },
    {
      functionality = "database"
      services = {
        rds      = true
        dynamodb = true
      }
    }
  ]
}
```

## Nomenclatura de Dashboards

Los dashboards se crean con la siguiente nomenclatura:

```
{client}-{project}-{environment}-{application}-{functionality}-dashboard
```

Ejemplo:
```
pragma-hefesto-dev-myapp-api-dashboard
pragma-hefesto-prod-myapp-ec2rds-dashboard
```

## Widgets por Servicio

### Amazon EC2
- CPU Utilization
- Network In
- Network Out
- Status Check Failed (Instance)
- Status Check Failed (System)

### Amazon RDS
- CPU Utilization
- Database Connections
- Free Storage Space
- Read Latency
- Write Latency
- Read IOPS
- Write IOPS

### AWS Lambda
- Invocations
- Errors
- Duration
- Throttles
- Concurrent Executions

### Application Load Balancer (ALB)
- Request Count
- HTTP 4XX Errors
- HTTP 5XX Errors
- Target Response Time
- Unhealthy Host Count
- Healthy Host Count
- Processed Bytes
- Active Connection Count

### Network Load Balancer (NLB)
- Active Flow Count
- Processed Bytes
- TCP Client Reset Count
- TCP Target Reset Count
- Unhealthy Host Count
- Healthy Host Count

### Amazon S3
- Bucket Size Bytes
- Number of Objects

### Amazon API Gateway
- 4XX Errors
- 5XX Errors
- Request Count
- Latency

### Amazon DynamoDB
- Consumed Read Capacity Units
- Consumed Write Capacity Units
- User Errors

### Amazon ECS
- CPU Utilization
- Memory Utilization

### Amazon SES
- Send
- Delivery
- Bounce
- Complaint
- Reject

### Amazon SQS
- Number of Messages Sent
- Number of Messages Received
- Approximate Number of Messages Visible
- Approximate Age of Oldest Message

### Amazon CloudFront
- Requests
- Bytes Downloaded
- Bytes Uploaded
- 4xx Error Rate
- 5xx Error Rate

## Características Técnicas

### Expresiones SEARCH

El módulo utiliza expresiones SEARCH de CloudWatch para descubrir métricas automáticamente:

```hcl
SEARCH('Namespace="AWS/EC2" MetricName="CPUUtilization"', 'Average', 300)
```

Esto permite:
- Descubrimiento automático de recursos
- No requiere especificar IDs de recursos individuales
- Escalabilidad automática cuando se agregan nuevos recursos
- Reducción de mantenimiento del código

### Propiedades Comunes de Widgets

Todos los widgets comparten propiedades comunes definidas en `locals.tf`:

```hcl
common_widget_properties = {
  view    = "timeSeries"
  stacked = false
  region  = var.aws_region
  period  = 300
  yAxis = {
    left = {
      showUnits = false
    }
  }
}
```

### Estructura de Widgets

Cada servicio incluye:
1. Widget de texto con título de sección
2. Widget de texto con enlace a la consola de AWS
3. Múltiples widgets de métricas con visualizaciones específicas

## Consideraciones de Seguridad

- Los dashboards son de solo lectura y no modifican recursos
- No se exponen credenciales en el código
- Se recomienda usar IAM roles con permisos mínimos necesarios
- Los dashboards respetan las políticas de IAM del usuario que los visualiza

## Permisos IAM Requeridos

Para crear y gestionar dashboards, se requieren los siguientes permisos:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:PutDashboard",
        "cloudwatch:GetDashboard",
        "cloudwatch:DeleteDashboards",
        "cloudwatch:ListDashboards"
      ],
      "Resource": "*"
    }
  ]
}
```

Para visualizar las métricas en los dashboards:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:GetMetricData",
        "cloudwatch:GetMetricStatistics",
        "cloudwatch:ListMetrics"
      ],
      "Resource": "*"
    }
  ]
}
```

## Limitaciones

- Los dashboards están limitados a 500 widgets por dashboard (límite de AWS)
- Las expresiones SEARCH pueden tener un impacto en el rendimiento con muchos recursos
- La región está hardcodeada en las expresiones SEARCH (us-east-1)
- No soporta dashboards cross-region en una sola configuración

## Troubleshooting

### Dashboard no muestra métricas

**Problema**: El dashboard se crea pero no muestra datos.

**Soluciones**:
1. Verificar que existan recursos del tipo especificado en la cuenta
2. Confirmar que los recursos estén generando métricas
3. Verificar que la región en las expresiones SEARCH coincida con la región de los recursos
4. Esperar hasta 5 minutos para que las métricas aparezcan

### Error al crear dashboard

**Problema**: Error durante `terraform apply`.

**Soluciones**:
1. Verificar permisos IAM
2. Confirmar que el nombre del dashboard no exceda 255 caracteres
3. Validar que la sintaxis JSON del dashboard sea correcta

### Widgets vacíos

**Problema**: Algunos widgets aparecen vacíos.

**Soluciones**:
1. Verificar que el servicio esté activo en la región
2. Confirmar que las métricas existan para ese servicio
3. Revisar el período de tiempo seleccionado en el dashboard

## Mejores Prácticas

1. **Organización de Dashboards**: Crear dashboards separados por funcionalidad o capa de aplicación
2. **Naming Convention**: Usar nombres descriptivos en el campo `functionality`
3. **Servicios Relevantes**: Habilitar solo los servicios que realmente se usan
4. **Monitoreo Progresivo**: Empezar con servicios críticos y expandir gradualmente
5. **Revisión Regular**: Revisar y actualizar dashboards según evoluciona la infraestructura
6. **Documentación**: Documentar el propósito de cada dashboard en el código

## Versionamiento

Este módulo sigue [Semantic Versioning](https://semver.org/):

- **MAJOR**: Cambios incompatibles en la API
- **MINOR**: Nuevas funcionalidades compatibles con versiones anteriores
- **PATCH**: Correcciones de bugs compatibles con versiones anteriores

## Changelog

### v1.0.0 (2026-02-10)
- Versión inicial del módulo
- Soporte para 11 servicios AWS
- Dashboards modulares con configuración flexible
- Expresiones SEARCH para descubrimiento automático
- Widgets predefinidos para cada servicio

## Contribución

Para contribuir al módulo:

1. Fork el repositorio
2. Crear una rama feature (`git checkout -b feature/nueva-funcionalidad`)
3. Commit los cambios (`git commit -am 'Agregar nueva funcionalidad'`)
4. Push a la rama (`git push origin feature/nueva-funcionalidad`)
5. Crear un Pull Request

## Soporte

Para reportar issues o solicitar nuevas funcionalidades:
- Crear un issue en el repositorio de GitHub
- Contactar al equipo de CloudOps

## Licencia

Este módulo es propiedad de Pragma y está destinado para uso interno.

## Autores

- **CloudOps Team** - Pragma

## Referencias

- [AWS CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)
- [CloudWatch SEARCH Expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-search-expressions.html)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)
