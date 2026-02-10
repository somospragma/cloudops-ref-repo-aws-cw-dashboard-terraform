# Ejemplo de Uso - AWS CloudWatch Dashboard Module

Este directorio contiene un ejemplo completo de cómo usar el módulo de CloudWatch Dashboard para crear dashboards personalizados que monitorean múltiples servicios AWS.

## Descripción del Ejemplo

Este ejemplo demuestra la creación de dos dashboards diferentes:

1. **Dashboard EC2/RDS**: Monitorea infraestructura de compute, bases de datos y servicios de almacenamiento
2. **Dashboard API**: Monitorea APIs, load balancers y servicios de aplicación

## Estructura de Archivos

```
sample/
├── main.tf                    # Configuración del módulo
├── variables.tf               # Definición de variables
├── providers.tf               # Configuración del provider AWS
├── terraform.auto.tfvars      # Valores de las variables
└── README.md                  # Esta documentación
```

## Configuración

### 1. Variables (terraform.auto.tfvars)

```hcl
aws_region = "us-east-1"
profile    = "pra_chaptercloudops_lab"
environment = "dev"
client      = "pragma"
project     = "hefesto"
application = "dashboard"

common_tags = {
  environment  = "dev"
  project-name = "Modulos Referencia"
  cost-center  = "-"
  owner        = "cristian.noguera@pragma.com.co"
  area         = "KCCC"
  provisioned  = "terraform"
  datatype     = "interno"
}
```

### 2. Configuración del Módulo (main.tf)

```hcl
module "aws_cloudwatch_dashboard" {
  source = "./module/cloudwatch"

  client      = var.client
  project     = var.project
  environment = var.environment
  application = var.application

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
    },
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

## Dashboards Creados

### Dashboard 1: pragma-hefesto-dev-dashboard-ec2rds-dashboard

Este dashboard incluye widgets para:

- **Amazon EC2**
  - CPU Utilization
  - Network In/Out
  - Status Check Failed

- **Amazon RDS**
  - CPU Utilization
  - Database Connections
  - Free Storage Space
  - Read/Write Latency
  - Read/Write IOPS

- **Amazon S3**
  - Bucket Size Bytes
  - Number of Objects

- **Amazon SQS**
  - Messages Sent/Received
  - Messages Visible
  - Age of Oldest Message

- **Amazon DynamoDB**
  - Consumed Read/Write Capacity Units
  - User Errors

- **Amazon SES**
  - Send/Delivery/Bounce/Complaint/Reject

- **Amazon ECS**
  - CPU Utilization
  - Memory Utilization

### Dashboard 2: pragma-hefesto-dev-dashboard-api-dashboard

Este dashboard incluye widgets para:

- **Amazon API Gateway**
  - 4XX/5XX Errors
  - Request Count
  - Latency

- **Application Load Balancer (ALB)**
  - Request Count
  - HTTP 4XX/5XX Errors
  - Target Response Time
  - Healthy/Unhealthy Host Count
  - Processed Bytes
  - Active Connections

- **Network Load Balancer (NLB)**
  - Active Flow Count
  - Processed Bytes
  - TCP Reset Counts
  - Healthy/Unhealthy Host Count

- **AWS Lambda**
  - Invocations
  - Errors
  - Duration
  - Throttles
  - Concurrent Executions

- **Amazon CloudFront**
  - Requests
  - Bytes Downloaded/Uploaded
  - 4xx/5xx Error Rate

- **Amazon ECS**
  - CPU Utilization
  - Memory Utilization

## Instrucciones de Despliegue

### Prerrequisitos

1. Terraform instalado (>= 1.0)
2. AWS CLI configurado con credenciales válidas
3. Perfil AWS configurado (`pra_chaptercloudops_lab` en este ejemplo)
4. Permisos IAM para crear dashboards de CloudWatch

### Pasos de Despliegue

1. **Clonar el repositorio**

```bash
git clone https://github.com/somospragma/cloudops-ref-repo-aws-cw-dashboard-terraform.git
cd cloudops-ref-repo-aws-cw-dashboard-terraform/sample
```

2. **Configurar variables**

Editar `terraform.auto.tfvars` con tus valores:

```hcl
aws_region  = "us-east-1"              # Tu región AWS
profile     = "tu-perfil-aws"          # Tu perfil AWS
environment = "dev"                     # Tu ambiente
client      = "tu-cliente"             # Tu cliente
project     = "tu-proyecto"            # Tu proyecto
application = "tu-aplicacion"          # Tu aplicación

