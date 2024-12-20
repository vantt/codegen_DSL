## 8. Usage Example
```
@dsl v1.0 {
    @metadata {
        name: "ComponentSpec"
        version: "1.0.0"
        description: "Component specification example"
    }

    @types {
        Visibility: enum {
            values: ["public", "private", "internal"]
            default: "internal"
        }
    }

    @sections {
        module "AuthModule" {
            @relationship {
                type: "containment"
                source: "AuthModule"
                target: ["UserService", "TokenService"]
                cardinality: "1..*"
                attributes: [
                    {name: "visibility", value: "internal"}
                ]
            }
        }
    }

    @validation {
        rules: [
            {
                name: "visibility_check"
                condition: "module.visibility in Visibility"
                severity: "error"
                message: "Invalid visibility value"
            }
        ]
    }
}
