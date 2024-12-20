## 2. Type System

### 2.1 Primitive Types
```
@types {
    // Basic types
    STRING {
        validation: ["pattern", "length", "enum"]
    }
    
    INTEGER {
        validation: ["range", "enum", "step"]
    }
    
    FLOAT {
        validation: ["range", "precision"]
    }
    
    BOOLEAN
    
    DATE {
        validation: ["range", "format"]
    }
    
    // Collection types
    ARRAY {
        element_type: type_reference
        validation: ["minItems", "maxItems", "unique"]
    }
    
    MAP {
        key_type: type_reference
        value_type: type_reference
        validation: ["required_keys"]
    }

    // Custom type definition
    CustomType {
        type: <base_type>
        validation: [<rules>]
        constraints: {
            <constraint_definitions>
        }
    }

    // Enum definition
    EnumType {
        type: enum
        values: [<value_list>]
        default: <default_value>
    }
}
