# Resource Reuse

Reuse is an experimental feature designed to simplify and enhance the development experience. Instead of disposing resources after the tests are finished, enabling reuse will retain the resources and reuse them in the next test run. Testcontainers assigns a hash value according to the builder configuration. If it identifies a matching resource, it will reuse this resource instead of creating a new one. Enabling reuse will disable the resource reaper, meaning the resource will not be cleaned up.

```csharp title="Enable container reuse"
_ = new ContainerBuilder()
  .WithReuse(true);
```

The reuse implementation does not currently consider (support) all builder APIs when calculating the hash value. Therefore, collisions may occur. To prevent collisions, simply use a distinct label to identify the resource.

```csharp title="Label container resource to identify it"
_ = new ContainerBuilder()
  .WithReuse(true)
  .WithLabel("reuse-id", "WeatherForecast");
```

!!!warning

    Reuse does not replace singleton implementations to improve test performance. Prefer proper shared instances according to your chosen test framework.

Calling `Dispose()` on a reusable container will stop it. Testcontainers will automatically start it in the next test run. This will assign a new random host port. Some services (e.g. Kafka) require the random assigned host port on the initial configuration. This may interfere with the new random assigned host port.