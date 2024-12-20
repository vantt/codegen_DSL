## 5. Validation Rules

### 5.1 Rule Structure
```
@validation {
    rules: [
        {
            name: string
            condition: expression
            severity: "error" | "warning" | "info"
            message: string
        }
    ]
    
    scopes: {
        global: [rule_reference]
        section: [rule_reference]
        relationship: [rule_reference]
    }
}
