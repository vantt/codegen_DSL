## 4. Sections Definition

### 4.1 Section Structure
```
@sections {
    @definition {
        required {
            name: identifier
            type: type_reference
            validation: validation_rules[]
        }
        optional {
            description: string
            metadata: metadata_block
        }
    }

    @validation {
        scope {
            section: validation_rules[]
            cross_section: validation_rules[]
        }
    }

    @inheritance {
        strategy: "merge" | "override"
        conflict: "error" | "warn"
    }
}
