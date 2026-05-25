# Exercise 6.2 — ALB con Listener y Target Group

La API de transcodificación de video de MediaStream corre en una sola instancia EC2 dentro de una VPC preexistente. Este workspace raíz coloca un Application Load Balancer (ALB) público frente a esa instancia, con un listener HTTP y un target group, para que el equipo pueda escalar horizontalmente más adelante.

## Estructura

```
oyd-exercise-6-2/
├── provider.tf            # Configuración del provider de AWS
├── variables.tf           # Variables de entrada (region, vpc_name, instance_name, listener_port, environment)
├── data.tf                # Lookups: aws_vpc, aws_subnets, aws_instance — sin IDs hardcodeados
├── main.tf                # ALB SG, target group, ALB, listener, target attachment
├── outputs.tf             # alb_dns_name, alb_arn, target_group_arn
├── terraform.tfvars       # Valores de environment y region
├── setup/                 # Infra base (VPC + EC2) — aplicar una vez antes del workspace raíz
└── evidence/              # state list y outputs capturados después del apply
```

## Uso

```bash
# 1. Provisionar la infra base (VPC + EC2)
cd setup
terraform init
terraform apply -auto-approve
cd ..

# 2. Provisionar la capa del ALB
terraform init
terraform apply -var-file=terraform.tfvars
```

## Evidencia

### `terraform state list`

```
data.aws_instance.api
data.aws_subnets.public
data.aws_vpc.main
aws_lb.main
aws_lb_listener.http
aws_lb_target_group.api
aws_lb_target_group_attachment.api
aws_security_group.alb
```

### `terraform output`

```
alb_arn = "arn:aws:elasticloadbalancing:us-east-1:544341949288:loadbalancer/app/mediastream-alb/7bba707eadd5376c"
alb_dns_name = "mediastream-alb-1910227608.us-east-1.elb.amazonaws.com"
target_group_arn = "arn:aws:elasticloadbalancing:us-east-1:544341949288:targetgroup/mediastream-api-tg/a4db9a1d037bba8d"
```
