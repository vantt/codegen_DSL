# L1-DesignDSL Language Syntax Specification

## 1. Overview

L1-DesignDSL is a domain-specific language designed to capture high-level system design decisions and architectural elements. It serves as the bridge between initial application ideas and detailed system specifications.

## 2. Base Types
```
Priority: HIGH | MED | LOW
Status: DRAFT | REVIEW | APPROVED
DocStatus: PLANNED | IN_PROG | DONE
Protocol: REST | GRPC | MQ
Style: MICROSERVICES | MONOLITH | SERVERLESS
DataSensitivity: HIGH | MED | LOW
Availability: 24x7 | BUSINESS_HOURS
DataType: MASTER | TRANS | REF
```

## 3. Language Structure

### 3.1 Document Header
```
L1Design v1.0 {
  metadata {
    created: "2024-12-18"
    updated: "2024-12-18"
    owner: "TeamName"
    status: DRAFT | REVIEW | APPROVED
    replaces: "v0.9"
    tags: ["tag1", "tag2"]
  }

  // Main content follows...
}
```

### 3.2 Core Sections

1. System Purpose
```
system "SystemName" {
  vision: "Vision statement"
  mission: "Mission statement"
  value {
    problem: "Problem description"
    solution: "Solution approach"
    users: ["User1", "User2"]
  }
}
```

2. Business Context
```
business {
  stakeholders: ["Role1", "Role2"]
  success_metrics: {
    "Metric1": "Target value"
    "Metric2": "Target value"
  }
  timeline: {
    phase1: "Description - Q1 2025"
    phase2: "Description - Q2 2025"
  }
}
```

3. Capabilities
```
capabilities {
  "CapabilityName" {
    description: "Description"
    priority: HIGH | MED | LOW
    status: PLANNED | IN_PROG | DONE
    depends: ["Cap1", "Cap2"]
  }
}
```

4. System Boundaries
```
boundaries {
  internal {
    "SubsystemName" {
      role: "Responsibility"
      critical: YES | NO
    }
  }
  external {
    "SystemName" {
      type: API | DB | SERVICE
      protocol: REST | GRPC | MQ
      sla: "99.9%"
      failover: "Strategy description"
      rate_limits: "1000/minute"
      data_sensitivity: HIGH | MED | LOW
      availability: "24/7 | Business Hours"
    }
  }
}
```

5. Architecture
```
architecture {
  patterns: ["Pattern1", "Pattern2"]
  principles: ["Principle1", "Principle2"]
  constraints: ["Constraint1"]
  tech_stack: {
    backend: ["Tech1", "Tech2"]
    frontend: ["Tech1", "Tech2"]
    data: ["Tech1", "Tech2"]
  }
  style: MICROSERVICES | MONOLITH | SERVERLESS
  decisions {
    "D1" {
      choice: "Selected approach"
      reason: "Rationale"
      impact: HIGH | MED | LOW
    }
  }
}
```

6. Data Strategy
```
data {
  lifecycle {
    retention: "Duration"
    archival: "Strategy"
    cleanup: "Policy"
  }
  domains {
    "DomainName" {
      owner: "TeamName"
      type: MASTER | TRANS | REF
      quality: HIGH | MED | LOW
    }
  }
  flows {
    "FlowName" {
      source: "SourceSystem"
      target: "TargetSystem"
      type: SYNC | ASYNC
      volume: "Rate"
    }
  }
}
```

7. Non-Functional Requirements
```
requirements {
  performance {
    latency: "Target"
    throughput: "Target"
  }
  scaling {
    users: "Target"
    data: "Growth rate"
  }
  security {
    auth: YES | NO
    encrypt: YES | NO
  }
}
```

8. References
```
references {
  specs: ["file1.l2spec", "file2.l2spec"]
  guides: ["guide1.l3guide", "guide2.l3guide"]
  docs: ["doc1.md", "doc2.md"]
}
```

## 4. Complete Example

