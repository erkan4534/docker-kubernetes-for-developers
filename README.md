# E-Commerce Microservices for Kubernetes Training

This project contains a complete microservices-based e-commerce system built for "Docker & Kubernetes for Developers" training. The system demonstrates real-world microservice patterns, inter-service communication, and progressive deployment strategies using Kubernetes.

## Architecture Overview

### Microservices

1. **User Service (Spring Boot + MySQL)**
   - User registration and authentication
   - User profile management
   - Technologies: Spring Boot 3.5.6, MySQL 8.0, RabbitMQ
   - Port: 8081

2. **Product Service (.NET + PostgreSQL)**
   - Product catalog management
   - Inventory tracking
   - Technologies: .NET 8, PostgreSQL 15, RabbitMQ
   - Port: 8082

3. **Order Service (Go + MongoDB)**
   - Order creation and management
   - Order status tracking
   - Technologies: Go 1.21, MongoDB 7.0, RabbitMQ
   - Port: 8083

### Infrastructure Components

- **MySQL**: User data persistence
- **PostgreSQL**: Product data persistence
- **MongoDB**: Order data persistence
- **RabbitMQ**: Message broker for async communication
- **Redis**: Caching layer

## Training Session Progression

This microservices system will be deployed progressively throughout the 8 training sessions:

- **Session 01**: Kubernetes introduction (no deployment)
- **Session 02**: Cluster setup and basic concepts
- **Session 03**: Deploy services as basic Pods and Deployments
- **Session 04**: Add Services and networking between microservices
- **Session 05**: Add persistent volumes for databases
- **Session 06**: Configure secrets and ConfigMaps
- **Session 07**: Implement CI/CD with GitHub Actions and GitOps
- **Session 08**: Add observability (logging, metrics, tracing) and Istio

## Quick Start

### Prerequisites

- Docker and Docker Compose
- Java 21+ (for User Service development)
- .NET 8+ (for Product Service development)
- Go 1.21+ (for Order Service development)

### Running with Docker Compose

1. Start all services:
```bash
cd microservices
docker-compose up -d
```

2. Verify services are running:
```bash
docker-compose ps
```

3. Check service health:
```bash
curl http://localhost:8081/api/v1/users/health
curl http://localhost:8082/api/v1/products/health
curl http://localhost:8083/api/v1/health
```

4. Access RabbitMQ Management UI:
   - URL: http://localhost:15672
   - Username: admin
   - Password: password

### API Endpoints

#### User Service (Port 8081)
- `POST /api/v1/users/register` - Register new user
- `GET /api/v1/users/{id}` - Get user by ID
- `GET /api/v1/users` - Get all active users
- `GET /api/v1/users/stats` - Get user statistics

#### Product Service (Port 8082)
- `POST /api/v1/products` - Create product
- `GET /api/v1/products/{id}` - Get product by ID
- `GET /api/v1/products` - Get all products
- `GET /api/v1/products/category/{category}` - Get products by category
- `GET /api/v1/products/stats` - Get product statistics

#### Order Service (Port 8083)
- `POST /api/v1/orders` - Create order
- `GET /api/v1/orders/{id}` - Get order by ID
- `GET /api/v1/orders/user/{userId}` - Get orders by user
- `PATCH /api/v1/orders/{id}/status` - Update order status
- `GET /api/v1/orders/stats` - Get order statistics

### Sample Data

The system comes pre-loaded with sample data:
- 3 sample users in MySQL
- 10 sample products in PostgreSQL
- 3 sample orders in MongoDB

### Message Flow Example

1. User registers → User Service publishes `user.created` event
2. User places order → Order Service publishes `order.created` event
3. Product Service listens to `order.created` and updates inventory
4. Order status updates → Order Service publishes `order.status.updated` event

## Development

### Building Individual Services

#### User Service (Spring Boot)
```bash
cd user-service
./mvnw clean package
docker build -t user-service:latest .
```

#### Product Service (.NET)
```bash
cd product-service
dotnet build
dotnet publish -c Release
docker build -t product-service:latest .
```

#### Order Service (Go)
```bash
cd order-service
go mod tidy
go build
docker build -t order-service:latest .
```

### Environment Variables

Each service can be configured via environment variables. See the docker-compose.yml file for the complete list of variables.

## Database Schemas

### MySQL (User Service)
- `users` table with user profile information
- Indexes on username, email, and active status

### PostgreSQL (Product Service)
- `products` table with product catalog
- Indexes on category, name, and active status

### MongoDB (Order Service)
- `orders` collection with order documents
- Indexes on user_id, order_number, status, and created_at

## Message Broker Setup

RabbitMQ is configured with:
- Virtual host: `ecommerce`
- Admin user: `admin/password`
- Pre-configured exchanges and queues for service communication
- Topic-based routing for event-driven architecture

## Troubleshooting

1. **Services failing to start**: Check if all infrastructure services (databases, RabbitMQ) are healthy
2. **Database connection issues**: Verify connection strings and credentials
3. **RabbitMQ connection issues**: Ensure RabbitMQ is running and accessible
4. **Port conflicts**: Make sure ports 8081-8083, 3306, 5432, 27017, 5672, and 15672 are available

## Next Steps

This microservices system will be progressively enhanced throughout the training with:
- Kubernetes manifests (Deployments, Services, ConfigMaps, Secrets)
- Helm charts for easier deployment
- Istio service mesh configuration
- Prometheus and Grafana monitoring setup
- Distributed tracing with Jaeger
- CI/CD pipelines with GitHub Actions
- GitOps workflows with FluxCD

---

Built for "Docker & Kubernetes for Developers" Training by Hüseyin Babal