# .NET Aspire Dashboard

## Featured Tags

* `9.3`
  * `docker pull mcr.microsoft.com/dotnet/aspire-dashboard:9.3`

## About

This image contains the .NET Aspire Dashboard.

Watch [discussions](https://github.com/dotnet/dotnet-docker/discussions/categories/announcements) for Docker-related .NET announcements.

## Usage

The .NET Aspire Dashboard is a browser-based app to view run-time information about your distributed application.

The dashboard shows:

* Resources that make up your app, such as .NET projects, executables and containers.
* Live console logs of resources.
* Live telemetry, such as structured logs, traces and metrics.

### Configuration

The dashboard must be configured when it is started. The configuration is done via environment variables. The following environment variables are supported:

* `ASPNETCORE_URLS` specifies one or more HTTP endpoints through which the dashboard frontend is served. The frontend endpoint is used to view the dashboard in a browser. Defaults to `http://localhost:18888`.
* `DOTNET_DASHBOARD_OTLP_ENDPOINT_URL` specifies the [OTLP/gRPC](https://opentelemetry.io/docs/specs/otlp/#otlpgrpc) endpoint. This endpoint hosts an OTLP service and receives telemetry using gRPC. When the dashboard is launched by the .NET Aspire app host this address is secured with HTTPS. Securing the dashboard with HTTPS is recommended. Defaults to `http://localhost:18889`.
* `DOTNET_DASHBOARD_OTLP_HTTP_ENDPOINT_URL` specifies the [OTLP/HTTP](https://opentelemetry.io/docs/specs/otlp/#otlphttp) endpoint. This endpoint hosts an OTLP service and receives telemetry using Protobuf over HTTP. Defaults to `http://localhost:18890`.
* `DOTNET_DASHBOARD_UNSECURED_ALLOW_ANONYMOUS` specifies the dashboard doesn't use authentication and accepts anonymous access. This setting is a shortcut to configuring `Dashboard:Frontend:AuthMode` and `Dashboard:Otlp:AuthMode` to `Unsecured`.
* `DOTNET_DASHBOARD_CONFIG_FILE_PATH` specifies the path for an optional JSON configuration file.

#### Frontend authentication

The dashboard's frontend supports OpenID Connect (OIDC). Set `Dashboard__Frontend__AuthMode` to `OpenIdConnect`, then add the following configuration:

* `Authentication__Schemes__OpenIdConnect__Authority` &mdash; URL to the identity provider (IdP)
* `Authentication__Schemes__OpenIdConnect__ClientId` &mdash; Identity of the relying party (RP)
* `Authentication__Schemes__OpenIdConnect__ClientSecret`&mdash; A secret that only the real RP would know
* Other properties of [`OpenIdConnectOptions`](https://learn.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.openidconnectoptions) specified in configuration container `Authentication__Schemes__OpenIdConnect__*`

It may also be run unsecured. Set `Dashboard__Frontend__AuthMode` to `Unsecured`. The frontend endpoint will allow anonymous access. This setting is used during local development, but is not recommended if you attempt to host the dashboard in other settings.

### OTLP authentication

The OTLP endpoint can be secured with [client certificate](https://learn.microsoft.com/aspnet/core/security/authentication/certauth) or API key authentication.

For client certification authentication, set `Dashboard__Otlp__AuthMode` to `Certificate`.

For API key authentication, set `Dashboard__Otlp__AuthMode` to `ApiKey`, then add the following configuration:

* `Dashboard__Otlp__PrimaryApiKey` specifies the primary API key. (required, string)
* `Dashboard__Otlp__SecondaryApiKey` specifies the secondary API key. (optional, string)

It may also be run unsecured. Set `Dashboard__Otlp__AuthMode` to `Unsecured`. The OTLP endpoint will allow anonymous access. This setting is used during local development, but is not recommended if you attempt to host the dashboard in other settings.

### Resources

* `Dashboard__ResourceServiceClient__Url` specifies the gRPC endpoint to which the dashboard connects for its data. There's no default. If this variable is unspecified, the dashboard shows OTEL data but no resource list or console logs.

The resource service client supports certificates. Set `Dashboard__ResourceServiceClient__AuthMode` to `Certificate`, then add the following configuration:

* `Dashboard__ResourceServiceClient__ClientCertificate__Source` (required) one of:
  * `File` to load the cert from a file path, configured with:
    * `Dashboard__ResourceServiceClient__ClientCertificate__FilePath` (required, string)
    * `Dashboard__ResourceServiceClient__ClientCertificate__Password` (optional, string)
  * `KeyStore` to load the cert from a key store, configured with:
    * `Dashboard__ResourceServiceClient__ClientCertificate__Subject` (required, string)
    * `Dashboard__ResourceServiceClient__ClientCertificate__Store` (optional, [`StoreName`](https://learn.microsoft.com/dotnet/api/system.security.cryptography.x509certificates.storename), defaults to `My`)
    * `Dashboard__ResourceServiceClient__ClientCertificate__Location` (optional, [`StoreLocation`](https://learn.microsoft.com/dotnet/api/system.security.cryptography.x509certificates.storelocation), defaults to `CurrentUser`)

To opt-out of authentication, set `Dashboard__ResourceServiceClient__AuthMode` to `Unsecured`. This completely disables all security for the resource service client. This setting is used during local development, but is not recommended if you attempt to host the dashboard in other settings.

#### Telemetry Limits

Telemetry is stored in-memory. To avoid excessive memory usage, the dashboard has limits on the count and size of stored telemetry. When a count limit is reached, new telemetry is added, and the oldest telemetry is removed. When a size limit is reached, data is truncated to the limit.

* `Dashboard__TelemetryLimits__MaxLogCount` specifies the maximum number of log entries. Defaults to 10,000.
* `Dashboard__TelemetryLimits__MaxTraceCount` specifies the maximum number of traces. Defaults to 10,000.
* `Dashboard__TelemetryLimits__MaxMetricsCount` specifies the maximum number of metric data points. Defaults to 50,000.
* `Dashboard__TelemetryLimits__MaxAttributeCount` specifies the maximum number of attributes on telemetry. Defaults to 128.
* `Dashboard__TelemetryLimits__MaxAttributeLength` specifies the maximum length of attributes. Defaults to unlimited.
* `Dashboard__TelemetryLimits__MaxSpanEventCount` specifies the maximum number of events on span attributes. Defaults to unlimited.

Limits are per-resource. For example, a `MaxLogCount` value of 10,000 configures the dashboard to store up to 10,000 log entries per-resource.

### Other

* `Dashboard__ApplicationName` specifies the application name to be displayed in the UI. This applies only when no resource service URL is specified. When a resource service exists, the service specifies the application name.

## Related Repositories

.NET:

* [dotnet](https://github.com/dotnet/dotnet-docker/blob/main/README.md): .NET
* [dotnet/sdk](https://github.com/dotnet/dotnet-docker/blob/main/README.sdk.md): .NET SDK
* [dotnet/aspnet](https://github.com/dotnet/dotnet-docker/blob/main/README.aspnet.md): ASP.NET Core Runtime
* [dotnet/runtime](https://github.com/dotnet/dotnet-docker/blob/main/README.runtime.md): .NET Runtime
* [dotnet/runtime-deps](https://github.com/dotnet/dotnet-docker/blob/main/README.runtime-deps.md): .NET Runtime Dependencies
* [dotnet/monitor](https://github.com/dotnet/dotnet-docker/blob/main/README.monitor.md): .NET Monitor Tool
* [dotnet/nightly/aspire-dashboard](https://github.com/dotnet/dotnet-docker/blob/nightly/README.aspire-dashboard.md): .NET Aspire Dashboard (Preview)
* [dotnet/samples](https://github.com/dotnet/dotnet-docker/blob/main/README.samples.md): .NET Samples

.NET Framework:

* [dotnet/framework](https://github.com/microsoft/dotnet-framework-docker/blob/main/README.md): .NET Framework, ASP.NET and WCF
* [dotnet/framework/samples](https://github.com/microsoft/dotnet-framework-docker/blob/main/README.samples.md): .NET Framework, ASP.NET and WCF Samples

## Full Tag Listing

### Linux amd64 Tags

Tags | Dockerfile | OS Version
-----------| -------------| -------------
9.3.0, 9.3, 9, latest | [Dockerfile](src/aspire-dashboard/9.3/azurelinux-distroless/amd64/Dockerfile) | Azure Linux 3.0

### Linux arm64 Tags

Tags | Dockerfile | OS Version
-----------| -------------| -------------
9.3.0, 9.3, 9, latest | [Dockerfile](src/aspire-dashboard/9.3/azurelinux-distroless/arm64v8/Dockerfile) | Azure Linux 3.0
<!--End of generated tags-->

*Tags not listed in the table above are not supported. See the [Supported Tags Policy](https://github.com/dotnet/dotnet-docker/blob/main/documentation/supported-tags.md). See the [full list of tags](https://mcr.microsoft.com/v2/dotnet/aspire-dashboard/tags/list) for all supported and unsupported tags.*

## Support

### Lifecycle

* [Microsoft Support for .NET](https://github.com/dotnet/core/blob/main/support.md)
* [Supported Container Platforms Policy](https://github.com/dotnet/dotnet-docker/blob/main/documentation/supported-platforms.md)
* [Supported Tags Policy](https://github.com/dotnet/dotnet-docker/blob/main/documentation/supported-tags.md)

### Image Update Policy

* **Base Image Updates:** Images are re-built within 12 hours of any updates to their base images (e.g. debian:bookworm-slim, windows/nanoserver:ltsc2022, etc.).
* **.NET Releases:** Images are re-built as part of releasing new .NET versions. This includes new major versions, minor versions, and servicing releases.
* **Critical CVEs:** Images are re-built to pick up critical CVE fixes as described by the CVE Update Policy below.
* **Monthly Re-builds:** Images are re-built monthly, typically on the second Tuesday of the month, in order to pick up lower-severity CVE fixes.
* **Out-Of-Band Updates:** Images can sometimes be re-built when out-of-band updates are necessary to address critical issues. If this happens, new fixed version tags will be updated according to the [Fixed version tags documentation](https://github.com/dotnet/dotnet-docker/blob/main/documentation/supported-tags.md#fixed-version-tags).

#### CVE Update Policy

.NET container images are regularly monitored for the presence of CVEs. A given image will be rebuilt to pick up fixes for a CVE when:

* We detect the image contains a CVE with a [CVSS](https://nvd.nist.gov/vuln-metrics/cvss) score of "Critical"
* **AND** the CVE is in a package that is added in our Dockerfile layers (meaning the CVE is in a package we explicitly install or any transitive dependencies of those packages)
* **AND** there is a CVE fix for the package available in the affected base image's package repository.

Please refer to the [Security Policy](https://github.com/dotnet/dotnet-docker/blob/main/SECURITY.md) and [Container Vulnerability Workflow](https://github.com/dotnet/dotnet-docker/blob/main/documentation/vulnerability-reporting.md) for more detail about what to do when a CVE is encountered in a .NET image.

### Feedback

* [File an issue](https://github.com/dotnet/dotnet-docker/issues/new/choose)
* [Contact Microsoft Support](https://support.microsoft.com/contactus/)

## License

* Legal Notice: [Container License Information](https://aka.ms/mcr/osslegalnotice)
* [.NET license](https://github.com/dotnet/dotnet-docker/blob/main/LICENSE)
* [Discover licensing for Linux image contents](https://github.com/dotnet/dotnet-docker/blob/main/documentation/image-artifact-details.md)
