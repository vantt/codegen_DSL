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
