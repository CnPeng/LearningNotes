[原文：《Recognizing API and implementation dependencies》](https://docs.gradle.org/current/userguide/java_library_plugin.html#sec:java_library_recognizing_dependencies)



第一原则：`Prefer the implementation configuration over api when possible` ，也就是说，优先选用 `implementation` 而不是 `api`


Configuration name	| Role	 | Consumable? | 	Resolvable? | 	Description
---|---|---|---|---
`api` | Declaring API dependencies | no | no | This is where you declare dependencies which are transitively exported to consumers, for compile time and runtime.
`implementation` | Declaring implementation dependencies | no | no | This is where you declare dependencies which are purely internal and not meant to be exposed to consumers (they are still exposed to consumers at runtime).
`compileOnly` | Declaring compile only dependencies | no | no | This is where you declare dependencies which are required at compile time, but not at runtime. This typically includes dependencies which are shaded when found at runtime.
`compileOnlyApi` | Declaring compile only API dependencies | no | no | This is where you declare dependencies which are required at compile time by your module and consumers, but not at runtime. This typically includes dependencies which are shaded when found at runtime.
`runtimeOnly` | Declaring runtime dependencies | no | no | This is where you declare dependencies which are only required at runtime, and not at compile time.
`testImplementation` | Test dependencies | no | no | This is where you declare dependencies which are used to compile tests.
`testCompileOnly` | Declaring test compile only dependencies | no | no | This is where you declare dependencies which are only required at test compile time, but should not leak into the runtime. This typically includes dependencies which are shaded when found at runtime.
`testRuntimeOnly` | Declaring test runtime dependencies | no | no | This is where you declare dependencies which are only required at test runtime, and not at test compile time.