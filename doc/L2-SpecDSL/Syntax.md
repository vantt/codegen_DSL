# L2-SpecDSL Language Syntax Specification

## Overview

L2-SpecDSL is organized as a modular specification system with separate files for system manifest and individual components. This structure ensures maintainability, scalability, and efficient processing by AI systems.

## File Organization

```bash
specs/
├── system.spec                # System manifest
├── components/               # Component specifications
│   ├── order-service.spec
│   ├── payment-service.spec
│   └── user-service.spec
└── shared/                  # Shared specifications
    ├── common-models.spec
    └── common-errors.spec
```

## System Manifest (system.spec)

The system manifest defines the overall system structure and component relationships.

### Syntax

```
system "<SystemName>" v<Version> {
    metadata {
        owner: "<TeamName>"
        status: "DRAFT|REVIEW|APPROVED"
        updated: "<YYYY-MM-DD>"
    }
    
    components {
        "<ComponentName>": "<PathToSpec>"
        // Additional components...
    }
    
    dependencies {
        "<ComponentName>" -> ["<DependencyName>", ...]
        // Additional dependencies...
    }
}
```

### Example

```
system "EcommerceSystem" v1.0 {
    metadata {
        owner: "EcommerceTeam"
        status: "DRAFT"
        updated: "2024-12-18"
    }
    
    components {
        "OrderService": "components/order-service.spec"
        "PaymentService": "components/payment-service.spec"
        "UserService": "components/user-service.spec"
    }
    
    dependencies {
        "OrderService" -> ["PaymentService", "UserService"]
        "PaymentService" -> ["UserService"]
    }
}
```

## Component Specification

Each component is defined in its own file with the following structure.

### Syntax

```
component "<ComponentName>" v<Version> {
    metadata {
        owner: "<TeamName>"
        updated: "<YYYY-MM-DD>"
    }
    
    core {
        type: "<service|library|module>"
        lifecycle { ... }
        config { ... }
    }
    
    interfaces { ... }
    behavior { ... }
    models { ... }
    integrations { ... }
    errors { ... }
    resources { ... }
    tests { ... }
    crosscutting { ... }
}
```

### Example (components/order-service.spec)

```
component "OrderService" v1.0 {
    metadata {
        owner: "OrderTeam"
        updated: "2024-12-18"
    }
    
    core {
        type: "service"
        lifecycle {
            init: ["DatabaseConnection", "MessageQueue"]
            startup: "sequential"
            cleanup: "parallel"
        }
        config {
            dbUrl: {type: "string", required: true}
            retryCount: {type: "int", default: 3}
        }
    }
    
    interfaces {
        "OrderAPI": {
            version: "1.0"
            protocol: "REST"
            methods {
                "createOrder": {
                    input: ["OrderRequest"]
                    output: "OrderResponse"
                    errors: ["ValidationError", "SystemError"]
                }
            }
        }
    }
    
    models {
        "Order": {
            fields: {
                "orderId": {type: "string", required: true}
                "amount": {type: "decimal", required: true}
                "status": {type: "enum", values: ["NEW", "PAID", "SHIPPED"]}
            }
        }
    }
}
```

## Shared Specifications

Common elements used across multiple components are defined in shared specification files.

### Common Models (shared/common-models.spec)

```
shared models v1.0 {
    "Address": {
        fields: {
            "street": {type: "string", required: true}
            "city": {type: "string", required: true}
            "country": {type: "string", required: true}
        }
    }
    
    "Money": {
        fields: {
            "amount": {type: "decimal", required: true}
            "currency": {type: "string", required: true}
        }
    }
}
```

### Common Errors (shared/common-errors.spec)

```
shared errors v1.0 {
    "ValidationError": {
        severity: "LOW"
        retry: false
        code: "VAL-001"
    }
    
    "SystemError": {
        severity: "HIGH"
        retry: true
        code: "SYS-001"
    }
}
```

## Cross-Component References

Components can reference elements from other components or shared specifications:

```
component "OrderService" v1.0 {
    models {
        "Order": {
            fields: {
                "address": {type: "ref:shared/common-models.Address"}
                "payment": {type: "ref:PaymentService.Payment"}
            }
        }
    }
}
```

