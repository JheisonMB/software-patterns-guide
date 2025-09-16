---
layout: page
title: "Backend for Frontend (BFF)"
---

## ❓ Qué problema resuelve
- **APIs genéricas**: Una API única que no se adapta bien a diferentes clientes (web, móvil, IoT)
- **Over-fetching**: Clientes reciben más datos de los necesarios
- **Under-fetching**: Múltiples llamadas para obtener datos relacionados
- **Acoplamiento**: Cambios en la API afectan a todos los clientes

## 🔧 Cómo funciona
Crea una capa de backend específica para cada tipo de frontend, optimizando la comunicación y adaptando los datos según las necesidades del cliente.

## 📊 Diagrama de Arquitectura

```mermaid
graph TB
    subgraph "Frontend Clients"
        WEB[Web App]
        MOBILE[Mobile App]
        IOT[IoT Devices]
    end
    
    subgraph "BFF Layer"
        WEB_BFF[Web BFF]
        MOBILE_BFF[Mobile BFF]
        IOT_BFF[IoT BFF]
    end
    
    subgraph "Backend Services"
        USER_SVC[User Service]
        ORDER_SVC[Order Service]
        PRODUCT_SVC[Product Service]
        PAYMENT_SVC[Payment Service]
    end
    
    WEB --> WEB_BFF
    MOBILE --> MOBILE_BFF
    IOT --> IOT_BFF
    
    WEB_BFF --> USER_SVC
    WEB_BFF --> ORDER_SVC
    WEB_BFF --> PRODUCT_SVC
    
    MOBILE_BFF --> USER_SVC
    MOBILE_BFF --> ORDER_SVC
    MOBILE_BFF --> PAYMENT_SVC
    
    IOT_BFF --> USER_SVC
    IOT_BFF --> PRODUCT_SVC
```

## 🏗️ Estructura de Implementación

### Web BFF
```
web-bff/
├── controllers/
│   ├── DashboardController.java
│   ├── ProductCatalogController.java
│   └── OrderManagementController.java
├── services/
│   ├── WebDashboardService.java
│   └── WebProductService.java
├── dto/
│   ├── WebProductDTO.java
│   ├── WebOrderDTO.java
│   └── DashboardSummaryDTO.java
└── aggregators/
    ├── DashboardAggregator.java
    └── ProductCatalogAggregator.java
```

### Mobile BFF
```
mobile-bff/
├── controllers/
│   ├── MobileAuthController.java
│   ├── MobileProductController.java
│   └── MobileOrderController.java
├── services/
│   ├── MobileProductService.java
│   └── MobileOrderService.java
├── dto/
│   ├── MobileProductDTO.java
│   ├── MobileOrderDTO.java
│   └── MobileUserDTO.java
└── aggregators/
    ├── MobileProductAggregator.java
    └── MobileOrderAggregator.java
```

## 📋 Comparación de Respuestas

### Producto para Web
```mermaid
classDiagram
    class WebProductDTO {
        +id: String
        +name: String
        +description: String
        +fullImageUrls: List~String~
        +detailedSpecs: Map~String,Object~
        +reviews: List~ReviewDTO~
        +relatedProducts: List~ProductDTO~
        +seoMetadata: SeoDTO
    }
```

### Producto para Mobile
```mermaid
classDiagram
    class MobileProductDTO {
        +id: String
        +name: String
        +shortDescription: String
        +thumbnailUrl: String
        +price: BigDecimal
        +rating: Double
        +inStock: Boolean
    }
```

## 🔄 Flujo de Datos

```mermaid
sequenceDiagram
    participant WEB as Web Client
    participant WEB_BFF as Web BFF
    participant PRODUCT as Product Service
    participant ORDER as Order Service
    participant USER as User Service
    
    WEB->>WEB_BFF: GET /dashboard
    WEB_BFF->>USER: GET /users/{id}
    WEB_BFF->>ORDER: GET /orders?userId={id}
    WEB_BFF->>PRODUCT: GET /products/recommended
    
    USER-->>WEB_BFF: User data
    ORDER-->>WEB_BFF: Order history
    PRODUCT-->>WEB_BFF: Recommended products
    
    WEB_BFF->>WEB_BFF: Aggregate & format for web
    WEB_BFF-->>WEB: Dashboard with rich data
```

