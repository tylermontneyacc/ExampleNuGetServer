# Overview
An ASP.NET project with NuGet Server which requires a client certificate. Created for https://github.com/PowerShell/PowerShell/issues/18209
# Configuration
- ASP.NET 4.8
- NuGet Server 3.4.2
- applicationhost.config
  - sslFlags: `Ssl, SslNegotiateCert, SslRequireCert`
  - iisClientCertificateMappingAuthentication: `true`
# Using this project
You will need a certificate signed by a trusted CA, as IIS will only accept certs that it trusts.
1. Import your trusted client certificate into PowerShell: `$GoodCert = Get-ChildItem -Path "Cert:\LocalMachine\My" -Recurse | Where-Object { $_.Thumbprint -eq "123ABC..." }`
1. Generate a bad cert: `$BadCert = New-SelfSignedCertificate -DnsName "www.fabrikam.com", "www.contoso.com" -CertStoreLocation "cert:\LocalMachine\My"`
    - **Remember to delete this once finished testing!**
1. Run the project in debug and copy the URL from the browser
1. `Invoke-WebRequest -Uri "https://localhost:12345/"` should fail
1. `Invoke-WebRequest -Uri "https://localhost:12345/" -Certificate $GoodCert` should succeed
1. `Invoke-WebRequest -Uri "https://localhost:12345/" -Certificate $BadCert` should fail