## Best Practices

1. **File Organization**
   - Keep related components in the same directory
   - Use consistent file naming
   - Group shared specifications logically

2. **Component Design**
   - Keep components focused and cohesive
   - Minimize dependencies between components
   - Use shared specifications for common elements

3. **Versioning**
   - Version each component independently
   - Use semantic versioning
   - Document breaking changes

4. **Documentation**
   - Document all public interfaces
   - Include examples for complex features
   - Keep documentation up-to-date with changes

## Validation Rules

1. **System Manifest**
   - All referenced component files must exist
   - Component dependencies must be valid
   - No circular dependencies allowed

2. **Components**
   - Must have unique names within the system
   - Must specify all required sections
   - Must follow version format

3. **References**
   - Must reference existing components/elements
   - Must use correct reference syntax
   - Must respect access restrictions

## AI Processing Guidelines

1. **File Size**
   - Keep individual files under 100KB
   - Split large components into subcomponents
   - Use references instead of duplication

2. **Structure**
   - Use consistent indentation
   - Follow naming conventions
   - Keep nesting depth reasonable

3. **Documentation**
   - Include clear descriptions
   - Document all assumptions
   - Provide usage examples

/////////////////////


# L2-SpecDSL Requirements Specification

## 1. Overview

L2-SpecDSL is a domain-specific language designed to define detailed system components and their interactions, building upon the architectural decisions captured in L1-DesignDSL. This language serves as the bridge between high-level architecture and implementation guidelines.

## 2. Core Objectives

- Define detailed system components and their structure
- Specify interfaces and data models
- Express relationships between components
- Define configuration parameters
- Specify validation rules and constraints
- Support code generation

## 3. Language Elements

### 3.1 Module Definition

```
module <ModuleName> {
    description: "<module description>"
    version: "<version number>"
    owner: "<team/owner>"
    dependencies: [
        "<module1>",
        "<module2>"
    ]
}
```

### 3.2 Interface Definition

```
interface <InterfaceName> {
    description: "<interface description>"
    version: "<version number>"
    
    methods {
        <MethodName>: {
            input: [
                {name: "<param>", type: "<type>", required: true/false}
            ]
            output: {
                type: "<return type>"
                format: "<format specification>"
            }
            throws: ["<exception1>", "<exception2>"]
            rateLimit: "<limit specification>"
            timeout: "<timeout value>"
        }
    }
    
    authentication: "<auth type>"
    authorization: ["<role1>", "<role2>"]
}
```

### 3.3 Data Model Definition

```
model <ModelName> {
    description: "<model description>"
    version: "<version number>"
    
    attributes {
        <AttributeName>: {
            type: "<data type>"
            required: true/false
            defaultValue: "<default>"
            validation: {
                pattern: "<regex pattern>"
                min: "<minimum value>"
                max: "<maximum value>"
                enum: ["<value1>", "<value2>"]
            }
        }
    }
    
    relationships {
        <RelationshipName>: {
            type: "OneToOne" | "OneToMany" | "ManyToMany"
            target: "<TargetModel>"
            cascade: "ALL" | "NONE" | "DELETE"
        }
    }
    
    indexes: [
        {
            fields: ["<field1>", "<field2>"]
            unique: true/false
        }
    ]
}
```

### 3.4 Component Configuration

```
configuration <ConfigName> {
    environment: "<env name>"
    parameters: {
        <ParamName>: {
            type: "<type>"
            defaultValue: "<default>"
            description: "<description>"
            required: true/false
        }
    }
    dependencies: {
        <DependencyName>: "<version constraint>"
    }
}
```

### 3.5 Service Definition

```
service <ServiceName> {
    implementation: "<implementation type>"
    interfaces: ["<interface1>", "<interface2>"]
    
    configuration: {
        pool: {
            min: "<minimum instances>"
            max: "<maximum instances>"
        }
        retry: {
            attempts: "<number>"
            backoff: "<backoff strategy>"
        }
        circuit: {
            threshold: "<failure threshold>"
            timeout: "<reset timeout>"
        }
    }
    
    dependencies: {
        services: ["<service1>", "<service2>"]
        resources: ["<resource1>", "<resource2>"]
    }
}
```