## 🎯 Patrones Complementarios

### API Gateway vs BFF
```mermaid
graph TB
    subgraph "Con API Gateway"
        CLIENT1[Web Client] --> GATEWAY[API Gateway]
        CLIENT2[Mobile Client] --> GATEWAY
        GATEWAY --> SERVICE1[Service A]
        GATEWAY --> SERVICE2[Service B]
    end
    
    subgraph "Con BFF"
        CLIENT3[Web Client] --> BFF1[Web BFF]
        CLIENT4[Mobile Client] --> BFF2[Mobile BFF]
        BFF1 --> SERVICE3[Service A]
        BFF1 --> SERVICE4[Service B]
        BFF2 --> SERVICE3
        BFF2 --> SERVICE4
    end
```

### GraphQL como BFF
```mermaid
graph TB
    subgraph "GraphQL BFF"
        WEB_CLIENT[Web Client] --> GRAPHQL[GraphQL Server]
        MOBILE_CLIENT[Mobile Client] --> GRAPHQL
        
        GRAPHQL --> RESOLVER1[User Resolver]
        GRAPHQL --> RESOLVER2[Product Resolver]
        GRAPHQL --> RESOLVER3[Order Resolver]
        
        RESOLVER1 --> USER_SERVICE[User Service]
        RESOLVER2 --> PRODUCT_SERVICE[Product Service]
        RESOLVER3 --> ORDER_SERVICE[Order Service]
    end
```

## ✅ Ventajas

- **Optimización específica**: Cada BFF optimizado para su cliente
- **Desarrollo independiente**: Teams pueden trabajar en paralelo
- **Reducción de tráfico**: Solo datos necesarios
- **Mejor UX**: Respuestas rápidas y específicas

## ❌ Desventajas

- **Duplicación de código**: Lógica similar en múltiples BFFs
- **Complejidad operacional**: Más servicios que mantener
- **Sincronización**: Cambios en backend requieren actualizar múltiples BFFs

## 🛠️ Implementación con Spring Boot

### Web BFF Controller
```java
@RestController
@RequestMapping("/api/web")
public class WebDashboardController {
    
    @Autowired
    private DashboardAggregator dashboardAggregator;
    
    @GetMapping("/dashboard/{userId}")
    public ResponseEntity<WebDashboardDTO> getDashboard(@PathVariable String userId) {
        WebDashboardDTO dashboard = dashboardAggregator.buildDashboard(userId);
        return ResponseEntity.ok(dashboard);
    }
}
```

### Mobile BFF Controller
```java
@RestController
@RequestMapping("/api/mobile")
public class MobileProductController {
    
    @Autowired
    private MobileProductService productService;
    
    @GetMapping("/products")
    public ResponseEntity<List<MobileProductDTO>> getProducts(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size) {
        
        List<MobileProductDTO> products = productService.getProductsForMobile(page, size);
        return ResponseEntity.ok(products);
    }
}
```

## 🎨 Casos de Uso Comunes

### E-commerce
- **Web BFF**: Catálogo completo, comparaciones, reviews detalladas
- **Mobile BFF**: Lista optimizada, imágenes pequeñas, checkout rápido
- **Admin BFF**: Métricas, reportes, gestión de inventario

### Banking
- **Web BFF**: Dashboard completo, gráficos, transacciones detalladas
- **Mobile BFF**: Balance, transferencias rápidas, notificaciones
- **ATM BFF**: Operaciones básicas, interfaz simplificada

## 📊 Métricas y Monitoreo

```mermaid
graph LR
    A[BFF Metrics] --> B[Response Time by Client]
    A --> C[Data Transfer Size]
    A --> D[Cache Hit Rate]
    A --> E[Error Rate by BFF]
    
    B --> F[Web: 200ms avg]
    B --> G[Mobile: 150ms avg]
    C --> H[Web: 2MB avg]
    C --> I[Mobile: 500KB avg]
```

---

*Patrón esencial para arquitecturas multi-cliente* 📱💻