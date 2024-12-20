## 7. Extension Points

### 7.1 Custom Types
```
@extensions {
    types {
        definition: {
            name: identifier
            base: type_reference
            properties: [property_def]
        }
    }
    
    relationships {
        definition: {
            name: identifier
            base: RelationType
            properties: [property_def]
        }
    }
    
    validation {
        rules: {
            name: identifier
            params: [param_def]
            implementation: code_block
        }
    }
}