### 3.6 Resource Definition

```
resource <ResourceName> {
    type: "<resource type>"
    provider: "<provider name>"
    
    configuration: {
        size: "<resource size>"
        replicas: "<replica count>"
        backup: {
            frequency: "<backup frequency>"
            retention: "<retention period>"
        }
    }
    
    access: {
        read: ["<role1>", "<role2>"]
        write: ["<role3>", "<role4>"]
    }
}
```

## 4. Constraints and Rules

1. Naming Conventions
   - All names must be in PascalCase for types/models
   - Method names must be in camelCase
   - Names must be unique within their scope
   
2. Version Control
   - All components must specify versions
   - Versions must follow semantic versioning
   - Dependencies must specify version constraints

3. Dependencies
   - Circular dependencies are not allowed
   - All dependencies must be declared
   - Version compatibility must be maintained

4. Security
   - Authentication must be specified for public interfaces
   - Authorization rules must be defined
   - Resource access must be explicitly granted

## 5. Integration Points

### 5.1 Input from L1-DesignDSL
- System boundaries and capabilities
- Architectural patterns
- Integration requirements
- Non-functional requirements

### 5.2 Output to L3-GuideDSL
- Component specifications
- Interface contracts
- Implementation details
- Configuration parameters

## 6. Example

```
module OrderManagement {
    description: "Handles order processing and management"
    version: "1.0.0"
    owner: "Commerce Team"
    
    interface OrderService {
        description: "Order management operations"
        version: "1.0.0"
        
        methods {
            createOrder: {
                input: [
                    {name: "orderDetails", type: "OrderCreateRequest", required: true}
                ]
                output: {
                    type: "OrderResponse"
                    format: "JSON"
                }
                throws: ["ValidationException", "SystemException"]
                rateLimit: "100/second"
                timeout: "5s"
            }
        }
        
        authentication: "OAuth2"
        authorization: ["ORDER_CREATE", "ORDER_ADMIN"]
    }
    
    model Order {
        description: "Order data model"
        version: "1.0.0"
        
        attributes {
            orderId: {
                type: "UUID"
                required: true
            }
            items: {
                type: "Array<OrderItem>"
                required: true
            }
            total: {
                type: "Decimal"
                required: true
                validation: {
                    min: "0.01"
                }
            }
            status: {
                type: "String"
                required: true
                validation: {
                    enum: ["CREATED", "PAID", "SHIPPED", "DELIVERED"]
                }
            }
        }
        
        relationships {
            customer: {
                type: "ManyToOne"
                target: "Customer"
                cascade: "NONE"
            }
        }
        
        indexes: [
            {
                fields: ["orderId"]
                unique: true
            }
        ]
    }
}
```

## 7. Validation Rules

1. Interface Validation:
   - All methods must have defined inputs and outputs
   - Rate limits must be specified
   - Authentication and authorization must be defined

2. Model Validation:
   - Required attributes must be marked
   - Validation rules must be complete
   - Relationships must reference valid models

3. Configuration Validation:
   - Required parameters must have defaults
   - Environment values must be valid
   - Resource limits must be specified

## 8. Best Practices

1. Component Design:
   - Keep components focused and cohesive
   - Follow single responsibility principle
   - Design for testability
   - Document all assumptions

2. Interface Design:
   - Use consistent naming patterns
   - Define clear error responses
   - Include rate limiting
   - Document all methods

3. Model Design:
   - Use appropriate data types
   - Define clear relationships
   - Include validation rules
   - Consider performance impacts

## 9. Tooling Requirements

1. Development Tools:
   - Syntax highlighting
   - Code completion
   - Validation checking
   - Quick fixes

2. Generation Tools:
   - Code generation
   - Documentation generation
   - Test case generation
   - Configuration generation

3. Integration Tools:
   - Version control integration
   - CI/CD pipeline integration
   - Dependency management
   - Code review tools