common_tags = {
  environment  = "dev"
  project-name = "Tu Proyecto"
  owner        = "tu-email@ejemplo.com"
  # ... otros tags
}
```

3. **Inicializar Terraform**

```bash
terraform init
```

4. **Revisar el plan**

```bash
terraform plan
```

5. **Aplicar la configuración**

```bash
terraform apply
```

6. **Confirmar la creación**

Cuando se solicite confirmación, escribir `yes`.

### Verificación

Después del despliegue exitoso, puedes verificar los dashboards:

1. **Vía AWS Console**:
   - Ir a CloudWatch → Dashboards
   - Buscar dashboards con el patrón: `{client}-{project}-{environment}-{application}-{functionality}-dashboard`

2. **Vía AWS CLI**:

```bash
aws cloudwatch list-dashboards --profile tu-perfil-aws
```

3. **Vía Terraform Output**:

```bash
terraform output
```

## Personalización

### Agregar un Nuevo Dashboard

Para agregar un dashboard adicional, añadir un nuevo objeto al array `dashboard_configs`:

```hcl
dashboard_configs = [
  # ... dashboards existentes ...
  {
    functionality = "storage"
    services = {
      s3       = true
      dynamodb = true
    }
  }
]
```

### Modificar Servicios en un Dashboard

Para habilitar/deshabilitar servicios, cambiar los valores booleanos:

```hcl
{
  functionality = "api"
  services = {
    apigateway = true   # Habilitado
    alb        = false  # Deshabilitado
    lambda     = true   # Habilitado
  }
}
```

### Cambiar la Región

Modificar en `terraform.auto.tfvars`:

```hcl
aws_region = "us-west-2"
```

**Nota**: También necesitarás actualizar la región hardcodeada en las expresiones SEARCH del módulo principal.

## Ejemplos de Configuraciones Alternativas

### Solo Monitoreo de Compute

```hcl
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
```

### Solo Monitoreo de Bases de Datos

```hcl
dashboard_configs = [
  {
    functionality = "database"
    services = {
      rds      = true
      dynamodb = true
    }
  }
]
```

### Monitoreo Completo (Todos los Servicios)

```hcl
dashboard_configs = [
  {
    functionality = "full"
    services = {
      ec2        = true
      rds        = true
      lambda     = true
      alb        = true
      nlb        = true
      s3         = true
      apigateway = true
      dynamodb   = true
      ecs        = true
      ses        = true
      sqs        = true
      cloudfront = true
    }
  }
]
```

### Múltiples Ambientes

```hcl
# Para desarrollo
module "cloudwatch_dashboard_dev" {
  source = "./module/cloudwatch"
  
  environment = "dev"
  # ... resto de configuración
}

# Para producción
module "cloudwatch_dashboard_prod" {
  source = "./module/cloudwatch"
  
  environment = "prod"
  # ... resto de configuración
}
```

## Costos

Los dashboards de CloudWatch tienen los siguientes costos:

- **Dashboards Personalizados**: $3.00 USD por dashboard por mes
- **Primeros 3 dashboards**: Gratis en la capa gratuita de AWS
- **Métricas**: Las métricas estándar de AWS son gratuitas

**Costo estimado de este ejemplo**: $0 USD (si tienes menos de 3 dashboards en total)

Si ya tienes 3 o más dashboards:
- Dashboard 1 (ec2rds): $3.00 USD/mes
- Dashboard 2 (api): $3.00 USD/mes
- **Total**: $6.00 USD/mes

## Troubleshooting

### Error: "No credentials found"

**Solución**: Configurar AWS CLI o verificar el perfil:

```bash
aws configure --profile pra_chaptercloudops_lab
```

### Error: "Access Denied"

**Solución**: Verificar permisos IAM. Necesitas:
- `cloudwatch:PutDashboard`
- `cloudwatch:GetDashboard`
- `cloudwatch:DeleteDashboards`

### Dashboard creado pero sin datos

**Posibles causas**:
1. No hay recursos del tipo especificado en la cuenta
2. Los recursos no están generando métricas
3. La región no coincide
4. Necesitas esperar unos minutos para que aparezcan las métricas

**Solución**: Verificar que existan recursos activos del tipo habilitado en el dashboard.

### Error: "Dashboard body is invalid"

**Solución**: Verificar la sintaxis JSON en el módulo. Ejecutar:

```bash
terraform validate
```

## Limpieza

Para eliminar todos los recursos creados:

```bash
terraform destroy
```

Confirmar con `yes` cuando se solicite.

## Mejores Prácticas

1. **Usar Variables**: No hardcodear valores, usar variables para flexibilidad
2. **Tags Consistentes**: Mantener tags consistentes para mejor organización
3. **Naming Convention**: Seguir la convención de nombres del módulo
4. **Versionamiento**: Usar tags de versión específicos del módulo en producción
5. **State Remoto**: Usar S3 backend para el state de Terraform en producción
6. **Revisión Regular**: Revisar y actualizar dashboards según evoluciona la infraestructura

## Configuración de Backend Remoto (Recomendado para Producción)

Agregar en `providers.tf`:

```hcl
terraform {
  backend "s3" {
    bucket         = "tu-bucket-terraform-state"
    key            = "cloudwatch-dashboards/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
}
```

## Integración con CI/CD

### Ejemplo con GitHub Actions

```yaml
name: Deploy CloudWatch Dashboards

on:
  push:
    branches: [main]

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1
        
      - name: Terraform Init
        run: terraform init
        working-directory: ./sample
        
      - name: Terraform Plan
        run: terraform plan
        working-directory: ./sample
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          
      - name: Terraform Apply
        run: terraform apply -auto-approve
        working-directory: ./sample
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

## Monitoreo y Alertas

Aunque este módulo crea dashboards de visualización, considera complementar con:

1. **CloudWatch Alarms**: Para alertas proactivas
2. **SNS Topics**: Para notificaciones
3. **Lambda Functions**: Para automatización de respuestas

## Recursos Adicionales

- [Documentación del Módulo Principal](../README.md)
- [AWS CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)
- [CloudWatch SEARCH Expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-search-expressions.html)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

## Soporte

Para preguntas o problemas:
- Crear un issue en el repositorio de GitHub
- Contactar al equipo de CloudOps de Pragma

## Changelog del Ejemplo

### v1.0.0 (2026-02-10)
- Ejemplo inicial con dos dashboards
- Configuración para 11 servicios AWS
- Documentación completa de uso
