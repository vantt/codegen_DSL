# Component Specification DSL

## Basic Structure
```
component <ComponentName> v<Version> {
    metadata { ... }
    core { ... }
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

## 1. Metadata Section
```
meta {
    team: "<TeamName>"
    updated: "<YYYY-MM-DD>"
    status: DRAFT|REVIEW|DONE
    tags: ["tag1", "tag2"]
}
```

## 2. Core Configuration
```
core {
    type: SVC|LIB|MOD     # service, library, module
    
    lifecycle {
        initialization {
            dependencies: ["Database", "MessageQueue", "Cache"]  # required dependencies
            order: SEQUENTIAL | PARALLEL                        # how to initialize dependencies
            timeout: "30s"                                      # max initialization time
            retry: {
                attempts: 3
                backoff: "exponential"                         # or "fixed", "linear"
                interval: "5s"
            }
        }
        
        startup {
            order: SEQUENTIAL | PARALLEL                       # how to start component services
            phases: [                                         # ordered startup phases
                {
                    name: "ConnectDatabase"
                    timeout: "10s"
                    required: true
                },
                {
                    name: "WarmCache"
                    timeout: "5s"
                    required: false
                }
            ]
            readiness: {                                      # readiness check
                path: "/health"
                interval: "5s"
                threshold: 3                                  # successful checks needed
            }
        }
        
        shutdown {
            order: SEQUENTIAL | PARALLEL                      # how to stop component services
            phases: [                                        # ordered shutdown phases
                {
                    name: "StopNewRequests"
                    timeout: "5s"
                },
                {
                    name: "CompleteProcessing"
                    timeout: "30s"
                },
                {
                    name: "CloseConnections"
                    timeout: "10s"
                }
            ]
            gracePeriod: "45s"                              # total shutdown time allowed
        }
        
        health {                                           # ongoing health management
            checks: [
                {
                    name: "DatabaseConnection"
                    interval: "30s"
                    timeout: "5s"
                    critical: true
                },
                {
                    name: "CacheConnection"
                    interval: "15s"
                    timeout: "3s"
                    critical: false
                }
            ]
            actions: {                                    # actions on health state changes
                onUnhealthy: RESTART | ALERT | IGNORE
                onRecovery: RESUME | REINITIALIZE
            }
        }
    }
    
    configuration {
        // Environment-specific configs
        environments: {
            development: {
                active: true                    // is this environment active
                override: "config/dev.yaml"     // override file location
            }
            production: {
                active: false
                override: "config/prod.yaml"
            }
        }

        // Parameter definitions
        parameters: {
            // Simple parameter with basic type
            "serverPort": {
                type: INTEGER
                default: 8080
                required: true
                validation: {
                    range: [1024, 65535]
                }
                description: "HTTP server port"
            }

            // Database configuration group
            "database": {
                type: GROUP
                parameters: {
                    "url": {
                        type: STRING
                        required: true
                        validation: {
                            pattern: "^jdbc:postgresql://.*"
                            minLength: 10
                            maxLength: 255
                        }
                        sensitive: true          // indicates password/sensitive data
                    }
                    "poolSize": {
                        type: INTEGER
                        default: 10
                        validation: {
                            range: [1, 100]
                        }
                    }
                    "timeout": {
                        type: DURATION
                        default: "30s"
                        validation: {
                            range: ["1s", "300s"]
                        }
                    }
                }
            }

            // Feature flags group
            "features": {
                type: GROUP
                parameters: {
                    "caching": {
                        type: BOOLEAN
                        default: true
                        description: "Enable response caching"
                    }
                    "rateLimit": {
                        type: GROUP
                        parameters: {
                            "enabled": {
                                type: BOOLEAN
                                default: false
                            }
                            "limit": {
                                type: INTEGER
                                default: 1000
                                validation: {
                                    range: [1, 10000]
                                }
                            }
                            "period": {
                                type: DURATION
                                default: "1m"
                            }
                        }
                    }
                }
            }

            // Array type example
            "allowedOrigins": {
                type: ARRAY
                itemType: STRING
                default: ["http://localhost:3000"]
                validation: {
                    minItems: 1
                    maxItems: 10
                    pattern: "^https?://.*"
                }
            }

            // Enum type example
            "logLevel": {
                type: ENUM
                values: ["DEBUG", "INFO", "WARN", "ERROR"]
                default: "INFO"
            }
        }

        // Dependent configurations
        dependencies: {
            "caching.enabled": [
                {
                    parameter: "caching.provider"
                    required: true
                },
                {
                    parameter: "caching.ttl"
                    required: true
                }
            ]
        }

        // Dynamic configuration
        dynamic: {
            "poolSize": {
                refreshInterval: "1m"
                onUpdate: GRACEFUL    // GRACEFUL, IMMEDIATE, or RESTART
            }
        }
    }
}
```

## 3. Interfaces
```
interfaces {
    api "<ApiName>" v<Version> {
        type: REST|GRPC|MQ
        auth: NONE|BASIC|JWT
        rate: "<limit/time>"
        
        ops {              # operations
            "<OpName>": {
                in: ["Type1"]
                out: "Type2"
                err: ["Err1", "Err2"]
                sla: "<duration>"
            }
        }
    }
    
    events {
        pub: ["Event1"]    # publish
        sub: ["Event2"]    # subscribe
    }
}
```

## 4. Behavior
```
behavior {
    state {
        init: "STATE1"
        flow: {
            "STATE1": ["STATE2", "STATE3"]
            "STATE2": ["STATE3"]
        }
    }
    
    rules {
        "RULE1": "<condition> => <action>"
        "RULE2": {
            when: "<condition>"
            then: ["<action1>", "<action2>"]
        }
    }
}
```

## 5. Models
```
models {
    "<ModelName>": {
        attr {
            field1: STR|INT|BOOL|REF
            field2: {
                type: STR|INT|BOOL|REF
                req: true|false
                def: "<value>"
                valid: "<rule>"
            }
        }
        
        rel {               # relationships
            "rel1": {
                to: "<Model>"
                type: 1-1|1-N|N-N
                cascade: ALL|NONE
            }
        }
        
        idx: [              # indexes
            {
                fields: ["f1", "f2"]
                unique: true|false
            }
        ]
    }
}
```

## 6. Integrations
```
integrations {
    external {            # external systems
        "<SysName>": {
            type: API|DB|SVC
            proto: REST|GRPC|MQ
            sla: "<value>"
            retry: {
                max: <number>
                delay: "<duration>"
            }
        }
    }
}
```

## 7. Errors
```
errors {
    "<ErrorName>": {
        code: "<code>"
        severity: LOW|MED|HIGH
        retry: true|false
        handle: RETRY|FAIL|IGNORE
    }
}
```

## 8. Resources
```
resources {
    compute {
        cpu: "<limit>"
        mem: "<limit>"
        store: "<limit>"
    }
    
    deps {                 # dependencies
        lib: ["<lib1>"]
        svc: ["<svc1>"]
    }
}
```

## 9. Tests
```
test {
    unit: ["<TestPattern>"]
    int: ["<TestPattern>"]   # integration
    perf: {
        latency: "<limit>"
        rps: "<limit>"
    }
}
```

## 10. Cross-cutting
```
crosscutting {
    security {            # security
        auth: true|false
        roles: ["<role1>"]
        encrypt: true|false
    }
    
    monitor {
        metrics: ["<metric1>"]
        alerts: ["<alert1>"]
        logs: ["<pattern1>"]
    }
}
```

## Example Usage
```
component OrderService v1.0 {
    metadata {
        team: "Commerce"
        updated: "2024-12-18"
        status: DRAFT
    }
    
    core {
        type: SVC
        life {
            init: ["DB", "MQ"]
            start: SEQ
        }
        cfg {
            dbUrl: {
                type: STR
                req: true
                valid: "url"
            }
        }
    }
    
    interfaces {
        api "OrderAPI" v1.0 {
            type: REST
            auth: JWT
            rate: "1000/s"
            
            ops {
                "create": {
                    in: ["OrderReq"]
                    out: "OrderRes"
                    err: ["ValErr", "SysErr"]
                    sla: "200ms"
                }
            }
        }
    }
    
    mod {
        "Order": {
            attr {
                id: STR
                amount: {
                    type: INT
                    req: true
                    valid: ">0"
                }
            }
        }
    }
}
```

## AI Processing Guidelines

1. Parsing Rules:
   - Section names are normalized
   - Types are enumerated
   - Values use consistent formats
   - Relationships are explicit

2. Validation Points:
   - Required sections presence
   - Type compatibility
   - Reference validity
   - Constraint compliance

3. Generation Targets:
   - Interface contracts
   - Data models
   - Configuration schemas
   - Test templates