```
L1Design v1.0 {
  metadata {
    created: "2024-12-18"
    updated: "2024-12-18"
    owner: "EcommerceTeam"
    status: DRAFT
    tags: ["ecommerce", "microservices"]
  }

  system "EcommerceSystem" {
    vision: "One-stop shopping platform"
    mission: "Provide seamless shopping experience"
    value {
      problem: "Complex shopping process"
      solution: "Unified shopping platform"
      users: ["Shoppers", "Sellers"]
    }
  }

  business {
    stakeholders: ["Product", "Sales", "Support"]
    success_metrics: {
      "UserGrowth": "10% monthly"
      "Revenue": "1M ARR"
      "Satisfaction": "CSAT > 4.5"
    }
    timeline: {
      phase1: "MVP - Q1 2025"
      phase2: "International - Q3 2025"
    }
  }

  capabilities {
    "OrderManagement" {
      description: "End-to-end order processing"
      priority: HIGH
      status: IN_PROG
      depends: ["Payment", "Inventory"]
    }
    "UserManagement" {
      description: "User account and profile management"
      priority: HIGH
      status: DONE
      depends: ["Authentication"]
    }
  }

  boundaries {
    internal {
      "OrderEngine" {
        role: "Order processing and fulfillment"
        critical: YES
      }
      "UserPortal" {
        role: "User interface and experience"
        critical: YES
      }
    }
    external {
      "PaymentGateway" {
        type: API
        protocol: REST
        sla: "99.99%"
        failover: "Active-active with regional fallback"
        rate_limits: "1000/minute"
        data_sensitivity: HIGH
        availability: "24/7"
      }
    }
  }

  architecture {
    patterns: ["Microservices", "CQRS", "Event-Sourcing"]
    principles: ["API-First", "Cloud-Native", "Security-by-Design"]
    constraints: ["Must use approved cloud services"]
    tech_stack: {
      backend: ["Java", "Python"]
      frontend: ["React", "TypeScript"]
      data: ["PostgreSQL", "Redis", "Kafka"]
    }
    style: MICROSERVICES
    decisions {
      "D1" {
        choice: "Event-driven architecture"
        reason: "Support scale and decoupling"
        impact: HIGH
      }
    }
  }

  data {
    lifecycle {
      retention: "7 years"
      archival: "After 1 year to cold storage"
      cleanup: "30 days after account closure"
    }
    domains {
      "Orders" {
        owner: "OrderTeam"
        type: MASTER
        quality: HIGH
      }
      "ProductCatalog" {
        owner: "ProductTeam"
        type: MASTER
        quality: HIGH
      }
    }
    flows {
      "OrderProcessing" {
        source: "OrderSystem"
        target: "PaymentSystem"
        type: ASYNC
        volume: "1000/s"
      }
    }
  }

  requirements {
    performance {
      latency: "200ms"
      throughput: "1000/s"
    }
    scaling {
      users: "1M"
      data: "500GB/month"
    }
    security {
      auth: YES
      encrypt: YES
    }
  }

  references {
    specs: ["OrderAPI.l2spec", "UserAPI.l2spec"]
    guides: ["Deployment.l3guide", "Testing.l3guide"]
    docs: ["Architecture.md", "API.md"]
  }
}
```

## 5. Validation Rules

1. Document Structure:
   - All required sections must be present
   - Section order should follow specification
   - No unknown sections allowed
   - No duplicate sections

2. Fields and Values:
   - All required fields must be present
   - Enum values must match defined types
   - References must be valid
   - IDs must be unique

3. Naming Conventions:
   - System names: PascalCase
   - Field names: camelCase
   - Enums: UPPER_CASE
   - Files: lowercase with appropriate extension

## 6. Best Practices

1. Documentation:
   - Keep descriptions clear and concise
   - Include rationale for key decisions
   - Maintain consistent detail level
   - Use concrete, measurable values

2. Structure:
   - Group related elements together
   - Maintain consistent formatting
   - Use appropriate indentation
   - Keep nesting levels reasonable

3. Integration:
   - Reference all external dependencies
   - Specify version requirements
   - Document integration points
   - Include necessary context