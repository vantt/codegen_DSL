## 3. Relationships

### 3.1 Relationship Types
```
@types {
    // Relationship primitive types
    RelationType: enum {
        values: ["reference", "containment", "association"]
        validation: ["pattern", "enum"]
    }
    
    RelationCardinality: string {
        validation: ["pattern: ^[0-9]+\\.\\.[0-9*]+$"]
    }
    
    RelationAttribute: object {
        properties: {
            name: string
            value: any
            required: boolean
        }
    }
}
```

### 3.2 Relationship Definition
```
@relationship {
    type: RelationType
    source: identifier
    target: identifier | [identifier]
    cardinality: RelationCardinality
    attributes: [
        {
            name: string
            value: any
            required: boolean
        }
    ]
}
```

### 3.3 Relationship Grammar
```
relationship_def ::= source rel_op target attributes?
rel_op         ::= "->" | "=>" | "<->"
source         ::= identifier
target         ::= identifier | "[" identifier_list "]"
attributes     ::= "[" attribute_list "]"
attribute_list ::= attribute ("," attribute)*
attribute      ::= identifier ":" value
